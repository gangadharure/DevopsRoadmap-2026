# 04 — Certified Kubernetes Administrator (CKA)

> Prove you can install, configure, and run a production-grade Kubernetes cluster.
> The most-respected Kubernetes credential — issued by **The Linux Foundation & CNCF**.

**Time:** 10–12 weeks · 1–2 hours/day
**Exam:** 100% hands-on, live terminal · 2 hours · **66%+ to pass** · **$445 USD** (1 free retake) · aligned with **Kubernetes v1.34** · **2-year validity** · **2 free exam-sim attempts** included
**Prereq:** Linux fluency ([`01-Linux`](../01-Linux)) · Docker basics

📘 Official: [CNCF CKA page](https://www.cncf.io/training/certification/cka/) · [LF Training](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)

---

## 🎯 Exam Domains & Weighting

| Domain | Weight |
|--------|--------|
| 1. Troubleshooting | **30%** |
| 2. Cluster Architecture, Installation & Configuration | **25%** |
| 3. Services & Networking | **20%** |
| 4. Workloads & Scheduling | **15%** |
| 5. Storage | **10%** |

> **Troubleshooting is 30% — drill it.**

---

## 📅 12-Week Roadmap

### Phase 1 — Foundations (Weeks 1–2)

- Containers — Docker basics, images, layers, registries
- Why orchestration? Why Kubernetes?
- Kubernetes architecture: control plane (kube-apiserver, etcd, scheduler, controller-manager), nodes (kubelet, kube-proxy, container runtime)
- Install **kubectl**, set up a local cluster with **minikube** or **kind**
- The `kubeconfig` file & contexts

**Hands-on:** Spin up a kind cluster, deploy nginx, expose it on NodePort, inspect everything with kubectl.

---

### Phase 2 — Core Objects (Weeks 3–4)

- **Pods** — lifecycle, init containers, multi-container patterns
- **Deployments**, **ReplicaSets**, rolling updates & rollbacks
- **StatefulSets**, **DaemonSets**, **Jobs** & **CronJobs**
- **ConfigMaps**, **Secrets** (and their limits)
- Requests & limits, QoS classes
- Namespaces & labels/selectors

**Hands-on:** Deploy a 3-replica app, do a rolling update, roll back; mount a ConfigMap + Secret into the pods.

---

### Phase 3 — Services & Networking (Weeks 5–6)

- **Service types:** ClusterIP, NodePort, LoadBalancer, ExternalName, Headless
- **Endpoints** & EndpointSlices
- **Ingress** controllers (Nginx, etc.), TLS termination, path/host routing
- **CoreDNS** & cluster DNS rules
- **NetworkPolicy** — default deny, ingress/egress rules
- CNI overview (Calico, Cilium, Flannel)

**Hands-on:** Deploy two apps, restrict communication with a NetworkPolicy, expose one publicly via Ingress with TLS.

---

### Phase 4 — Storage (Week 7)

- Volumes vs PersistentVolumes vs PersistentVolumeClaims
- **StorageClass** & dynamic provisioning
- **CSI** drivers (concept)
- Access modes (RWO/ROX/RWX/RWOP), reclaim policies
- StatefulSets + PVC templates

**Hands-on:** Spin up a Postgres StatefulSet with a dynamic PVC, snapshot/restore, scale.

---

### Phase 5 — Cluster Install, Config & Security (Weeks 8–9)

- Cluster install with **kubeadm**: init, join, control plane HA
- **etcd** backup & restore (this WILL be on the exam)
- Cluster upgrade: kubeadm upgrade flow
- **RBAC**: Roles, ClusterRoles, RoleBindings, ServiceAccounts
- TLS & certificates (kubeadm rotates these — know where they live)
- **Helm** + **Kustomize** basics

**Hands-on:** Build a 3-node kubeadm cluster from scratch on 3 VMs, then upgrade it to the next minor version. Backup etcd, simulate disaster, restore.

---

### Phase 6 — Workloads & Scheduling (Week 10)

- The scheduler: how a pod gets a node
- **NodeSelector**, **Affinity/Anti-Affinity**, **Taints & Tolerations**
- **Pod Disruption Budgets**, **Priority Classes** & preemption
- Horizontal Pod Autoscaler (HPA), Vertical (VPA concept), Cluster Autoscaler
- **Probes**: liveness, readiness, startup

**Hands-on:** Spread an app across 3 zones using anti-affinity; configure HPA on CPU.

---

### Phase 7 — Troubleshooting (Week 11) ⭐ THE 30%

| Layer | Tools |
|-------|-------|
| App | `kubectl logs -f --previous`, `kubectl describe`, `kubectl exec -it pod -- sh` |
| Service | check selectors match labels, `kubectl get endpoints` |
| DNS | `kubectl run -it --rm dnstest --image=busybox -- nslookup …` |
| Node | `journalctl -u kubelet`, `crictl ps`, `df -h`, `top` |
| Control plane | `/etc/kubernetes/manifests/*.yaml`, kube-apiserver logs |
| etcd | `etcdctl endpoint health`, backup/restore |
| Networking | `kubectl get networkpolicy`, CNI logs |
| RBAC | `kubectl auth can-i …` |

**Hands-on:** Use [killercoda free scenarios](https://killercoda.com/playgrounds/scenario/kubernetes) and the **2 free Killer.sh** attempts that ship with your exam registration to drill broken-cluster scenarios.

---

### Phase 8 — Exam Prep (Week 12)

- Read the **official curriculum PDF** end-to-end
- Memorize **kubectl shortcuts** (alias `k=kubectl`, `--dry-run=client -o yaml`)
- Vim/tmux basics — both available in the exam terminal
- Use only **kubernetes.io/docs** (the ONLY allowed in-exam reference)
- Take both free **Killer.sh** simulator attempts (closest thing to the real exam)
- Schedule with a 4-hour buffer on either side; eat well, hydrate

---

## 📚 Official Resources

| Resource | Use |
|----------|-----|
| **[CKA Curriculum (Linux Foundation, GitHub)](https://github.com/cncf/curriculum)** | The official skills list |
| **[kubernetes.io/docs](https://kubernetes.io/docs/)** | ONLY allowed reference during the exam — master its search |
| **[Kubernetes the Hard Way (Kelsey Hightower)](https://github.com/kelseyhightower/kubernetes-the-hard-way)** | Best deep-dive into cluster internals (free, official-grade) |
| **[CNCF Slack](https://slack.cncf.io)** | Official community |
| **Killer.sh simulator** | 2 free attempts included with exam — use both |

---

## 🧪 Capstone Projects

1. **3-node kubeadm cluster** on Ubuntu VMs (control plane + 2 workers) with Calico CNI.
2. **etcd backup + restore drill** — simulate a control-plane disaster.
3. **TLS-terminated Ingress** with cert-manager + Let's Encrypt staging.
4. **Stateful app** — Postgres StatefulSet with dynamic PVCs, backup CronJob.
5. **Cluster upgrade** — kubeadm upgrade from v1.33 → v1.34 with zero downtime to a 3-replica nginx.

---

## 🧠 Interview Topics

- "Walk me through what happens when you `kubectl apply` a Deployment."
- "How does the scheduler decide where a pod goes?"
- "ClusterIP vs NodePort vs LoadBalancer vs Ingress?"
- "ConfigMap vs Secret — and why is a Secret only base64?"
- "Walk me through etcd backup and restore."
- "How would you debug a pod stuck in CrashLoopBackOff?"
- "What's a NetworkPolicy default-deny posture and how do you set it?"
- "Explain RBAC: Role vs ClusterRole vs Binding."

---

## ▶️ Next stop

- Layer on platform tools → [`05-DevOps`](../05-DevOps)
- Specialize → CKS (Security), CKAD (Developer)
- Add a managed K8s cloud → EKS (AWS), AKS (Azure), GKE (GCP)
