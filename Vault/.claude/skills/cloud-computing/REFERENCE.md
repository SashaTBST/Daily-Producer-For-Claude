# Cloud Computing Skill — Reference
AWS / GCP / Azure | Last verified: 2026-04-30

## Research Sources
- Cloud security 2026: https://www.cyberlearn.systems/blog/cloud-security-aws-azure-gcp-best-practices
- AWS IAM best practices 2026: https://dev.to/karaniph/aws-iam-security-best-practices-in-2026-a-complete-guide-o14
- Pricing mix guide: https://www.usage.ai/blog/on-demand-vs-reserved-vs-spot-instances
- Lambda cold start 2026: https://www.agilesoftlabs.com/blog/2026/02/aws-lambda-cold-start-7-proven-fixes
- Cloud DR strategies: https://calmops.com/cloud/cloud-disaster-recovery-2026/

---

## IAM — Least Privilege Pattern (AWS)

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:GetObject"],
    "Resource": "arn:aws:s3:::my-bucket/app-logs/*",
    "Condition": {
      "StringEquals": {"aws:PrincipalOrgID": "o-xxxxx"}
    }
  }]
}
```

- Scope to specific resources, never `"Resource": "*"` for write operations
- Use `iam:PassRole` only to specific ARNs — common privilege escalation path
- Run IAM Access Analyzer to auto-detect unused permissions from CloudTrail

---

## VPC Subnet Layout (Standard)

```
VPC CIDR: 10.0.0.0/16
  Public subnet:   10.0.1.0/24   ← ALB, NAT Gateway
  Private subnet:  10.0.10.0/24  ← Application servers
  DB subnet:       10.0.20.0/24  ← RDS, ElastiCache (no internet route)
```

- Allocate 2–4× more IPs than initial estimate (growth buffer)
- Never overlap CIDR between environments or on-premises (most common design mistake)
- AWS reserves 5 IPs per subnet (.0 .1 .2 .3 .255)

---

## VPC Connectivity Decision

| Need | Use | Why |
|------|-----|-----|
| 2–9 VPCs, direct peering | VPC Peering | No SPOF, free within region |
| 10+ VPCs, transitive routing | Transit Gateway | Hub-and-spoke, cost-effective at scale |
| Private access to AWS services | PrivateLink / VPC Endpoints | No internet egress, no data transfer cost |

---

## Public Bucket Prevention (All Clouds)

```bash
# AWS — enforce at account level (not per-bucket)
aws s3control put-public-access-block \
  --account-id 123456789012 \
  --public-access-block-configuration \
  "BlockPublicAcls=true,IgnorePublicAcls=true,\
   BlockPublicPolicy=true,RestrictPublicBuckets=true"

# GCP — uniform bucket-level access
gcloud storage buckets update gs://my-bucket \
  --uniform-bucket-level-access
# Then deny allUsers in IAM

# Azure — disable public blob access
az storage account update \
  --name mystorageaccount \
  --allow-blob-public-access false
```

---

## Lambda Cold Start Mitigation

| Technique | Savings | Cost |
|-----------|---------|------|
| SnapStart (Java) | ~90% cold start reduction | None (pre-initialised snapshot) |
| ARM64 Graviton2 | 13–24% faster | Same price or cheaper |
| Node.js 20 / Python 3.12 | 15–20% faster than older runtimes | None |
| Provisioned concurrency | Eliminates cold start | Per-concurrency-hour always-on charge |
| Minimal package size | Proportional | None |

---

## RTO/RPO Decision Tree

```
RTO < 1 min    → Active-active multi-region (expensive, complex)
RTO 5–60 min   → Warm standby + auto DNS failover (pre-promoted read replica)
RTO 1–8 hrs    → Pilot light (minimal compute, restore from snapshots)
RTO > 8 hrs    → Backup & restore (cheapest, test quarterly)
```

Multi-AZ protects from datacenter failure. Multi-region protects from full region failure (rare, but AWS UAE went offline March 2026).

---

## Serverless Stateless Pattern

```javascript
// ✗ NEVER — state in function memory (lost on cold start)
let cache = {};
export const handler = async (event) => {
  if (!cache[event.id]) cache[event.id] = await fetchData(event.id);
};

// ✓ External state always
export const handler = async (event) => {
  const cached = await redis.get(`data:${event.id}`);
  if (cached) return JSON.parse(cached);
  const data = await fetchData(event.id);
  await redis.setex(`data:${event.id}`, 3600, JSON.stringify(data));
  return data;
};
```

---

## Top Misconfigurations Causing Breaches (2026)

| # | Misconfiguration | Fix |
|---|-----------------|-----|
| 1 | Public storage bucket | Block at account level, not per bucket |
| 2 | Wildcard IAM (`"Action": "*"`) | Scope to specific actions and resources |
| 3 | No MFA on root/admin accounts | MFA mandatory on all privileged identities |
| 4 | Credentials committed to Git | Pre-commit hook + Gitleaks scan |
| 5 | No centralised audit logs | Enable CloudTrail / Cloud Audit Logs, ship to SIEM |

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No hardcoded credentials | Access keys or service account keys in code | PASS/FAIL |
| 2 | Storage not public | S3/GCS/Blob without account-level block | PASS/FAIL |
| 3 | IAM least privilege | Wildcard action or resource in policy | PASS/FAIL |
| 4 | MFA on privileged identities | Root account or admin users without MFA | PASS/FAIL |
| 5 | Secrets in manager | Secrets not in Secrets Manager / Key Vault | PASS/FAIL |
| 6 | Multi-AZ deployment | Single-AZ production databases or compute | PASS/FAIL |
| 7 | Encryption at rest | Storage or DB without encryption enabled | PASS/FAIL |
| 8 | Audit logs enabled | CloudTrail / VPC Flow Logs not active | PASS/FAIL |
| 9 | Serverless stateless | State stored in function memory | PASS/FAIL |
| 10 | Cost tier strategy | 100% on-demand without Reserved/Spot plan | PASS/WARN |
