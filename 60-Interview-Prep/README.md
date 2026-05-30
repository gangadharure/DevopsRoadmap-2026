# 60 — Interview Prep

Real questions that get asked in cloud / DevOps / SRE interviews in 2026 — grouped by topic. Each file has the **question + a short model answer + follow-ups** an interviewer will dig into.

| # | Topic | File |
|---|-------|------|
| 1 | Linux | [01-Linux.md](./01-Linux.md) |
| 2 | Docker & Kubernetes | [02-Docker-Kubernetes.md](./02-Docker-Kubernetes.md) |
| 3 | CI/CD | [03-CI-CD.md](./03-CI-CD.md) |
| 4 | AWS | [04-AWS.md](./04-AWS.md) |
| 5 | Azure | [05-Azure.md](./05-Azure.md) |
| 6 | Terraform / IaC | [06-Terraform.md](./06-Terraform.md) |
| 7 | Scenario-based ("how would you…") | [07-Scenario-Based.md](./07-Scenario-Based.md) |
| 8 | System Design (cloud-native) | [08-System-Design.md](./08-System-Design.md) |

---

## How to use

1. **Read the question first.** Try answering out loud (or write it down) before reading the answer.
2. **Then check the model answer.** Note what you missed.
3. **Practise the follow-ups** — interviewers always probe deeper.
4. **Speak the answer.** Knowing it ≠ being able to explain it cleanly under pressure.

> **STAR rule for experience questions:** Situation → Task → Action → Result. Always quantify the Result (latency cut by 40%, deploy time reduced from 1 hr → 5 min, etc).

---

## Mock-interview formats interviewers actually use

| Round | What they test |
|-------|----------------|
| Phone screen (30-45 min) | Linux + one cloud + one CI/CD tool basics |
| Technical (60-90 min) | Deep dive on K8s, IaC, scripting; live troubleshooting |
| Coding (45-60 min) | Bash/Python script: parse log, hit API, retry with backoff |
| System design (45-60 min) | Design a deploy pipeline / observability stack / multi-region service |
| Behavioural (45 min) | Conflict, incident, ownership, on-call experience |
| Hiring manager (30 min) | Why this role, career goals, comp |

---

## ✅ Day-before checklist

- [ ] Re-read the job description; note 3 keywords they emphasise (Kubernetes? Terraform? GitOps? AWS?). Have **one strong story per keyword**.
- [ ] Have 2 production incidents ready (one you fixed, one you learned from).
- [ ] Know your most recent project end-to-end — every line of YAML/HCL you can defend.
- [ ] 3 thoughtful questions to ask the interviewer.
- [ ] `kubectl` cheatsheet open in a tab for systems-design rounds.
