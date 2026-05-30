# 01 — Linux for Cloud & DevOps

> Linux is the **#1 hidden prerequisite** for every cloud and DevOps role.
> Over **90% of cloud workloads run on Linux** — and every cert (CKA, SAA, AZ-104) drops you into a terminal.

**Time:** 4–6 weeks · 1 hour/day
**Outcome:** Comfortable in any Linux terminal · ready for any cloud cert

---

## 🤔 Why Linux first?

- **Containers ARE Linux.** Docker, Kubernetes, ECS, AKS — all Linux kernels under the hood.
- **Cloud servers default to Linux.** Ubuntu, Amazon Linux, RHEL, Debian.
- **Every cert is hands-on.** CKA, AZ-104, RHCSA — you'll be in a terminal.
- **DevOps tools are Linux-first.** Ansible, Terraform, Jenkins, GitHub Actions runners, Prometheus.
- **Interviews test it.** Permissions, processes, networking, systemd, logs.

> **Don't skip this step.** Spend 4–6 weeks here and every future cert becomes 2× easier.

---

## 🛠️ Setup (Day 1)

Pick ONE — all are free:

| Platform | Setup | When to choose |
|----------|-------|----------------|
| **WSL2 + Ubuntu** | `wsl --install` on Windows 11 | Easiest if on Windows |
| **AWS EC2 t3.micro** | Free tier, Amazon Linux 2023 | Practice real cloud Linux |
| **Azure B1s** | Free tier, Ubuntu 24.04 | If you'll do AZ-104 |
| **VirtualBox + Ubuntu** | Local VM, snapshots | Safe to break things |
| **macOS Terminal** | Already installed | BSD ≠ Linux, but close |

**Recommendation:** WSL2 + Ubuntu 24.04 LTS for daily learning, plus a free EC2 micro for real-cloud practice.

---

## 📅 6-Week Roadmap (1 hour/day)

### Week 1 — Survive the Terminal

| Topic | Commands |
|-------|----------|
| Navigation | `pwd`, `ls -la`, `cd`, `tree` |
| Files | `cat`, `less`, `head`, `tail`, `wc` |
| Search | `find`, `locate`, `which`, `whereis` |
| Manipulation | `mkdir`, `mv`, `cp`, `rm`, `ln -s` |
| Help | `man`, `--help`, `info`, `apropos` |

**Exercise:** Recreate `/etc` structure inside `~/practice/` using only `mkdir -p` and `touch`.

---

### Week 2 — Filesystem, Permissions, Users

| Topic | Commands |
|-------|----------|
| Filesystem hierarchy | `/etc /var /home /tmp /usr /opt /proc /sys` |
| Permissions | `chmod 755`, `chown`, octal vs symbolic, `umask` |
| Users & groups | `useradd`, `usermod`, `passwd`, `id`, `groups`, `su`, `sudo` |
| ACLs | `getfacl`, `setfacl` |
| Disk usage | `df -h`, `du -sh`, `lsblk`, `mount`, `fdisk -l` |

**Exercise:** Create user `deploy`, make `/srv/app` group-writable by `webteam` only, prove with `ls -la`.

---

### Week 3 — Processes, Services, Logs

| Topic | Commands |
|-------|----------|
| Processes | `ps -ef`, `pstree`, `top`, `htop`, `kill -9`, `nice`, `nohup`, `&` |
| Services | `systemctl status/start/stop/enable`, `systemctl list-units` |
| Unit files | `/etc/systemd/system/*.service`, `daemon-reload` |
| Logs | `journalctl -u <svc>`, `journalctl -f`, `/var/log/`, `dmesg` |
| Scheduling | `cron`, `crontab -e`, `systemd timers`, `at` |

**Exercise:** Write a systemd unit that runs a Python script as user `app`, restarts on failure, logs to journal.

---

### Week 4 — Networking

| Topic | Commands |
|-------|----------|
| Interfaces | `ip a`, `ip link`, `ip route` |
| Connectivity | `ping`, `traceroute`, `mtr`, `dig`, `nslookup` |
| Sockets | `ss -tulpn`, `lsof -i`, `netstat -tnlp` |
| HTTP | `curl -v`, `wget`, `httpie` |
| Firewall | `ufw`, `firewalld`, `iptables` basics |
| SSH | `ssh-keygen`, `~/.ssh/config`, `ssh -L` (port forward) |

**Exercise:** From your laptop, SSH into a free EC2 with a key-only login, port-forward Postgres 5432 securely.

---

### Week 5 — Bash Scripting & Text-Fu

| Topic | Commands |
|-------|----------|
| Bash basics | variables, `$( )`, `if/elif/else`, `for`, `while`, functions |
| Quoting | `'single'` vs `"double"` vs `$'ANSI'` |
| Pipes & redirects | `\|`, `>`, `>>`, `2>&1`, `tee` |
| Text-fu | `grep -E`, `sed -i`, `awk '{print $2}'`, `cut`, `sort -u`, `xargs` |
| Error handling | `set -euo pipefail`, exit codes, `trap` |
| Debug | `bash -x script.sh` |

**Exercise:** Write a script that tails `/var/log/syslog` for `ERROR`, counts per hour, and emails a daily report.

---

### Week 6 — Package Mgmt, SSH at Scale, Backups

| Topic | Commands |
|-------|----------|
| APT (Debian/Ubuntu) | `apt update/upgrade/install/search/show/remove` |
| DNF/YUM (RHEL/Amazon) | `dnf install/update/search/info` |
| Snap / Flatpak | `snap install`, `flatpak install` |
| SSH at scale | `~/.ssh/config` aliases, `ssh-copy-id`, jump hosts |
| Sync & backup | `rsync -avhP`, `tar -czvf`, `gzip`/`xz`, `dd` |
| Compression | When to use `gz` vs `xz` vs `zstd` |

**Exercise:** Rsync-backup a directory to S3 (`aws s3 sync`) on a cron schedule, compressed and timestamped.

---

## 📚 Official Resources (free)

| Resource | What it covers |
|----------|----------------|
| **[Linux Foundation — Intro to Linux (LFS101)](https://training.linuxfoundation.org/training/introduction-to-linux/)** | Free on edX, the gold standard intro |
| **`man <command>`** | Built-in. Read at least one per day. |
| **[kernel.org/doc](https://www.kernel.org/doc/)** | The source of truth |
| **[The Linux Documentation Project (TLDP)](https://tldp.org/)** | Free, deep guides |
| **[Bash Reference Manual (GNU)](https://www.gnu.org/software/bash/manual/)** | Official shell docs |
| **[Arch Wiki](https://wiki.archlinux.org/)** | Distro-agnostic, often more useful than Ubuntu wiki |

---

## 🧪 Hands-On Projects

1. **Static site with Nginx** — install Nginx, serve HTML, enable systemd, set permissions correctly.
2. **Hardened SSH server** — disable root login, key-only, fail2ban, custom port.
3. **Log rotator** — bash script that rotates `/var/log/myapp.log` daily, keeps 7 days, compresses old ones.
4. **System health report** — cron job that emails CPU/disk/memory daily.
5. **One-script LAMP installer** — idempotent bash script that installs Apache + MySQL + PHP on a fresh Ubuntu.

---

## 🎯 You're ready for cloud certs when…

- ✅ You can navigate the FS and edit files without thinking
- ✅ `chmod`, `chown`, `sudo` are reflexive
- ✅ You can write a 30-line bash script with error handling
- ✅ `systemctl` and `journalctl` feel natural
- ✅ You can SSH into a remote box, debug a failing service, and fix it

---

## ❓ "Do I need RHCSA or Linux+?"

**No — not to start.**

- For **DevOps / Cloud roles** → Linux **skill** is required, but the **cert** is optional.
- If you want to specialize as a **Linux SysAdmin** → then yes, **RHCSA** is the gold standard.
- For most people: be **fluent**, then chase cloud + Kubernetes certs.

---

## ▶️ Next stop

After 6 weeks here, pick one:
- **[AWS Path](../../40-Cloud-Certification-Paths/01-AWS-Path)** if you're choosing AWS
- **[Azure Path](../../40-Cloud-Certification-Paths/02-Azure-Path)** if you're choosing Azure
- **[Kubernetes (CKA)](../../10-Containers-Orchestration/02-Kubernetes-CKA)** if containers/platform is your path
