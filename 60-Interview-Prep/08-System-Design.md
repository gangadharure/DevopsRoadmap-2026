# 08 — System Design (Cloud-Native)

> System-design rounds for cloud/DevOps roles aren't pure SWE LeetCode design. They focus on:
> **infrastructure shape, deploy pipeline, observability, scaling, failure modes, security, cost.**

---

## 🧱 Universal framework

1. **Clarify requirements** — users, regions, RPS, payload size, durability, SLA.
2. **Estimate scale** — back-of-envelope (storage = users × records × size; QPS = DAU × actions / 86400 × peak).
3. **Sketch high-level diagram** — clients → edge → LB → service → cache → DB → queue → workers → blob → analytics.
4. **Pick technologies** — and justify (managed vs self-hosted, SQL vs NoSQL).
5. **Discuss failure modes** — AZ down, region down, DB failover, poison message, thundering herd, runaway cost.
6. **Observability & deploy** — metrics, logs, traces, alerts, blue/green or canary.
7. **Security** — auth, network isolation, secrets, encryption, audit.
8. **Cost** — main drivers + how to control them.

---

## 📐 Common building blocks (reach for these by default)

| Need | Pick |
|------|------|
| Static assets / SPA | S3/Blob + CloudFront/CDN |
| TLS termination + WAF | CloudFront / Front Door / ALB + AWS WAF / Cloud Armor |
| L7 routing | ALB / App Gateway / NGINX / Istio Gateway |
| Compute (stateless web/API) | EKS/AKS/GKE, ECS Fargate, Cloud Run, App Service |
| Async work | SQS / Service Bus / Pub-Sub + workers (KEDA scale) |
| Event streaming | Kafka / Kinesis / Event Hubs |
| Relational DB | RDS/Aurora / Azure SQL / Cloud SQL — Multi-AZ |
| KV / low-latency | DynamoDB / Cosmos DB / Bigtable |
| Cache | ElastiCache Redis / Azure Cache for Redis / Memorystore |
| Search | OpenSearch / Azure AI Search |
| Object store | S3 / Blob / GCS |
| Secrets | Secrets Manager / Key Vault / Secret Manager |
| CI/CD | GitHub Actions + ArgoCD (GitOps) |
| Observability | Prometheus + Grafana + Loki + Tempo + OpenTelemetry |

---

## Scenario A — Design a global URL shortener (TinyURL)

**Reqs:** 100M new URLs/day, 10B redirects/day, <100ms p95 latency globally, 5-year retention, custom aliases.

**Estimate:**
- Writes: 100M/day ≈ 1.2k/s avg, ~5k/s peak
- Reads: 10B/day ≈ 116k/s avg, ~500k/s peak (100× read-heavy)
- Storage (5 yr): 100M × 365 × 5 × ~500 B = ~90 TB

**Design:**
1. **ID generation:** base62 of a counter from a distributed ID service (Snowflake-like) or hash(url)+collision check. Pre-generate batches per shard.
2. **Write path:** API → DynamoDB (or Cosmos DB) — short_code as partition key, attributes: long_url, owner, created_at, expires_at.
3. **Read path:** CloudFront cache (very high hit rate, URLs are immutable) → API Gateway → Lambda or container → DynamoDB. 301 vs 302 trade-off (301 caches forever).
4. **Edge cache TTL** of hours; cache invalidation only if user deletes.
5. **Custom aliases:** uniqueness check via conditional put.
6. **Analytics:** click event → Kinesis Firehose → S3 → Athena/BigQuery.
7. **Multi-region:** DynamoDB Global Tables, multi-region CloudFront.
8. **Cost driver:** the read tier — solved by aggressive CDN caching.

---

## Scenario B — Design a CI/CD platform for 200 microservices

**Reqs:** monorepo + polyrepo mix, ≤30 min commit-to-prod, multi-cloud, audit trail, secret-free runners.

**Design:**
1. **Source:** GitHub Enterprise, branch protection, signed commits, CODEOWNERS, required reviews.
2. **CI:** GitHub Actions with self-hosted runners on EKS (Karpenter autoscale to zero), ephemeral, per-job.
3. **Build:** BuildKit with remote cache (S3) and image layer sharing across services. Distroless base images.
4. **Security gates:** Semgrep (SAST), Trivy (image + IaC), Snyk/Dependabot (deps), Cosign signing, SBOM via Syft.
5. **Artifact registry:** ECR with replication; immutable tags.
6. **CD (GitOps):** ArgoCD per cluster, ApplicationSets generate one app per service from the Git directory. Bot PR bumps image tag per env.
7. **Deploy strategy:** Argo Rollouts canary, driven by Prometheus success rate.
8. **Observability:** OpenTelemetry from the runner → Tempo → Grafana for pipeline traces. Backstage as developer portal.
9. **Secrets:** OIDC federation from runners to AWS/Azure/GCP — zero long-lived keys.
10. **Audit:** All actions → CloudTrail + GitHub audit log → SIEM.

---

## Scenario C — Design a Kubernetes platform for an internal "PaaS"

**Reqs:** 50 dev teams should self-serve deployments without learning K8s YAML.

**Design:**
1. **Cluster layout:** 3 envs × 2 regions = 6 EKS/AKS clusters. Karpenter for nodes. Cluster-mesh via Cilium or Istio multi-cluster.
2. **Abstraction:** Crossplane / Backstage templates / KubeVela / cdk8s — devs commit a small YAML (`service: name, image, port, env, secrets`) → controller renders full K8s manifests.
3. **Multi-tenancy:** Namespace per service, NetworkPolicy default-deny, ResourceQuota, PodSecurity restricted.
4. **Ingress:** Single shared Ingress controller (ALB / Gateway API) — automatic DNS via ExternalDNS, cert via cert-manager + Let's Encrypt.
5. **Secrets:** External Secrets Operator → Vault / cloud secret store.
6. **CI/CD:** ArgoCD per cluster + repo-per-team.
7. **Observability built in:** Prometheus auto-scrape, Loki log shipping via DaemonSet, OpenTelemetry SDK injected via OperatorHub.
8. **Cost:** Kubecost dashboards, chargeback by namespace label.
9. **Developer UX:** Backstage portal — create service → spawns repo + ArgoCD app + dashboards.

---

## Scenario D — Design a logging pipeline for 50 TB/day

**Design:**
1. **Collectors:** OpenTelemetry Collector / Fluent Bit DaemonSet on every node. Resource limits + back-pressure.
2. **Transport:** Kafka / Kinesis — buffer + decouple producers from storage.
3. **Hot store:** Loki (cheap, label-indexed) or Elasticsearch (full text, expensive) — 7–14 days.
4. **Cold store:** S3 / Blob (parquet) for compliance retention (1–7 years), queried by Athena / BigQuery on demand.
5. **Filtering at the edge:** drop debug logs in prod, redact PII, sample high-volume noisy services.
6. **Cost control:** target $X/GB ingested; alert if a service blows past its quota; per-team chargeback.
7. **Reliability:** at-least-once delivery, persistent buffer on disk, multi-AZ Kafka, DLQ for malformed events.

---

## Scenario E — Design a multi-region active-active SaaS API

**Reqs:** 99.99% availability (~52 min/yr), <200 ms p95 globally, GDPR data residency.

**Design:**
1. **Edge:** Route 53 / Front Door — latency-based routing → 3 regions (us-east, eu-west, ap-south).
2. **Each region**: own ALB → EKS → service → regional DB.
3. **Data layer (the hard part):**
   - **Option A** — Aurora Global Database / Cosmos DB multi-region writes (CRDT or LWW conflict resolution).
   - **Option B** — partition users by region (data residency wins); each user's writes go to their home region; cross-region reads via cache + eventual replication.
4. **Caching:** Redis cluster per region, write-through, short TTL for cross-region invalidation via Pub/Sub.
5. **Failover:** Route 53 health checks pull a region out of rotation in <60s. Practice with quarterly chaos game-days.
6. **Deploy:** progressive — canary in one region first, soak, then roll across regions.
7. **Observability:** Per-region SLOs aggregated to a global SLO; error-budget alerts.
8. **GDPR:** EU users' data only in eu-west region; control via tenant routing layer.

---

## Scenario F — Design an observability stack from scratch on K8s

**Pick:**
- **Metrics:** Prometheus (kube-prometheus-stack), long-term storage = Thanos or Mimir.
- **Logs:** Loki + Promtail/Alloy.
- **Traces:** Tempo or Jaeger, instrumented via OpenTelemetry SDKs auto-injected.
- **Dashboards:** Grafana (one tool, three datasources).
- **Alerting:** Alertmanager → PagerDuty + Slack. Alert rules in git, applied via ArgoCD.
- **SLO tracking:** Sloth or Pyrra to generate burn-rate alerts from PromQL.
- **APM:** correlate traces ↔ logs ↔ metrics via `trace_id` label.

**Capacity:** size Prometheus by series count (sample ~2 bytes × samples/s × retention). Use recording rules to pre-aggregate hot queries.

---

## Scenario G — Disaster recovery for a stateful workload

**Define:**
- **RPO** (data loss tolerated) — drives backup frequency
- **RTO** (time-to-recover) — drives architecture

| RTO | Strategy |
|-----|----------|
| Hours | Backup & restore (snapshots → S3 cross-region) |
| <1 hr | Pilot light (minimal env warm in DR region, scale on failover) |
| Minutes | Warm standby (running but smaller) |
| Seconds | Multi-region active-active |

**Test the DR plan** quarterly with a real failover drill — untested DR is no DR.

---

## ⚠️ Trade-offs interviewers love to hear

- **Consistency vs availability** (CAP) — pick per data category.
- **Build vs buy** (managed service vs self-host) — speed vs cost vs control.
- **Pull (GitOps) vs push** (CI deploys) — security & audit vs simplicity.
- **One big cluster vs many small** — efficiency vs blast radius.
- **Monolith vs microservices** — operational cost vs team independence.
- **Synchronous vs async** (queue between services) — latency vs resilience.

---

## ✅ Closing the round well

End with: *"Things I'd build in V2 if we had more time…"* — caching layer, cross-region replication, cost controls, fine-grained authz. Shows you know what you're skipping and why.
