# Task 3 - Kubernetes Deployment

This task deploys frontend and backend using Kubernetes YAML manifests.

## Files

- `k8s/task3/backend-deployment.yaml`
- `k8s/task3/backend-service.yaml`
- `k8s/task3/frontend-deployment.yaml`
- `k8s/task3/frontend-service.yaml`
- `k8s/task3/backend-secret.yaml`

## Important

Update this value first:

- `k8s/task3/backend-secret.yaml` -> `MONGO_URI`

## Apply manifests

```bash
kubectl apply -k k8s/task3
```

## Verify resources

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Expected:
- `frontend` deployment has `3` replicas
- `backend` deployment has `3` replicas

## Access app

- Frontend NodePort service is configured at `30081`
- Open: `http://localhost:30081`

## Debug if needed

```bash
kubectl logs deployment/backend
kubectl logs deployment/frontend
kubectl describe pod <pod-name>
```
