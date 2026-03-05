# sentry-cli skill

Claude Code skill for investigating Sentry issues via the `sentry` CLI.

## Install

```bash
# From the skill root
ln -s "$(pwd)" ~/.claude/skills/sentry-cli
```

## Prerequisites

- `sentry` CLI installed and in PATH
- Authenticated: `sentry auth login`

## What it does

- **Auto-triggers** on Sentry URLs dropped into conversation
- **Core investigation**: view issues (with latest event), list/search issues, view traces and events
- **AI analysis** (`explain`, `plan`) available on explicit request — see REFERENCE.md
- Always pipes `--json` through `jq`, never uses `sentry api`

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Skill instructions + core investigation commands |
| `REFERENCE.md` | Auth, discovery, AI analysis, ID formats, pagination |
| `README.md` | This file |
