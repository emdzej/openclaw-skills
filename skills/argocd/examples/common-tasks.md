Common tasks — Argo CD CLI

1) Deploy a new application
- Add your Git repo (if needed):
  argocd repo add https://github.com/your-org/your-repo.git --username <user> --password <pass>
- Create the app:
  argocd app create my-app --repo https://github.com/your-org/your-repo.git --path path/to/manifests --dest-server https://kubernetes.default.svc --dest-namespace my-namespace
- Sync to deploy:
  argocd app sync my-app

2) Sync an application (trigger deployment)
- Check diff first:
  argocd app diff my-app
- Sync with pruning:
  argocd app sync my-app --prune
- Check status:
  argocd app get my-app

3) Rollback to previous revision
- View history:
  argocd app history my-app
- Rollback (use ID from history):
  argocd app rollback my-app 2
- Verify status and sync if necessary:
  argocd app get my-app

4) Add a Git repository
- SSH key method:
  argocd repo add git@github.com:your-org/your-repo.git --ssh-private-key-path ~/.ssh/id_rsa
- HTTP(S) method with credentials:
  argocd repo add https://github.com/your-org/your-repo.git --username myuser --password mypass
- Verify:
  argocd repo list

5) Add a cluster to Argo CD
- List kubeconfig contexts:
  kubectl config get-contexts -o name
- Add cluster by context name:
  argocd cluster add <CONTEXT_NAME>
- Verify:
  argocd cluster list

Notes
- For clusters you control, argocd cluster add installs an argocd-manager ServiceAccount in the target cluster. Review permissions before adding to limit scope.
- Use --insecure if your Argo CD server uses a self-signed certificate and you cannot add it to trust store (only for development).
