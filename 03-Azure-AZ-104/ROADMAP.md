# 03 — Microsoft Azure Administrator Associate (AZ-104)

> Implement, manage, and monitor an organization's entire Microsoft Azure environment.
> The most-respected Azure associate-level certification.

**Time:** 10–12 weeks · 1–2 hours/day
**Exam:** AZ-104 · 100 min · MCQ + interactive lab tasks · **700/1000 to pass** · **$165 USD** · **12-month renewal (free)**
**Prereq:** Linux fluency ([`01-Linux`](../01-Linux)) · familiarity with PowerShell, Azure CLI, the Azure portal, ARM/Bicep, Microsoft Entra ID

📘 Official: [AZ-104 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-104) · [Cert page](https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator/)

---

## 🎯 Skills Measured (official, as of April 17, 2026)

| Domain | Weight |
|--------|--------|
| 1. Manage Azure identities and governance | **20–25%** |
| 2. Implement and manage storage | **15–20%** |
| 3. Deploy and manage Azure compute resources | **20–25%** |
| 4. Implement and manage virtual networking | **15–20%** |
| 5. Monitor and maintain Azure resources | **10–15%** |

---

## 📅 12-Week Roadmap

### Phase 1 — Foundations (Weeks 1–2)

- Azure global infrastructure: regions, availability zones, paired regions
- Subscriptions, management groups, resource groups, tags
- The 3 main interfaces: Azure portal, Azure CLI, Azure PowerShell, plus Cloud Shell
- ARM templates vs **Bicep** (modern preferred)
- Microsoft Entra ID basics (formerly Azure AD)

**Hands-on:** Create a free Azure account → deploy a resource group with 3 tags → deploy one storage account via portal, CLI, and Bicep.

---

### Phase 2 — Identity & Governance (Weeks 3–4)

- Entra users, groups, dynamic groups, external (guest) users
- Licenses & **Self-Service Password Reset (SSPR)**
- Azure RBAC: built-in roles, custom roles, scope (mg/sub/RG/resource), interpreting effective access
- **Azure Policy** (built-in & custom), **resource locks**, tags
- Cost management: budgets, alerts, **Azure Advisor**

**Hands-on:** Create users + a group, assign Contributor at RG scope, attach a Policy denying public IPs in production.

---

### Phase 3 — Storage (Week 5)

- Storage account types & redundancy (LRS, ZRS, GRS, RA-GRS, GZRS)
- **Blob storage** — tiers (Hot/Cool/Cold/Archive), lifecycle mgmt, soft delete, versioning, object replication
- **Azure Files** — SMB/NFS shares, identity-based access, snapshots
- **SAS tokens** (user delegation vs service), stored access policies, access keys, storage firewalls, private endpoints
- Tools: **Storage Explorer**, **AzCopy**

**Hands-on:** Create a blob container with lifecycle (Hot→Cool 30d→Archive 180d), upload with `azcopy`, generate a time-bound SAS.

---

### Phase 4 — Compute (Weeks 6–7)

- **VMs:** sizes, generations, availability sets vs zones, **VM Scale Sets** (uniform vs flexible), encryption at host, disk types (Standard HDD/SSD, Premium SSD, Ultra)
- Move VMs between RG/sub/region
- **Containers:** Azure Container Registry (ACR), Azure Container Instances (ACI), Azure Container Apps
- **App Service:** plans (tiers), scaling, custom domains + TLS, deployment slots, backups, VNet integration
- IaC: deploy compute via **Bicep / ARM**

**Hands-on:** Deploy a VMSS with auto-scale rules; deploy a container to Container Apps; deploy an App Service with a staging slot and swap.

---

### Phase 5 — Networking (Weeks 8–9)

- VNets, subnets, **VNet peering** (regional & global), public IPs, **user-defined routes**
- **NSGs** vs **ASGs**, effective security rules
- **Azure Bastion** (RDP/SSH without public IPs), **service endpoints** vs **private endpoints**
- **Azure DNS** (public + private zones), **Load Balancer** (basic vs standard, internal vs public)
- Troubleshooting: **Network Watcher**, Connection Monitor, NSG flow logs

**Hands-on:** Hub-and-spoke VNet topology with Bastion in the hub; private endpoint to a storage account; internal load balancer for a 2-VM web set.

---

### Phase 6 — Monitor & Backup (Week 10)

- **Azure Monitor** — metrics, **Log Analytics workspaces**, KQL basics, alerts (action groups, alert processing rules)
- **Monitor Insights** for VMs, storage, networks
- **Recovery Services vault** vs **Backup vault**
- Backup policies, restore operations
- **Azure Site Recovery** — failover to secondary region

**Hands-on:** Send VM logs to Log Analytics, write a KQL query for failed logins, configure a Recovery vault backup on a VM and run a test restore.

---

### Phase 7 — Exam Prep (Weeks 11–12)

- Take the **official Microsoft Practice Assessment** (free) until you score 85%+
- Try the **Exam Sandbox** to learn the UI of interactive lab questions
- Re-read the **AZ-104 Study Guide**
- Practice the 4 most common lab task types: user/role creation, VNet peering, Policy assignment, VM deploy + disk attach
- Sit two timed practice exams

---

## 📚 Official Resources

| Resource | Use |
|----------|-----|
| **[AZ-104 Study Guide (aka.ms/AZ104-StudyGuide)](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-104)** | THE source of truth |
| **[Microsoft Learn — AZ-104 collection](https://learn.microsoft.com/en-us/training/courses/az-104t00/)** | 6 free learning paths, official |
| **[Practice Assessment (free)](https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator/practice/assessment?assessment-type=practice&assessmentId=21&practice-assessment-type=certification)** | Official practice questions |
| **[Exam Sandbox](https://go.microsoft.com/fwlink/?linkid=2226877)** | Try the exam UI |
| **[Microsoft Learn docs](https://learn.microsoft.com/en-us/azure/)** | The full Azure documentation |
| **[Exam Readiness Zone — AZ-104 videos](https://learn.microsoft.com/en-us/shows/exam-readiness-zone/preparing-for-az-104-manage-azure-identities-and-governance-1-of-5/)** | Microsoft's official video prep |

---

## 🧪 Capstone Projects

1. **Hub-and-spoke enterprise topology** — hub VNet + Bastion + 2 spoke VNets peered + UDR + NSGs.
2. **Backup & DR** — VM in region A backed up to a Recovery vault; ASR replication to region B; perform a test failover.
3. **Storage data lake** — lifecycle policies moving raw → cool → archive automatically; private endpoint only.
4. **Identity hardening** — SSPR + Conditional Access + named admin roles + emergency access account.
5. **IaC pipeline** — Bicep file deploying VNet + VMSS + App Service from a GitHub Actions workflow.

---

## 🧠 Interview Topics

- "Difference between Azure Policy, RBAC, and resource locks?"
- "Service endpoint vs private endpoint?"
- "When would you use VMSS vs App Service vs Container Apps?"
- "Explain Azure storage redundancy options."
- "How do you secure access to Azure Files for on-prem users?"
- "Walk me through a hub-and-spoke design."
- "Recovery vault vs Backup vault — when to use each?"
- "What's the difference between Microsoft Entra ID and on-prem AD?"

---

## ▶️ Next stop

- Add Kubernetes on Azure → [`04-CKA-Kubernetes`](../04-CKA-Kubernetes) (concepts transfer to AKS)
- Specialize → AZ-305 (Solutions Architect Expert), AZ-500 (Security)
- Full DevOps → [`05-DevOps`](../05-DevOps)
