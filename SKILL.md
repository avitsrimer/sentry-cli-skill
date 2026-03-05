---
name: sentry-cli
description: Guide for using the Sentry CLI to interact with Sentry from the command line. Use when the user asks about viewing issues, events, projects, organizations, making API calls, or authenticating with Sentry via CLI.
allowed-tools: Bash, Read, AskUserQuestion
---

# Sentry CLI

Interact with Sentry via the `sentry` CLI. Always pipe `--json` output through `jq`. Never use `sentry api` — use dedicated subcommands.

## Auto-trigger conditions

Activate when the user:
- Pastes a Sentry URL: `https://*.sentry.io/issues/...` or `https://sentry.io/organizations/...`
- Explicitly invokes `/sentry-cli`

Do NOT trigger on bare issue keys (e.g. `PROJ-4SM`) — these may be Jira tickets. Only act when there is clear Sentry intent.

## Healthcheck

Before any operation, verify auth:
```bash
sentry auth status
```

If not authenticated:
```bash
sentry auth login
```

## Issue investigation

### View an issue (includes latest event automatically)
```bash
sentry issue view <issue-id> --json | jq
sentry issue view PROJ-4SM --json | jq
sentry issue view 123456789 --json | jq
```

Key fields to extract from JSON:
- `.issue.id`, `.issue.shortId`, `.issue.title`
- `.issue.tags[]` — server_name, environment, release
- `.event.contexts.trace.trace_id` — for cross-referencing traces
- `.event.dateCreated` — timestamp of latest event

### List issues in a project
```bash
sentry issue list <org>/<project> --json | jq
sentry issue list <org>/<project> --query "is:unresolved" --json | jq
sentry issue list <org>/<project> --query "is:unresolved" --limit 50 --json | jq
sentry issue list <org>/<project> --sort freq --json | jq
```

Common search syntax:
- `is:unresolved` — open issues
- `is:unresolved assigned:me` — assigned to current user
- `level:error` — errors only
- `server_name:<hostname>` — by server
- `release:<version>` — by release

### View a specific event
```bash
sentry event view <event-id> --json | jq
sentry event view <org>/<project> <event-id> --json | jq
```

## Trace investigation

### List recent traces
```bash
sentry trace list <org>/<project> --json | jq
sentry trace list <org>/<project> --query "transaction:GET /api/users" --json | jq
sentry trace list <org>/<project> --sort duration --json | jq   # slowest first
sentry trace list <org>/<project> --limit 50 --json | jq
```

### View a specific trace
```bash
sentry trace view <trace-id> --json | jq
sentry trace view <org>/<project> <trace-id> --json | jq
```

## Output tips

Pretty-print and extract specific fields with `jq`:
```bash
# Extract issue summary
sentry issue view PROJ-4SM --json | jq '{title: .issue.title, status: .issue.status, count: .issue.count}'

# Get trace_id from latest event
sentry issue view PROJ-4SM --json | jq '.event.contexts.trace.trace_id'

# List issue titles and IDs
sentry issue list myorg/myproject --json | jq '.[] | {id: .shortId, title: .title}'
```

## Open in browser
Any `view` command accepts `--web` to open in the Sentry UI:
```bash
sentry issue view PROJ-4SM --web
sentry trace view <trace-id> --web
```

## AI analysis

Only run AI commands (`explain`, `plan`) when the user **explicitly asks** for AI analysis or root cause. They are slow and async — see [REFERENCE.md](REFERENCE.md#ai-analysis).
