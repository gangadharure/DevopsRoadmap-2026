# 03 — Google Cloud Certification Path (GCP)

> Google Cloud is the smaller of the Big 3 (after AWS & Azure) but dominant in **data/AI workloads** and the **default cloud for Kubernetes-first teams** (GKE invented K8s).

📘 Official: [cloud.google.com/learn/certification](https://cloud.google.com/learn/certification)

---

## 🗺️ The Path

```
        ┌──────────────────────────────────┐
        │  Cloud Digital Leader (CDL)      │   ← Foundational, optional
        └──────────────┬───────────────────┘
                       │
        ┌──────────────▼───────────────────┐
        │  Associate Cloud Engineer (ACE)  │   ← Start here for engineers
        └──────┬───────────┬───────────┬───┘
               │           │           │
   ┌───────────▼──┐   ┌────▼──────┐   ┌▼───────────────┐
   │ Pro Cloud   │   │ Pro Cloud │   │ Pro Cloud      │
   │ Architect   │   │ DevOps    │   │ Security       │
   │ (PCA)       │   │ (PCDE)    │   │ (PCSE)         │
   └─────────────┘   └───────────┘   └────────────────┘
        Specializations: Data, ML, Network, Database
```

---

## 🎯 Recommended Sequence

### For DevOps / Cloud Engineers
1. **Associate Cloud Engineer (ACE)** — foundation, hands-on
2. **Professional Cloud DevOps Engineer (PCDE)** — CI/CD, SRE, observability on GCP
3. *(Optional)* **Professional Cloud Architect (PCA)** — system design + multi-product

### For Solutions Architects
1. **Associate Cloud Engineer (ACE)**
2. **Professional Cloud Architect (PCA)** — the headline cert

### For Security
1. **ACE** → **Professional Cloud Security Engineer (PCSE)**

### For Data / ML
1. **ACE** → **Professional Data Engineer** or **Professional Machine Learning Engineer**

---

## 📋 Exam Quick Facts (2026)

| Cert | Level | Duration | Cost | Validity |
|------|-------|----------|------|----------|
| Cloud Digital Leader | Foundational | 90 min | $99 | 3 yr |
| **Associate Cloud Engineer (ACE)** | Associate | 120 min | $125 | 3 yr |
| Professional Cloud Architect (PCA) | Professional | 120 min | $200 | 2 yr |
| Professional Cloud DevOps Engineer (PCDE) | Professional | 120 min | $200 | 2 yr |
| Professional Cloud Security Engineer (PCSE) | Professional | 120 min | $200 | 2 yr |
| Professional Data Engineer (PDE) | Professional | 120 min | $200 | 2 yr |

> Format: MCQ + multi-select scenarios. Passing scores aren't published (you get pass/fail only).

---

## 📚 Official Resources (free)

| Resource | What |
|----------|------|
| **[Google Cloud Skills Boost](https://www.cloudskillsboost.google/)** | Official labs + learning paths (Qwiklabs heritage) |
| **[cloud.google.com/docs](https://cloud.google.com/docs)** | Full GCP documentation |
| **[Architecture Center](https://cloud.google.com/architecture)** | Reference architectures |
| **[Google Cloud Free Tier](https://cloud.google.com/free)** | $300 credit + always-free products |
| **[Google Cloud Tech YouTube](https://www.youtube.com/@googlecloudtech)** | Official channel |
| **[gcloud CLI docs](https://cloud.google.com/sdk/docs)** | CLI reference |
| **[GCP Blog](https://cloud.google.com/blog/)** | Service updates |

---

## 🏗️ Core Services to Master (for ACE)

| Category | Services |
|----------|----------|
| **Compute** | Compute Engine (VM), GKE, Cloud Run, Cloud Functions, App Engine |
| **Storage** | Cloud Storage (buckets + classes), Persistent Disk, Filestore |
| **Networking** | VPC, Subnets, Cloud Load Balancing (HTTPS/TCP/UDP), Cloud DNS, Cloud CDN, Cloud Interconnect, Cloud NAT |
| **Database** | Cloud SQL, Spanner, Firestore, Bigtable, AlloyDB, Memorystore |
| **Identity** | IAM, IAM Conditions, Workload Identity Federation, Service Accounts |
| **Security** | Cloud KMS, Secret Manager, Cloud Armor, Security Command Center |
| **Operations** | Cloud Monitoring, Cloud Logging, Cloud Trace, Cloud Profiler (all Stackdriver heritage) |
| **Data/AI** | BigQuery, Dataflow, Pub/Sub, Vertex AI, Gemini APIs |
| **DevOps** | Cloud Build, Artifact Registry, Cloud Deploy, Cloud Source Repos |

---

## 📅 12-Week ACE Roadmap

| Week | Focus |
|------|-------|
| 1 | GCP global infrastructure (regions/zones), Projects, IAM, billing, `gcloud` CLI |
| 2 | Compute Engine: VMs, instance groups (MIGs), images, persistent disks |
| 3 | GKE basics, Cloud Run, Cloud Functions |
| 4 | VPC, subnets, firewall rules, Cloud NAT, peering, Shared VPC |
| 5 | Load Balancing (Global HTTP(S), Internal, Network), Cloud DNS, Cloud CDN |
| 6 | Cloud Storage classes, lifecycle, signed URLs, transfer |
| 7 | Cloud SQL, Spanner overview, Firestore, choosing the right DB |
| 8 | IAM deep dive: roles, policies, conditions, organization hierarchy |
| 9 | Operations Suite: Monitoring, Logging, Trace, alerting |
| 10 | Cloud Build + Artifact Registry + Cloud Deploy |
| 11 | Security: KMS, Secret Manager, Cloud Armor basics |
| 12 | Practice exams + Skills Boost ACE learning path |

---

## 🧪 Capstone Projects

1. **Static site:** Cloud Storage bucket + Cloud CDN + Cloud Load Balancing + custom domain via Cloud DNS.
2. **Microservice on Cloud Run** with Cloud SQL backend + Secret Manager + Cloud Build CI from GitHub.
3. **GKE Autopilot cluster** deploying a Helm chart, with Cloud Logging + Monitoring integrated.
4. **BigQuery pipeline:** ingest via Pub/Sub → Dataflow → BigQuery → Looker Studio dashboard.
5. **Multi-project org:** Folders + Projects + Org Policies + Shared VPC + Workload Identity Federation from GitHub Actions.

---

## 🧠 Interview Topics

- "Cloud Run vs GKE vs Compute Engine — when each?"
- "Shared VPC — why and how?"
- "BigQuery vs Spanner vs Cloud SQL — when each?"
- "How do you authenticate from GitHub Actions to GCP without long-lived keys?"
- "Explain IAM Conditions."
- "What's a Service Account and how do you scope it?"
- "Walk me through a multi-region HA design on GCP."

---

## ▶️ After ACE

- **PCA** → architecture-heavy, system design, cross-product
- **PCDE** → SRE, CI/CD, observability on GCP (most relevant for DevOps)
- **PDE** → data engineering on BigQuery + Dataflow + Pub/Sub
- Build a hybrid project across AWS + GCP using **Anthos** (rare, valuable)
