# OVH Skill

Manage OVHcloud services via the official `ovhcloud` CLI.

## Requirements
- `ovhcloud` CLI installed (`brew install --cask ovh/tap/ovhcloud-cli`)
- Credentials in `~/.ovh.conf` or environment variables

## Authentication

### Method 1: Interactive login
```bash
ovhcloud login
```

### Method 2: Config file (~/.ovh.conf)
```ini
[default]
endpoint=ovh-eu

[ovh-eu]
application_key=xxx
application_secret=xxx
consumer_key=xxx
```

### Method 3: Environment variables
```bash
export OVH_ENDPOINT=ovh-eu
export OVH_APPLICATION_KEY=xxx
export OVH_APPLICATION_SECRET=xxx
export OVH_CONSUMER_KEY=xxx
```

## Available Commands

### DNS (domain-zone)
```bash
# List records
ovhcloud domain-zone record list <domain>

# Add A record
ovhcloud domain-zone record create <domain> --field-type A --target <ip> --sub-domain <subdomain>

# Delete record
ovhcloud domain-zone record delete <domain> <record_id>

# Refresh zone
ovhcloud domain-zone refresh <domain>
```

### VPS
```bash
ovhcloud vps list
ovhcloud vps get <service_id> --json
ovhcloud vps reboot <service_id>
```

### Dedicated Servers (baremetal)
```bash
ovhcloud baremetal list
ovhcloud baremetal get <service_id>
ovhcloud baremetal reinstall <service_id> --editor
```

### Public Cloud
```bash
# Projects
ovhcloud cloud project list

# Instances
ovhcloud cloud instance list
ovhcloud cloud instance get <instance_id>

# Kubernetes (MKS)
ovhcloud cloud kube list
ovhcloud cloud kube nodepool list <cluster_id>
```

### Email
```bash
ovhcloud email-domain list
ovhcloud email-mxplan list
```

## Output Formats
- `--json` — JSON output
- `--yaml` — YAML output  
- `-f 'field'` — Extract specific field (gval)
- `--filter 'condition'` — Filter results

## Usage Examples

### Update DNS record
```bash
# Find record
ovhcloud domain-zone record list example.com --filter 'subDomain=="api"' --json

# Delete old
ovhcloud domain-zone record delete example.com <id>

# Add new
ovhcloud domain-zone record create example.com \
  --field-type A \
  --target 1.2.3.4 \
  --sub-domain api

# Refresh
ovhcloud domain-zone refresh example.com
```

### List instances by region
```bash
ovhcloud cloud instance list --filter 'region=="GRA9"'
```

## ⚠️ Restrictions
- **NEVER modify NS records** without explicit user approval
- Always confirm with user before deleting resources
- Use `--json` for parsing results programmatically
