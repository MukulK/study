# Kubernetes (K8s) Troubleshooting Commands for Amazon EKS

## 1. Check Cluster Info
### Verify cluster connection
```sh
kubectl cluster-info
```

### Verify nodes in the cluster
```sh
kubectl get nodes -o wide
```

---

## 2. Check Namespaces
```sh
kubectl get namespaces
```

Check pods in a specific namespace:
```sh
kubectl get pods -n <namespace>
```

---

## 3. Troubleshoot Pods
### Check all pods in all namespaces
```sh
kubectl get pods -A
```

### Describe a specific pod
```sh
kubectl describe pod <pod-name> -n <namespace>
```

### Check pod logs
```sh
kubectl logs <pod-name> -n <namespace>
```
For multi-container pods:
```sh
kubectl logs <pod-name> -c <container-name> -n <namespace>
```
Live streaming logs:
```sh
kubectl logs -f <pod-name> -n <namespace>
```

---

## 4. Troubleshoot Deployments
### Check deployment status
```sh
kubectl get deployments -n <namespace>
```

### Describe deployment
```sh
kubectl describe deployment <deployment-name> -n <namespace>
```

### Check rollout history
```sh
kubectl rollout history deployment/<deployment-name> -n <namespace>
```

### Rollback to the previous version
```sh
kubectl rollout undo deployment <deployment-name> -n <namespace>
```

---

## 5. Troubleshoot Services
### List services
```sh
kubectl get svc -A
```

### Describe a service
```sh
kubectl describe svc <service-name> -n <namespace>
```

### Test service reachability
```sh
kubectl run curlpod --image=curlimages/curl -i --tty --rm -- curl <service-name>:<port>
```

---

## 6. Troubleshoot Ingress
### Get ingress resources
```sh
kubectl get ingress -A
```

### Describe ingress
```sh
kubectl describe ingress <ingress-name> -n <namespace>
```

### Test ingress externally
```sh
curl -v http://<ingress-domain>
```

---

## 7. Check Logs for Nodes & Kubelet
### Check node status
```sh
kubectl get nodes
kubectl describe node <node-name>
```

### Check kubelet logs
```sh
journalctl -u kubelet -f
```

For AWS-managed EKS nodes:
```sh
aws eks describe-nodegroup --cluster-name <cluster-name> --nodegroup-name <nodegroup-name>
```

---

## 8. Check Events in the Cluster
```sh
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## 9. Troubleshoot Persistent Volumes
### Check Persistent Volume Claims (PVCs)
```sh
kubectl get pvc -A
```

### Describe PVC
```sh
kubectl describe pvc <pvc-name> -n <namespace>
```

---

## 10. Debug Networking
### Get all network policies
```sh
kubectl get networkpolicy -A
```

### Check DNS resolution
```sh
kubectl run busybox --rm -it --image=busybox -- nslookup <service-name>
```

---

## 11. Debug EKS Cluster Logs
### Check AWS CloudWatch logs for EKS control plane
```sh
aws logs describe-log-groups --log-group-name-prefix /aws/eks/<cluster-name>/cluster
```

Fetch logs:
```sh
aws logs get-log-events --log-group-name /aws/eks/<cluster-name>/cluster --log-stream-name <stream-name>
```

---

## Common Issues & Fixes
| Issue | Cause | Fix |
|--------|--------|--------|
| Pod stuck in `Pending` | Insufficient resources, missing node selector, or unscheduled node | `kubectl describe pod <pod-name>` and check events |
| Pod keeps restarting | CrashLoopBackOff due to errors in the container | `kubectl logs <pod-name>` and fix configuration issues |
| Service not accessible | No endpoints associated or incorrect service selector | `kubectl get svc <service-name>` and `kubectl get ep <service-name>` |
| Ingress not working | Incorrect ingress rules or missing annotations | `kubectl describe ingress <ingress-name>` |
| PVC stuck in `Pending` | Storage class issues or volume not provisioned | `kubectl describe pvc <pvc-name>` |
| Node in `NotReady` state | Insufficient resources or kubelet issues | `kubectl describe node <node-name>` and check kubelet logs |
| Image pull failures | Incorrect image name, registry access issues | `kubectl describe pod <pod-name>` and check `ImagePullBackOff` events |
| High pod latency | Network congestion or resource limits | Monitor with `kubectl top pod` and check CloudWatch metrics |
| Failed Helm release | Incorrect values or missing dependencies | `helm list -A` and `helm status <release-name>` |
| CoreDNS resolving issues | Misconfigured CoreDNS config map | `kubectl get cm -n kube-system` and check logs `kubectl logs -n kube-system -l k8s-app=kube-dns` |
| HPA not scaling pods | Missing metrics-server or incorrect CPU/memory requests | `kubectl get hpa -A` and `kubectl describe hpa <hpa-name>` |
| Pod failing readiness probe | Health check failures | `kubectl describe pod <pod-name>` and check `readinessProbe` logs |
| Persistent volume not mounting | Incorrect access mode or volume binding issues | `kubectl describe pvc <pvc-name>` and check storage class |

---


