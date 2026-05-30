# 05 — Azure Interview Questions

---

### Q1. Azure hierarchy — explain.
**Tenant (Entra ID)** → **Management Groups** → **Subscriptions** → **Resource Groups** → **Resources**. Policies/RBAC inherit downward. Subscriptions are the billing + isolation boundary.

### Q2. Entra ID (formerly Azure AD) vs on-prem Active Directory?
On-prem AD is LDAP/Kerberos, domain-joined Windows. **Entra ID** is cloud identity for SaaS apps (OAuth2 / OIDC / SAML), no LDAP/Kerberos. Connect them with **Entra Connect** (sync) or **Entra Domain Services** (managed DC in cloud).

### Q3. Azure RBAC — how does assignment work?
**Scope** (mgmt group / sub / RG / resource) + **Role definition** (Reader, Contributor, custom) + **Principal** (user, group, service principal, managed identity). Most-permissive wins; deny assignments override allow.

### Q4. Service Principal vs Managed Identity?
- **Service Principal** — identity for app/script, you manage the secret/cert.
- **Managed Identity** — Azure-managed SP, no secret to rotate. **System-assigned** (lifecycle = resource) vs **User-assigned** (shared across resources). Always prefer Managed Identity.

### Q5. VM compute options?
- **VMs** — IaaS
- **VM Scale Sets (VMSS)** — auto-scale identical VMs
- **App Service** — managed web app PaaS (deploy code/container)
- **Container Apps** — serverless containers (KEDA-based scale-to-zero)
- **AKS** — managed Kubernetes
- **Functions** — serverless event-driven
- **Azure Container Instances (ACI)** — one-off container, no orchestrator

### Q6. Storage account types?
| Service | Use |
|---------|-----|
| **Blob** | Object store (hot/cool/cold/archive tiers) |
| **File** | SMB/NFS shares |
| **Queue** | Simple message queue |
| **Table** | NoSQL KV (legacy; prefer Cosmos DB Table API) |
| **Disk** | Managed disks for VMs |

Replication: **LRS** (3 copies in 1 DC), **ZRS** (3 AZs), **GRS** (paired region async), **GZRS** (ZRS + paired region).

### Q7. Public vs Private endpoint?
- **Service endpoint** — extends VNet identity to Azure service over the backbone (still public endpoint, just allowlists your VNet).
- **Private Endpoint** — NIC in your VNet with a private IP for the PaaS service. **Preferred** — true network isolation.

### Q8. Azure SQL options?
- **Azure SQL Database** — single DB or elastic pool, PaaS
- **SQL Managed Instance** — nearly full SQL Server features, ideal for lift-and-shift
- **SQL Server on VM** — full control, IaaS
Plus **Cosmos DB** (multi-model NoSQL with SLAs on latency/throughput/availability), **Postgres / MySQL Flexible Server**.

### Q9. AKS — what does Azure manage vs you?
Azure manages the **control plane** (free tier, or paid Uptime SLA). You manage **node pools** (or use AKS Automatic). Add-ons: AGIC (Ingress), Azure Monitor for containers, Azure CNI, Workload Identity, Azure Key Vault CSI driver.

### Q10. Workload Identity in AKS — how?
Pod's ServiceAccount is federated to an Entra ID app/managed identity. K8s issues a projected JWT; AKS exchanges it for an Azure AD token via OIDC. **No secrets in the pod.** Replaces the legacy AAD Pod Identity.

### Q11. Network Security Group (NSG) vs Azure Firewall?
- **NSG** — stateful L3/L4 rules at subnet or NIC level. Free.
- **Azure Firewall** — managed L4-L7 stateful firewall, FQDN filtering, threat intel, TLS inspection (premium). Costs per hour + per GB.
- **Application Gateway** — L7 web LB with WAF.

### Q12. How does VNet peering differ from VPN gateway?
- **Peering** — direct, low-latency, Azure backbone, intra-region or global. No encryption (already on private network). Cheap per GB.
- **VPN Gateway** — IPSec tunnel over public internet, for hybrid (on-prem ↔ Azure) or cross-tenant.
- **ExpressRoute** — dedicated private circuit through a provider, predictable bandwidth.

### Q13. Storage account access — how do you secure?
- **Disable Shared Key access**, use **Entra ID auth** for blob/queue/table
- **SAS tokens** (user-delegated SAS preferred — signed by Entra creds, not the account key)
- Private Endpoints + Storage Firewall (default deny)
- Customer-managed keys (CMK) in Key Vault
- Enable soft delete + versioning + immutable storage for ransomware resilience

### Q14. Azure Key Vault — what's in it?
**Secrets** (strings), **Keys** (cryptographic, can be HSM-backed), **Certificates** (with auto-rotation). Access via **Azure RBAC** (modern) or legacy access policies. Mount into AKS via CSI driver; pull into App Service via references; integrate with ARM/Bicep.

### Q15. ARM vs Bicep vs Terraform on Azure?
- **ARM templates** — native JSON, verbose
- **Bicep** — DSL that transpiles to ARM, much cleaner, same coverage day-one
- **Terraform** — multi-cloud, biggest community, state file. Some Azure features lag a release.

### Q16. Hub-and-spoke topology — why?
Central **hub VNet** holds shared services (firewall, DNS, ExpressRoute gateway, jump boxes). Workload **spoke VNets** peer to the hub. Benefits: centralised egress + inspection, simpler policy, fewer peerings (n vs n²). Standard enterprise landing zone pattern.

### Q17. How do you do a Zero-Downtime deployment on App Service?
**Deployment slots**. Deploy to `staging` slot, warm it up, run smoke tests, then **swap** with `production` — DNS/routes flip atomically. Auto-swap, slot-specific settings, traffic % for canary.

### Q18. Azure Monitor — what's in it?
- **Metrics** (numeric, time-series)
- **Logs** (Log Analytics workspace, Kusto/KQL queries)
- **Application Insights** (APM for apps)
- **Alerts** + **Action Groups** (email, webhook, function, ITSM)
- **Workbooks** & **Dashboards**
- **Container Insights**, **VM Insights**

### Q19. How do you write a Kusto (KQL) query for last hour's errors?
```kusto
AppExceptions
| where TimeGenerated > ago(1h)
| summarize count() by ProblemId, bin(TimeGenerated, 5m)
| order by count_ desc
```

### Q20. AZ-104 favorite: how do you grant a VM access to a Storage Account?
1. Enable **system-assigned managed identity** on the VM
2. Assign **Storage Blob Data Reader** role to that identity, scoped to the storage account
3. In code, use `DefaultAzureCredential()` from the Azure SDK — it picks up the MI automatically

No secrets, no keys. Done.

---

### Follow-up topics
- Azure Landing Zones (CAF)
- Front Door + CDN + Traffic Manager — when each
- Cosmos DB consistency levels (Strong → Eventual)
- Azure Policy + Initiatives for governance
- Defender for Cloud, Sentinel
- Azure DevOps Pipelines vs GitHub Actions on Azure
