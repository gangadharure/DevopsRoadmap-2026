# 02 — Jenkins & ArgoCD (Legacy CI + Modern GitOps CD)

> Many enterprises still run on **Jenkins**. Modern teams ship with **ArgoCD** (GitOps). Know both.

**Time:** 2 weeks · 1 hour/day

---

## Part A — Jenkins

> Still the most-deployed CI platform in enterprises. You'll find it in banks, insurance, telcos, and any team that's been around > 5 years.

### What to learn

| # | Topic |
|---|-------|
| 1 | Install + run Jenkins (Docker or K8s) |
| 2 | **Declarative pipelines** (Jenkinsfile) — `Scripted` is legacy |
| 3 | Multi-branch pipelines |
| 4 | Agents (master + workers / K8s plugin) |
| 5 | Credentials manager (Vault integration ideal) |
| 6 | Shared libraries (DRY pipelines across teams) |
| 7 | Blue Ocean UI |
| 8 | Backup, RBAC, plugins hygiene |

### Sample declarative pipeline
```groovy
pipeline {
  agent { kubernetes { yaml '...' } }
  options { timeout(time: 30, unit: 'MINUTES') }
  environment { IMAGE = "ghcr.io/me/app:${env.GIT_COMMIT}" }
  stages {
    stage('Test')   { steps { sh 'pytest -q' } }
    stage('Scan')   { steps { sh 'trivy fs --exit-code 1 .' } }
    stage('Build')  { steps { sh 'docker build -t $IMAGE .' } }
    stage('Push')   { when { branch 'main' }
                      steps { withCredentials(...) { sh 'docker push $IMAGE' } } }
    stage('Deploy') { when { branch 'main' }
                      steps { sh "argocd app sync myapp --revision $GIT_COMMIT" } }
  }
  post { always { junit 'reports/*.xml' } }
}
```

### Official resources
- **[jenkins.io/doc](https://www.jenkins.io/doc/)** — official
- **[Pipeline syntax reference](https://www.jenkins.io/doc/book/pipeline/syntax/)** — declarative + scripted
- **[Jenkins Kubernetes plugin docs](https://plugins.jenkins.io/kubernetes/)**
- **[Jenkins Configuration as Code (JCasC)](https://github.com/jenkinsci/configuration-as-code-plugin)** — manage Jenkins itself as code

### Interview Qs
- "Declarative vs scripted pipeline?"
- "How do you isolate jobs? (agents, K8s pod templates)"
- "How do you manage Jenkins as code?"
- "Migration story: Jenkins → GitHub Actions / GitLab CI — what changed?"

---

## Part B — ArgoCD (GitOps CD)

> The modern way to deploy to Kubernetes: **Git is the source of truth.** No more `kubectl apply` from a laptop.

### Why GitOps
1. Every change is a Git commit → auditable, reviewable, revertable
2. Cluster always matches Git (continuous reconciliation)
3. Disaster recovery = `argocd app sync` (or just re-bootstrap from Git)
4. No CD credentials on developer laptops

### What to learn

| # | Topic |
|---|-------|
| 1 | Install ArgoCD on K8s (Helm or manifest) |
| 2 | **Application** CRD (Helm / Kustomize / plain manifests / Jsonnet) |
| 3 | **ApplicationSet** for multi-cluster / multi-tenant generation |
| 4 | **Projects** + RBAC + SSO (OIDC, SAML) |
| 5 | Sync waves & hooks |
| 6 | **Image Updater** — auto-bump image tags via PR/commit |
| 7 | Notifications (Slack/MS Teams) |
| 8 | Multi-cluster registration |

### Sample Application
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/me/charts
    targetRevision: HEAD
    path: charts/my-app
    helm:
      valueFiles: [values-prod.yaml]
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
  syncPolicy:
    automated: { prune: true, selfHeal: true }
    syncOptions: [CreateNamespace=true, ServerSideApply=true]
```

### GitOps repo layout (common)
```
infra-gitops/
├── apps/              # ArgoCD Application manifests (the "app of apps")
├── charts/            # Helm charts (or kustomize/)
└── values/            # per-env values: values-dev/stage/prod.yaml
```

### Alternatives to know
- **Flux CD** — CNCF graduate, GitOps too
- **Spinnaker** — heavyweight CD (legacy enterprise)
- **Tekton + Argo Rollouts** — pipelines + progressive delivery

### Official resources
- **[argo-cd.readthedocs.io](https://argo-cd.readthedocs.io/)** — official
- **[ArgoCD Best Practices](https://argo-cd.readthedocs.io/en/stable/user-guide/best_practices/)**
- **[GitOps Working Group (CNCF)](https://opengitops.dev/)** — principles
- **[Flux CD docs](https://fluxcd.io/flux/)** — the alternative
- **[Argo Rollouts](https://argo-rollouts.readthedocs.io/)** — canary / blue-green on K8s

### Interview Qs
- "What is GitOps in one minute?"
- "ArgoCD vs Flux — when each?"
- "How do you handle secrets in GitOps?"
- "How do you do canary deploys with ArgoCD?"
- "Walk me through your image-promotion flow dev → stage → prod."

---

## 🧪 Capstone

A **complete GitOps platform**:
1. Jenkins (or GitHub Actions) builds + scans + pushes images
2. Image-update PR opens against a Helm `values.yaml`
3. Merge → ArgoCD syncs → app deploys to K8s
4. Argo Rollouts does canary 10% → 50% → 100% on success metrics
5. Slack notifies the team

> One repo demonstrating this is worth **3 certifications** in interviews.

---

## ▶️ Next stop

[`20-CI-CD-IaC/03-IaC-Terraform-Ansible`](../03-IaC-Terraform-Ansible)
