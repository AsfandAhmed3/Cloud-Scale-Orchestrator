# Assignment 2: Containerization and Deployment using Docker & Kubernetes

## Project Overview

This project demonstrates a containerized MERN application deployment using:

- Docker (Task 1)
- Docker Compose (Task 2)
- Kubernetes Deployments/Services (Task 3)
- Persistent storage with PV/PVC (Task 4)
- Horizontal Pod Autoscaling (Task 5)

Base application used: `bradtraversy/proshop-v2` (customized for assignment work).

---

## Tools and Technologies Used

### Core Stack
- Frontend: React (CRA)
- Backend: Node.js + Express
- Database: MongoDB

### DevOps / Platform
- Docker Desktop (Windows)
- Docker CLI + Docker Compose
- Kubernetes (Docker Desktop `kind` cluster)
- `kubectl`

---

## Application Architecture

### Services

1. **Frontend service**
   - Containerized static React build served by Nginx
   - Proxies `/api` and `/uploads` to backend service

2. **Backend service**
   - Node.js Express API
   - Connects to MongoDB

3. **Database service**
   - MongoDB container / Kubernetes deployment
   - Uses persistent volume claim in Kubernetes

### Service Communication

- Docker Compose:
  - `frontend -> backend -> mongo`
- Kubernetes:
  - `frontend Service -> backend Service -> mongo Service`

---

## Project Structure

```text
proshop-v2/
├── backend/
│   └── Dockerfile
├── frontend/
│   ├── Dockerfile
│   └── nginx.conf
├── docker-compose.yml
├── k8s/
│   ├── task3/
│   │   ├── backend-deployment.yaml
│   │   ├── backend-service.yaml
│   │   ├── frontend-deployment.yaml
│   │   ├── frontend-service.yaml
│   │   └── backend-secret.yaml
│   ├── task4/
│   │   ├── mongo-pv.yaml
│   │   ├── mongo-pvc.yaml
│   │   ├── mongo-deployment.yaml
│   │   ├── mongo-service.yaml
│   │   └── backend-secret.yaml
│   └── task5/
│       └── backend-hpa.yaml
├── TASK1.md
├── TASK2.md
├── TASK3.md
├── TASK4.md
├── TASK5.md
└── readme.md
```

---

## Task 1: Docker Build and Run Instructions

### Build backend image

```bash
docker build -f backend/Dockerfile -t proshop-backend:task1 .
```

### Run backend container

```bash
docker run --name proshop-backend-task1 --env-file .env -p 5001:5000 -d proshop-backend:task1
```

### Verify

```bash
docker ps
```

Open:
- `http://localhost:5001/api/products`

### Stop

```bash
docker stop proshop-backend-task1
docker rm proshop-backend-task1
```

---

## Task 2: Docker Compose Setup

### Start all services

```bash
docker compose up --build -d
```

### Check services

```bash
docker compose ps
```

### Verify

- Frontend: `http://localhost:3001`
- Backend: `http://localhost:5002/api/products`
- MongoDB mapped port: `27018`

### Stop services

```bash
docker compose down
```

---

## Task 3: Kubernetes Deployment Steps

### Prerequisites

- Enable Kubernetes in Docker Desktop
- Ensure context is set:

```bash
kubectl config get-contexts
kubectl config use-context docker-desktop
kubectl get nodes
```

### Apply task 3 manifests

```bash
kubectl apply -k k8s/task3
```

### Verify deployments and services

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Expected:
- `backend` replicas = 3
- `frontend` replicas = 3

---

## Task 4: Persistent Storage Configuration

### Apply PV/PVC + Mongo deployment

```bash
kubectl apply -k k8s/task4
kubectl rollout restart deployment/backend
```

### Verify storage

```bash
kubectl get pv,pvc
kubectl get pods -l app=mongo -o wide
kubectl describe pod -l app=mongo
```

Expected:
- PV `mongo-pv` = `Bound`
- PVC `mongo-pvc` = `Bound`
- Mongo mount path = `/data/db`

---

## Task 5: Scaling Configuration (HPA)

### Apply backend resource requests + HPA

```bash
kubectl apply -f k8s/task3/backend-deployment.yaml
kubectl apply -k k8s/task5
```

### Verify HPA

```bash
kubectl get hpa
kubectl describe hpa backend-hpa
kubectl top pods -l app=backend
```

Configured values:
- Minimum Pods: `2`
- Maximum Pods: `5`
- CPU Utilization Target: `70%`

### Metrics server note (local Docker Desktop)

If HPA shows `cpu: <unknown>`, install metrics-server:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Then patch args for local TLS compatibility:

```bash
kubectl -n kube-system patch deployment metrics-server --type=json --patch='[
  {"op":"replace","path":"/spec/template/spec/containers/0/args/2","value":"--kubelet-preferred-address-types=InternalIP,Hostname,ExternalIP"},
  {"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}
]'
kubectl -n kube-system rollout status deployment/metrics-server
```

---

## Screenshot Checklist (for Submission)

Take these screenshots and add them to your report/repo:

1. **Running Docker containers**
   - `docker ps` (Task 1 / Task 2)

2. **Running Kubernetes pods**
   - `kubectl get pods`

3. **Services and scaling configuration**
   - `kubectl get services`
   - `kubectl get hpa`
   - `kubectl describe hpa backend-hpa`

4. **Persistent storage proof**
   - `kubectl get pv,pvc`
   - `kubectl describe pod -l app=mongo` (show `mongo-pvc` and `/data/db`)

---

## Common Windows Fixes

### `kubectl` not recognized

Use Docker Desktop kubectl path:

```powershell
$k = "C:\Program Files\Docker\Docker\resources\bin\kubectl.exe"
& $k get nodes
```

Or add once per terminal:

```powershell
$env:Path = "C:\Program Files\Docker\Docker\resources\bin;" + $env:Path
```

### PowerShell path with spaces

Use `&` call operator:

```powershell
& "C:\Program Files\Docker\Docker\resources\bin\kubectl.exe" get hpa
```

---

## Submission Notes

- Commit all Docker files, Compose file, Kubernetes manifests, and README.
- Include screenshot evidence for all required tasks.
- Push to your GitHub repository and submit repository link.
