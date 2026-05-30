# 01 — Observability: Prometheus, Grafana, Loki, OpenTelemetry

> **Metrics + Logs + Traces** — the 3 pillars. Open-source stack: **Prometheus + Loki + Tempo + Grafana**. Industry de-facto.

**Time:** 2 weeks · 1 hour/day
**Prereqs:** Docker + Kubernetes basics

---

## 🎯 The 3 pillars

| Pillar | Tool | What it answers |
|--------|------|-----------------|
| **Metrics** | Prometheus | "How much / how often / how fast?" |
| **Logs** | Loki (or Elasticsearch / OpenSearch) | "What happened in detail?" |
| **Traces** | Tempo / Jaeger | "Where did the request slow down?" |
| **Visualize** | Grafana | All-in-one dashboards & alerts |
| **Instrument** | **OpenTelemetry** | The vendor-neutral SDK + protocol |

---

## 📅 2-Week Roadmap

### Week 1 — Prometheus + Grafana

**Day 1 — Concepts**
- **Pull model** (Prometheus scrapes targets) vs push
- Time-series data model: `metric{label=value} value @timestamp`
- 4 metric types: **Counter, Gauge, Histogram, Summary**
- Service discovery (K8s, Consul, EC2, file_sd)

**Day 2 — Install & first scrape**
```bash
docker run -d -p 9090:9090 -v $PWD/prom.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
```yaml
# prom.yml
global: { scrape_interval: 15s }
scrape_configs:
  - job_name: node
    static_configs: [{ targets: ["node-exporter:9100"] }]
```

**Day 3 — Exporters (the rich ecosystem)**
- `node_exporter` (host CPU/RAM/disk/net) — every server runs this
- `cAdvisor` (container metrics)
- `blackbox_exporter` (HTTP/TCP/ICMP probe)
- `kube-state-metrics` + `kube-prometheus-stack` Helm chart for K8s
- DB exporters: `postgres_exporter`, `mysqld_exporter`, `redis_exporter`

**Day 4 — PromQL (the must-learn)**
```promql
# request rate per second
rate(http_requests_total[5m])

# error rate %
sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m])) * 100

# 95th-percentile latency
histogram_quantile(0.95,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (le))

# CPU per pod
sum(rate(container_cpu_usage_seconds_total{namespace="prod"}[5m])) by (pod)
```
> **Master `rate`, `sum`, `by`, `histogram_quantile`. 80% of PromQL.**

**Day 5 — Alerting (Alertmanager)**
```yaml
groups:
- name: api
  rules:
  - alert: HighErrorRate
    expr: |
      sum(rate(http_requests_total{status=~"5.."}[5m]))
        / sum(rate(http_requests_total[5m])) > 0.05
    for: 10m
    labels: { severity: page }
    annotations:
      summary: "{{ $labels.job }} 5xx > 5% for 10m"
```
- Route alerts to Slack / PagerDuty / OpsGenie / email
- Inhibition + grouping + silences

**Day 6 — Grafana**
- Install: `docker run -d -p 3000:3000 grafana/grafana`
- Add Prometheus as a data source
- Build a dashboard: 4 RED panels (Rate, Errors, Duration) + saturation
- Variables for `$namespace`, `$pod`
- Provision dashboards as JSON via IaC (don't click-build prod dashboards)

**Day 7 — Kubernetes-native: `kube-prometheus-stack`**
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```
- Ships: Prometheus + Alertmanager + Grafana + node-exporter + kube-state-metrics + 50+ default dashboards + ServiceMonitor CRDs

---

### Week 2 — Logs (Loki) + Traces (Tempo) + OTel

**Day 8 — Loki (logs)**
- Concept: **"Like Prometheus, but for logs"** — index labels only, content is compressed blobs (cheap)
- LogQL similar to PromQL: `{app="api"} |= "ERROR" | rate(1m)`
- Push agents: **Promtail**, **Grafana Alloy** (modern, replaces Promtail), or **Fluent Bit**

**Day 9 — Loki in K8s**
- Helm: `grafana/loki-stack` (single-binary) or `grafana/loki` (microservices for scale)
- Grafana → add Loki data source → Explore → query
- Build a log panel next to metric panels for incident triage

**Day 10 — Tempo (traces)**
- Distributed tracing: parent/child spans, propagation headers (W3C TraceContext)
- Install: `grafana/tempo` Helm chart
- Visualize: Grafana → Explore → Tempo → trace waterfall

**Day 11 — OpenTelemetry (the unifier)**
- **OTel SDK** in your app — instrument metrics + logs + traces in one place
- **OTel Collector** — receives OTLP → routes to Prometheus / Loki / Tempo / vendors
- Auto-instrumentation for Python, Java, Node, Go, .NET, Ruby
- Why it matters: **vendor-neutral** — swap backends without re-instrumenting

**Day 12 — SRE concepts (apply to dashboards)**
- **SLI** (Service Level Indicator) — what you measure (latency, error rate, throughput, freshness)
- **SLO** — target (e.g. 99.9% successful requests / month)
- **Error budget** — `(1 - SLO) * total requests`; burn rate alerts
- **RED method** (Rate, Errors, Duration) for services
- **USE method** (Utilization, Saturation, Errors) for resources

**Day 13 — Alerting maturity**
- Good alerts: **actionable**, **symptom-based**, **page only when human action needed**
- Bad alerts: noisy, threshold-based, "CPU > 80%" alone
- Multi-window, multi-burn-rate alerts (Google SRE book)

**Day 14 — Cost-aware observability**
- Cardinality kills Prometheus → no high-cardinality labels (`user_id`, `request_id`)
- Retention tiers: 15 days hot, 90 days warm (Thanos / Mimir / Cortex)
- Sample traces (head-based 1%, or tail-based)
- Log levels: INFO+ in prod, DEBUG only on demand

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[prometheus.io/docs](https://prometheus.io/docs/)** | Official Prometheus + PromQL |
| **[grafana.com/docs](https://grafana.com/docs/grafana/latest/)** | Grafana docs |
| **[grafana.com/docs/loki](https://grafana.com/docs/loki/latest/)** | Loki + LogQL |
| **[grafana.com/docs/tempo](https://grafana.com/docs/tempo/latest/)** | Tempo (traces) |
| **[opentelemetry.io/docs](https://opentelemetry.io/docs/)** | OTel SDKs + Collector |
| **[Google SRE Books (free online)](https://sre.google/books/)** | Site Reliability Engineering + Workbook |
| **[USE method (Brendan Gregg)](https://www.brendangregg.com/usemethod.html)** | Performance methodology |
| **[RED method (Tom Wilkie)](https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/)** | Service methodology |
| **[awesome-prometheus-alerts](https://samber.github.io/awesome-prometheus-alerts/)** | Battle-tested alert rules |

---

## 🧪 Capstone

A complete observability stack on Kubernetes:
1. `kube-prometheus-stack` deployed
2. App instrumented via OpenTelemetry SDK → metrics + traces to OTel Collector → Prometheus + Tempo
3. Logs via Grafana Alloy → Loki
4. Grafana dashboard with RED + traces + log panel per service
5. 3 alerts (high error rate, slow latency p95, pod restarts) → Slack
6. SLO of 99.9% on the main service + error-budget burn-rate alert
7. Everything provisioned via Helm + values in Git (no click-ops)

---

## 🧠 Interview Questions

- "What's the difference between Prometheus and Loki?"
- "Why pull model in Prometheus?"
- "What's high cardinality and why is it dangerous?"
- "Explain SLI, SLO, error budget."
- "When would you use traces vs logs vs metrics?"
- "Why OpenTelemetry?"
- "Walk me through an incident: how did your dashboards help?"
- "RED vs USE method?"

---

## ▶️ Next stop

[`40-Cloud-Certification-Paths`](../../40-Cloud-Certification-Paths) → pick AWS / Azure / GCP cert track.
