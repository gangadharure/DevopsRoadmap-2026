# 02 — AWS Certified Solutions Architect — Associate (SAA-C03)

> Design well-architected, cost-optimized, secure, and resilient systems on AWS.
> The most-respected AWS associate-level certification.

**Time:** 10–12 weeks · 1–2 hours/day
**Exam:** SAA-C03 · 130 min · 65 questions (MCQ + multi-response) · ~720/1000 to pass · $150 USD · 3-year validity
**Prereq:** Linux fluency (see [`01-Linux`](../01-Linux))

---

## 🎯 Exam Domains & Weighting (official)

| Domain | Weight |
|--------|--------|
| 1. Design Secure Architectures | **30%** |
| 2. Design Resilient Architectures | **26%** |
| 3. Design High-Performing Architectures | **24%** |
| 4. Design Cost-Optimized Architectures | **20%** |

📘 Official source: [AWS SAA-C03 Exam Guide (PDF)](https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide.pdf)

---

## 📅 12-Week Roadmap (1–2 hours/day)

### Phase 1 — Foundations (Weeks 1–2)

- **Week 1:** AWS global infrastructure (Regions, AZs, Edge), IAM users/groups/roles/policies, Organizations, OU, SCP, AWS CLI v2, Identity Center (SSO)
- **Week 2:** VPC basics — subnets (public/private), Internet Gateway, NAT Gateway, route tables, security groups vs NACLs, VPC peering, Transit Gateway

**Hands-on:** Build a 2-tier VPC (public + private subnet) in 2 AZs, launch an EC2 in the private subnet, reach it via a bastion / Systems Manager Session Manager.

---

### Phase 2 — Compute (Weeks 3–4)

- **Week 3:** EC2 instance families & sizing, AMIs, EBS (gp3/io2/st1/sc1), instance store, snapshots, Elastic IPs, placement groups, dedicated hosts/tenancy
- **Week 4:** Auto Scaling Groups, Launch Templates, Load Balancers (ALB, NLB, GWLB), target groups, sticky sessions, ECS vs EKS vs Fargate vs Lambda decision criteria

**Hands-on:** Build an ASG behind an ALB across 2 AZs, scale on CPU, run a containerized app on Fargate.

---

### Phase 3 — Storage & Data (Weeks 5–6)

- **Week 5:** S3 storage classes (Standard / IA / Glacier tiers), lifecycle policies, versioning, replication (CRR/SRR), Object Lock, S3 Access Points, CloudFront + OAC, Storage Gateway types, EFS vs FSx (Windows/Lustre/NetApp/OpenZFS)
- **Week 6:** RDS (Multi-AZ vs Read Replica), Aurora (Global, Serverless v2), DynamoDB (partition key design, GSI vs LSI, DAX, Streams), ElastiCache (Redis vs Memcached), Redshift basics

**Hands-on:** S3 static site + CloudFront + Route 53; RDS Multi-AZ Postgres with a read replica.

---

### Phase 4 — Security & Identity (Weeks 7–8)

- **Week 7:** IAM deep dive (policy evaluation logic, permission boundaries, ABAC), STS, Identity Center, Cognito (User Pools vs Identity Pools), Directory Service
- **Week 8:** KMS (CMK types, key policies, envelope encryption), Secrets Manager vs Parameter Store, ACM, WAF, Shield (Standard vs Advanced), GuardDuty, Inspector, Macie, Security Hub, CloudTrail vs Config

**Hands-on:** Encrypt an S3 bucket + RDS with KMS CMK; rotate a secret in Secrets Manager; enable GuardDuty + Security Hub in an Organization.

---

### Phase 5 — Networking, Integration, Migration (Weeks 9–10)

- **Week 9:** Route 53 (routing policies: simple/weighted/latency/failover/geolocation/geoproximity/multivalue), VPC endpoints (Gateway vs Interface), PrivateLink, Direct Connect vs Site-to-Site VPN, Global Accelerator
- **Week 10:** SQS (Standard vs FIFO, DLQ), SNS, EventBridge, Step Functions, API Gateway (REST vs HTTP vs WebSocket), AppSync, Kinesis (Data Streams / Firehose / Data Analytics), MSK; Migration: DMS, SMS, Application Migration Service, Snow Family, DataSync

**Hands-on:** Build an event-driven pipeline: API Gateway → Lambda → SQS → Lambda → DynamoDB + SNS notification on failure.

---

### Phase 6 — Cost, Ops, Well-Architected (Week 11)

- Cost: pricing models (On-Demand, Reserved, Spot, Savings Plans), Cost Explorer, Budgets, Cost & Usage Report, Compute Optimizer
- Ops: CloudWatch (metrics, logs, alarms, dashboards, EventBridge), Systems Manager (Session Manager, Patch Manager, Parameter Store), Trusted Advisor
- The **6 pillars of the AWS Well-Architected Framework**

**Hands-on:** Run the Well-Architected Tool on your earlier projects.

---

### Phase 7 — Exam Prep (Week 12)

- Take the **official AWS practice questions** (free on AWS Skill Builder)
- Re-read the **Exam Guide PDF**
- Re-read **AWS Well-Architected** white-paper
- Build flashcards for service decision criteria (e.g. "SQS FIFO vs SNS FIFO vs Kinesis")
- Take 2 full-length timed practice exams

---

## 📚 Official Resources

| Resource | Use |
|----------|-----|
| **[AWS SAA-C03 Exam Guide (PDF)](https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide.pdf)** | THE source of truth — read 3× |
| **[AWS Skill Builder — Exam Prep Standard Course (free)](https://skillbuilder.aws/)** | Official prep, free |
| **[AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)** | Pillars + design principles |
| **[AWS Whitepapers Library](https://aws.amazon.com/whitepapers/)** | "Overview of AWS", security best practices |
| **[AWS FAQs](https://aws.amazon.com/faqs/)** | One per major service — exam questions hide here |
| **[AWS re:Post](https://repost.aws/)** | Official Q&A community |
| **[AWS Free Tier](https://aws.amazon.com/free/)** | 12-month + always-free for hands-on |

---

## 🧪 Capstone Projects (build before the exam)

1. **3-tier web app** — ALB + ASG (web) + ASG (app) + RDS Multi-AZ + ElastiCache.
2. **Serverless image-resize pipeline** — S3 → Lambda → S3 + DynamoDB metadata + CloudFront.
3. **Hybrid networking** — VPC + Site-to-Site VPN to your home network or another VPC via Transit Gateway.
4. **Cost-optimized batch** — Spot Fleet + S3 + EventBridge schedule + CloudWatch alarms.
5. **Multi-account org** — Organizations + SCPs + Identity Center + a "logging" + "security" + "workload" account split.

---

## 🧠 Interview Topics

- IAM policy evaluation logic (explicit deny > allow > implicit deny)
- VPC subnet sizing & CIDR planning
- "When would you use SQS vs SNS vs Kinesis vs EventBridge?"
- "ALB vs NLB vs API Gateway?"
- "DynamoDB partition key design pitfalls"
- "S3 consistency model + how to enforce encryption"
- "Multi-AZ vs Multi-Region for RTO/RPO"
- "Cost optimization: Spot, Savings Plans, RI matrix"

---

## ▶️ Next stop

- Add Kubernetes → [`04-CKA-Kubernetes`](../04-CKA-Kubernetes)
- Become a full DevOps engineer → [`05-DevOps`](../05-DevOps)
