# 04 — Scripting for DevOps (Bash + Python)

> Two scripting languages will cover **99% of DevOps automation work**: **Bash** for the system, **Python** for everything else.

**Time:** 3–4 weeks · 1 hour/day
**Outcome:** Write production-grade scripts confidently — with error handling, logging, tests, and proper exit codes.

---

## 🎯 The honest take

- **Bash** — universal on every Linux box. Use for: glue, build steps, CI scripts, simple automation.
- **Python** — when Bash gets ugly (>50 lines, JSON, APIs, data manipulation, complex logic).
- **Go** — bonus: most cloud-native tools (Kubernetes, Terraform, Docker) are written in Go. Worth learning *after* you're solid in Python.

> Pick **Bash + Python first**. Don't chase Go until you ship Python automations daily.

---

## 📅 Roadmap

### Week 1 — Bash basics

**Day 1 — Setup & basics**
```bash
#!/usr/bin/env bash
set -euo pipefail        # ALWAYS start with this
IFS=$'\n\t'
echo "hello $USER"
```

- Shebangs, file permissions (`chmod +x`)
- `set -e` exit on error, `-u` undefined vars, `-o pipefail`, `-x` debug

**Day 2 — Variables & quoting**
- `var=value` (no spaces), `${var}`, `${var:-default}`, `${var:?required}`
- `'single'` vs `"double"` vs backticks (`don't use`) vs `$(modern)`
- Arrays: `arr=(a b c)`, `${arr[@]}`

**Day 3 — Control flow**
```bash
if [[ -f file ]]; then ...; fi
for f in *.log; do echo "$f"; done
while read -r line; do ...; done < file.txt
case "$1" in start) ...;; stop) ...;; esac
```

**Day 4 — Functions & arguments**
```bash
log() { echo "[$(date +%FT%T)] $*"; }
deploy() {
  local env="${1:?env required}"
  log "deploying to $env"
}
deploy "$@"
```

**Day 5 — Pipes, redirects, text-fu**
- `|`, `>`, `>>`, `2>&1`, `tee`, `xargs`
- `grep -E`, `sed -i 's/x/y/g'`, `awk '{print $2}'`, `cut`, `sort -u`, `uniq -c`, `jq` (for JSON)

**Day 6 — Error handling & traps**
```bash
trap 'echo "ERROR on line $LINENO"; cleanup' ERR
trap 'cleanup' EXIT
```

**Day 7 — Real script**
- Write a backup script: tar a folder → compress → upload to S3/Blob → keep last 7 days → log to journal.

---

### Week 2 — Bash for DevOps (real-world)

**Day 8 — CLI patterns**
- Long opts via `getopts` or manual parsing
- Help text + usage function
- Subcommands pattern (`mytool deploy / mytool rollback`)

**Day 9 — Working with cloud CLIs**
- `aws s3 sync ...`, `az storage ...`, `gcloud ...`, `kubectl ...`
- Parsing CLI output with `jq` / `yq`
- Idempotent operations (check before create)

**Day 10 — Testing Bash**
- **`bats-core`** — the de-facto Bash test framework
- `shellcheck` — must-run linter (install + integrate in editor + CI)
- `shfmt` — auto-formatter

**Day 11 — When NOT to use Bash**
- Anything > 100 lines → switch to Python
- Complex data (nested JSON/YAML) → Python
- HTTP APIs with auth → Python
- Cross-platform → Python

---

### Week 3 — Python for DevOps

**Day 12 — Python 3.12 setup**
- `pyenv` (or system Python ≥ 3.11)
- `uv` (modern, super-fast) or `pip` + `venv`
- `ruff` (lint + format), `pytest` (tests), `mypy` (types)

**Day 13 — Language essentials**
```python
from pathlib import Path
for p in Path("/var/log").rglob("*.log"):
    print(p, p.stat().st_size)
```
- f-strings, list/dict comprehensions, `enumerate`, `zip`
- `pathlib` (modern file paths)
- `argparse` for CLIs (or **`typer`** / **`click`** for nicer)

**Day 14 — Shell-out & subprocesses**
```python
import subprocess
r = subprocess.run(["kubectl", "get", "pods", "-o", "json"],
                   capture_output=True, text=True, check=True)
import json
pods = json.loads(r.stdout)
```

**Day 15 — HTTP & APIs**
- `httpx` (modern) or `requests`
- Retries + timeouts + backoff (`tenacity`)
- Async basics (`asyncio`, `httpx.AsyncClient`) — for fan-out

**Day 16 — Cloud SDKs**
- AWS: **`boto3`** — list S3, manage EC2, IAM
- Azure: **`azure-sdk-for-python`** — Resource Mgmt, Storage, Identity
- Kubernetes: **`kubernetes`** Python client

**Day 17 — Data handling**
- `pyyaml` for YAML
- `json` stdlib + **`pydantic`** for validation
- CSV with `csv` module
- Working with templates (`jinja2`)

**Day 18 — Logging & errors**
```python
import logging
logging.basicConfig(level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s")
log = logging.getLogger(__name__)
```
- Structured logging with `structlog`
- Custom exceptions, proper exit codes (`sys.exit(2)` for arg errors)

---

### Week 4 — Tests, packaging, shipping

**Day 19 — Testing**
- `pytest` basics, fixtures, parametrize
- Mocking subprocess and HTTP (`pytest-mock`, `respx`)

**Day 20 — Packaging**
- `pyproject.toml` (modern, PEP 621)
- Console entry points → installs as a real CLI
- `uv build` / `python -m build`
- Distribute via PyPI or GitHub Release

**Day 21 — Containerize your tool**
- Multi-stage Dockerfile (build → distroless runtime)
- Ship as a container, not a script

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[GNU Bash Reference Manual](https://www.gnu.org/software/bash/manual/)** | THE Bash spec |
| **[ShellCheck](https://www.shellcheck.net/)** | Online + CLI linter — non-negotiable |
| **[BashGuide (mywiki.wooledge.org)](https://mywiki.wooledge.org/BashGuide)** | Best free Bash guide |
| **[`bats-core` docs](https://bats-core.readthedocs.io/)** | Bash testing |
| **[Python docs](https://docs.python.org/3/)** | Official, deep, free |
| **[Real Python](https://realpython.com/)** | Practical articles, free tier |
| **[boto3 docs](https://boto3.amazonaws.com/v1/documentation/api/latest/)** | AWS Python SDK |
| **[Kubernetes Python client](https://github.com/kubernetes-client/python)** | Official K8s SDK |
| **[Pydantic docs](https://docs.pydantic.dev/)** | Data validation |
| **[typer.tiangolo.com](https://typer.tiangolo.com/)** | Modern Python CLIs |

---

## 🧪 Hands-On Mini-Projects

1. **Bash:** backup-and-rotate script (mentioned above).
2. **Bash:** `health-check.sh` that pings 20 internal services and emails a daily Slack report.
3. **Python:** CLI tool `ec2-janitor` that finds unused EBS volumes and snapshots > 90 days and prints/deletes them.
4. **Python:** `k8s-image-audit` that lists every image running in every namespace, with its CVE count from Trivy.
5. **Python:** `cost-explorer` that pulls AWS billing data via boto3 and prints top-10 cost centers month-over-month.

---

## � Copy-paste templates you'll actually reuse

### Bash — safe script skeleton
```bash
#!/usr/bin/env bash
# Purpose: <what this does>
# Usage:   ./script.sh [-v] <arg>
set -Eeuo pipefail
IFS=$'\n\t'

readonly SCRIPT_NAME="$(basename "$0")"
readonly LOG_FILE="/var/log/${SCRIPT_NAME%.sh}.log"

log()  { printf '[%s] %s\n' "$(date -Iseconds)" "$*" | tee -a "$LOG_FILE" >&2; }
die()  { log "FATAL: $*"; exit 1; }
trap 'die "line $LINENO exited with $?"' ERR
trap 'log "interrupted"; exit 130' INT TERM

main() {
  [[ $# -ge 1 ]] || die "usage: $SCRIPT_NAME <arg>"
  log "starting with arg=$1"
  # ... work here ...
  log "done"
}
main "$@"
```

### Bash — log parser (top error sources)
```bash
# Find top 10 error messages from nginx access log in last hour
awk -v t="$(date -d '1 hour ago' '+%d/%b/%Y:%H')" '
  $4 > "["t && $9 ~ /^5/ { print $7, $9 }
' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10
```

### Bash — S3 cleanup (objects older than 30 days)
```bash
#!/usr/bin/env bash
set -Eeuo pipefail
BUCKET="my-bucket"
CUTOFF=$(date -d '30 days ago' +%s)

aws s3api list-objects-v2 --bucket "$BUCKET" \
  --query 'Contents[].[Key,LastModified]' --output text |
while read -r key modified; do
  ts=$(date -d "$modified" +%s)
  if (( ts < CUTOFF )); then
    echo "Deleting $key (modified $modified)"
    aws s3 rm "s3://$BUCKET/$key"
  fi
done
```

### Python — retry with exponential backoff
```python
import time, random, functools, logging

def retry(tries=5, base=1.0, cap=30.0, exceptions=(Exception,)):
    """Decorator: exponential backoff with jitter."""
    def deco(fn):
        @functools.wraps(fn)
        def wrap(*a, **kw):
            for attempt in range(1, tries + 1):
                try:
                    return fn(*a, **kw)
                except exceptions as e:
                    if attempt == tries:
                        raise
                    sleep = min(cap, base * 2 ** (attempt - 1)) + random.random()
                    logging.warning("attempt %d/%d failed: %s — retrying in %.1fs",
                                    attempt, tries, e, sleep)
                    time.sleep(sleep)
        return wrap
    return deco

@retry(tries=5, exceptions=(ConnectionError, TimeoutError))
def call_api():
    import requests
    return requests.get("https://api.example.com/health", timeout=5).json()
```

### Python — production CLI skeleton (argparse + logging)
```python
#!/usr/bin/env python3
"""ec2-janitor — find or delete unused EBS volumes."""
import argparse, logging, sys
import boto3

def parse_args():
    p = argparse.ArgumentParser(description=__doc__)
    p.add_argument("--region", default="us-east-1")
    p.add_argument("--delete", action="store_true", help="actually delete (default: dry-run)")
    p.add_argument("-v", "--verbose", action="count", default=0)
    return p.parse_args()

def main() -> int:
    args = parse_args()
    logging.basicConfig(
        level=logging.DEBUG if args.verbose else logging.INFO,
        format="%(asctime)s %(levelname)s %(message)s",
    )
    ec2 = boto3.client("ec2", region_name=args.region)
    vols = ec2.describe_volumes(Filters=[{"Name": "status", "Values": ["available"]}])["Volumes"]
    logging.info("Found %d unattached volumes", len(vols))
    for v in vols:
        logging.info("  %s  %dGB  created=%s", v["VolumeId"], v["Size"], v["CreateTime"])
        if args.delete:
            ec2.delete_volume(VolumeId=v["VolumeId"])
    return 0

if __name__ == "__main__":
    sys.exit(main())
```

### Python — k8s pod health checker
```python
from kubernetes import client, config

config.load_kube_config()  # or load_incluster_config() inside a pod
v1 = client.CoreV1Api()

unhealthy = []
for pod in v1.list_pod_for_all_namespaces(watch=False).items:
    for cs in (pod.status.container_statuses or []):
        if not cs.ready:
            reason = (cs.state.waiting or cs.state.terminated or cs.state.running).reason or "Unknown"
            unhealthy.append((pod.metadata.namespace, pod.metadata.name, cs.name, reason, cs.restart_count))

print(f"{'NAMESPACE':20} {'POD':40} {'CONTAINER':20} {'REASON':20} RESTARTS")
for ns, pod, c, reason, n in unhealthy:
    print(f"{ns:20} {pod:40} {c:20} {reason:20} {n}")
```

### Python — parallel API calls with `concurrent.futures`
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import requests

urls = [f"https://api.example.com/items/{i}" for i in range(1, 101)]

def fetch(url):
    r = requests.get(url, timeout=10)
    r.raise_for_status()
    return url, r.json()

with ThreadPoolExecutor(max_workers=20) as pool:
    futures = {pool.submit(fetch, u): u for u in urls}
    for fut in as_completed(futures):
        try:
            url, data = fut.result()
            print(f"OK {url}")
        except Exception as e:
            print(f"FAIL {futures[fut]}: {e}")
```

---

## �🧠 Interview Questions

- "Why `set -euo pipefail`?"
- "When do you switch from Bash to Python?"
- "How do you handle retries with backoff?"
- "What's the difference between `subprocess.run` and `subprocess.Popen`?"
- "How do you make a Python script a real CLI tool installable with pip?"
- "Walk me through a script you've shipped to production."

---

## ▶️ Next stop

[`10-Containers-Orchestration/01-Docker`](../../10-Containers-Orchestration/01-Docker) → containers.
