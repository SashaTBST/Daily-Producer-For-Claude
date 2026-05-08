---
name: docker-kubernetes
description: Docker and Kubernetes production patterns â€” multi-stage builds, security contexts, resource limits, health probes, HPA, Helm, and network policies. Use when writing Dockerfiles, K8s manifests, Helm charts, or reviewing container security.
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

`dockerfile` â€” write or review a Dockerfile (multi-stage, distroless, non-root, layer cache)
`compose` â€” Docker Compose v2 for local dev with health checks and dependency ordering
`k8s` â€” K8s manifests: Deployment, Service, HPA, PDB, NetworkPolicy
`helm` â€” Helm chart structure, values.yaml with schema validation, environment overlays
`review` â€” audit Dockerfiles or manifests for security and reliability issues

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

`dockerfile` â†’ complete Dockerfile with inline annotations for key decisions
`compose` â†’ complete docker-compose.yml with health checks and depends_on conditions
`k8s` â†’ complete YAML with security context, probes, resources, PDB
`helm` â†’ chart directory listing + values.yaml + values.schema.json snippet
`review` â†’ checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes

## Anti-patterns
âœ— Never run containers as root â€” `USER nonroot` or `USER 1000` in final stage
âœ— Never use `:latest` tag â€” pin explicit versions
âœ— Never deploy without resource requests AND limits â€” missing limits = node crash risk
âœ— Never skip health probes on production K8s workloads
âœ— Never skip NetworkPolicy â€” default-deny-all, then explicit allows

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.