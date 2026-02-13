# Common talosctl Tasks

## Bootstrap a New Cluster

### 1. Generate configs
```bash
talosctl gen config my-cluster https://192.168.1.10:6443 \
  --output-dir _out \
  --with-docs=false \
  --with-examples=false
```

### 2. Apply to control plane (insecure first time)
```bash
talosctl apply-config --insecure -n 192.168.1.10 -f _out/controlplane.yaml
```

### 3. Bootstrap etcd
```bash
talosctl bootstrap -n 192.168.1.10 -e 192.168.1.10
```

### 4. Get kubeconfig
```bash
talosctl kubeconfig -n 192.168.1.10 -e 192.168.1.10
```

### 5. Add more control planes
```bash
talosctl apply-config --insecure -n 192.168.1.11 -f _out/controlplane.yaml
talosctl apply-config --insecure -n 192.168.1.12 -f _out/controlplane.yaml
```

### 6. Add workers
```bash
talosctl apply-config --insecure -n 192.168.1.20 -f _out/worker.yaml
```

## Upgrade Talos

### Check current version
```bash
talosctl version
```

### Upgrade single node
```bash
talosctl upgrade -n 192.168.1.10 --image ghcr.io/siderolabs/installer:v1.6.0
```

### Upgrade all nodes (rolling)
```bash
for node in 192.168.1.10 192.168.1.11 192.168.1.12; do
  echo "Upgrading $node..."
  talosctl upgrade -n $node --image ghcr.io/siderolabs/installer:v1.6.0 --wait
done
```

## Upgrade Kubernetes

```bash
# Check current version
talosctl -n 192.168.1.10 get kubeletstatus

# Upgrade to new version
talosctl upgrade-k8s -n 192.168.1.10 --to 1.29.0
```

## etcd Recovery

### Snapshot etcd
```bash
talosctl etcd snapshot -n 192.168.1.10 /tmp/etcd-backup.snap
```

### Restore from snapshot
```bash
talosctl bootstrap -n 192.168.1.10 --recover-from=/tmp/etcd-backup.snap
```

### Remove failed etcd member
```bash
# List members
talosctl etcd members -n 192.168.1.10

# Remove member
talosctl etcd remove-member <member-id> -n 192.168.1.10
```

## Debug a Node

### Check overall status
```bash
talosctl dashboard -n 192.168.1.10
```

### Check services
```bash
talosctl services -n 192.168.1.10
talosctl logs kubelet -n 192.168.1.10 --tail 100
```

### Check resources
```bash
talosctl memory -n 192.168.1.10
talosctl processes -n 192.168.1.10
```

### Network debugging
```bash
talosctl get addresses -n 192.168.1.10
talosctl get routes -n 192.168.1.10
talosctl netstat -n 192.168.1.10
```

## Patch Machine Config

### Add registry mirror
```bash
talosctl patch machineconfig -n 192.168.1.10 --patch '[
  {
    "op": "add",
    "path": "/machine/registries/mirrors",
    "value": {
      "docker.io": {
        "endpoints": ["https://registry-mirror.local"]
      }
    }
  }
]'
```

### Add extra mount
```bash
talosctl patch machineconfig -n 192.168.1.10 --patch '[
  {
    "op": "add",
    "path": "/machine/disks/0/partitions/-",
    "value": {
      "mountpoint": "/var/mnt/data"
    }
  }
]'
```

## Generate Config Patches

### Control plane patch file
```yaml
# controlplane-patch.yaml
machine:
  network:
    hostname: cp1
  install:
    disk: /dev/sda
cluster:
  network:
    cni:
      name: none
```

### Apply with patch
```bash
talosctl gen config my-cluster https://192.168.1.10:6443 \
  --config-patch-control-plane @controlplane-patch.yaml
```

## Maintenance Mode

### Reset node to maintenance
```bash
talosctl reset -n 192.168.1.10 --graceful
```

### Apply new config in maintenance mode
```bash
talosctl apply-config --insecure -n 192.168.1.10 -f new-config.yaml
```
