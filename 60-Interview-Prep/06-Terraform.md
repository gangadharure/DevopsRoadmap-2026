# 06 — Terraform / IaC Interview Questions

---

### Q1. What is the Terraform state and why does it matter?
A JSON file mapping your config resources → real cloud resource IDs + metadata. Terraform uses it to detect drift, plan changes, manage dependencies. Without state, TF doesn't know what it already created. **Never commit state to git** — store remotely (S3 + DynamoDB lock, Azure Storage + lease, GCS, Terraform Cloud).

### Q2. Why use a remote backend with locking?
Two engineers running `apply` at once = race condition + corrupt state. Remote backend with **lock** (DynamoDB / blob lease / Postgres) serialises operations. Also enables team sharing + encryption at rest.

### Q3. Difference: `terraform plan`, `apply`, `refresh`, `import`?
- `plan` — read state, read real infra, compute diff. No changes.
- `apply` — execute that diff.
- `refresh` — sync state with real infra without changing anything (now part of `plan`).
- `import` — bring an existing resource into state. Modern: `import {}` blocks in HCL (TF 1.5+).

### Q4. Modules — why and when?
A reusable bundle of resources with inputs (variables) and outputs. Use when you'd otherwise copy-paste resources across envs/teams (e.g. a "vpc" module, "eks-cluster" module). Source from local path, git, or a private/public registry. **Pin versions.**

### Q5. Workspaces vs separate state files per environment?
- **Workspaces** — multiple states under one config. OK for trivial envs, but easy to deploy to wrong env, and one config = one set of providers/versions.
- **Separate root modules per env** (dev/, staging/, prod/ folders pointing to shared modules) — explicit, safer, what most teams use in production.

### Q6. `count` vs `for_each`?
- `count = N` — list-indexed; removing an item in the middle re-creates everything after. Use when the list is truly stable or count is a number.
- `for_each = toset([...])` / `for_each = { k = v }` — map/set keyed; safe add/remove. **Prefer `for_each`.**

### Q7. Data sources vs resources?
- **Resource** — Terraform manages lifecycle (create/update/destroy).
- **Data source** — read-only lookup of something that exists (latest AMI, current AWS account ID, existing VPC).

### Q8. How do you handle secrets in Terraform?
1. Don't hard-code. Use TF variables marked `sensitive = true` (hides in logs).
2. Pull from a secret manager at apply-time (Vault provider, AWS Secrets Manager data source, SOPS).
3. Don't store secrets in state if avoidable — but assume **state contains secrets** and encrypt it (S3 SSE-KMS + bucket policy).
4. CI runs as a federated OIDC identity, not long-lived keys.

### Q9. What's drift and how do you detect it?
Drift = real infra ≠ Terraform state (someone clicked in the console). Detect: scheduled `terraform plan` job, alert on non-empty diff. Tools: `driftctl`, native TF Cloud drift detection. Fix: either re-apply (revert manual change) or import the change into config.

### Q10. Terraform vs OpenTofu vs Pulumi vs CDK?
- **Terraform** — HashiCorp, HCL, BSL licence (since 2023)
- **OpenTofu** — Linux Foundation MPL fork of Terraform, drop-in
- **Pulumi** — write IaC in real languages (TS/Python/Go); state model similar
- **CDK / CDKTF** — also real languages; CDK is AWS-native, CDKTF compiles to TF JSON

Pick Terraform/OpenTofu for ecosystem maturity. Pulumi/CDK if your team strongly prefers real languages and reusable abstractions.

### Q11. How do you upgrade a Terraform module across 30 services?
1. Version the module (git tag `v2.0.0`)
2. Bump in one service, plan, apply, test
3. Roll out to remaining services in batches via PR automation
4. Use **Atlantis** or **Terraform Cloud** to comment plans on PRs
5. Always pin: `source = "git::...?ref=v2.0.0"`

### Q12. `terraform_remote_state` data source — what is it?
Reads outputs of another root module's state. E.g. networking stack outputs `vpc_id` → app stack reads it. Alternatives that are often cleaner: write outputs to SSM Parameter Store / Consul and read those (decouples backend choice).

### Q13. Provisioners — when to use? (trick question)
**Almost never.** They're an escape hatch (e.g. `local-exec` to run a script). Imperative + non-idempotent + run only on create. Prefer cloud-init / user_data, configuration management (Ansible), or a CI step after `apply`.

### Q14. What does `terraform fmt`, `validate`, `tflint`, `checkov`, `tfsec` do?
- `fmt` — canonical formatting
- `validate` — HCL syntax + provider schema check (no API call)
- `tflint` — linter for provider-specific best practices
- `checkov` / `tfsec` / `kics` — static security scans (open S3 bucket, no MFA, etc)
- `terraform-docs` — auto-generate module README
- All run in pre-commit and CI.

### Q15. Difference: `null_resource` vs `terraform_data`?
`null_resource` (legacy, in `hashicorp/null`) — placeholder for triggering provisioners.
`terraform_data` (TF 1.4+, built-in) — modern replacement; can also store arbitrary values to force replacement of other resources via `replace_triggered_by`.

### Q16. How do you do blue/green with Terraform?
Module that creates a versioned name (`app-${var.color}`), deploy `app-blue`, validate, change DNS/ALB target group weight via TF or via the CD pipeline, then destroy `app-blue` on next cycle. For K8s/ECS prefer the orchestrator's native blue/green (Argo Rollouts, CodeDeploy) — TF is too coarse-grained for fast traffic shifts.

### Q17. Ansible vs Terraform — when each?
- **Terraform** — provisioning (create infra)
- **Ansible** — configuration (configure the OS, install packages, deploy app)

They compose: TF creates VMs, hands IPs to an Ansible inventory, Ansible configures. For containers you usually don't need Ansible.

### Q18. Atlantis — what is it?
PR automation for Terraform. Bot comments `terraform plan` on a PR, applies on `atlantis apply` comment after review/approval. Centralises state, enforces workflows, audit trail. Alternatives: Terraform Cloud / Spacelift / env0 / Scalr.

### Q19. How do you write a Terraform module test?
- **Terratest** (Go) — spins up real infra, asserts, destroys
- **terraform test** (built-in since TF 1.6) — `.tftest.hcl` files, supports mocks
- For policy: **OPA + Conftest** against the plan JSON

### Q20. "Last engineer destroyed prod state. What do you do?"
1. Restore S3 versioned state file (you DO have versioning on the state bucket, right?).
2. If gone: `terraform import` every critical resource by hand. Painful — write a script that lists resources via cloud API and emits `import` blocks.
3. Going forward: enable bucket versioning + MFA delete; lock down `s3:DeleteObject` permissions; backups; CI-only apply; never `destroy` from a laptop.

---

### Follow-up topics
- Sentinel / OPA policy-as-code
- Custom providers
- Dynamic blocks
- Refactoring with `moved {}` blocks (TF 1.1+)
- `removed {}` blocks (TF 1.7+)
- Stacks (TF 1.10 preview)
