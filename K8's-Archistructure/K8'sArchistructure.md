
# 🚀What is Kubernetes? | Kubernetes Architecture Explained ☸️

Today, we explore **Kubernetes Architecture** — its components and how they work together.

---

## 🌐 Kubernetes High-Level Architecture

```
                      +-------------+
                      |   Client    |
                      | (kubectl)   |
                      +-------------+
                             |
                             v
                      +-------------+
                      | API Server  |
                      +-------------+
                             |
    +------------------------+------------------------+
    |                        |                        |
    v                        v                        v
+-----------+        +----------------+       +--------------+
| Scheduler |        | Controller Mgr |       |     etcd     |
+-----------+        +----------------+       +--------------+
                                                   (Stores
                                                cluster state)
```

- The **API Server** is the central access point to the cluster.
- **Scheduler** decides where to run Pods.
- **Controller Manager** ensures the cluster state matches the desired state.
- **etcd** is the cluster's key-value database.

---

## 🧱 Master (Control Plane) vs Worker Node

```
                        +------------------+
                        |  Control Plane   |
                        +------------------+
                        | API Server       |
                        | Scheduler        |
                        | Controller Mgr   |
                        | etcd             |
                        +------------------+

                               || communicates with ||

                        +------------------+
                        |   Worker Node    |
                        +------------------+
                        | Kubelet          |
                        | Kube Proxy       |
                        | Container Runtime|
                        +------------------+
```

- **Control Plane** makes all decisions.
- **Worker Nodes** run the application Pods.

---

## 📡 API Server – Entry Point

```
Client (kubectl)
      |
      v
+-------------+
| API Server  |
+-------------+
```

- Accepts REST requests (e.g., `kubectl apply -f ...`)
- Communicates with all components: Scheduler, Controllers, Kubelets, etcd

---

## 🧠 Scheduler – Pod Placement Decision Maker

```
+-------------+
|   Scheduler |
+-------------+
      |
      v
Decides the best node
based on:
- CPU/memory
- Taints & tolerations
- Node affinity
```

---

## 🔄 Controller Manager – Desired State Manager

```
+---------------------+
| Controller Manager  |
+---------------------+
| - Node Controller   |
| - ReplicaSet Ctrl   |
| - Job Controller    |
| - Endpoints Ctrl    |
+---------------------+
```

- Constantly watches cluster state
- Takes corrective actions if actual ≠ desired

---

## 🗃️ etcd – Cluster State Storage

```
+------- etcd -------+
| Key-Value database |
| Stores entire      |
| cluster config and |
| current state      |
+--------------------+
```

- Highly available & consistent
- Queried and updated by API server

---

## 👷 Kubelet – Node Agent

```
+---------- Worker Node ----------+
|                                 |
|  +--------+     +-------------+ |
|  | Kubelet| <--> | API Server | |
|  +--------+     +-------------+ |
|     |                             |
|     v                             |
|  Runs Pods using Container Runtime|
+----------------------------------+
```

- Talks to API server
- Makes sure containers (Pods) are running
- Reports node & pod status

---

## 🔌 Kube Proxy – Networking Layer

```
Pod A <--> Kube Proxy <--> Pod B
```

- Manages networking rules
- Forwards traffic inside the cluster
- Supports services (ClusterIP, NodePort, etc.)

---
### ➡️ Horizontal Flowchart: Control Plane → Worker Node

```
[Your System]
     │
     ▼
[kubectl CLI]
     │
     ▼
┌──────────────────────────────┐                                ┌────────────────────────────┐
│     Control Plane (Master)   │                                │       Worker Node          │
│ ───────────────────────────  │                                │ ─────────────────────────  │
│                              │                                │                            │
│ [API Server] <──────────────────────────────┐                 │                            │
│     │                                       │                 │                            │
│     ▼                                       │                 │                            │
│ [etcd (stores desired state)]              │                 │                            │
│     │                                       │                 │                            │
│     ▼                                       │                 │                            │
│ [Controller Manager]                        │                 │                            │
│     │                                       │                 │                            │
│     ▼                                       │                 │                            │
│ [Scheduler]                                 │                 │                            │
│     │                                       │                 │                            │
└─────┼───────────────────────────────────────┘                 │                            │
      │                                                         ▼                            ▼
      └──────────── Schedules Pod ───────────────────────────► [Kubelet] (on Worker Node)    │
                                                              │     │                       │
                                                              │     ▼                       │
                                                              │  Pull Image                │
                                                              │     │                       │
                                                              │     ▼                       │
                                                              │  Create Pod                │
                                                              │     ▼                       │
                                                              │  Start Container(s)        │
                                                              │     │                       │
                                                              │     ▼                       │
                                                              │  Run App + Health Checks   │
                                                              │     │                       │
                                                              └─────┼───────────────────────┘
                                                                    ▼
                                                        [Pod Status → API Server]
```

---
## ✅ Summary

- **kubectl** sends YAML to the API server.
- **API server** validates and saves it in etcd.
- **Controller Manager** watches and reacts to changes.
- **Scheduler** assigns Pods to suitable worker nodes.
- **Kubelet** on those nodes pulls the image and creates the pod.
- Application is started and monitored via health checks.

## 🔄 Summary Table

| Component           | Runs On         | Role                                                       |
|--------------------|------------------|------------------------------------------------------------|
| API Server         | Control Plane    | Entry point to cluster; handles REST requests              |
| Scheduler          | Control Plane    | Assigns Pods to Nodes based on resource requirements       |
| Controller Manager | Control Plane    | Maintains cluster's desired state                          |
| etcd               | Control Plane    | Key-value store for cluster state                          |
| Kubelet            | Worker Node      | Runs and monitors containers                               |
| Kube Proxy         | Worker Node      | Handles Pod-to-Pod networking                              |
| Container Runtime  | Worker Node      | Runs containers (e.g., containerd, Docker)                 |

---

✅ **Next Steps**:
- Try setting up a cluster using Minikube.
- Use `kubectl get componentstatuses` to view control plane health.
- Run a sample deployment and observe where it gets scheduled.

---
## 🧠 Authored by

**Chandrasekhar** – DevOps Engineer | Cloud & Container Enthusiast  
*Let the YAML do the talking. Let Kubernetes do the walking.*
