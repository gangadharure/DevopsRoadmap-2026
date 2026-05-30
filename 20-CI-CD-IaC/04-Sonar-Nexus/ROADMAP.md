# 04 — SonarQube & Nexus / Artifactory

> Two enterprise pillars: **code quality (Sonar)** and **artifact storage (Nexus / Artifactory)**.
> If you work at any company with > 200 engineers, you'll touch both.

**Time:** 1 week · 1 hour/day

---

## Part A — SonarQube (Code Quality)

> Static analysis platform for code quality, security hotspots, code coverage, and tech-debt tracking. The de-facto enterprise tool.

### What it does
- **SAST** — bugs, vulnerabilities, security hotspots in 30+ languages
- **Code smells** — duplications, complexity, naming
- **Coverage** — ingests JaCoCo / coverage.py / lcov reports
- **Quality Gate** — pass/fail criteria block merges
- **History & trends** — see tech debt over time

### Editions
- **SonarQube Community Edition** — free, single-branch
- **Developer / Enterprise** — paid, multi-branch + PR decoration
- **SonarCloud** — SaaS, free for public repos
- Alternatives: **CodeQL** (GitHub), **Semgrep** (free + open-source), **Snyk Code**

### Quick start
```bash
docker run -d --name sonar -p 9000:9000 sonarqube:community
# scanner
docker run --rm -v $PWD:/usr/src sonarsource/sonar-scanner-cli \
  -Dsonar.projectKey=my-app \
  -Dsonar.host.url=http://host.docker.internal:9000 \
  -Dsonar.login=$SONAR_TOKEN
```

### CI integration (GitHub Actions)
```yaml
- uses: SonarSource/sonarqube-scan-action@v3
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
```

### Best practices
1. Set a **Quality Gate** with: 0 new bugs, 0 new vulnerabilities, coverage ≥ 80% on new code, duplications ≤ 3%
2. Block merge on quality gate failure (PR check)
3. Track only **new code** — don't sink the team trying to fix old debt
4. Audit security hotspots monthly
5. Backup the Postgres DB regularly

### Official resources
- **[docs.sonarsource.com/sonarqube](https://docs.sonarsource.com/sonarqube/)**
- **[Sonar Rules](https://rules.sonarsource.com/)** — every rule explained
- **[SonarCloud](https://sonarcloud.io)** — free for OSS

### Interview Qs
- "What's a Quality Gate?"
- "Difference between bug, vulnerability, code smell?"
- "How do you stop devs from gaming coverage?"
- "How do you integrate Sonar with PR workflow?"

---

## Part B — Nexus Repository / JFrog Artifactory (Artifact Mgmt)

> Universal artifact repository: stores **everything** your CI produces and consumes (Docker images, npm/PyPI/Maven packages, Helm charts, Terraform modules, OS packages, raw files).

### Why you need one
- **Caching proxy** of public repos (Docker Hub, PyPI, npm) → faster, available offline, survives upstream outages
- **Private artifacts** stored once, retained per policy
- **Promotion workflow** dev → stage → prod
- **CVE scanning** (paid editions)
- **License compliance**

### The two big players
| | **Nexus Repository OSS** | **JFrog Artifactory** |
|---|--------------------------|----------------------|
| Open-source | ✅ Free | ❌ Paid (Free OSS is limited) |
| Formats supported | ~15 | ~30+ |
| UI | Basic | Polished |
| Enterprise features | Nexus Pro (paid) | Native |
| Typical use | Mid-size shops | Large enterprise |

### Sonatype Nexus Repository OSS quick start
```bash
docker run -d -p 8081:8081 --name nexus sonatype/nexus3
# default admin password in /nexus-data/admin.password
```

Common repo types:
- `maven-central` (proxy) + `maven-releases` (hosted) + `maven-snapshots` (hosted) + `maven-public` (group)
- `docker-hub` (proxy) + `docker-internal` (hosted) + `docker-all` (group)
- `pypi-proxy`, `npm-proxy`, `helm-hosted`, `raw-hosted` for binaries

### Promotion pattern
```
dev-repo  →  stage-repo  →  prod-repo
   ↑              ↑              ↑
   CI builds      QA promotes    Release manager promotes
```

### CI consumption (pip example)
```bash
pip install --index-url https://nexus.company.com/repository/pypi-proxy/simple mypkg
```

### Best practices
1. Cache **everything public** through your proxy (resilience + speed)
2. Separate **snapshot vs release** repos
3. Cleanup policies — Docker images > 90 days untagged → delete
4. RBAC: dev = read snapshot/release, CI = write snapshot, release-mgr = write release
5. Backup blob store + database regularly
6. Run behind HTTPS with auth always

### Official resources
- **[Sonatype Nexus docs](https://help.sonatype.com/repomanager3)**
- **[JFrog Artifactory docs](https://jfrog.com/help/r/jfrog-artifactory-documentation)**
- **[OCI distribution spec](https://github.com/opencontainers/distribution-spec)** — the registry standard

### Interview Qs
- "Why not just push to Docker Hub / PyPI?"
- "Hosted vs proxy vs group repo?"
- "How do you handle retention on container images?"
- "Promotion strategy across environments?"

---

## 🧪 Hands-On Projects

1. Stand up SonarQube + scanner in CI → fail PR if coverage drops or bugs > 0.
2. Stand up Nexus → proxy Docker Hub, PyPI, npm → point CI at it → measure build-time delta.
3. Container image promotion: CI pushes to `docker-snapshots` → manual promotion to `docker-releases`.
4. Audit a repo against Sonar Quality Gate; track tech debt for 30 days.

---

## ▶️ Next stop

[`30-Observability/01-Prometheus-Grafana-Loki`](../../30-Observability/01-Prometheus-Grafana-Loki)
