# Helm Guide and Useful Commands with Examples

## Introduction to Helm
Helm is a package manager for Kubernetes that helps deploy and manage applications efficiently using Helm charts. Charts are pre-configured Kubernetes resources packaged together.

---

## 1. Helm Installation
### Install Helm on Linux/macOS
```sh
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### Install Helm on Windows (via Chocolatey)
```sh
choco install kubernetes-helm
```

### Verify Installation
```sh
helm version
```

---

## 2. Helm Basics
### Add a Helm Repository
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
```

### Update Helm Repositories
```sh
helm repo update
```

### Search for Charts
```sh
helm search repo nginx
```

---

## 3. Deploying Applications with Helm
### Install a Helm Chart
```sh
helm install my-release bitnami/nginx
```

### List Installed Releases
```sh
helm list -A
```

### Upgrade a Release
```sh
helm upgrade my-release bitnami/nginx --set service.type=LoadBalancer
```

### Rollback to a Previous Version
```sh
helm rollback my-release 1
```

### Uninstall a Release
```sh
helm uninstall my-release
```

---

## 4. Working with Helm Charts
### Create a New Helm Chart
```sh
helm create mychart
```

### View the Chart Structure
```sh
ls mychart
```

### Package a Chart
```sh
helm package mychart
```

### Deploy a Local Chart
```sh
helm install mychart ./mychart
```

---

## 5. Helm Values and Customization
### View Default Values of a Chart
```sh
helm show values bitnami/nginx
```

### Install a Chart with Custom Values
```sh
helm install my-release bitnami/nginx -f custom-values.yaml
```

### Use `--set` to Override Values
```sh
helm install my-release bitnami/nginx --set image.tag=latest
```

---

## 6. Debugging Helm Issues
### Check Helm Release Status
```sh
helm status my-release
```

### Get Release History
```sh
helm history my-release
```

### Debug a Chart Before Installing
```sh
helm template my-release bitnami/nginx
```

### Dry Run an Installation
```sh
helm install my-release bitnami/nginx --dry-run --debug
```

### Check Logs of a Helm Deployed Application
```sh
kubectl logs -l app.kubernetes.io/name=nginx
```

---

## 7. Managing Helm Repositories
### List All Repositories
```sh
helm repo list
```

### Remove a Repository
```sh
helm repo remove bitnami
```

### Add a Custom Repository
```sh
helm repo add myrepo https://my.custom.repo
```

### Index a Local Chart Repository
```sh
helm repo index ./myrepo
```

---

## 8. Helm Security Best Practices
- Always verify Helm chart sources before deploying.
- Use `--verify` when installing charts.
- Store Helm releases in a secured backend (e.g., S3, Google Cloud Storage).
- Avoid storing sensitive values directly in `values.yaml`, use Kubernetes Secrets instead.

---

## 9. Common Helm Issues & Fixes

| Issue | Cause | Fix |
|--------|--------|--------|
| `helm install` fails | Chart dependency missing | Run `helm dependency update` before installing |
| Release stuck in `Pending` | Insufficient resources | Check `kubectl get pods -A` for issues |
| `helm upgrade` not applying changes | Kubernetes caching issue | Use `helm upgrade --force` |
| Service not reachable | Incorrect values.yaml settings | Check `kubectl describe svc <service>` |
| PersistentVolumeClaim not bound | Storage class mismatch | Check `kubectl get pvc -A` |

---

## 10. Troubleshooting Horizontal Pod Autoscaler (HPA)
### Check HPA Status
```sh
kubectl get hpa
```

### Describe HPA for Details
```sh
kubectl describe hpa my-hpa
```

### Check Metrics Server
```sh
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"
```

### Debug HPA Scaling Issues
```sh
kubectl get pods -w
```

---

## 11. Troubleshooting CoreDNS Issues
### Check CoreDNS Pods
```sh
kubectl get pods -n kube-system -l k8s-app=kube-dns
```

### Describe CoreDNS Deployment
```sh
kubectl describe deployment coredns -n kube-system
```

### Check CoreDNS Logs
```sh
kubectl logs -n kube-system -l k8s-app=kube-dns
```

### Test DNS Resolution Inside a Pod
```sh
kubectl run -it --rm --restart=Never busybox --image=busybox -- nslookup google.com
```

---
