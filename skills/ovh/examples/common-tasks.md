# Common OVH Tasks

## DNS Management

### Add A record
```bash
ovhcloud domain-zone record create example.com --field-type A --target 192.168.1.1 --sub-domain www
ovhcloud domain-zone refresh example.com
```

### Add CNAME record
```bash
ovhcloud domain-zone record create example.com --field-type CNAME --target target.example.com. --sub-domain alias
ovhcloud domain-zone refresh example.com
```

### List all records
```bash
ovhcloud domain-zone record list example.com --json | jq '.[]'
```

## VPS Operations

### Get VPS info
```bash
ovhcloud vps get vps-xxxxx --json
```

### Reboot VPS
```bash
ovhcloud vps reboot vps-xxxxx
```

## Public Cloud

### List all instances
```bash
ovhcloud cloud instance list --json
```

### Filter by region
```bash
ovhcloud cloud instance list --filter 'region=="GRA9"'
```
