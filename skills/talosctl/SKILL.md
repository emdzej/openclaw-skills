# talosctl Skill

Manage Talos Linux clusters via the `talosctl` command-line tool.

## Requirements
- `talosctl` installed (`brew install siderolabs/tap/talosctl` or from https://github.com/siderolabs/talos/releases)
- Talosconfig configured (`~/.talos/config` or `TALOSCONFIG` env var)

## Configuration

### Config file locations
1. `$XDG_CONFIG_HOME/talos/config.yaml` (if XDG_CONFIG_HOME set)
2. `$HOME/.talos/config`
3. `/var/run/secrets/talos.dev/config`
4. Override with `TALOSCONFIG` env var or `--talosconfig` flag

### View & manage config
```bash
talosctl config info                   # Show current config
talosctl config contexts               # List all contexts
talosctl config context <name>         # Switch context
talosctl config endpoint <ip>...       # Set endpoints for current context
talosctl config node <ip>...           # Set default nodes
talosctl config merge <file>           # Merge another config file
```

## Key Concepts

### Endpoints vs Nodes
- **Endpoints (`-e`)**: Control plane IPs that talosctl connects to (proxies requests)
- **Nodes (`-n`)**: Target nodes for the operation (can be any node in cluster)

Endpoints proxy requests to nodes, so you only need control plane endpoints configured.

## Common Operations

### Cluster Bootstrap
```bash
# Generate cluster config
talosctl gen config <cluster-name> https://<endpoint>:6443

# Apply config to node (first time, insecure)
talosctl apply-config -i -n <ip> -f controlplane.yaml

# Bootstrap etcd on first control plane
talosctl bootstrap -n <ip>

# Get kubeconfig
talosctl kubeconfig -n <ip>
```

### Node Information
```bash
talosctl version                       # Talos version
talosctl dashboard                     # Interactive TUI dashboard
talosctl get members                   # Cluster members
talosctl health                        # Cluster health check
talosctl disks                         # List disks on node
talosctl mounts                        # List mounts
talosctl memory                        # Memory info
talosctl processes                     # Running processes
```

### Services
```bash
talosctl services                      # List services
talosctl service <name> status         # Service status
talosctl service <name> restart        # Restart service
talosctl service <name> stop           # Stop service
talosctl service <name> start          # Start service
```

### Logs
```bash
talosctl logs <service>                # Service logs
talosctl logs kubelet                  # Kubelet logs
talosctl logs etcd                     # etcd logs
talosctl logs containerd               # containerd logs
talosctl dmesg                         # Kernel messages
```

### Configuration
```bash
talosctl get machineconfig             # Current machine config
talosctl edit machineconfig            # Edit config interactively

# Apply config changes
talosctl apply-config -f <file>        # Apply new config
talosctl apply-config -f <file> -m no-reboot  # Without reboot
talosctl apply-config -f <file> -m staged     # Staged (apply on next boot)

# Patch config
talosctl patch machineconfig -p '[{"op":"add","path":"/...","value":"..."}]'
```

### Upgrades
```bash
# Upgrade Talos
talosctl upgrade --image ghcr.io/siderolabs/installer:v1.6.0

# Upgrade Kubernetes
talosctl upgrade-k8s --to 1.29.0
```

### Reset & Reboot
```bash
talosctl reboot                        # Reboot node
talosctl shutdown                      # Shutdown node
talosctl reset                         # Reset node to maintenance mode
talosctl reset --graceful=false        # Force reset
```

### etcd Operations
```bash
talosctl etcd members                  # List etcd members
talosctl etcd status                   # etcd cluster status
talosctl etcd forfeit-leadership       # Force leader election
talosctl etcd leave                    # Remove node from etcd
talosctl etcd snapshot <file>          # Snapshot etcd to file
```

### File Operations
```bash
talosctl read /etc/os-release          # Read file from node
talosctl list /var/log                 # List directory
talosctl copy /var/log/messages .      # Copy file from node
```

### Containers
```bash
talosctl containers                    # List containers (k8s namespace)
talosctl containers -k                 # System containers
talosctl images                        # List container images
```

## Local Development Cluster

### Create local cluster (Docker)
```bash
talosctl cluster create --name dev
talosctl cluster show
talosctl cluster destroy --name dev
```

### Create QEMU cluster
```bash
talosctl cluster create --provisioner qemu --name dev
```

## Troubleshooting

### Check cluster health
```bash
talosctl health --wait-timeout 5m
talosctl get members
talosctl etcd status
```

### Debug networking
```bash
talosctl get addresses                 # IP addresses
talosctl get routes                    # Routing table
talosctl get links                     # Network interfaces
talosctl netstat                       # Network connections
talosctl pcap -i eth0 -o capture.pcap  # Packet capture
```

### Debug resources
```bash
talosctl get <resource>                # Get resource
talosctl get all                       # All resources
talosctl inspect dependencies          # Resource dependencies
```

### Common resources
```bash
talosctl get machineconfig             # Machine configuration
talosctl get machinestatus             # Machine status
talosctl get services                  # Service status
talosctl get nodename                  # Node name
talosctl get etcdmembers               # etcd members
```

## Multi-node Operations

```bash
# Target multiple nodes
talosctl -n 10.0.0.1,10.0.0.2 version

# Target all nodes (from config)
talosctl version --nodes $(talosctl config info -o json | jq -r '.nodes[]')
```

## Output Formats
- `-o yaml` — YAML output
- `-o json` — JSON output
- `-o table` — Table format (default for some commands)

## ⚠️ Restrictions
- **NEVER run `talosctl reset`** without explicit user approval (destroys node state)
- **NEVER run `talosctl etcd leave`** on control plane without confirmation
- **Be careful with `talosctl upgrade`** — plan maintenance windows
- **Avoid `talosctl shutdown`** on all nodes simultaneously
- Always use `--dry-run` when testing config changes
