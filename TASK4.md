# Task 4 - Persistent Storage (PV/PVC) for Database

This task deploys MongoDB with persistent storage in Kubernetes.

## Files

- `k8s/task4/mongo-pv.yaml`
- `k8s/task4/mongo-pvc.yaml`
- `k8s/task4/mongo-deployment.yaml`
- `k8s/task4/mongo-service.yaml`
- `k8s/task4/backend-secret.yaml`

## Apply

```bash
kubectl apply -k k8s/task4
kubectl rollout restart deployment/backend
```

## Verify

```bash
kubectl get pv
kubectl get pvc
kubectl get pods -l app=mongo
kubectl describe pod -l app=mongo
```

Expected:
- PV status: `Bound`
- PVC status: `Bound`
- MongoDB pod is running with volume mounted at `/data/db`

## Optional data check

```bash
kubectl exec deployment/backend -- node backend/seeder
kubectl port-forward svc/backend 5005:5000
```

Then open:
- `http://localhost:5005/api/products`
