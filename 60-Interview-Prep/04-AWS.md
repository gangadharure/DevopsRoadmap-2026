# 04 — AWS Interview Questions

---

### Q1. Region vs AZ vs Edge location?
- **Region** — geographic area (us-east-1). Independent.
- **AZ** — one or more data centres within a region, isolated power/cooling/network but low-latency linked. Always design **multi-AZ** for HA.
- **Edge location** — CloudFront/Global Accelerator POPs (400+) for caching close to users.

### Q2. EC2 instance types — how do you pick?
Family by workload:
- **t** (burstable) — dev/low-traffic web
- **m** (general) — balanced
- **c** (compute) — CPU-heavy
- **r/x** (memory) — caches, in-memory DBs
- **i/d** (storage) — high IOPS / large local NVMe
- **g/p** (GPU) — ML training/inference
- **a** (Graviton/ARM) — cheaper, modern

Right-size with CloudWatch + Compute Optimizer; commit via **Savings Plans** / Reserved Instances when steady; **Spot** for fault-tolerant batch.

### Q3. S3 storage classes?
| Class | Use |
|-------|-----|
| Standard | Hot data |
| Intelligent-Tiering | Auto-move when unknown access pattern |
| Standard-IA / One Zone-IA | Infrequent access, ms retrieval |
| Glacier Instant Retrieval | Quarterly access, ms |
| Glacier Flexible | Minutes-hours retrieval, cheap |
| Glacier Deep Archive | 12-hr retrieval, cheapest |

Lifecycle policies move objects automatically.

### Q4. IAM Roles vs Users vs Policies vs Groups?
- **User** — identity for a human or legacy app (with long-lived keys — avoid).
- **Group** — bundle of users sharing policies.
- **Role** — identity assumed temporarily; grants short-lived STS creds. Use for EC2, Lambda, cross-account, federation, OIDC.
- **Policy** — JSON document defining permissions; attached to user/group/role.

**Rule:** prefer roles over users; never embed access keys.

### Q5. How does an EC2 instance get AWS permissions?
Attach an **Instance Profile** (wraps an IAM role). The EC2 metadata service (IMDSv2 — token-required) provides short-lived creds at `http://169.254.169.254/latest/meta-data/iam/security-credentials/<role>`. SDK auto-discovers.

### Q6. VPC core concepts?
- VPC = isolated network in a region, CIDR you choose (e.g. 10.0.0.0/16)
- **Subnets** are per-AZ; **public** (has route to IGW) vs **private** (only NAT GW for egress)
- **Route Tables** decide where traffic goes
- **Security Groups** = stateful, attached to ENI, default-deny ingress
- **NACLs** = stateless, subnet-level, allow + deny rules
- **VPC Endpoints** (Gateway: S3/Dynamo; Interface: everything else via PrivateLink) — private path to AWS APIs

### Q7. Public vs Private subnet — what's the actual difference?
A subnet is "public" if its route table sends `0.0.0.0/0` to an **Internet Gateway (IGW)** AND instances have public IPs / Elastic IPs. Private subnets route `0.0.0.0/0` to a **NAT Gateway** for outbound-only.

### Q8. ALB vs NLB vs CLB?
- **ALB** (L7) — HTTP/HTTPS, host/path routing, WAF, target groups, websockets, gRPC
- **NLB** (L4) — TCP/UDP, ultra-low latency, millions of CPS, preserves source IP
- **CLB** — legacy, avoid for new work
- **GWLB** — for inline security appliance chains

### Q9. RDS vs Aurora vs DynamoDB?
- **RDS** — managed MySQL/Postgres/etc. Up to 64 TB, single-writer.
- **Aurora** — AWS-built MySQL/Postgres-compatible storage layer; 6-way replicated, auto-grows to 128 TB, faster failover, read replicas. Aurora Serverless v2 for variable workloads.
- **DynamoDB** — managed NoSQL KV/document, single-digit-ms, infinite scale, pay per request or provisioned. Use for high-scale lookups, sessions, IoT.

### Q10. How do you make RDS highly available?
Enable **Multi-AZ** — synchronous standby in another AZ, automatic failover (~60s). Add **read replicas** for read scaling (async). Backups: automated + manual snapshots. Cross-region: cross-region read replicas or Aurora Global Database.

### Q11. S3 — how do you secure a bucket?
1. Block Public Access ON (account & bucket)
2. Bucket policy with explicit conditions (`aws:SourceVpce`, `aws:SecureTransport`)
3. Object Ownership = Bucket owner enforced (disables ACLs)
4. Default encryption (SSE-S3, SSE-KMS for audit/rotation)
5. Versioning + MFA Delete + Object Lock for ransomware resilience
6. Access logs to a separate bucket
7. Macie for PII detection

### Q12. CloudFront — when to use?
CDN: cache static + dynamic content at edge. Use for: latency reduction, DDoS absorption (with Shield/WAF), TLS termination near user, signed URLs/cookies for private content, Lambda@Edge / CloudFront Functions for request rewriting.

### Q13. Difference: SQS vs SNS vs EventBridge vs Kinesis?
- **SQS** — point-to-point queue (1 producer → 1 consumer group). Standard or FIFO.
- **SNS** — pub/sub fan-out (1→many topic subscribers).
- **EventBridge** — schema-aware event bus with routing rules + 200+ SaaS integrations.
- **Kinesis Data Streams** — ordered, replayable, sharded stream (Kafka-like) for high-throughput data pipelines.

### Q14. How do you build a serverless API?
API Gateway (REST or HTTP API) → Lambda → DynamoDB. Auth via Cognito or Lambda Authorizer. Static assets on S3 + CloudFront. CI/CD via SAM / Serverless Framework / CDK. Observability: X-Ray + CloudWatch.

### Q15. Pricing — how do you cut AWS bill?
- Right-size (Compute Optimizer)
- **Savings Plans** (1 or 3 yr commit) or RIs — up to ~70% off
- **Spot** for batch/CI runners — up to 90% off
- Graviton (ARM) instances — ~20% cheaper, often faster
- S3 lifecycle to Glacier
- Delete unattached EBS volumes, old snapshots, idle load balancers, unused EIPs
- VPC endpoints (cut NAT GW egress charges — these surprise everyone)
- Reserved Capacity for DynamoDB/RDS
- Cost Anomaly Detection + Budgets alerts

### Q16. CloudFormation vs CDK vs Terraform on AWS?
- **CloudFormation** — native YAML/JSON; supports every service first; verbose.
- **CDK** — write in TS/Python/Java; compiles to CFN; great for DRY.
- **Terraform** — multi-cloud, huge community, state file you manage (S3+DynamoDB lock).

Pick Terraform for multi-cloud teams; CDK if AWS-only and devs prefer code.

### Q17. Cross-account access — how?
**AssumeRole**. Account B's role trusts Account A's principal; A calls `sts:AssumeRole`, gets short-lived creds for B. Used for centralized CI/CD, security audit accounts (often via AWS **Organizations** + **Control Tower** + **IAM Identity Center**).

### Q18. How do you secure the AWS root account?
MFA (hardware key ideally), no access keys, lock it away, use IAM Identity Center for daily access. Enable CloudTrail org-wide. Use SCPs at the org level to prevent root use.

### Q19. CloudWatch vs CloudTrail vs Config?
- **CloudWatch** — metrics, logs, alarms (operational telemetry)
- **CloudTrail** — API audit log (who did what, when)
- **AWS Config** — resource configuration history + compliance rules

### Q20. Multi-region active-active — what's the AWS playbook?
Route 53 latency-based or geolocation routing → regional ALBs → regional auto-scaling apps → Aurora Global Database (or DynamoDB Global Tables) → S3 Cross-Region Replication → SQS/SNS regional with replication via EventBridge global endpoints. Test failover quarterly.

---

### Follow-up topics
- Lambda cold starts, provisioned concurrency, Lambda SnapStart
- ECS vs EKS vs Fargate
- Step Functions for workflow orchestration
- AWS WAF + Shield Advanced
- Organizations, SCPs, AWS Control Tower
- Backup, AWS Resilience Hub
