# Common kubectl Tasks

## Troubleshooting a Pod

### Check pod status
```bash
kubectl get pod <pod> -o wide
kubectl describe pod <pod>
```

### Check logs
```bash
kubectl logs <pod>
kubectl logs <pod> --previous  # After crash
kubectl logs <pod> -f          # Follow
```

### Get shell access
```bash
kubectl exec -it <pod> -- /bin/sh
```

### Check events
```bash
kubectl get events --field-selector involvedObject.name=<pod>
```

## Deploy an Application

### From image
```bash
kubectl create deployment nginx --image=nginx:latest
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

### From YAML
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### Scale
```bash
kubectl scale deployment nginx --replicas=3
```

## Update Application

### Change image
```bash
kubectl set image deployment/nginx nginx=nginx:1.25
```

### Watch rollout
```bash
kubectl rollout status deployment/nginx
```

### Rollback
```bash
kubectl rollout undo deployment/nginx
```

## Debug Network Issues

### Check service endpoints
```bash
kubectl get endpoints <service>
kubectl describe svc <service>
```

### Test connectivity from a pod
```bash
kubectl exec -it <pod> -- curl http://<service>:<port>
kubectl exec -it <pod> -- nslookup <service>
```

### Port forward for local testing
```bash
kubectl port-forward svc/<service> 8080:80
```

## Manage ConfigMaps & Secrets

### Create ConfigMap
```bash
# From file
kubectl create configmap my-config --from-file=config.yaml

# From literal
kubectl create configmap my-config --from-literal=key1=value1

# From env file
kubectl create configmap my-config --from-env-file=config.env
```

### Create Secret
```bash
kubectl create secret generic my-secret --from-literal=password=secret123
kubectl create secret docker-registry my-registry --docker-server=... --docker-username=... --docker-password=...
```

### View secret (decoded)
```bash
kubectl get secret my-secret -o jsonpath='{.data.password}' | base64 -d
```

## Resource Cleanup

### Delete completed jobs
```bash
kubectl delete jobs --field-selector status.successful=1
```

### Delete failed pods
```bash
kubectl delete pods --field-selector=status.phase=Failed
```

### Delete evicted pods
```bash
kubectl get pods | grep Evicted | awk '{print $1}' | xargs kubectl delete pod
```

## Generate YAML Templates

### Deployment
```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml
```

### Service
```bash
kubectl expose deployment nginx --port=80 --dry-run=client -o yaml > service.yaml
```

### CronJob
```bash
kubectl create cronjob backup --image=busybox --schedule="0 2 * * *" --dry-run=client -o yaml -- /bin/sh -c "echo backup"
```
