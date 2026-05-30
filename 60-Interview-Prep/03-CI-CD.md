# 03 — CI/CD Interview Questions

---

### Q1. CI vs CD vs Continuous Deployment?
- **Continuous Integration** — every commit triggers automated build + tests on a shared branch.
- **Continuous Delivery** — every passing build produces a deployable artifact, deploy is **manual** approval.
- **Continuous Deployment** — every passing build auto-deploys to production. No human gate.

### Q2. Walk me through a typical pipeline.
Source (git push) → Build (compile, build container) → Unit test → Static analysis (SonarQube, lint) → SCA (dependency scan: Snyk/Trivy) → SAST → Container scan → Push to registry (signed with cosign) → Deploy to dev (ArgoCD/Helm) → Integration / contract tests → Deploy to staging → DAST → Approval → Deploy to prod (blue/green or canary) → Smoke tests → Monitor.

### Q3. Build once, deploy many — why?
Same immutable artifact (container, jar) flows from dev → staging → prod. Environment differences come from **config** (env vars, ConfigMaps), not separate builds. Guarantees the thing you tested is the thing that ships.

### Q4. Trunk-based vs Gitflow?
- **Gitflow**: long-lived `develop` + `feature/*` + `release/*` + `hotfix/*`. Heavy. Suits scheduled releases.
- **Trunk-based**: everyone merges short-lived branches (<1 day) into `main` daily, feature flags hide unfinished work. **Default for modern DevOps** — enables true CD.

### Q5. Blue/Green vs Canary vs Rolling?
| Strategy | How | Risk | Cost |
|----------|-----|------|------|
| **Rolling** | Replace pods N at a time | Medium (every user sees mix) | Low |
| **Blue/Green** | Stand up full new version, flip LB | Very low, instant rollback | 2× infra briefly |
| **Canary** | Send 1% → 5% → 25% → 100% by metric watch | Low, gradual | Small overhead |
| **Shadow** | Mirror traffic to new version, ignore response | Zero user impact | 2× compute |

### Q6. GitHub Actions vs Jenkins?
| | GitHub Actions | Jenkins |
|---|----------------|---------|
| Hosting | SaaS (or self-hosted runners) | You run it |
| Config | YAML in repo | Groovy `Jenkinsfile` (or UI) |
| Plugins | Marketplace actions | 1800+ plugins |
| Cost | Free for OSS, per-min for private | Free, but you pay infra + ops |
| Best for | Modern Git-centric teams | Complex enterprise + legacy |

### Q7. What's a self-hosted runner and why use one?
A GH Actions / GitLab runner you operate. Reasons: access to private network (your VPC), GPU/large machines, faster cache, compliance (data stays on prem), custom OS images. Risks: **never run on public repos** without isolation — code-execution as anyone who can PR.

### Q8. How do you store secrets in a pipeline?
- Native: GitHub Encrypted Secrets / GitLab CI Variables (masked) / Jenkins Credentials.
- Better: pull from **Vault** / AWS Secrets Manager / Azure Key Vault at runtime.
- Best: **OIDC federation** — pipeline gets a short-lived cloud token without any long-lived key. (`aws-actions/configure-aws-credentials`, `azure/login`.)

### Q9. What's GitOps?
Git is the **single source of truth** for system state. A controller in the cluster (ArgoCD / Flux) continuously reconciles cluster state to match Git. Rollback = `git revert`. Audit = git log. Push-vs-pull = pull (cluster pulls from git, no inbound creds needed).

### Q10. ArgoCD core concepts?
- **Application** — a tracked git path → cluster target
- **Project** — group of apps with shared permissions/repos
- **Sync** — apply manifests; **auto-sync** + **self-heal** + **prune**
- **App of Apps** / **ApplicationSet** — declaratively manage many apps

### Q11. How do you handle DB migrations in CI/CD?
- Tool: Flyway / Liquibase / Alembic / golang-migrate
- Run as a **Job** (K8s) or pipeline step BEFORE the app deploy
- Rule: migrations must be **backwards compatible** (so old app keeps working during rollout) — split breaking changes into two releases (expand, then contract).

### Q12. Pipeline takes 45 min. How do you speed it up?
- Parallelise jobs (matrix builds, fan-out tests)
- Cache deps (`actions/cache`, Docker layer cache, BuildKit)
- Test sharding / split slowest 10%
- Skip unaffected paths (path filters / nx / bazel)
- Bigger runners
- Build once, reuse artifact across jobs
- Profile first — find the actual bottleneck

### Q13. How do you test infra changes?
- Unit: `terraform validate`, `tflint`, `tfsec`/`checkov`
- Plan + manual review in PR (`terraform plan` posted as comment via Atlantis)
- Integration: `terratest` / `kitchen-terraform` — spin up real cloud, assert
- Policy as code: OPA/Conftest, Sentinel

### Q14. What's an artifact registry and why do I need one?
Stores built artifacts (containers, jars, npm) for promotion across envs. Examples: ECR, ACR, GCR, Harbor, **Nexus**, JFrog Artifactory. Reasons: speed (local mirror), security scanning, retention policies, signing, immutability, audit.

### Q15. Helm vs Kustomize?
- **Helm** — templating + package manager (charts), versioned releases. Powerful, complex.
- **Kustomize** — pure overlays on plain YAML, no templating. Simpler, K8s-native (`kubectl -k`).
- Many teams: Helm chart + Kustomize overlay per env.

### Q16. How do you do a hotfix in prod?
1. Branch from the prod tag, not `main`.
2. Smallest possible fix + test.
3. Cherry-pick the same fix into `main`.
4. Tag + run hotfix pipeline (skip dev/staging if defined, with explicit approval).
5. Post-mortem the root cause.

### Q17. Pipeline failed at "push to registry" — debug?
- Auth (token expired? OIDC role?)
- Registry quota / disk
- Image too large
- Network egress (corp proxy?)
- Wrong image name/tag
- Registry retention policy deleted base layer

### Q18. What's a feature flag and why use it in CI/CD?
A runtime toggle to enable/disable code paths without redeploying. Tools: LaunchDarkly, Unleash, Flagsmith, or homegrown ConfigMap. Lets you **deploy ≠ release**, do gradual rollout, A/B tests, kill switches.

### Q19. SBOM — what and why?
**Software Bill of Materials** — list of every component (and version) in your artifact. Generate with `syft` / `cyclonedx`, attach to image as an attestation. Mandated by US Exec Order 14028; lets you instantly answer "are we affected by CVE-X?".

### Q20. Difference between SAST, DAST, SCA, IAST?
- **SAST** — static analysis of source code (Sonar, Semgrep)
- **DAST** — runtime black-box testing of a running app (OWASP ZAP, Burp)
- **SCA** — scans dependencies for known CVEs (Snyk, Trivy, Dependabot)
- **IAST** — instrument running app during tests for vuln detection

---

### Follow-up topics
- Progressive delivery: Argo Rollouts, Flagger, weighted traffic via service mesh
- Pipelines as Code: Tekton, Dagger
- Reusable workflows / shared libraries (GH composite actions, Jenkins shared libs)
- Mono-repo CI patterns (Bazel, Nx, Turborepo)
- Supply-chain attestations: SLSA, Sigstore, in-toto
