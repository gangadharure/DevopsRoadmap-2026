# 01 — GitHub Actions (CI/CD)

> The most-used CI/CD platform in 2026. Free for public repos, generous private tier, and the right default for most teams.

**Time:** 1–2 weeks · 1 hour/day
**Prereqs:** Git ([`00-Foundations/02-Git-GitHub-GitLab`](../../00-Foundations/02-Git-GitHub-GitLab))

---

## 🎯 What you need to learn

| # | Concept |
|---|---------|
| 1 | Workflow file anatomy (triggers, jobs, steps) |
| 2 | Runners (GitHub-hosted vs self-hosted) |
| 3 | Marketplace actions vs custom (composite / Docker / JavaScript) |
| 4 | Secrets, variables, environments + protection rules |
| 5 | OIDC to cloud (no long-lived secrets) |
| 6 | Matrix builds & reusable workflows |
| 7 | Caching, artifacts, concurrency |
| 8 | Build → test → scan → push → deploy patterns |

---

## 📅 Roadmap

### Week 1 — Fundamentals

**Day 1 — Anatomy**
```yaml
name: CI
on:
  push:        { branches: [main] }
  pull_request:
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install -r requirements.txt
      - run: pytest -q
```
- Triggers: `push`, `pull_request`, `workflow_dispatch`, `schedule`, `release`
- File location: `.github/workflows/*.yml`

**Day 2 — Marketplace actions**
- `actions/checkout@v4`, `actions/setup-*@v5`, `actions/cache@v4`, `actions/upload-artifact@v4`
- Pin **to SHA**, not tag, for security: `actions/checkout@8e5e7e5a...`

**Day 3 — Secrets, vars, environments**
- Repo secrets: Settings → Secrets and variables → Actions
- `${{ secrets.MY_TOKEN }}`, `${{ vars.REGION }}`
- **Environments** with required reviewers (prod approval gate)

**Day 4 — Matrix builds**
```yaml
strategy:
  matrix:
    python: ["3.11", "3.12"]
    os: [ubuntu-latest, macos-latest]
```

**Day 5 — Reusable workflows & composite actions**
- Reusable workflow: `uses: org/repo/.github/workflows/x.yml@main`
- Composite action: package multiple steps as a custom action

**Day 6 — Caching & artifacts**
- `actions/cache@v4` — for dep caches (pip, npm, gradle, Go mod)
- `actions/upload-artifact` / `download-artifact` — pass files between jobs
- Concurrency control: cancel old runs on the same PR

**Day 7 — Self-hosted runners**
- When? Cost, custom hardware, private network access, GPUs
- ARC (Actions Runner Controller) on Kubernetes

---

### Week 2 — Production patterns

**Day 8 — OIDC to cloud (no static secrets) 🔐**
- AWS: configure IAM OIDC provider → trust GitHub
  ```yaml
  permissions: { id-token: write, contents: read }
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123:role/gh-deployer
      aws-region: us-east-1
  ```
- Azure: federated credentials on a service principal → `azure/login@v2`
- GCP: Workload Identity Federation → `google-github-actions/auth@v2`
- **No more long-lived AWS keys in secrets. Ever.**

**Day 9 — Build → test → scan → push**
```yaml
- uses: docker/setup-buildx-action@v3
- uses: docker/login-action@v3
  with: { registry: ghcr.io, username: ${{ github.actor }}, password: ${{ secrets.GITHUB_TOKEN }} }
- uses: aquasecurity/trivy-action@master
  with: { image-ref: ghcr.io/me/app:${{ github.sha }} }
- uses: docker/build-push-action@v6
  with: { push: true, tags: ghcr.io/me/app:${{ github.sha }}, cache-from: type=gha, cache-to: type=gha,mode=max }
```

**Day 10 — Sign images + SBOM**
- `sigstore/cosign-installer@v3` → `cosign sign --yes ghcr.io/me/app@digest`
- `anchore/sbom-action@v0` → attach SPDX SBOM

**Day 11 — Deploy patterns**
- Push to **EKS/AKS** via `kubectl` + cloud OIDC
- Push to **App Service / ECS**
- Trigger **ArgoCD** sync (GitOps — preferred)
- Environment approval gate before prod

**Day 12 — Quality gates**
- `super-linter`, `ruff`, `eslint`, `gofmt`
- SAST: `github/codeql-action`, Semgrep
- Secrets: `gitleaks-action`
- Dependency review: GitHub's built-in `dependency-review-action`

**Day 13 — Cost & efficiency**
- Concurrency cancellation on stale runs
- Conditional jobs (`if: github.event_name == 'push'`)
- Path filters on triggers
- Self-hosted for heavy / GPU workloads
- Job timeouts (`timeout-minutes: 15`)

**Day 14 — Observability**
- Job summaries (`$GITHUB_STEP_SUMMARY`)
- Status badges
- Required checks for branch protection
- Notifications: Slack via `slackapi/slack-github-action@v1`

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[docs.github.com/actions](https://docs.github.com/en/actions)** | Official, complete |
| **[Workflow syntax reference](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)** | The YAML schema |
| **[GitHub Actions security hardening](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)** | Pin SHAs, OIDC, least privilege |
| **[awesome-actions](https://github.com/sdras/awesome-actions)** | Curated list |
| **[Actions Runner Controller (ARC)](https://github.com/actions/actions-runner-controller)** | Self-hosted runners on K8s |

---

## 🧪 Hands-On Projects

1. CI for a Python repo: lint, test, coverage, on every PR.
2. Build → Trivy scan → Cosign sign → push to GHCR on every `main` push.
3. OIDC-federated deploy to AWS EKS (no static secrets).
4. Reusable workflow shared across 3 repos via `workflow_call`.
5. Self-hosted runner on Kubernetes via ARC.

---

## 🧠 Interview Questions

- "Why OIDC instead of long-lived cloud keys in secrets?"
- "Reusable workflow vs composite action — when each?"
- "How do you cache dependencies safely?"
- "How do you stop a workflow from running on doc-only PRs?"
- "How do you implement a manual approval gate before prod?"
- "How do you pin a third-party action securely?"

---

## ▶️ Next stop

[`20-CI-CD-IaC/02-Jenkins-ArgoCD`](../02-Jenkins-ArgoCD) → Jenkins (legacy heavy) + ArgoCD (GitOps).
