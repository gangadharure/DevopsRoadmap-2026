# 07 — Scenario-Based Interview Questions

> These are the "how would you handle..." questions. The interviewer is testing **judgement, structure, and communication** — not memorised commands. Always:
> 1. Clarify scope first
> 2. State assumptions out loud
> 3. Walk through your thinking step-by-step
> 4. Mention trade-offs

---

### Scenario 1 — Production is down. What's your first 10 minutes?

1. **Acknowledge** the alert in PagerDuty so others know it's owned.
2. **Open the incident channel** (Slack `#inc-XYZ`), assign **Incident Commander** + **Comms** roles if it's bigger than one person.
3. **Check the obvious dashboards** — error rate, latency, saturation (RED method). Is it the app, the DB, the network, or a recent deploy?
4. **Check change calendar** — was anything deployed in the last 30 min? If yes, **roll back first, investigate after**.
5. **Stabilise > fix.** Failover, scale up, flip a feature flag — get users working again. Root cause can wait.
6. **Communicate every 15 minutes** to stakeholders even with "still investigating".
7. After resolution: **blameless post-mortem** within 5 business days.

---

### Scenario 2 — CI pipeline takes 45 minutes. Speed it up.

1. **Measure first.** Add timing to each stage. Is it tests? Build? Push? Approval wait?
2. **Parallelise** independent jobs (test sharding, matrix builds).
3. **Cache** dependencies (`actions/cache`, BuildKit cache, Maven/Gradle local repo, npm/pip).
4. **Skip unaffected paths** — path filters, monorepo tooling (Nx, Bazel, Turborepo).
5. **Split slowest tests** to their own job; consider quarantining flaky ones.
6. **Bigger runners** (instances or self-hosted with NVMe).
7. **Build once, reuse** the artifact through stages.
8. **Container image:** multi-stage, smaller base, smarter layer order (deps before app code).
9. Re-measure; iterate. Aim for "commit-to-prod < 30 min" as the long-term target.

---

### Scenario 3 — Service has 5% error rate spike. Walk me through debugging.

1. **Scope:** which endpoint? Which region? Which user segment? Tail latency vs errors?
2. **Recent deploy?** Roll back if correlated.
3. **Dependencies:** is the DB / cache / downstream API slow or failing? Check their dashboards.
4. **Resource saturation:** CPU? Memory? File descriptors? Network? `kubectl top`, Prometheus.
5. **Logs at the same time window** — `kubectl logs --since=15m | grep ERROR`, or Loki / CloudWatch query.
6. **Traces** — pick a sample of failed requests, follow the span with errors (OpenTelemetry / Jaeger).
7. Form a hypothesis, test smallest fix possible (rate-limit a client, restart a pod, scale up). Don't shotgun.

---

### Scenario 4 — You inherited a "snowflake" prod server (no IaC). How do you migrate it?

1. **Document current state** — inventory running services, ports, mounts, cron jobs, users (`systemctl list-units`, `crontab -l`, `ss -tlnp`, `dpkg -l`).
2. **Take a snapshot** before touching anything.
3. **Pick the target** — containerise on K8s? Replace with a managed service (RDS, App Service)? Lift-and-shift with Terraform-managed VM + Ansible config?
4. **Write IaC for the new version** in parallel; do NOT modify the snowflake.
5. **Stand up the new env**, sync data, run smoke tests.
6. **Cutover** (DNS flip or blue/green at the LB).
7. **Decommission** the snowflake after a safety window (1-2 weeks).

---

### Scenario 5 — Cloud bill jumped 40% this month. What do you do?

1. **Cost Explorer / Cost Analysis** — break down by service, region, tag, account. Find the top 3 contributors.
2. **What changed?** New workload? Forgotten environment? Egress spike (NAT GW, CloudFront origin pulls)?
3. **Quick wins:**
   - Untagged resources → owner missing → suspect zombies
   - Idle resources (unattached EBS, old snapshots, idle LBs, oversized RDS)
   - Dev envs running 24/7 (auto-stop at night/weekends)
   - NAT GW egress to S3/ECR → add VPC endpoints
4. **Structural fixes:** Savings Plans/RIs for steady workloads, Spot/Graviton for batch, S3 lifecycle, right-sizing.
5. **Process:** tag everything (cost-allocation tags), budgets + anomaly detection, monthly FinOps review.

---

### Scenario 6 — Designing a deployment pipeline from scratch for a new product. Walk me through it.

1. **Source control:** GitHub mono-repo with trunk-based dev + short-lived branches + branch protection (required reviews, status checks, signed commits).
2. **CI:** GitHub Actions on PR → lint → unit tests → build container → SAST (Semgrep) → SCA (Trivy) → image scan → push to registry signed with cosign + SBOM.
3. **CD (GitOps):** ArgoCD watches `infra/` git repo. CI bumps image tag in `dev/values.yaml` → ArgoCD syncs to dev cluster.
4. **Promotion:** PR to `staging/values.yaml`, then `prod/values.yaml`. Each gated by manual approval + automated smoke tests.
5. **Prod:** Argo Rollouts canary (10% → 50% → 100%), driven by Prometheus success metrics.
6. **Observability hooks:** OpenTelemetry, Prometheus, Loki, alerting tied into PagerDuty.
7. **Rollback:** `argocd app rollback` or `kubectl argo rollouts abort`.

---

### Scenario 7 — A teammate pushed AWS keys to a public GitHub repo. Go.

1. **Rotate immediately** — IAM console → deactivate + delete keys. Generate new ones (or, better, switch the workload to a role / OIDC).
2. **Audit CloudTrail** for the leaked key — what did it touch in the past N hours? Filter by `userIdentity.accessKeyId`.
3. **Force-remove from git history** — `git filter-repo` / BFG, force-push. **Note:** GitHub may have cached / forked it; treat as compromised forever.
4. **Notify security team.** Document.
5. **Prevent:** repo-level secret scanning (GitHub Advanced Security), `git-secrets` / `gitleaks` pre-commit hook, never use long-lived keys for CI — use OIDC.

---

### Scenario 8 — Pod is `CrashLoopBackOff` in prod, no logs.

1. `kubectl describe pod <p>` — events + reason + exit code.
2. `kubectl logs <p> --previous` — logs of the last crashed instance.
3. **Exit code 137** = OOMKilled → bump memory limit OR fix the leak.
4. **Exit code 1/2** = app error — check liveness/readiness; bad config? missing env var? secret not mounted?
5. **Exit code 139** = segfault. **143** = SIGTERM (probe killed it too fast — bump `initialDelaySeconds`).
6. `kubectl debug` to run an ephemeral container with shell + same volumes if app has no shell.
7. If image is broken: pull locally, `docker run --rm -it --entrypoint sh image:tag` to inspect.

---

### Scenario 9 — Designing on-call rotation for a 6-engineer team. How?

- 1-week primary + 1-week secondary, follow-the-sun if possible.
- Pager only for **user-impacting + actionable** alerts; ruthlessly tune the rest.
- **Compensation:** time-off-in-lieu or pay supplement; don't burn people.
- Every incident → ticket → post-mortem → action item with an owner + due date.
- Weekly review of pages: which alerts woke us, which were noise, what's the fix.
- SLO-driven alerting (error budget burn rate), not threshold spam.
- Onboarding: shadow before solo; up-to-date runbooks per service.

---

### Scenario 10 — Your monitoring is too noisy. How do you fix it?

1. **Burn-rate alerts** instead of static thresholds (Google SRE method): page only when SLO error budget is being burned fast enough to exhaust in N hours.
2. **Tier alerts** — P1 page, P2 ticket, P3 dashboard-only.
3. **De-dupe** at the alertmanager (group by `alertname, cluster, namespace`).
4. **Symptom alerts** (user-facing: error rate, latency) > **cause alerts** (CPU > 80%).
5. Every alert needs a **runbook URL** in its annotation.
6. Run a monthly "alert hygiene" review — delete or fix every noisy alert.

---

### Scenario 11 — You need to give a developer access to debug a prod pod, without giving full prod access.

- **K8s RBAC:** ClusterRole or namespace Role with verbs `get`, `list`, `watch`, `pods/log`, `pods/exec` only — no `create`, `delete`, `update`. Bind to a Group via OIDC.
- **Just-in-time access:** ticket → temporary group membership (Teleport, Boundary, AWS IAM Identity Center session policies) that auto-expires.
- **Audit everything:** kube-apiserver audit log → SIEM. They'll be watched.
- Better: build good observability so devs rarely need shell on prod.

---

### Scenario 12 — Two teams want to share a Kubernetes cluster. How do you isolate them?

- **Namespaces** per team + **ResourceQuota** + **LimitRange** so neither can starve the other.
- **NetworkPolicy** denying cross-namespace traffic by default.
- **RBAC** — each team only sees their namespace.
- **PodSecurity admission** standards (restricted profile baseline).
- **Node pools / taints + tolerations** if workloads have different needs (GPU pool, spot pool).
- **Cost allocation** via labels → Kubecost / OpenCost.
- If isolation needs go beyond this: **separate clusters**, not just separate namespaces.

---

### General framework for any "how would you…" question

```
1. Clarify   → "What scale? What constraints? What's already in place?"
2. Constrain → "I'll assume <x>, <y>, <z>."
3. Sketch    → high-level approach + diagram
4. Detail    → one component at a time
5. Trade-offs → "I'd pick A over B because…"
6. Operate   → How will we monitor, alert, roll back, handle failure?
```
