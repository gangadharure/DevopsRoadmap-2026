# 02 — Docker & Kubernetes Interview Questions

---

## 🐳 Docker

### Q1. What's the difference between a container and a VM?
A VM virtualises **hardware** (each VM has its own kernel via a hypervisor). A container virtualises the **OS** (shares the host kernel, isolated via Linux **namespaces** for view + **cgroups** for resources). Result: containers are MBs not GBs, start in ms not seconds, but all run the same kernel.

### Q2. What's inside a Docker image?
A stack of **read-only layers** (one per Dockerfile instruction) + a manifest + config JSON. Stored as content-addressed tarballs (sha256). At run-time a thin writable layer is added on top (copy-on-write). Layers are cached and shared across images.

### Q3. `COPY` vs `ADD`?
Both copy files in. `ADD` extra: auto-extracts local tar archives, can fetch URLs. **Best practice: prefer `COPY`** — `ADD`'s magic causes surprises. Use `curl` + `RUN` for URL fetches.

### Q4. `CMD` vs `ENTRYPOINT`?
- `ENTRYPOINT` = the executable (immutable from CLI unless `--entrypoint`)
- `CMD` = default args, easily overridden by `docker run image <args>`

Best practice: `ENTRYPOINT ["myapp"]` + `CMD ["--help"]`.

### Q5. How do you shrink an image?
1. **Multi-stage builds** — build in a fat image, copy artifacts into `scratch` or `distroless`/`alpine`.
2. Combine `RUN` steps + clean caches in the same layer: `RUN apt-get update && apt-get install -y x && rm -rf /var/lib/apt/lists/*`.
3. `.dockerignore` (drop `.git`, `node_modules`, tests).
4. Use specific base tags, not `latest`.
5. Tools: `dive`, `docker history`.

### Q6. Container can't reach the internet — debug?
`docker exec` in → `cat /etc/resolv.conf`, `nslookup google.com`, `curl -v https://...`, `ip route`. Check Docker daemon network mode (`bridge`, `host`, `none`, custom). Inspect: `docker network inspect bridge`. On host: `iptables -L -n -v -t nat | grep -i docker`.

### Q7. Difference between `docker volume`, bind mount, tmpfs?
| Type | Where | Use |
|------|-------|-----|
| Volume | Docker-managed area | DB data, prod persistence |
| Bind mount | Any host path | Dev: live-mount source code |
| tmpfs | RAM | Secrets, scratch |

### Q8. Image security best practices?
Non-root `USER`; pin base tags by digest; scan with `trivy` or `grype`; sign with `cosign`; minimal base (`distroless`/`scratch`); drop Linux caps; read-only root FS; no secrets baked in — use BuildKit secrets or runtime injection.

---

## ☸️ Kubernetes

### Q9. Control plane components?
- **kube-apiserver** — front door, all reads/writes
- **etcd** — KV store of cluster state
- **kube-scheduler** — picks node for new pods
- **kube-controller-manager** — runs the controllers (ReplicaSet, Node, etc) — reconcile loops
- **cloud-controller-manager** — cloud-specific (LBs, volumes)

Node components: **kubelet** (talks to API, runs pods), **kube-proxy** (Service iptables/IPVS), **container runtime** (containerd/CRI-O).

### Q10. Deployment vs StatefulSet vs DaemonSet?
- **Deployment** — stateless replicas, interchangeable (web app)
- **StatefulSet** — stable identity (`pod-0`, `pod-1`), stable storage (PVC per pod), ordered rollout (DB, Kafka)
- **DaemonSet** — one pod per node (log shipper, CNI, monitoring agent)
- Job/CronJob — run-to-completion

### Q11. Service types?
- **ClusterIP** — internal only (default)
- **NodePort** — exposed on every node `<NodeIP>:<30000-32767>`
- **LoadBalancer** — cloud LB in front
- **ExternalName** — DNS CNAME alias
- **Headless** (`clusterIP: None`) — DNS A records for each pod, used by StatefulSets

### Q12. How does a pod reach a Service?
Service has a virtual ClusterIP. `kube-proxy` programs iptables/IPVS rules on every node — DNAT from ClusterIP to one of the backing **endpoint** pod IPs. Endpoints object kept in sync by the endpoints controller based on selector match + pod readiness.

### Q13. Liveness vs Readiness vs Startup probes?
- **Liveness** — restart container if failing (deadlock detection)
- **Readiness** — remove pod from Service endpoints (don't send traffic) but DON'T restart
- **Startup** — for slow-starting apps; disables the other two until it passes

### Q14. Requests vs Limits?
- **Requests** — what the scheduler reserves; basis for pod placement
- **Limits** — hard ceiling; CPU = throttled, memory = OOMKilled

Best practice: set requests=limits for guaranteed QoS class on critical pods.

### Q15. Pod stuck in `Pending` — debug order?
1. `kubectl describe pod <p>` — read **Events** at the bottom
2. Common causes: no node fits requests (insufficient cpu/mem); taints without tolerations; PVC not bound; image pull secret missing; node selector/affinity unsatisfied
3. `kubectl get nodes -o wide` + `kubectl describe node` to verify capacity

### Q16. Pod stuck in `CrashLoopBackOff` — debug?
`kubectl logs <pod> --previous` (logs of the crashed instance), `kubectl describe pod` for exit code, check probes, check command/entrypoint, exec into a sidecar or run with `kubectl debug`.

### Q17. How does the rolling update work?
Deployment has `strategy: RollingUpdate` with `maxSurge` (extra pods allowed) and `maxUnavailable`. The Deployment controller creates a **new** ReplicaSet, scales it up while scaling the old one down, respecting those bounds. Rollback: `kubectl rollout undo deployment/x`.

### Q18. ConfigMap vs Secret?
Both are KV stores mounted into pods (as env vars or files). Secret is **base64-encoded** (not encrypted!) — for real encryption enable encryption-at-rest in etcd, use **External Secrets Operator** / **Vault** / cloud KMS for true secret management.

### Q19. RBAC basics?
- **Role** / **ClusterRole** — what verbs on what resources
- **RoleBinding** / **ClusterRoleBinding** — bind to a Subject (User, Group, ServiceAccount)
- ServiceAccount is the identity a pod runs as
- Check: `kubectl auth can-i list pods --as=system:serviceaccount:default:myapp`

### Q20. Ingress vs Service of type LoadBalancer?
- LoadBalancer Service: **one cloud LB per service** (L4 typically) — expensive at scale
- Ingress: **one** LB fronts an Ingress controller (nginx, Traefik, ALB controller) which does L7 host/path routing to many Services
- **Gateway API** is the modern successor to Ingress

### Q21. What is a Network Policy?
Pod-level firewall. By default all pod-to-pod traffic in K8s is allowed; a NetworkPolicy switches the namespace to **deny-by-default** for matched pods and you whitelist ingress/egress. Requires a CNI that enforces them (Calico, Cilium, etc).

### Q22. How does etcd backup/restore work?
```bash
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-$(date +%s).db \
  --endpoints=https://127.0.0.1:2379 --cacert=... --cert=... --key=...
# Restore (CKA classic):
ETCDCTL_API=3 etcdctl snapshot restore snap.db --data-dir /var/lib/etcd-new
# Then point kube-apiserver --etcd-data-dir at the new dir.
```

### Q23. Pod-to-pod networking — how?
Every pod gets its own IP from the CNI (Calico, Cilium, flannel). All pods can reach all pods without NAT (K8s requirement). CNI plugin sets up the veth pairs + routes/eBPF/overlay.

### Q24. What's a sidecar pattern? Example?
A helper container co-scheduled in the same pod. Shares network + (optionally) volumes. Examples: Envoy proxy in a service mesh, log shipper, OAuth2-proxy, config reloader. K8s 1.29+ has **native sidecar containers** (init containers with `restartPolicy: Always`).

### Q25. CKA-style: "schedule pod only on nodes with SSD"
Label nodes: `kubectl label node node1 disk=ssd`. Then in pod spec:
```yaml
spec:
  nodeSelector:
    disk: ssd
```
For richer logic use `affinity.nodeAffinity` with `requiredDuringSchedulingIgnoredDuringExecution`.

---

### Follow-up topics
- HPA / VPA / Cluster Autoscaler / Karpenter
- PodDisruptionBudgets
- Admission controllers (mutating/validating) + Kyverno / OPA-Gatekeeper
- Service mesh: Istio, Linkerd — when do you actually need one?
- Multi-tenancy patterns
- Operators & CRDs
