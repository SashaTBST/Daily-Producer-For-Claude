---
name: docker-kubernetes
description: Docker and Kubernetes production patterns — multi-stage builds, security contexts, resource limits, health probes, HPA, Helm, and network policies. Use when writing Dockerfiles, K8s manifests, Helm charts, or reviewing container security.
argument-hint: "[dockerfile|compose|k8s|helm|review] [paste Dockerfile, manifest, or describe what to build]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# Docker / Kubernetes Skill

## Modes

`dockerfile` — write or review a Dockerfile (multi-stage, distroless, non-root, layer cache)
`compose` — Docker Compose v2 for local dev with health checks and dependency ordering
`k8s` — K8s manifests: Deployment, Service, HPA, PDB, NetworkPolicy
`helm` — Helm chart structure, values.yaml with schema validation, environment overlays
`review` — audit Dockerfiles or manifests for security and reliability issues

## Non-Negotiable Rules

**Docker:**
- Multi-stage builds always. Final image from `gcr.io/distroless/base-debian12:nonroot` or `alpine`.
- Never run as root. `USER nonroot` or `USER 1000` in final stage.
- Never use `:latest` tag. Pin explicit versions everywhere.
- Layer order: dependency files first, source last (cache deps, not code).

**Kubernetes:**
- Resource requests AND limits on every container. No limits = node crash risk.
- Three probes: startup (slow apps), liveness (restart unhealthy), readiness (route traffic).
- `readOnlyRootFilesystem: true` + emptyDir volumes for writable paths.
- `runAsNonRoot: true` + `allowPrivilegeEscalation: false` + `capabilities: drop: [ALL]`
- PodDisruptionBudget on every production Deployment.
- NetworkPolicy: default-deny-all first, then explicit allows.
- HPA on CPU + memory; set stabilizationWindowSeconds to avoid flapping.

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [root user risk] | [resource limits] | [latest tag] | [no health probes] | CLEAR
```

## Output Format

`dockerfile` → complete Dockerfile with inline annotations for key decisions
`compose` → complete docker-compose.yml with health checks and depends_on conditions
`k8s` → complete YAML with security context, probes, resources, PDB
`helm` → chart directory listing + values.yaml + values.schema.json snippet
`review` → checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes

## Anti-patterns
✗ Never run containers as root — `USER nonroot` or `USER 1000` in final stage
✗ Never use `:latest` tag — pin explicit versions
✗ Never deploy without resource requests AND limits — missing limits = node crash risk
✗ Never skip health probes on production K8s workloads
✗ Never skip NetworkPolicy — default-deny-all, then explicit allows

Every response ends with NEXT MOVE.