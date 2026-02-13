# OpenClaw Skills

Collection of skills for OpenClaw AI agents.

## Available Skills

| Skill | Description |
|-------|-------------|
| `ovh` | OVHcloud CLI integration - manage DNS, VPS, servers, Public Cloud |

## Installation

Copy skill folder to your OpenClaw workspace:
```bash
cp -r skills/ovh ~/.openclaw/workspace/skills/
```

## Contributing

PRs welcome! Each skill should have:
- `SKILL.md` - main instructions for the agent
- `examples/` - common usage examples (optional)
- `scripts/` - helper scripts (optional)
