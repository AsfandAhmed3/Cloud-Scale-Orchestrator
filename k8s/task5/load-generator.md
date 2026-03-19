# Optional HPA Load Test

Run this only if you want to capture autoscaling changes in screenshots.

```bash
kubectl run load-generator --rm -it --restart=Never --image=busybox -- /bin/sh
```

Inside shell:

```sh
while true; do wget -q -O- http://backend:5000/api/products > /dev/null; done
```

In another terminal:

```bash
kubectl get hpa -w
kubectl get pods -l app=backend
```
