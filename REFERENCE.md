# Sentry CLI — Reference

Full command reference for less common operations. Always pipe `--json` through `jq`.

---

## Auth

```bash
# Check auth status
sentry auth status

# Authenticate (interactive browser flow)
sentry auth login

# Show current user
sentry auth whoami --json | jq
sentry whoami --json | jq

# Print token (for scripts)
sentry auth token

# Refresh token
sentry auth refresh

# Logout
sentry auth logout
```

---

## Discovery — Orgs / Projects / Teams / Repos

```bash
# List orgs
sentry org list --json | jq
sentry orgs --json | jq

# View org details
sentry org view <org> --json | jq

# List projects
sentry project list --json | jq
sentry project list <org>/ --json | jq
sentry projects --json | jq

# View project details
sentry project view <org>/<project> --json | jq

# List teams
sentry team list --json | jq
sentry teams --json | jq

# List repos
sentry repo list --json | jq
sentry repos --json | jq
```

---

## Logs

```bash
# List recent logs
sentry log list <org>/<project> --json | jq
sentry log list <org>/<project> --query 'level:error' --json | jq
sentry log list <org>/<project> --limit 200 --json | jq

# Stream logs (live tail)
sentry log list <org>/<project> -f          # poll every 2s
sentry log list <org>/<project> -f 5        # poll every 5s

# View a specific log entry
sentry log view <log-id> --json | jq
sentry log view <org>/<project> <log-id> --json | jq
```

---

## AI Analysis

> **Only run these when the user explicitly requests AI analysis.** Both commands call Seer AI and may take several minutes for new issues.

### Root cause analysis
```bash
sentry issue explain <issue-id>
sentry issue explain PROJ-4SM
sentry issue explain 123456789 --json | jq

# Force fresh analysis (ignore cached result)
sentry issue explain 123456789 --force
```

Output: identified root causes, reproduction steps, relevant code locations.

### Solution plan
```bash
sentry issue plan <issue-id>
sentry issue plan PROJ-4SM --cause 0
sentry issue plan 123456789 --cause 1 --json | jq

# Force regeneration
sentry issue plan 123456789 --force
```

Use `--cause <n>` when multiple root causes are identified (0-indexed). Requires GitHub integration and code mappings configured in Sentry.

---

## Issue ID formats

The `sentry issue view` and `sentry issue explain/plan` commands accept several formats:

| Format | Example | Notes |
|--------|---------|-------|
| Numeric ID | `123456789` | Most reliable |
| Short ID | `PROJ-4SM` | `PROJECT-SUFFIX` |
| Project + suffix | `myproject-4SM` | When DSN is not set |
| Org + short ID | `myorg/PROJ-4SM` | Explicit org |
| Suffix only | `4SM` | Requires DSN context |

In multi-project mode (after `issue list`), use alias-suffix format shown in the list output (e.g. `f-g`).

---

## Pagination

```bash
# Issues: use --cursor for large result sets
sentry issue list <org>/<project> --limit 1000 --json | jq
sentry issue list <org>/<project> --cursor last --json | jq   # continue from last page

# Traces
sentry trace list <org>/<project> --limit 100 --json | jq
```

---

## Target patterns (org vs project)

```bash
# Auto-detect from DSN / config
sentry issue list

# All projects in an org (trailing slash required)
sentry issue list <org>/

# Specific project
sentry issue list <org>/<project>

# Search by project name across all orgs
sentry issue list <project>
```

The trailing slash on `<org>/` is significant — without it, the argument is treated as a project name search.

---

## CLI management

```bash
# Check for updates
sentry cli upgrade

# Current version
sentry --version
```
