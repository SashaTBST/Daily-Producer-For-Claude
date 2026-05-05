# Infrastructure as Code Skill — Reference
Terraform 1.10+ / OpenTofu / Checkov + Trivy / Terratest | Last verified: 2026-04-30

## Research Sources
- OpenTofu vs Terraform 2026: https://dev.to/mechcloud_academy/opentofu-vs-terraform-in-2026-is-the-fork-finally-worth-it-3nd1
- Terraform module best practices: https://oneuptime.com/blog/post/2026-02-23-how-to-use-terraform-module-best-practices-for-large-organizations/view
- S3 native state locking: https://medium.com/aws-specialists/dynamodb-not-needed-for-terraform-state-locking-in-s3-anymore-29a8054fc0e9
- Secrets management: https://oneuptime.com/blog/post/2026-02-23-how-to-avoid-hardcoding-credentials-in-terraform/view
- IaC scanning tools 2026: https://spacelift.io/blog/terraform-scanning-tools

---

## Remote State — S3 Native Locking (2026 Standard)

```hcl
# backend.tf — TF 1.10+ with S3-native locking (DynamoDB deprecated)
terraform {
  required_version = ">= 1.10.0"

  backend "s3" {
    bucket       = "my-org-terraform-state"
    key          = "prod/terraform.tfstate"
    region       = "us-east-1"
    encrypt      = true
    use_lockfile = true   # replaces DynamoDB locking
  }
}
```

Initialize with separate file to keep credentials out of code:
```bash
terraform init -backend-config="envs/prod.backend.tfbackend"
```

---

## Directory Layout

```
terraform/
├── live/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   └── prod/
│       └── ...
└── modules/
    └── vpc/
        ├── main.tf
        ├── variables.tf    # descriptions + validation blocks
        ├── outputs.tf      # sensitive = true where needed
        ├── versions.tf
        └── README.md
```

---

## Secrets — Never Hardcode

```hcl
# ✗ NEVER — leaked forever in git history
provider "aws" {
  access_key = "AKIAIOSFODNN7EXAMPLE"
  secret_key = "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
}

# ✓ Environment variables (interim)
provider "aws" {}  # reads AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY

# ✓ Vault (production)
data "vault_generic_secret" "aws" {
  path = "secret/aws/credentials"
}
provider "aws" {
  access_key = data.vault_generic_secret.aws.data["access_key"]
  secret_key = data.vault_generic_secret.aws.data["secret_key"]
}
```

---

## Variable Validation

```hcl
variable "environment" {
  type        = string
  description = "Deployment environment"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Must be dev, staging, or prod."
  }
}

variable "cidr_block" {
  type        = string
  description = "VPC CIDR block"
  validation {
    condition     = can(cidrhost(var.cidr_block, 0))
    error_message = "Must be a valid CIDR block."
  }
}
```

---

## Sensitive Outputs

```hcl
# ✗ Exposed in console and state diff
output "db_password" {
  value = aws_db_instance.main.password
}

# ✓ Hidden from console, still in state (encrypt state!)
output "db_password" {
  value     = aws_db_instance.main.password
  sensitive = true
}
```

---

## Destroy Safely

```bash
# ✗ NEVER
terraform destroy -auto-approve

# ✓ Always review first
terraform plan -destroy -out=destroy.plan
# review destroy.plan
terraform apply destroy.plan
```

---

## CI/CD — Checkov + Trivy Scanning

```yaml
# .github/workflows/terraform-ci.yml
- name: Checkov scan (1000+ policies)
  uses: bridgecrewio/checkov-action@master
  with:
    directory: terraform
    framework: terraform
    soft_fail: false

- name: Trivy IaC scan (replaces deprecated tfsec)
  uses: aquasecurity/trivy-action@master
  with:
    scan-type: config
    scan-ref: terraform
```

Note: tfsec deprecated 2023–2024. Migrate to Trivy.

---

## Terratest (Go Integration Test)

```go
package test

import (
    "testing"
    "github.com/gruntwork-io/terratest/modules/terraform"
    "github.com/stretchr/testify/assert"
)

func TestVPCModule(t *testing.T) {
    opts := &terraform.Options{TerraformDir: "../modules/vpc"}
    defer terraform.Destroy(t, opts)
    terraform.InitAndApply(t, opts)
    vpcID := terraform.Output(t, opts, "vpc_id")
    assert.NotEmpty(t, vpcID)
}
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No hardcoded credentials | `access_key`, `secret_key`, passwords in .tf files | PASS/FAIL |
| 2 | Remote backend configured | Missing backend block (local state only) | PASS/FAIL |
| 3 | State locking enabled | Backend without `use_lockfile` or DynamoDB | PASS/FAIL |
| 4 | State encrypted | Backend without `encrypt = true` | PASS/FAIL |
| 5 | Sensitive outputs marked | Passwords/tokens in output without `sensitive = true` | PASS/FAIL |
| 6 | Variable validation | Variables without type or validation blocks | PASS/WARN |
| 7 | No destroy auto-approve | `-auto-approve` on destroy command | PASS/FAIL |
| 8 | Security scanning in CI | No Checkov or Trivy step in pipeline | PASS/WARN |
| 9 | Module README | Module directory missing README.md | PASS/WARN |
| 10 | Version constraints | No `required_providers` version pinning | PASS/WARN |
