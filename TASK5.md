# Task 5 - Application Scaling (HPA)

This task enables Horizontal Pod Autoscaling for backend deployment.

## Requirement mapping

- Minimum Pods: `2`
- Maximum Pods: `5`
- CPU Utilization Target: `70%`

## Files

- `k8s/task5/backend-hpa.yaml`
- `k8s/task3/backend-deployment.yaml` (contains CPU requests required by HPA)

## Apply

```bash
kubectl apply -f k8s/task3/backend-deployment.yaml
kubectl apply -k k8s/task5
```

## Metrics prerequisite (local clusters)

If `kubectl top` or HPA shows `cpu: <unknown>`, install metrics-server:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

For Docker Desktop `kind` cluster, apply kubelet TLS compatibility patch:

```bash
kubectl -n kube-system patch deployment metrics-server --type=json --patch='[
	{"op":"replace","path":"/spec/template/spec/containers/0/args/2","value":"--kubelet-preferred-address-types=InternalIP,Hostname,ExternalIP"},
	{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}
]'
kubectl -n kube-system rollout status deployment/metrics-server
```

## Verify

```bash
kubectl get hpa
kubectl describe hpa backend-hpa
kubectl get deployment backend
kubectl top pods -l app=backend
```

## Optional: generate load to observe scaling

```bash
kubectl run load-generator --rm -it --restart=Never --image=busybox -- /bin/sh
# inside pod:
while true; do wget -q -O- http://backend:5000/api/products > /dev/null; done
```

In another terminal:

```bash
kubectl get hpa -w
```
