# kubectl Skill

Manage Kubernetes clusters via the `kubectl` command-line tool.

## Requirements
- `kubectl` installed (`brew install kubectl` or from https://kubernetes.io/docs/tasks/tools/)
- Kubeconfig configured (`~/.kube/config` or `KUBECONFIG` env var)

## Configuration

### View current config
```bash
kubectl config view                    # Show merged kubeconfig
kubectl config current-context         # Display current context
kubectl config get-contexts            # List all contexts
```

### Switch context/namespace
```bash
kubectl config use-context <context>   # Switch context
kubectl config set-context --current --namespace=<ns>  # Set default namespace
```

## Common Operations

### Get Resources
```bash
kubectl get pods                       # List pods in current namespace
kubectl get pods -A                    # List pods in ALL namespaces
kubectl get pods -o wide               # More details (node, IP)
kubectl get pods -o yaml               # Full YAML output
kubectl get svc,deploy,pods            # Multiple resource types
kubectl get all                        # All common resources
```

### Resource shortcuts
| Full name | Shortcut |
|-----------|----------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| namespaces | ns |
| nodes | no |
| configmaps | cm |
| secrets | - |
| persistentvolumeclaims | pvc |
| persistentvolumes | pv |
| ingresses | ing |
| daemonsets | ds |
| statefulsets | sts |
| cronjobs | cj |
| jobs | - |

### Describe & Debug
```bash
kubectl describe pod <pod>             # Detailed info about a pod
kubectl describe node <node>           # Node details
kubectl logs <pod>                     # Container logs
kubectl logs <pod> -c <container>      # Specific container logs
kubectl logs <pod> -f                  # Follow logs (tail -f)
kubectl logs <pod> --previous          # Previous container logs (after crash)
```

### Execute Commands
```bash
kubectl exec -it <pod> -- /bin/sh      # Interactive shell
kubectl exec <pod> -- <command>        # Run command in pod
kubectl exec -it <pod> -c <container> -- /bin/sh  # Specific container
```

### Apply & Delete
```bash
kubectl apply -f <file.yaml>           # Create/update from file
kubectl apply -f <dir>/                # Apply all files in directory
kubectl delete -f <file.yaml>          # Delete resources from file
kubectl delete pod <pod>               # Delete specific pod
kubectl delete pods --all              # Delete all pods in namespace
```

### Create Resources
```bash
kubectl create deployment <name> --image=<image>
kubectl create service clusterip <name> --tcp=80:8080
kubectl create configmap <name> --from-file=<path>
kubectl create secret generic <name> --from-literal=key=value
kubectl create namespace <name>
```

### Scaling
```bash
kubectl scale deployment <name> --replicas=3
kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=80
```

### Rollouts
```bash
kubectl rollout status deployment/<name>    # Watch rollout progress
kubectl rollout history deployment/<name>   # Rollout history
kubectl rollout undo deployment/<name>      # Rollback to previous
kubectl rollout restart deployment/<name>   # Rolling restart
```

### Port Forwarding
```bash
kubectl port-forward pod/<pod> 8080:80     # Forward local:container
kubectl port-forward svc/<svc> 8080:80     # Forward to service
kubectl port-forward deploy/<deploy> 8080:80
```

### Copy Files
```bash
kubectl cp <pod>:/path/to/file ./local     # From pod to local
kubectl cp ./local <pod>:/path/to/file     # From local to pod
```

## Advanced Queries

### JSONPath
```bash
# Get all pod names
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# Get pod IPs
kubectl get pods -o jsonpath='{.items[*].status.podIP}'

# Get node external IPs
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'
```

### Filtering
```bash
kubectl get pods -l app=nginx           # By label
kubectl get pods --field-selector=status.phase=Running
kubectl get pods --selector='!node-role.kubernetes.io/control-plane'
```

### Sorting
```bash
kubectl get pods --sort-by=.metadata.creationTimestamp
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'
kubectl get pv --sort-by=.spec.capacity.storage
```

### Custom Columns
```bash
kubectl get pods -o custom-columns='NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP'
```

## Cluster Info
```bash
kubectl cluster-info                    # Cluster endpoint info
kubectl get nodes                       # List nodes
kubectl top nodes                       # Node resource usage
kubectl top pods                        # Pod resource usage
kubectl api-resources                   # All available resources
kubectl api-versions                    # API versions
```

## Namespace Operations
```bash
kubectl get namespaces                  # List namespaces
kubectl create namespace <name>         # Create namespace
kubectl delete namespace <name>         # Delete namespace (and all resources!)
```

## Events
```bash
kubectl get events                      # List events
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl events --types=Warning          # Only warnings
```

## Output Formats
- `-o yaml` — YAML output
- `-o json` — JSON output
- `-o wide` — Additional columns
- `-o name` — Only resource names
- `-o jsonpath='{...}'` — JSONPath extraction
- `-o custom-columns='...'` — Custom columns

## Dry Run
```bash
kubectl apply -f file.yaml --dry-run=client  # Validate locally
kubectl apply -f file.yaml --dry-run=server  # Validate on server
kubectl create deployment x --image=y --dry-run=client -o yaml  # Generate YAML
```

## ⚠️ Restrictions
- **NEVER delete namespaces** without explicit user approval (deletes ALL resources)
- **NEVER run `kubectl delete --all`** without confirmation
- **Avoid `kubectl exec` with destructive commands** without asking
- Use `--dry-run=client` when generating manifests
- Prefer `kubectl apply` over `kubectl create` for idempotency
