# 05 — DevOps Engineer (6-Month Roadmap)

> Become a hire-ready DevOps Engineer in 6 months — Linux foundations, one cloud, containers, CI/CD, IaC, observability.
> Vendor-neutral with optional cloud-specific paths.

**Time:** 6 months · 1–2 hours/day + weekend projects
**Prereqs:** Linux ([`Linux`](../../00-Foundations/01-Linux)) + one cloud cert ([`AWS Path`](../../40-Cloud-Certification-Paths/01-AWS-Path) or [`Azure Path`](../../40-Cloud-Certification-Paths/02-Azure-Path))

---

## 🧭 What does a DevOps Engineer actually do?

> Bridges **dev** and **ops** to ship software faster and more reliably — through automation, IaC, CI/CD, containers, observability, and cloud platforms.

**Salary signal (2026, India/US):** ₹12–35 LPA / $110k–180k base for mid-level DevOps.

---

## 🛠️ The DevOps Toolchain (the truth)

| Layer | The dominant tools in 2026 |
|-------|----------------------------|
| OS | **Linux** (Ubuntu, RHEL, Amazon Linux) |
| VCS | **Git** + **GitHub** / GitLab |
| Cloud | **AWS** (most jobs) · Azure · GCP |
| Containers | **Docker**, **Buildah**, OCI images |
| Orchestration | **Kubernetes** (EKS / AKS / GKE) |
| CI/CD | **GitHub Actions**, **GitLab CI**, **Jenkins**, **ArgoCD** (GitOps) |
| IaC | **Terraform** / **OpenTofu**, **Pulumi**, **Bicep** (Azure), **CloudFormation** (AWS) |
| Config mgmt | **Ansible** |
| Secrets | **HashiCorp Vault**, AWS Secrets Manager, Azure Key Vault |
| Observability | **Prometheus + Grafana**, Loki, Tempo, OpenTelemetry, Datadog |
| Service mesh | Istio, Linkerd (advanced) |
| Languages | **Bash**, **Python**, **Go** (one is enough to start) |

---

## 📅 6-Month Roadmap

### Month 1 — Linux + Git + One Language

- Complete the [Linux roadmap](../../00-Foundations/01-Linux/ROADMAP.md) (skip if done)
- **Git deep dive:** branches, rebase vs merge, cherry-pick, stash, reset vs revert, tags, hooks, `.gitignore`, `.gitattributes`
- Trunk-based vs GitFlow workflows; PR etiquette
- Pick **Python** (easiest) or **Go** (fastest): variables, control flow, file I/O, calling shell, HTTP requests
- **Project:** CLI tool in Python/Go that posts the local system's CPU/disk/mem to a JSON endpoint.

---

### Month 2 — Cloud Foundations

- Pick **one** cloud (AWS for most jobs) → finish [`AWS Path`](../../40-Cloud-Certification-Paths/01-AWS-Path) or [`Azure Path`](../../40-Cloud-Certification-Paths/02-Azure-Path)
- Focus on: networking (VPC/VNet), IAM/RBAC, compute (EC2/VM, ASG/VMSS), storage (S3/Blob), and DNS
- **Project:** Manually build a 3-tier web app (ALB → ASG → RDS or App Service → DB) — click through portal once, then recreate via CLI.

---

### Month 3 — Containers & Kubernetes

- **Docker:** Dockerfile best practices, multi-stage builds, image scanning, registries, networks, volumes, `docker compose`
- **Kubernetes:** complete the [`Kubernetes (CKA)`](../../10-Containers-Orchestration/02-Kubernetes-CKA) Phase 1–4 (core objects, services, storage)
- **Helm** & **Kustomize** basics
- Managed K8s on your chosen cloud (EKS or AKS)
- **Project:** Containerize the app from Month 2, push to ECR/ACR, deploy to EKS/AKS with Helm + an Ingress with TLS.

---

### Month 4 — Infrastructure as Code (IaC)

- **Terraform** (or **OpenTofu** — open-source fork): providers, resources, variables, outputs, modules, **remote state** (S3+DynamoDB / Azure Storage), workspaces, `terraform plan` workflows
- **Ansible** basics: inventory, playbooks, roles, vault, idempotency
- IaC project layouts: env-per-folder vs env-per-workspace
- **Project:** Re-provision the entire Month-3 app (VPC + EKS + RDS + app deploy) with Terraform, idempotent, with remote state. Use Ansible to configure any non-K8s VMs.

---

### Month 5 — CI/CD + GitOps + Secrets

- **GitHub Actions** (or GitLab CI): jobs, matrix, reusable workflows, OIDC to cloud (no long-lived secrets), self-hosted runners, environments & approvals
- **Pipeline patterns:** build → test → scan → push → deploy
- **GitOps with ArgoCD:** declarative, sync waves, image updater
- **Secrets:** HashiCorp Vault basics, External Secrets Operator on K8s, OIDC federation from CI
- **Project:** GitOps pipeline — push to `main` → GitHub Actions builds & pushes image → ArgoCD auto-syncs the Helm chart to EKS/AKS. Zero `kubectl apply` from a laptop.

---

### Month 6 — Observability, SRE & Reliability

- **Prometheus** + **Grafana** + Alertmanager (the open-source classic)
- Logs: **Loki** or ELK / OpenSearch
- Traces: OpenTelemetry + Tempo/Jaeger
- **SRE basics:** SLI, SLO, error budgets, on-call, postmortems
- Capacity planning, load testing (k6 / Locust), chaos engineering (LitmusChaos)
- **Project:** Add full observability to the Month-5 stack — RED metrics on the app, SLO of 99.9%, error-budget alert in Slack, public Grafana dashboard.

---

## 📚 Official Resources

| Topic | Source |
|-------|--------|
| Git | [git-scm.com/doc](https://git-scm.com/doc) |
| Docker | [docs.docker.com](https://docs.docker.com/) |
| Kubernetes | [kubernetes.io/docs](https://kubernetes.io/docs/) |
| Terraform | [developer.hashicorp.com/terraform](https://developer.hashicorp.com/terraform/docs) |
| OpenTofu | [opentofu.org/docs](https://opentofu.org/docs/) |
| Ansible | [docs.ansible.com](https://docs.ansible.com/) |
| GitHub Actions | [docs.github.com/actions](https://docs.github.com/en/actions) |
| Prometheus | [prometheus.io/docs](https://prometheus.io/docs/) |
| OpenTelemetry | [opentelemetry.io/docs](https://opentelemetry.io/docs/) |
| SRE | [sre.google/books](https://sre.google/books/) (free books from Google) |
| AWS | [docs.aws.amazon.com](https://docs.aws.amazon.com/) |
| Azure | [learn.microsoft.com/azure](https://learn.microsoft.com/en-us/azure/) |

---

## 🎓 Certifications (in priority order)

1. **Linux foundations** — RHCSA optional (skill > cert)
2. **One cloud cert** — AWS SAA *or* AZ-104
3. **CKA** — Certified Kubernetes Administrator
4. **HashiCorp Terraform Associate** (Terraform-Associate-003)
5. **AWS DevOps Engineer Professional** *or* **AZ-400** (after 1 yr exp)

---

## 🧪 Capstone Portfolio (3 GitHub repos)

1. **`platform-iac`** — Terraform monorepo provisioning a complete multi-env (dev/stage/prod) EKS or AKS platform with networking, IAM, observability stack.
2. **`gitops-apps`** — ArgoCD-managed Helm charts for 3 demo apps, image auto-updates from GHCR.
3. **`devops-runbooks`** — markdown runbooks for "node down", "DB failover", "secret rotation", "cert expiry" + the bash/Python scripts they reference.

> **A single great GitHub portfolio beats 5 certifications.**

---

## 🧠 Interview Topics

- "Walk me through your CI/CD pipeline end-to-end."
- "Blue/green vs canary vs rolling — when each?"
- "How do you manage secrets at scale?"
- "How do you make Terraform state safe in a team?"
- "Explain GitOps in one minute. Why use ArgoCD over plain `kubectl apply`?"
- "What's an SLO? Give an example with an error budget."
- "Container image is huge — how do you slim it?"
- "Describe a production incident you led."

---

## ▶️ Next stop

- Add security depth → [`DevSecOps Engineer`](../../50-Specializations/02-DevSecOps-Engineer)
- Move into AI infrastructure → [`Agentic AI Engineer`](../../50-Specializations/03-Agentic-AI-Engineer)
