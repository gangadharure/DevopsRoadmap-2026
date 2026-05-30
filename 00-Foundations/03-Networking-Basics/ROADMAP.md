# 03 — Networking Basics for DevOps & Cloud

> Every cloud problem you'll debug eventually comes down to **networking**. Get the fundamentals right and the rest is easy.

**Time:** 1–2 weeks · 1 hour/day
**Outcome:** Comfortable troubleshooting connectivity in any cloud, K8s, or container environment.

---

## 🎯 What you actually need (the 80/20)

| # | Topic | Why it matters |
|---|-------|---------------|
| 1 | **TCP/IP model** | Foundation of everything |
| 2 | **IPv4 + CIDR + subnetting** | VPCs, subnets, NSGs, security groups |
| 3 | **DNS** | Failure cause #1 in every outage post-mortem |
| 4 | **HTTP/HTTPS + TLS** | APIs, load balancers, ingress |
| 5 | **NAT, gateways, routing** | Cloud network design |
| 6 | **Firewalls / SG / NSG / NetworkPolicy** | Security every day |
| 7 | **Linux network commands** | Day-to-day troubleshooting |

> You don't need CCNA. You need to **understand and debug** these 7 topics.

---

## 📅 2-Week Roadmap

### Week 1 — The fundamentals

**Day 1 — TCP/IP stack**
- 4-layer model (Link / Internet / Transport / Application)
- TCP (reliable, ordered, slow handshake) vs UDP (fast, unordered)
- 3-way handshake (SYN → SYN-ACK → ACK), connection teardown
- Common ports: 22 SSH, 53 DNS, 80 HTTP, 443 HTTPS, 3306 MySQL, 5432 Postgres, 6379 Redis, 27017 Mongo

**Day 2 — IPv4 + CIDR**
- IPv4 anatomy (32-bit, four octets)
- CIDR notation: `10.0.0.0/16` = 65,536 IPs · `/24` = 256 IPs · `/28` = 16 IPs
- Private ranges: `10/8`, `172.16/12`, `192.168/16`
- Subnetting cheat: every `+1` to mask = `/2` size
- IPv6 awareness — you'll see it but don't need to deeply subnet

**Day 3 — DNS (the #1 outage cause)**
- Record types: **A, AAAA, CNAME, MX, TXT, NS, SOA, PTR, SRV**
- Recursion vs iteration, root → TLD → authoritative
- TTL — when to lower it before a migration
- `dig +trace example.com`, `nslookup`, `host`
- `/etc/hosts`, `/etc/resolv.conf`, **systemd-resolved**

**Day 4 — HTTP/HTTPS + TLS**
- HTTP/1.1 vs HTTP/2 vs HTTP/3 (QUIC over UDP)
- Methods, status codes (know **2xx 3xx 4xx 5xx** cold)
- Headers, cookies, CORS
- TLS handshake (basic): ClientHello → ServerHello + cert → key exchange → session
- Cert chain, CA, SAN, wildcard certs
- mTLS basics
- Tools: `curl -v`, `openssl s_client -connect host:443`

**Day 5 — Routing, NAT, Gateways**
- Default gateway, route tables
- NAT (1:1, NAPT) — why private subnets need NAT Gateway to reach internet
- Internet Gateway vs NAT Gateway vs VPN Gateway vs Transit Gateway
- BGP overview (you won't run it, you'll see it)

**Day 6 — Firewalls & cloud-equivalent**
- Stateful vs stateless
- Linux: `ufw`, `firewalld`, `iptables` / `nftables`
- AWS Security Groups (stateful) vs NACLs (stateless)
- Azure NSGs (stateful)
- Kubernetes NetworkPolicy (default-allow → default-deny)

**Day 7 — Practice & wrap**
- Subnetting practice: design a VPC `10.50.0.0/16` with 3 public + 3 private /20 subnets across 3 AZs

---

### Week 2 — Linux networking commands & troubleshooting

**Day 8 — Modern Linux network commands (iproute2)**
```bash
ip a                       # interfaces (replaces ifconfig)
ip route                   # routing table (replaces route -n)
ip link set eth0 up/down
ss -tulpn                  # listening sockets (replaces netstat)
ss -t state established
```

**Day 9 — Connectivity testing**
```bash
ping -c 4 8.8.8.8          # ICMP reachable?
traceroute / mtr           # path + per-hop latency
curl -v https://x.com      # full HTTP debug
nc -zv host 443            # port open?
tcpdump -i eth0 port 443   # packet capture
```

**Day 10 — DNS troubleshooting**
```bash
dig example.com +short
dig +trace example.com
dig @8.8.8.8 example.com   # query specific resolver
host example.com
resolvectl status          # systemd-resolved view
```

**Day 11 — TLS troubleshooting**
```bash
openssl s_client -connect host:443 -servername host
openssl x509 -in cert.pem -text -noout
curl -vI https://host      # see cert + headers
```

**Day 12 — Cloud-specific patterns**
- AWS VPC: Internet GW + NAT GW + route table + SG architecture
- Azure VNet: peering + NSG + Bastion + private endpoints
- VPC peering vs Transit Gateway, VNet peering vs Virtual WAN
- Service endpoints vs Private endpoints (Azure) / VPC endpoints (AWS)

**Day 13 — Kubernetes networking**
- Pod-to-pod (CNI), Service (kube-proxy / IPVS / eBPF)
- ClusterIP / NodePort / LoadBalancer / Ingress
- CoreDNS basics
- NetworkPolicy default-deny

**Day 14 — Real troubleshooting drills**
1. Can I reach the DB from this pod? (`nslookup`, `nc -zv`, NetworkPolicy)
2. Service has no endpoints? (selector mismatch)
3. TLS expired? (`openssl s_client`)
4. Slow latency? (`mtr` + cloud route)
5. NAT exhausted? (cloud metric)

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[IETF RFCs](https://www.rfc-editor.org/)** | TCP (793), HTTP/1.1 (9110), DNS (1035) — the source |
| **[Cloudflare Learning Center](https://www.cloudflare.com/learning/)** | Plain-English networking basics, free |
| **[High Performance Browser Networking (free online)](https://hpbn.co/)** | Ilya Grigorik's classic book — free, official |
| **[Linux iproute2 docs (`man ip`)](https://man.archlinux.org/man/ip.8)** | Modern Linux network commands |
| **[AWS VPC docs](https://docs.aws.amazon.com/vpc/)** / **[Azure Virtual Network docs](https://learn.microsoft.com/en-us/azure/virtual-network/)** | Cloud-specific |
| **[Kubernetes Networking docs](https://kubernetes.io/docs/concepts/services-networking/)** | K8s view |

---

## 🧪 Hands-On Mini-Projects

1. Design a 3-AZ VPC on paper (CIDR, subnets, route tables), then build it in AWS/Azure.
2. Capture a TLS handshake with `tcpdump` and decode it.
3. Set up a self-hosted DNS resolver (Unbound/CoreDNS) and point your laptop at it.
4. Configure a Kubernetes NetworkPolicy default-deny + explicit-allow.
5. Diagnose 5 broken scenarios on a friend's box (without them telling you what's broken).

---

## 🧠 Interview Questions

- "Walk me through what happens when you type `https://x.com` in a browser."
- "TCP vs UDP — when each?"
- "Difference between Security Group and NACL?"
- "What's a NAT Gateway and why do private subnets need one?"
- "How does DNS resolve `app.svc.cluster.local` in Kubernetes?"
- "What's the difference between a service endpoint and a private endpoint?"
- "How would you debug a pod that can't reach the internet?"

---

## ▶️ Next stop

[`00-Foundations/04-Scripting`](../04-Scripting) → Bash + Python for DevOps.
