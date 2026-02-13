Argo CD CLI Skill

Requirements
- argocd CLI installed (https://github.com/argoproj/argo-cd/releases). On macOS: brew install argocd
- kubectl configured and able to access target clusters (~/.kube/config)

Authentication
- Login to an Argo CD server:
  - argocd login <ARGOCD_SERVER> [--username <user>] [--password <pass>] [--insecure]
  - You can use ARGOCD_AUTH_TOKEN environment variable or --auth-token flag
- Use contexts to switch between servers:
  - argocd context list
  - argocd context set <context-name>
  - Global flag --argocd-context <name> selects context per command
- For local cluster installation, you can use --core to talk directly to Kubernetes

Common commands
- Applications
  - List: argocd app list
  - Get details: argocd app get <APP_NAME>
  - Create: argocd app create <NAME> --repo <REPO_URL> --path <PATH> --dest-server <K8S_API> --dest-namespace <NS>
  - Sync: argocd app sync <APP_NAME> [--prune] [--revision <rev>]
  - Delete: argocd app delete <APP_NAME>
  - Diff: argocd app diff <APP_NAME> [--local <PATH>|--revision <rev>]
  - History: argocd app history <APP_NAME>
  - Rollback: argocd app rollback <APP_NAME> <HISTORY_ID/REVISION>

- Repositories
  - Add: argocd repo add <REPO_URL> --type git --username <user> --password <pass> [--ssh-private-key-path <file>]
  - List: argocd repo list
  - Remove: argocd repo rm <REPO_URL_OR_NAME>

- Clusters
  - Add cluster (from kubeconfig context): argocd cluster add <CONTEXT_NAME>
  - List: argocd cluster list
  - Remove: argocd cluster rm <SERVER_URL_OR_NAME>

- Projects
  - List: argocd proj list
  - Create: argocd proj create <PROJECT_NAME> --description "..." --dest <server>:<namespace>
  - Delete: argocd proj delete <PROJECT_NAME>

- Accounts
  - List: argocd account list
  - Get user info: argocd account get-user-info

Output formats
- Many commands support -o/--output (json|yaml) or --refresh to re-sync state
- Example: argocd app get <APP_NAME> -o json

Restrictions & Safety
- Destructive actions (argocd app delete, argocd cluster rm, repo rm) are irreversible through CLI and should not be executed without explicit confirmation.
- When automating, prefer --dry-run (where available) and review diffs with argocd app diff before syncing.
- Use role-based access controls in production; avoid running CLI as admin where possible.

References
- Official command reference: https://argo-cd.readthedocs.io/en/latest/user-guide/commands/argocd/
- Getting Started: https://argo-cd.readthedocs.io/en/latest/getting_started/
