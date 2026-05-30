# 01 — Docker

> Containers are the unit of deployment in 2026. Master Docker first, then Kubernetes makes 10× more sense.

**Time:** 2 weeks · 1 hour/day
**Prereqs:** Linux ([`00-Foundations/01-Linux`](../../00-Foundations/01-Linux))

---

## 🎯 What you actually need

| # | Skill |
|---|-------|
| 1 | Run, build, push, pull images |
| 2 | Write a clean, multi-stage Dockerfile |
| 3 | Network containers; volumes for state |
| 4 | `docker compose` for multi-container local dev |
| 5 | Image security: minimal base, non-root, scan, sign |
| 6 | Registries: Docker Hub, GHCR, ECR, ACR |

---

## 📅 2-Week Roadmap

### Week 1 — Container basics

**Day 1 — Concepts & install**
- Container vs VM
- Image = layered filesystem · Container = running instance
- Install **Docker Desktop** (Win/Mac) or **Docker Engine** (Linux)
- Alternative engines: **Podman** (daemonless, rootless) — API-compatible

**Day 2 — Run containers**
```bash
docker run --rm -it ubuntu:24.04 bash
docker run -d -p 8080:80 --name web nginx
docker ps / ps -a / logs / exec -it web sh / stop / rm
docker pull / images / rmi
```

**Day 3 — Write a Dockerfile (Python example)**
```dockerfile
FROM python:3.12-slim AS base
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
USER 1000:1000
EXPOSE 8000
HEALTHCHECK --interval=30s CMD curl -f http://localhost:8000/health || exit 1
CMD ["python", "-m", "uvicorn", "app:app", "--host", "0.0.0.0"]
```

**Day 4 — Layers, cache, .dockerignore**
- Order layers from least → most changing
- `.dockerignore` (mirror your `.gitignore` + add tests/, docs/)
- `docker build --no-cache` for clean builds
- `docker history image` to inspect layers

**Day 5 — Multi-stage builds (huge wins)**
```dockerfile
FROM golang:1.22 AS build
WORKDIR /src
COPY . .
RUN CGO_ENABLED=0 go build -o /app .

FROM gcr.io/distroless/static-debian12
COPY --from=build /app /app
USER nonroot:nonroot
ENTRYPOINT ["/app"]
```
→ runtime image: ~5–20 MB · no shell · no CVEs from build tools

**Day 6 — Networking & volumes**
```bash
docker network create app-net
docker run -d --network app-net --name db postgres:16
docker run -d --network app-net -e DB_HOST=db --name api myapi
docker volume create pgdata
docker run -d -v pgdata:/var/lib/postgresql/data postgres:16
```

**Day 7 — docker compose**
- `compose.yaml` for multi-container local dev
- `depends_on`, healthchecks, profiles, env_file
- `docker compose up -d / logs -f / down -v`

---

### Week 2 — Production & security

**Day 8 — Image security**
- **Base image:** prefer `distroless` or `chainguard` or `alpine` (small + fewer CVEs)
- **Run as non-root** (`USER 1000:1000`) — non-negotiable
- **Pin digests:** `FROM python:3.12-slim@sha256:abc…` (not just `:latest`)
- Drop unused tools, no `curl`/`bash` in runtime image
- Read-only root filesystem at runtime (`--read-only`)

**Day 9 — Scan images**
- **`trivy image myimg:tag`** — the de-facto open-source scanner
- Alternatives: Grype, Snyk
- Fail builds on CVSS ≥ 7 in CI

**Day 10 — Sign images (supply chain)**
- **Cosign** (Sigstore) — keyless signing with OIDC
- `cosign sign ghcr.io/me/img:tag`
- `cosign verify ghcr.io/me/img:tag --certificate-identity ...`
- SBOM with **Syft**: `syft myimg:tag -o spdx-json`

**Day 11 — Registries**
- Public: Docker Hub, **GHCR (GitHub Container Registry)** — free for public
- Cloud: **ECR** (AWS), **ACR** (Azure), **Artifact Registry** (GCP)
- Login: `docker login ghcr.io -u USER -p $TOKEN`
- Push: `docker push ghcr.io/USER/repo:tag`
- Immutable tags / retention policies

**Day 12 — Runtime hardening**
- `--read-only`, `--cap-drop ALL --cap-add NET_BIND_SERVICE`
- `--security-opt no-new-privileges`
- Limit resources: `--cpus 1 --memory 512m --pids-limit 100`
- Don't mount `/var/run/docker.sock` into containers (privilege escalation)

**Day 13 — Build in CI**
- **Docker Buildx** for multi-arch (`linux/amd64,linux/arm64`)
- BuildKit cache import/export (GHA cache, registry cache)
- GitHub Action: `docker/build-push-action@v6`

**Day 14 — When to use what**
| Need | Pick |
|------|------|
| Local dev with DB | `docker compose` |
| Production runtime | Kubernetes |
| Quick CI tooling | Docker run |
| Edge / single host | Docker compose + Watchtower or Portainer |
| Rootless | Podman |

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[docs.docker.com](https://docs.docker.com/)** | Official Docker docs |
| **[Dockerfile reference](https://docs.docker.com/engine/reference/builder/)** | Every instruction explained |
| **[Compose spec](https://compose-spec.io/)** | Vendor-neutral spec |
| **[OCI Image Spec](https://github.com/opencontainers/image-spec)** | What an image really is |
| **[Trivy docs](https://aquasecurity.github.io/trivy/)** | Scanner docs |
| **[Sigstore / Cosign docs](https://docs.sigstore.dev/)** | Signing |
| **[Distroless images](https://github.com/GoogleContainerTools/distroless)** | Tiny secure base images |
| **[Chainguard images](https://www.chainguard.dev/chainguard-images)** | Modern hardened base images |

---

## 🧪 Hands-On Projects

1. Containerize a Flask/FastAPI app with multi-stage build → final image < 80 MB.
2. Compose stack: app + Postgres + Redis + nginx reverse proxy → `docker compose up`.
3. Build, scan with Trivy, sign with Cosign, push to GHCR via GitHub Actions.
4. Multi-arch image (amd64 + arm64) with Buildx, pushed to GHCR.
5. Migrate the same app to Podman rootless to learn the daemonless model.

---

## 🧠 Interview Questions

- "Container vs VM — explain to a junior."
- "Multi-stage build — why?"
- "How do you reduce image size?"
- "How do you keep secrets out of images?"
- "Why run as non-root?"
- "How do you trust an image you pulled?"
- "Difference between `CMD` and `ENTRYPOINT`?"
- "What is `docker compose` good for, and what's it NOT good for?"

---

## ▶️ Next stop

[`10-Containers-Orchestration/02-Kubernetes-CKA`](../02-Kubernetes-CKA) → orchestration at scale.
