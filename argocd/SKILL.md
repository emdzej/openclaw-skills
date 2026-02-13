---
name: argocd
description: Manage ArgoCD GitOps deployments. Use for application sync, rollback, status, history, repository management, cluster management, and project configuration. Triggers on ArgoCD, GitOps, application deployment, sync, rollback queries.
---

# ArgoCD Skill

Manage ArgoCD via CLI. Requires `argocd` CLI installed and authenticated.

## Prerequisites

```bash
# Install (macOS)
brew install argocd

# Login
argocd login <SERVER> [--username <user>] [--password <pass>] [--insecure]
# Or use ARGOCD_AUTH_TOKEN env var
```

## Authentication

```bash
# Login to server
argocd login argocd.example.com --username admin --password secret

# List/switch contexts
argocd context list
argocd context set <name>

# Per-command context
argocd app list --argocd-context prod

# Direct K8s access (in-cluster)
argocd app list --core
```

## Applications

### List & Status

```bash
# List all apps
argocd app list
argocd app list -o wide
argocd app list -o json

# Get app details
argocd app get <APP>
argocd app get <APP> -o json
argocd app get <APP> --refresh  # Force refresh

# Show manifests
argocd app manifests <APP>
```

### Sync

```bash
# Sync application
argocd app sync <APP>
argocd app sync <APP> --prune           # Remove orphaned resources
argocd app sync <APP> --force           # Force sync
argocd app sync <APP> --revision <REV>  # Specific revision
argocd app sync <APP> --dry-run         # Preview changes

# Sync with options
argocd app sync <APP> --resource <GROUP>:<KIND>:<NAME>  # Specific resource
argocd app sync <APP> --async           # Don't wait
```

### History & Rollback

```bash
# View history
argocd app history <APP>

# Rollback to revision
argocd app rollback <APP> <HISTORY_ID>
argocd app rollback <APP> <HISTORY_ID> --prune
```

### Diff

```bash
# Compare live vs desired
argocd app diff <APP>
argocd app diff <APP> --revision <REV>
argocd app diff <APP> --local <PATH>  # Local manifests
```

### Create & Delete

```bash
# Create application
argocd app create <APP> \
  --repo <REPO_URL> \
  --path <PATH> \
  --dest-server <K8S_API> \
  --dest-namespace <NS> \
  --project <PROJECT>

# From Helm
argocd app create <APP> \
  --repo <HELM_REPO> \
  --helm-chart <CHART> \
  --revision <VERSION> \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace <NS>

# Delete (⚠️ destructive)
argocd app delete <APP>
argocd app delete <APP> --cascade=false  # Keep resources
```

### App Management

```bash
# Set parameters
argocd app set <APP> --parameter key=value
argocd app set <APP> --helm-set key=value
argocd app set <APP> --values values.yaml

# Actions
argocd app wait <APP>                    # Wait for healthy
argocd app terminate-op <APP>            # Cancel sync
argocd app resources <APP>               # List resources
argocd app logs <APP> --container <CNT>  # View logs
```

## Projects

```bash
# List projects
argocd proj list

# Create project
argocd proj create <PROJ> \
  --description "Description" \
  --dest <SERVER>,<NS> \
  --src <REPO_URL>

# Get project details
argocd proj get <PROJ>

# Add allowed destinations
argocd proj add-destination <PROJ> <SERVER> <NS>

# Add source repos
argocd proj add-source <PROJ> <REPO_URL>

# Set roles
argocd proj role create <PROJ> <ROLE>
argocd proj role add-policy <PROJ> <ROLE> -a <ACTION> -p allow -o <APP>

# Delete project (⚠️ destructive)
argocd proj delete <PROJ>
```

## Repositories

```bash
# List repos
argocd repo list

# Add Git repo (HTTPS)
argocd repo add <REPO_URL> \
  --username <USER> \
  --password <TOKEN>

# Add Git repo (SSH)
argocd repo add <REPO_URL> \
  --ssh-private-key-path ~/.ssh/id_rsa

# Add Helm repo
argocd repo add <HELM_REPO_URL> \
  --type helm \
  --name <NAME>

# Remove repo (⚠️ destructive)
argocd repo rm <REPO_URL>
```

## Clusters

```bash
# List clusters
argocd cluster list

# Add cluster from kubeconfig
argocd cluster add <CONTEXT_NAME>
argocd cluster add <CONTEXT_NAME> --name <DISPLAY_NAME>

# Get cluster info
argocd cluster get <SERVER_URL>

# Remove cluster (⚠️ destructive)
argocd cluster rm <SERVER_URL>
```

## Accounts & RBAC

```bash
# List accounts
argocd account list

# Get current user
argocd account get-user-info

# Update password
argocd account update-password

# Generate API token
argocd account generate-token --account <ACCOUNT>
```

## Output Formats

Most commands support:
- `-o json` - JSON output
- `-o yaml` - YAML output
- `-o wide` - Extended table
- `--refresh` - Force state refresh

## Common Patterns

### Deploy new version
```bash
argocd app diff <APP> --revision <NEW_REV>
argocd app sync <APP> --revision <NEW_REV>
argocd app wait <APP>
```

### Quick rollback
```bash
argocd app history <APP>
argocd app rollback <APP> <PREV_ID>
```

### Check all apps health
```bash
argocd app list -o json | jq '.[] | {name: .metadata.name, health: .status.health.status, sync: .status.sync.status}'
```

## Safety Rules

- ⚠️ Always `--dry-run` or `diff` before sync in production
- ⚠️ `delete`, `rm` commands are irreversible
- ⚠️ Use `--cascade=false` to preserve K8s resources when deleting app
- ⚠️ Prefer RBAC-limited accounts over admin access
