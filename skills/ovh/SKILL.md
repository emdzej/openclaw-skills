# OVH Skill

Zarządzanie usługami OVHcloud przez `ovhcloud` CLI.

## Wymagania
- `ovhcloud` CLI zainstalowane (`brew install --cask ovh/tap/ovhcloud-cli`)
- Credentials w `~/.ovh.conf` lub env vars

## Autentykacja

### Metoda 1: Interactive login
```bash
ovhcloud login
```

### Metoda 2: Config file (~/.ovh.conf)
```ini
[default]
endpoint=ovh-eu

[ovh-eu]
application_key=xxx
application_secret=xxx
consumer_key=xxx
```

### Metoda 3: Environment variables
```bash
export OVH_ENDPOINT=ovh-eu
export OVH_APPLICATION_KEY=xxx
export OVH_APPLICATION_SECRET=xxx
export OVH_CONSUMER_KEY=xxx
```

## Dostępne komendy

### DNS (domain-zone)
```bash
# Lista rekordów
ovhcloud domain-zone record list <domena>

# Dodaj rekord A
ovhcloud domain-zone record create <domena> --field-type A --target <ip> --sub-domain <subdomena>

# Usuń rekord
ovhcloud domain-zone record delete <domena> <record_id>

# Refresh strefy
ovhcloud domain-zone refresh <domena>
```

### VPS
```bash
ovhcloud vps list
ovhcloud vps get <service_id> --json
ovhcloud vps reboot <service_id>
```

### Serwery dedykowane (baremetal)
```bash
ovhcloud baremetal list
ovhcloud baremetal get <service_id>
ovhcloud baremetal reinstall <service_id> --editor
```

### Public Cloud
```bash
# Projekty
ovhcloud cloud project list

# Instancje
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

## Output formats
- `--json` — JSON output
- `--yaml` — YAML output  
- `-f 'field'` — Extract specific field (gval)
- `--filter 'condition'` — Filter results

## Przykłady użycia

### Aktualizacja rekordu DNS
```bash
# Znajdź rekord
ovhcloud domain-zone record list ksiazkomol.pl --filter 'subDomain=="api"' --json

# Usuń stary
ovhcloud domain-zone record delete ksiazkomol.pl <id>

# Dodaj nowy
ovhcloud domain-zone record create ksiazkomol.pl \
  --field-type A \
  --target 1.2.3.4 \
  --sub-domain api

# Refresh
ovhcloud domain-zone refresh ksiazkomol.pl
```

### Lista instancji w regionie
```bash
ovhcloud cloud instance list --filter 'region=="GRA9"'
```

## ⚠️ Ograniczenia
- **NIGDY nie modyfikuj rekordów NS** bez explicit approval
- Przed usunięciem — zawsze potwierdź z użytkownikiem
- Używaj `--json` do parsowania wyników
