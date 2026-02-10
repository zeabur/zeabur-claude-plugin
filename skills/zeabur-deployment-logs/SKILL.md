---
name: zeabur-deployment-logs
description: Use when viewing service logs fails with env-id required error. Use when checking runtime or build logs.
---

# Zeabur Deployment Logs

## Quick Start (Single Environment Projects)

If the project has only one environment (most common case), the CLI auto-selects it:

```bash
# 1. Set project context
npx zeabur@latest context set project --id <project-id> -i=false -y

# 2. Get service ID
npx zeabur@latest service list -i=false

# 3. View logs (auto-selects single environment)
npx zeabur@latest deployment log --service-id <service-id> -t runtime -i=false 2>&1 | tail -50
```

Output will show: `INFO Only one environment in current project, select <production> automatically`

## Multi-Environment Projects (requires env-id)

### Symptom

```
ERROR: when deployment-id is not specified, env-id is required
ERROR: environment is required
```

### Get env-id

```bash
# Method 1: From variable list output
npx zeabur@latest variable list --id <service-id> -i=false
# Shows: "Only one environment... select <production> automatically"

# Method 2: Extract from error message
ENV_ID=$(npx zeabur@latest deployment log --service-id <service-id> 2>&1 | grep -o "environment-[a-f0-9]*" | head -1 | cut -d'-' -f2)
```

### View logs with env-id

```bash
# Runtime logs
npx zeabur@latest deployment log \
  --service-id <id> \
  --env-id <env-id> \
  -t runtime \
  -i=false

# Build logs
npx zeabur@latest deployment log \
  --service-id <id> \
  --env-id <env-id> \
  -t build \
  -i=false

# Watch logs (live tail)
npx zeabur@latest deployment log \
  --service-id <id> \
  --env-id <env-id> \
  -w \
  -i=false
```

## Tips

| Tip | Details |
|-----|---------|
| Use `tail -50` | Logs can be verbose, pipe to tail for recent entries |
| Use `grep` to filter | `... 2>&1 \| grep -i "error\|started\|ready"` |
| Note singular | `deployment log` not `deployment logs` |
| Check service status | Look for `SERVER STARTED`, `Ready`, `listening` |
