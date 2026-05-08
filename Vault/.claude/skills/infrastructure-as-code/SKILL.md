---
name: infrastructure-as-code
description: Terraform and OpenTofu patterns for production infrastructure — remote state, module structure, secrets management, security scanning (Checkov, Trivy), and CI/CD integration. Use when writing, reviewing, or testing IaC configurations.
argument-hint: "[terraform|module|review|test] [paste HCL or describe what to build]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# Infrastructure as Code Skill

## Modes

`terraform` — write Terraform/OpenTofu configs, backends, variables, providers
`module` — design or review a reusable module with clean interface and README
`review` — audit HCL for security, state, and credential issues
`test` — write Terratest (Go) or set up Checkov/Trivy scanning pipeline

## Tool Landscape 2026

- **Terraform** (32.8% share) — dominant, BSL license, HCP Terraform integration
- **OpenTofu** (12%, fork) — MPL 2.0 open-source, Linux Foundation, S3 native locking
- **Pulumi** — real language (Python/Go/TS), native testing, 150+ providers

Default: Terraform. OpenTofu for open-source-only requirements.

## Non-Negotiable Rules

- Never hardcode credentials in `.tf` files or `.tfvars`. Use environment variables or Vault.
- Never commit `.tfstate` or `.tfstate.backup`. Remote backend always.
- State locking always. Use `use_lockfile = true` (TF 1.10+) — replaces deprecated DynamoDB.
- Mark sensitive outputs: `sensitive = true`. Never expose passwords or tokens in outputs.
- `terraform plan -destroy` before any `destroy`. Never `-auto-approve` on destroy.
- One state file per environment (dev/staging/prod). Separate state for shared infrastructure.
- Security scan: Checkov + Trivy (replaces tfsec, deprecated 2023) in every PR.

## Module Structure

```
modules/[name]/
├── main.tf         # Resources
├── variables.tf    # Input with descriptions and validation
├── outputs.tf      # Outputs (mark sensitive where needed)
├── versions.tf     # required_providers with version constraints
└── README.md       # Usage example, inputs/outputs table
```

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [hardcoded creds] | [no remote state] | [no state lock] | [sensitive outputs] | CLEAR
```

## Output Format

`terraform` → complete HCL with backend config, provider constraints, variable validation
`module` → full module directory with all required files
`review` → checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes
`test` → Terratest Go file or GitHub Actions workflow with Checkov + Trivy steps
