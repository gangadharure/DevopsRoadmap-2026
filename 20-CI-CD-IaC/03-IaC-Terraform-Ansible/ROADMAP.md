# 03 — Infrastructure as Code: Terraform & Ansible

> **Terraform** provisions infrastructure (the *what*). **Ansible** configures it (the *how*).
> Most DevOps roles want both — Terraform for cloud, Ansible for the OS/app layer.

**Time:** 4 weeks · 1 hour/day
**Prereqs:** One cloud cert ([`40-Cloud-Certification-Paths`](../../40-Cloud-Certification-Paths))

---

## Part A — Terraform / OpenTofu (3 weeks)

> **OpenTofu** is the MIT-licensed fork of Terraform 1.5. Syntax is identical. Pick OpenTofu if you want fully open-source; pick Terraform for HashiCorp Cloud features.

### Week 1 — Fundamentals

**Day 1 — Concepts**
- Declarative IaC, plan/apply, state file
- Resource graph, dependency resolution
- `terraform` vs `tofu` (drop-in replacement)
- Install (Windows: `winget install Hashicorp.Terraform`)

**Day 2 — Providers + first resource**
```hcl
terraform {
  required_version = ">= 1.6"
  required_providers {
    aws = { source = "hashicorp/aws", version = "~> 5.50" }
  }
}
provider "aws" { region = "us-east-1" }

resource "aws_s3_bucket" "demo" {
  bucket = "myco-demo-${random_id.s.hex}"
}
```
- `terraform init / plan / apply / destroy`

**Day 3 — Variables, outputs, locals**
- Input vars (type, default, validation, sensitive)
- `terraform.tfvars`, `-var`, env vars `TF_VAR_*`
- `output`, `locals` for derived values

**Day 4 — State management 🚨 (the dangerous one)**
- Default: local `terraform.tfstate` (DON'T use for teams)
- **Remote state:** S3 + DynamoDB lock (AWS) · Azure Blob (Azure) · GCS (GCP) · Terraform Cloud
- `terraform import`, `terraform state mv/rm`, `terraform refresh`
- Never edit state by hand. Never commit state to Git.

**Day 5 — Modules**
- Module = reusable bundle of resources
- Local modules (`./modules/vpc`) vs registry modules
- Versioned modules from Git (`source = "git::https://...?ref=v1.2.0"`)

**Day 6 — Workspaces / Workspaces-vs-folders**
- Workspaces: 1 state per workspace (good for dev/stage/prod isolation)
- Folder-per-env: cleaner, more explicit, recommended for serious teams

**Day 7 — Loops & conditionals**
- `count` vs `for_each` (always prefer `for_each` for maps/sets)
- Dynamic blocks
- `try()`, `coalesce()`, `lookup()`

### Week 2 — Production patterns

**Day 8 — Code structure**
```
infra/
├── modules/             # reusable building blocks
│   ├── vpc/
│   └── eks/
├── envs/
│   ├── dev/
│   ├── stage/
│   └── prod/
└── policies/            # OPA / Sentinel / Checkov configs
```

**Day 9 — Security**
- **No secrets in code** → Vault / SSM / Key Vault, mark vars `sensitive = true`
- **OIDC from CI** (no static AWS keys) — see GitHub Actions roadmap
- `iam` least-privilege for the Terraform role itself

**Day 10 — Policy as Code**
- **Checkov** / **tfsec** — IaC misconfigurations
- **OPA + Conftest** — custom policies
- **Sentinel** — HashiCorp Terraform Cloud (paid)

**Day 11 — Drift detection & remediation**
- `terraform plan` in CI on schedule → alert on drift
- Tools: Terraform Cloud, **`driftctl`**

**Day 12 — Testing**
- **`terraform-test`** native (v1.6+): `terraform test`
- **Terratest** (Go) — integration tests
- **`tflint`** — lint rules

**Day 13 — CI/CD with Atlantis or TFC**
- **Atlantis** — open-source PR-based Terraform automation (self-hosted)
- **Terraform Cloud** — HashiCorp hosted
- **Spacelift** / **env0** — modern alternatives

**Day 14 — Cost & Cleanup**
- **Infracost** in CI — show $$ delta per PR
- `terraform destroy` discipline for ephemeral envs
- Tag everything (`tags = { env = var.env, owner = "platform" }`)

### Week 3 — Pulumi / Bicep / CloudFormation (awareness only)

- **Pulumi:** IaC in Python/TS/Go (real code, not HCL)
- **Bicep:** Azure-only ARM template DSL (great for Azure-pure shops)
- **CloudFormation:** AWS-only — still common for AWS-pure shops
- **CDK** (AWS) / **CDK for Terraform** — IaC in your language

> Stick with Terraform/OpenTofu for **multi-cloud** and DevOps career. Add Bicep if you go deep Azure.

---

## Part B — Ansible (1 week)

> The agentless config-mgmt tool. Use it for OS configuration, app deploys to VMs, ad-hoc fleet operations.

### Why Ansible (still)
- **Agentless** (just SSH)
- **Idempotent** modules for everything
- **Inventory + playbook + roles** mental model is simple
- Massive collection of community modules

### What to learn (7 days)

**Day 1 — Install + first ping**
```bash
pip install ansible
echo "[web]\n10.0.0.5 ansible_user=ubuntu" > hosts
ansible -i hosts web -m ping
```

**Day 2 — Playbook basics**
```yaml
- hosts: web
  become: true
  tasks:
    - name: Install nginx
      apt: { name: nginx, state: present, update_cache: yes }
    - name: Start & enable
      service: { name: nginx, state: started, enabled: yes }
```

**Day 3 — Inventory & variables**
- Static vs dynamic inventory (AWS EC2, Azure plugins)
- `group_vars/`, `host_vars/`
- Vars precedence (memorize the order)

**Day 4 — Roles + Ansible Galaxy**
- `ansible-galaxy init role-name`
- Standard layout: `tasks/`, `handlers/`, `templates/`, `vars/`, `defaults/`, `meta/`

**Day 5 — Templates + handlers + conditionals + loops**
- Jinja2 templates (`templates/nginx.conf.j2`)
- `notify:` + `handlers:` for restarts
- `when:`, `loop:`

**Day 6 — Vault & secrets**
- `ansible-vault encrypt secrets.yml`
- Vault password file + `--vault-password-file`
- Better: integrate with HashiCorp Vault `community.hashi_vault.vault_kv_get_v2`

**Day 7 — Production patterns**
- `ansible-lint` + `molecule` for testing roles
- AWX / Ansible Automation Platform (Red Hat) for UI + RBAC
- Wrap with **`ansible-pull`** for K8s-style agents

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[developer.hashicorp.com/terraform](https://developer.hashicorp.com/terraform/docs)** | Terraform official |
| **[opentofu.org/docs](https://opentofu.org/docs/)** | OpenTofu official |
| **[Terraform Registry](https://registry.terraform.io/)** | Providers + modules |
| **[Checkov docs](https://www.checkov.io/)** | IaC scanning |
| **[Atlantis](https://www.runatlantis.io/)** | PR-driven Terraform |
| **[docs.ansible.com](https://docs.ansible.com/)** | Ansible official |
| **[ansible-lint](https://ansible-lint.readthedocs.io/)** | Linter |
| **[Molecule](https://ansible.readthedocs.io/projects/molecule/)** | Role testing |

---

## 🧪 Hands-On Projects

1. Terraform module that builds a VPC + EKS + node groups; deploy to dev/stage/prod via folder-per-env.
2. Same in Azure: VNet + AKS via Bicep or Terraform.
3. Atlantis on GitHub PRs → `terraform plan` comments + `apply` on merge.
4. Ansible role that hardens an Ubuntu server to CIS Benchmark Level 1.
5. Combo: Terraform creates VMs → triggers Ansible playbook → app deployed.

---

## 🧠 Interview Questions

- "Terraform vs CloudFormation vs Pulumi — when each?"
- "What's stored in tfstate and why is it sensitive?"
- "How do you do multi-env without copy-pasting?"
- "How do you handle drift?"
- "Ansible vs Terraform — overlap?"
- "Idempotency — give 3 examples in Ansible."
- "How do you secure Ansible Vault in CI?"

---

## 🎓 Certifications

1. **HashiCorp Certified: Terraform Associate (003)** — well-respected, vendor-neutral, $70.50 USD
2. **Red Hat Certified Specialist in Ansible Automation** — for Red Hat shops

---

## ▶️ Next stop

[`20-CI-CD-IaC/04-Sonar-Nexus`](../04-Sonar-Nexus) → quality + artifact mgmt.
