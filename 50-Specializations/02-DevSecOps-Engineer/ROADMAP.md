# 06 — DevSecOps Engineer (6-Month Roadmap)

> Shift security **left** — into code, pipelines, containers, infrastructure, and supply chains.
> A DevOps engineer who treats security as a first-class concern, not an afterthought.

**Time:** 6 months · 1–2 hours/day
**Prereqs:** [`DevOps Engineer`](../../50-Specializations/01-DevOps-Engineer) or equivalent experience (Linux + cloud + CI/CD + containers + IaC)

---

## 🛡️ What is DevSecOps (in one line)?

> **"Build security into every stage of the SDLC — from `git commit` to production runtime — using automation and policy as code."**

---

## 🧭 The 8 pillars of DevSecOps

| # | Pillar | Example tools |
|---|--------|---------------|
| 1 | **SAST** — Static App Security Testing | Semgrep, SonarQube, CodeQL, Snyk Code |
| 2 | **SCA** — Software Composition Analysis (deps & licenses) | Trivy, Grype, OWASP Dep-Check, Snyk Open Source |
| 3 | **DAST** — Dynamic App Security Testing | OWASP ZAP, Burp Suite, Nuclei |
| 4 | **Container & Image Security** | Trivy, Grype, Clair, Cosign (signing) |
| 5 | **IaC Security** | Checkov, tfsec, Terrascan, OPA Conftest |
| 6 | **Secrets Management** | HashiCorp Vault, AWS/Azure Secrets, Sealed Secrets, gitleaks, trufflehog |
| 7 | **Runtime / Cloud Security (CNAPP/CSPM)** | Falco, Trivy K8s, Prowler, ScoutSuite, kube-bench, kube-hunter |
| 8 | **Supply Chain Security** | SBOM (Syft), SLSA, Sigstore (Cosign), in-toto, Dependency Review |

---

## 📅 6-Month Roadmap

### Month 1 — AppSec Foundations

- **OWASP Top 10** (latest) — XSS, SQLi, IDOR, SSRF, deserialization, etc.
- **OWASP API Top 10** + **OWASP LLM Top 10** (new in 2024)
- AuthN vs AuthZ; OAuth 2.0, OIDC, JWT pitfalls
- TLS basics, cert chains, mutual TLS
- Threat modeling: **STRIDE**, attack trees
- **Project:** Pick a deliberately vulnerable app (OWASP Juice Shop or DVWA) — exploit 10 vulns, then write the fix PRs.

---

### Month 2 — Code Security in the Pipeline (SAST + Secrets + SCA)

- **Pre-commit hooks:** gitleaks, trufflehog, ruff/eslint, conventional commits
- **SAST** in CI: Semgrep, CodeQL — fail builds on HIGH findings
- **SCA**: Trivy + OWASP Dep-Check — generate SBOM (CycloneDX/SPDX)
- License scanning, transitive dep risks
- Branch protection, signed commits (Sigstore gitsign or GPG)
- **Project:** Add a GitHub Actions pipeline that runs `gitleaks → semgrep → trivy fs` on every PR. Fail builds on HIGH; comment findings on the PR.

---

### Month 3 — Container & Image Security

- Dockerfile best practices: minimal base (distroless / chainguard / alpine), non-root, healthchecks, multi-stage, no `latest`
- **Image scanning:** Trivy, Grype — exit non-zero on CVSS ≥7
- **Image signing & verification** with **Cosign** + **Sigstore**
- **SBOM generation** with **Syft**; attach as image artifact
- **SLSA** levels & provenance attestation
- Registry security: ACR/ECR scan-on-push, immutable tags
- **Project:** Build a "secure pipeline": Dockerfile → `trivy image` → `cosign sign` → push to GHCR → admission policy that only allows cosign-verified images on the cluster.

---

### Month 4 — IaC & Cloud Security

- **IaC scanning:** Checkov, tfsec, Terrascan in CI on every Terraform PR
- **Policy as Code:** OPA + Conftest, Sentinel (Terraform Cloud), Kyverno (K8s)
- **Cloud posture (CSPM):** Prowler (AWS), ScoutSuite (multi-cloud), kube-bench (K8s CIS)
- AWS: IAM Access Analyzer, GuardDuty, Security Hub, Inspector, Config
- Azure: Defender for Cloud, Policy, Sentinel
- Secrets: HashiCorp Vault dynamic creds, AWS Secrets Manager + rotation, External Secrets Operator
- **Project:** Run Prowler/ScoutSuite against your sandbox cloud account; fix the top-20 findings; commit the fixes as Terraform changes.

---

### Month 5 — Runtime Security & K8s Hardening

- **CIS Kubernetes Benchmark** with `kube-bench`
- `kube-hunter` for cluster pen-testing
- **Pod Security Standards** (Restricted profile), drop capabilities, read-only root FS, non-root UID
- **NetworkPolicies** — default-deny + explicit allow
- **Admission control:** **Kyverno** or **Gatekeeper (OPA)** — block privileged pods, enforce image registries, require labels
- **Runtime detection:** **Falco** — detect shell-in-container, crypto miners, syscall anomalies
- mTLS via service mesh (Istio / Linkerd) — overview
- **Project:** Take your CKA lab cluster — apply CIS hardening, install Kyverno with 5 enforcement policies, install Falco with Slack alerts. Try to break in. Document findings.

---

### Month 6 — Supply Chain, Threat Hunting, Compliance, Incident Response

- **Supply chain frameworks:** **SLSA** (levels 1–4), **in-toto**, **CIS Software Supply Chain Security Guide**
- **Provenance & attestation** — Cosign attest + verify
- SIEM basics: ingest CloudTrail / Audit logs → query (Splunk, ELK, Wazuh, Loki)
- Threat hunting: MITRE ATT&CK framework
- **Compliance frameworks:** SOC 2, ISO 27001, PCI-DSS, HIPAA, GDPR (overview, not deep)
- **Incident response runbooks:** detect → contain → eradicate → recover → learn
- **Project:** Full DevSecOps reference repo (see below) — publish as a GitHub portfolio piece.

---

## 📚 Official Resources (free)

| Resource | What |
|----------|------|
| **[OWASP](https://owasp.org/)** | Top 10, ASVS, API Top 10, LLM Top 10 |
| **[CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)** | Hardening guides for Linux, K8s, AWS, Azure, Docker |
| **[NIST SP 800-218 (SSDF)](https://csrc.nist.gov/Projects/ssdf)** | Secure Software Development Framework |
| **[SLSA framework](https://slsa.dev/)** | Supply chain levels |
| **[Sigstore docs](https://docs.sigstore.dev/)** | Cosign, Rekor, Fulcio |
| **[MITRE ATT&CK](https://attack.mitre.org/)** | Threat technique knowledge base |
| **[CNCF Cloud Native Security Whitepaper](https://github.com/cncf/tag-security/tree/main/security-whitepaper)** | Free, official |
| **[Kubernetes Security docs](https://kubernetes.io/docs/concepts/security/)** | Official PSS, NetworkPolicy, RBAC |

---

## 🎓 Certifications (priority order)

1. **CKS** — Certified Kubernetes Security Specialist (after CKA)
2. **AWS Security Specialty** *or* **AZ-500** (Azure Security)
3. **CompTIA Security+** (broad foundation, optional)
4. **Practical Network Penetration Tester (PNPT)** or **OSCP** (only if going red-team adjacent)

---

## 🧪 Capstone — `devsecops-reference` GitHub repo

A single, publishable repo that includes:
- ✅ A demo app (Python or Go) with intentional & fixed vulns
- ✅ GitHub Actions: pre-commit, SAST, SCA, secrets scan, container scan, IaC scan, sign + SBOM
- ✅ Terraform for AWS/Azure with Checkov + OPA policies
- ✅ K8s manifests + Kyverno policies + Falco rules
- ✅ Runbooks/ folder: incident response, secret rotation, image-CVE response
- ✅ `THREAT_MODEL.md` using STRIDE
- ✅ `COMPLIANCE.md` mapping controls to SOC 2 / CIS

> One great repo demonstrates DevSecOps better than any cert.

---

## 🧠 Interview Topics

- "Walk me through your secure SDLC."
- "How do you prevent secrets from leaking via Git?"
- "SAST vs SCA vs DAST — when to use which?"
- "What's SLSA Level 3 and how would you achieve it?"
- "How do you sign and verify container images in your pipeline?"
- "Explain Kubernetes Pod Security Standards."
- "How would you detect a crypto miner running in a pod?"
- "Walk me through your incident response for a leaked AWS key."

---

## ▶️ Next stop

- Specialize in cloud security → AWS Security Specialty / AZ-500 / GCP PCSE
- Move toward security architecture → CISSP (after 5 yrs exp)
- Combine with AI → secure LLM apps & agent infrastructure ([`Agentic AI Engineer`](../../50-Specializations/03-Agentic-AI-Engineer))
