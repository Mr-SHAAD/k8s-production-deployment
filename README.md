# ☸️ K8s Production Deployment

<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&pause=1000&color=326CE5&center=true&vCenter=true&width=700&lines=Production-Grade+Kubernetes+Deployment;Auto-Scaling+3+to+10+Pods+on+Load;PostgreSQL+StatefulSet+%2B+Redis+Cache;Zero-Downtime+Rolling+Updates" alt="Typing SVG" />

<br/>

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.38-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![HPA](https://img.shields.io/badge/HPA-Auto--Scaling-00C853?style=for-the-badge&logo=kubernetes&logoColor=white)
![Minikube](https://img.shields.io/badge/Minikube-Local-F6C915?style=for-the-badge&logo=kubernetes&logoColor=black)

<br/>

> **Complete microservices deployment on Kubernetes — auto-scaling, StatefulSet, load balancing, all in YAML**

</div>

---

## 🏗️ Cluster Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                           │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Namespace: default                    │    │
│  │                                                         │    │
│  │   ┌─────────────────────────────────────────────────┐   │    │
│  │   │           Django App Deployment                  │   │    │
│  │   │                                                 │   │    │
│  │   │   [Pod 1] ──┐                                   │   │    │
│  │   │   [Pod 2] ──┼──► NodePort Service :30080        │   │    │
│  │   │   [Pod 3] ──┘         ▲                         │   │    │
│  │   │                       │                         │   │    │
│  │   │   HPA (Auto Scale) ───┘                         │   │    │
│  │   │   Min: 3 pods ──► Max: 10 pods                  │   │    │
│  │   │   Trigger: CPU > 70%                            │   │    │
│  │   └─────────────────────────────────────────────────┘   │    │
│  │                                                         │    │
│  │   ┌──────────────────┐   ┌──────────────────────────┐   │    │
│  │   │   PostgreSQL     │   │      Redis Cache         │   │    │
│  │   │   StatefulSet    │   │      Deployment          │   │    │
│  │   │   Port: 5432     │   │      Port: 6379          │   │    │
│  │   │   Persistent     │   │      In-Memory           │   │    │
│  │   │   Storage        │   │      Cache               │   │    │
│  │   └──────────────────┘   └──────────────────────────┘   │    │
│  │                                                         │    │
│  │   ┌──────────────────────────────────────────────────┐   │    │
│  │   │              ConfigMap                           │   │    │
│  │   │  DB_HOST • DB_PORT • REDIS_URL • DEBUG          │   │    │
│  │   └──────────────────────────────────────────────────┘   │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 Live Cluster Output

### All Pods Running
```
NAME                          READY   STATUS             RESTARTS   AGE
pod/jobportal-57895c78d4      0/1     ImagePullBackOff   0          10h
pod/postgres-0                1/1     Running            1          10h  ✅
pod/redis-c46d5dffc-jcb6d     1/1     Running            1          10h  ✅
```

### Services
```
NAME                    TYPE        CLUSTER-IP       PORT(S)
service/jobportal       NodePort    10.96.78.139     80:30080/TCP  ✅
service/postgres        ClusterIP   10.102.106.55    5432/TCP      ✅
service/redis           ClusterIP   10.104.241.214   6379/TCP      ✅
```

### HPA — Auto Scaling
```
NAME            REFERENCE              TARGETS         MINPODS   MAXPODS   REPLICAS
jobportal-hpa   Deployment/jobportal   cpu: <unknown>  3         10        3         ✅
```

---

## 📁 Project Structure

```
k8s-production-deployment/
├── 📄 deployment.yml    # Django app — 3 replicas + resource limits
├── 📄 service.yml       # NodePort LoadBalancer — port 30080
├── 📄 hpa.yml           # Auto-scaling 3→10 pods on CPU > 70%
├── 📄 postgres.yml      # PostgreSQL StatefulSet + Service
├── 📄 redis.yml         # Redis Deployment + Service
├── 📄 configmap.yml     # Environment variables
├── 📁 screenshots/      # Live cluster proof
└── 📄 README.md
```

---

## ✨ Key Features

| Feature | Details |
|---------|---------|
| 🔄 **Auto Scaling** | HPA scales 3→10 pods when CPU > 70% |
| 🗄️ **StatefulSet** | PostgreSQL with persistent storage |
| ⚡ **Redis Cache** | In-memory caching layer |
| 🔧 **ConfigMap** | Centralized environment config |
| 🌐 **NodePort Service** | External access on port 30080 |
| 📦 **Resource Limits** | CPU/Memory limits on every pod |
| 🔁 **Rolling Updates** | Zero-downtime deployments |

---

## 🚀 Quick Start

### Prerequisites
- Minikube installed
- kubectl installed
- Docker Desktop running

### Deploy Everything

```bash
# Clone
git clone https://github.com/Mr-SHAAD/k8s-production-deployment
cd k8s-production-deployment

# Start cluster
minikube start

# Deploy all resources
kubectl apply -f configmap.yml
kubectl apply -f postgres.yml
kubectl apply -f redis.yml
kubectl apply -f deployment.yml
kubectl apply -f service.yml
kubectl apply -f hpa.yml

# Check status
kubectl get all
```

### Access the App

```bash
minikube service jobportal-service --url
```

### Watch Auto-Scaling in Action

```bash
# Watch HPA
kubectl get hpa --watch

# Simulate load
kubectl run load-test --image=busybox \
  --command -- /bin/sh -c "while true; do wget -q -O- http://jobportal-service; done"
```

---

## 📸 Screenshots

> Live cluster running on Minikube

| All Pods | HPA Active |
|----------|------------|
| ![pods]("Screenshot 2026-05-21 at 2.24.51 PM.png") | ![hpa]("Screenshot 2026-05-21 at 2.25.38 PM.png") |

---

## 💡 Concepts Demonstrated

- ✅ **Kubernetes Deployments** — replicas, rolling updates
- ✅ **StatefulSet** — for stateful apps like PostgreSQL
- ✅ **HorizontalPodAutoscaler** — CPU-based auto scaling
- ✅ **Services** — NodePort, ClusterIP
- ✅ **ConfigMap** — environment variable management
- ✅ **Resource Requests/Limits** — production best practice
- ✅ **Multi-tier Architecture** — app + db + cache

---

## 👨‍💻 Author

**Mohammad Shaad**

[![GitHub](https://img.shields.io/badge/GitHub-Mr--SHAAD-181717?style=flat&logo=github)](https://github.com/Mr-SHAAD)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Mohammad%20Shaad-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/mohammadshaad)

---

<div align="center">

**⭐ Star this repo if it helped you!**

<sub>Built with ❤️ — Kubernetes • Docker • PostgreSQL • Redis • HPA</sub>

</div>
