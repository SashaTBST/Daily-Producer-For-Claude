# Docker / Kubernetes Skill — Reference
Docker Engine 26+ / Kubernetes 1.36 / Helm 3 | Last verified: 2026-04-30

## Research Sources
- Docker best practices 2026: https://docs.docker.com/build/building/best-practices/
- Multi-stage builds: https://devtoolbox.dedyn.io/blog/docker-multi-stage-builds-guide
- Kubernetes best practices 2026: https://www.cloudzero.com/blog/kubernetes-best-practices/
- K8s security context: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
- Docker Compose health checks: https://last9.io/blog/docker-compose-health-checks/

---

## Multi-Stage Dockerfile (Distroless)

```dockerfile
# Stage 1: build
FROM golang:1.23-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download                          # cache deps before source copy
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-w -s" -o /app/service .

# Stage 2: minimal runtime (4 MB vs 1 GB+)
FROM gcr.io/distroless/base-debian12:nonroot
COPY --from=builder --chown=nonroot:nonroot /app/service /app/service
EXPOSE 8080
USER nonroot
ENTRYPOINT ["/app/service"]
```

Base image selection:
- `distroless/base-debian12:nonroot` — best security, glibc compatible
- `alpine:3.x` — smaller, but musl libc can cause issues
- `debian:bookworm-slim` — middle ground with broader compatibility

---

## Docker Compose v2 — Health Checks and Dependencies

```yaml
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: appdb
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d appdb"]
      interval: 5s
      timeout: 5s
      retries: 5
      start_period: 10s

  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      postgres:
        condition: service_healthy   # waits for health, not just started
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

---

## Kubernetes Deployment — Full Production Template

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      serviceAccountName: myapp
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 3000
        seccompProfile:
          type: RuntimeDefault
      containers:
      - name: app
        image: myregistry.io/myapp:v1.2.3  # never :latest
        resources:
          requests:
            cpu: "250m"
            memory: "512Mi"
          limits:
            cpu: "1000m"
            memory: "1Gi"
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop: [ALL]
        startupProbe:        # for slow-starting apps
          httpGet:
            path: /health/startup
            port: 8080
          periodSeconds: 10
          failureThreshold: 30
        livenessProbe:       # restart unhealthy pods
          httpGet:
            path: /health/live
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
          failureThreshold: 3
        readinessProbe:      # route traffic only when ready
          httpGet:
            path: /health/ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 2
        volumeMounts:
        - name: tmp
          mountPath: /tmp
      volumes:
      - name: tmp
        emptyDir: {}
```

---

## PodDisruptionBudget (Required for Production)

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
  namespace: production
spec:
  minAvailable: 2    # keep 2 running during voluntary disruption
  selector:
    matchLabels:
      app: myapp
```

---

## Horizontal Pod Autoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300  # wait 5 min before scaling down
    scaleUp:
      stabilizationWindowSeconds: 0    # scale up immediately
```

Resource requests MUST be set for HPA to function.

---

## Network Policy — Default Deny

```yaml
# Block all traffic by default
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
---
# Explicitly allow DNS (required for all pods)
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-dns
  namespace: production
spec:
  podSelector: {}
  policyTypes: [Egress]
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: kube-system
    ports:
    - protocol: UDP
      port: 53
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | Non-root user | No `USER` instruction or `USER root` | PASS/FAIL |
| 2 | Multi-stage build | Single-stage with build tools in final image | PASS/WARN |
| 3 | No `:latest` tag | `image: myapp:latest` in any manifest | PASS/FAIL |
| 4 | Resource limits set | Container missing `resources.limits` | PASS/FAIL |
| 5 | Resource requests set | Container missing `resources.requests` (HPA breaks) | PASS/FAIL |
| 6 | Readiness probe | No `readinessProbe` on deployment | PASS/FAIL |
| 7 | Liveness probe | No `livenessProbe` on deployment | PASS/WARN |
| 8 | Read-only root FS | `readOnlyRootFilesystem: false` or not set | PASS/WARN |
| 9 | PDB configured | No PodDisruptionBudget on production deployment | PASS/WARN |
| 10 | Network policy | No NetworkPolicy (default allow all) | PASS/FAIL |
