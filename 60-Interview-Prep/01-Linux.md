# 01 — Linux Interview Questions

> The interviewer's goal here: confirm you've actually used Linux as a daily driver, not just memorised commands.

---

### Q1. How do you check what's eating CPU on a Linux box?

**Answer:** `top` / `htop` first (look at `%CPU` column, sort with `P`). For deeper: `pidstat -u 1`, `ps -eo pid,pcpu,pmem,comm --sort=-pcpu | head`. For threads: `top -H -p <pid>`. For a flame graph: `perf record -F 99 -p <pid> -g -- sleep 30 && perf script | flamegraph.pl`.

**Follow-up:** "What if `top` shows 0% but the box feels slow?" → check **load average** (`uptime`); high load + low CPU = I/O wait (`iostat -x 1`, look at `%util` and `await`).

---

### Q2. What happens when you run `ls`?

**Answer:** Shell parses the command → looks up `ls` via `$PATH` → `fork()` + `execve()` → kernel loads the ELF binary, sets up memory, jumps to entry point → `ls` calls `getdents64()` syscall on current dir's inode → kernel returns directory entries → `ls` writes formatted output to stdout (FD 1) → process exits with status code, parent shell reaps it with `wait()`.

**Follow-up:** "Where does it find `ls`?" → `which ls` (`/usr/bin/ls`), determined by `PATH` search order.

---

### Q3. Difference between `>`, `>>`, `2>`, `&>`, `|`?

| Symbol | Meaning |
|--------|---------|
| `>` | Redirect stdout, **truncate** target |
| `>>` | Redirect stdout, **append** |
| `2>` | Redirect stderr |
| `2>&1` | Send stderr to wherever stdout is going |
| `&>` (bash) | Both stdout & stderr |
| `|` | Pipe stdout of left to stdin of right |
| `<` | Read stdin from file |

**Gotcha:** `cmd > out 2>&1` works, `cmd 2>&1 > out` does NOT (order matters — duplication happens at the time of redirection).

---

### Q4. Hard link vs soft (symbolic) link?

| | Hard link | Soft link |
|---|-----------|-----------|
| Same inode? | Yes | No |
| Cross filesystem? | No | Yes |
| Target deleted? | Still works | Becomes dangling |
| Directories? | No (usually) | Yes |
| Size | 0 extra bytes | Stores target path |

`ln file hardlink` vs `ln -s file softlink`.

---

### Q5. Linux file permissions — explain `rwxr-xr--`.

owner=rwx (7), group=r-x (5), others=r-- (4) → **754**. Plus 1 char at the start (`-` file, `d` dir, `l` symlink, `c` char device). Special bits: setuid (4xxx, e.g. `passwd`), setgid (2xxx), sticky (1xxx — e.g. `/tmp`, only owner can delete).

**Follow-up:** "How do you find all setuid files?" → `find / -perm -4000 -type f 2>/dev/null`.

---

### Q6. How do you find a file?

- By name: `find / -name "nginx.conf" 2>/dev/null`
- Modified in last 10 min: `find /var/log -mmin -10`
- Larger than 100 MB: `find / -type f -size +100M`
- Owned by a user: `find / -user gangadhar`
- Fast indexed: `locate nginx.conf` (after `updatedb`).
- Content search: `grep -rIn "ERROR" /var/log/`.

---

### Q7. Process is stuck — how do you kill it?

`kill <pid>` (SIGTERM 15, graceful). If unresponsive: `kill -9 <pid>` (SIGKILL, can't be trapped). For a name: `pkill -f nginx`. To find PID: `pgrep -f nginx` or `ps -ef | grep nginx`.

**Follow-up:** "Why might `kill -9` not work?" → process is in **uninterruptible sleep (D state)** waiting on hardware I/O — kernel won't kill it. Fix the underlying I/O (NFS mount, disk).

---

### Q8. What's a zombie process? Defunct process?

A process that has exited but its parent hasn't called `wait()` to reap its exit status. Shows as `Z` in `ps` with `<defunct>` next to it. Harmless individually, but leaks PID slots if there are many. Fix: kill or restart the **parent**, or fix the parent code to reap children.

---

### Q9. systemd basics — how do you make a service start on boot?

```bash
sudo systemctl enable nginx       # boot start
sudo systemctl start nginx        # start now
sudo systemctl status nginx       # check
sudo systemctl daemon-reload      # after editing a .service file
journalctl -u nginx -f --since "1 hour ago"
```

Custom service file at `/etc/systemd/system/myapp.service` with `[Unit]`, `[Service]` (`ExecStart`, `Restart=always`), `[Install]` (`WantedBy=multi-user.target`).

---

### Q10. Disk is full — what do you do?

```bash
df -h                                # which mount is full?
du -sh /* 2>/dev/null | sort -hr     # biggest top-level dirs
du -sh /var/* | sort -hr             # drill down
find / -size +1G -type f 2>/dev/null # huge files
journalctl --vacuum-size=200M        # trim systemd journal
docker system prune -af              # if docker is on the box
lsof | grep deleted                  # deleted but held-open files (restart the holder)
```

**Gotcha:** if `du` and `df` disagree → an open file was deleted but a process still holds it (`lsof | grep deleted`). Restart that process to free the space.

---

### Q11. Difference between `ext4`, `xfs`, `btrfs`, `zfs`?

| FS | Pros | Cons |
|----|------|------|
| **ext4** | Default everywhere, stable | No snapshots, max 1 EB |
| **xfs** | RHEL default, great for large files, online grow | Cannot shrink |
| **btrfs** | Snapshots, CoW, RAID | Less battle-tested |
| **zfs** | Snapshots, dedup, compression, RAIDZ | RAM-hungry, licensing on Linux |

---

### Q12. How do you check open ports on Linux?

```bash
ss -tlnp                # TCP listening, with process
ss -ulnp                # UDP listening
netstat -tlnp           # legacy alternative
lsof -i :8080           # who's using port 8080
nmap -p- localhost      # scan all ports
```

---

### Q13. Bash: difference between `$@` and `$*`?

Both expand to script arguments. **`"$@"`** expands each arg as a **separate quoted string** (`"$1" "$2" "$3"`). **`"$*"`** joins them into a **single string** separated by `IFS` (default space). Almost always use `"$@"`.

---

### Q14. What's in `/proc`?

A virtual filesystem exposing kernel & process state. Highlights:
- `/proc/cpuinfo`, `/proc/meminfo`, `/proc/loadavg`
- `/proc/<pid>/status`, `/proc/<pid>/fd/` (open file descriptors), `/proc/<pid>/limits`
- `/proc/sys/...` — tunables (e.g. `net.ipv4.ip_forward`); persist with `sysctl`/`/etc/sysctl.conf`.

---

### Q15. SSH troubleshooting — `Permission denied (publickey)`. What's your check order?

1. `ssh -vvv user@host` — read the verbose log
2. Right key offered? `ssh -i ~/.ssh/id_ed25519 ...`
3. Permissions: `~/.ssh` = 700, `authorized_keys` = 600, home dir not group-writable
4. Username correct? (ec2-user, ubuntu, azureuser…)
5. Server-side: `/var/log/auth.log` or `journalctl -u sshd`
6. `sshd_config` — `PubkeyAuthentication yes`, `PasswordAuthentication no`?

---

### Follow-up topics the interviewer will probe
- Signals & traps in bash scripts
- `cgroups` and `namespaces` (the basis of containers!)
- `iptables` / `nftables` chains
- LVM (PV → VG → LV) and resizing
- SELinux / AppArmor enforcing vs permissive
- Kernel modules: `lsmod`, `modprobe`, `dmesg`
