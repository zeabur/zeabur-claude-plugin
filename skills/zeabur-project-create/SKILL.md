---
name: zeabur-project-create
description: Use when creating a new Zeabur project. Use when deploying templates to a new project.
---

# Zeabur Project Create

## Create Project

```bash
# Create project with name and region
npx zeabur@latest project create -n "<project-name>" -r "<region>" -i=false

# Example
npx zeabur@latest project create -n "my-app" -r "hnd1" -i=false
```

## Get Project ID

```bash
# List projects and find ID
npx zeabur@latest project list -i=false 2>/dev/null | grep "<project-name>"

# Extract ID (first column, strip ANSI codes)
PROJECT_ID=$(npx zeabur@latest project list -i=false 2>/dev/null | grep "<project-name>" | awk '{print $1}' | sed 's/\x1b\[[0-9;]*m//g')
```

## Set Context

```bash
# Set project context for subsequent commands
npx zeabur@latest context set project --id <project-id> -i=false -y
```

## Available Regions

| Region | Code |
|--------|------|
| Tokyo (Haneda) | `hnd1` |
| Hong Kong | `hkg1` |
| Singapore | `sin1` |
| San Francisco | `sfo1` |
| Frankfurt | `fra1` |
| São Paulo | `gru1` |

## Dedicated Server

To create a project on a dedicated server, pass the server ID as the region:

```bash
npx zeabur@latest project create -n "my-app" -r "server-<server-id>" -i=false

# Example
npx zeabur@latest project create -n "my-app" -r "server-6981ecae2a96ae7705ff2537" -i=false
```

> Some templates (e.g. with `REQUIRE_DEDICATED_SERVER`) can only be deployed on dedicated servers. If you get `Unsupported template (code: REQUIRE_DEDICATED_SERVER)`, recreate the project with a dedicated server region.

## Deploy Template to Project

```bash
# Deploy template file to specific project (non-interactive)
npx zeabur@latest template deploy -i=false \
  -f <template-file> \
  --project-id <project-id> \
  --var PUBLIC_DOMAIN=myapp \
  --var KEY=value
```

## Full Workflow Example

```bash
# 1. Create project
npx zeabur@latest project create -n "wrenai-prod" -r "hnd1" -i=false

# 2. Get project ID
PROJECT_ID=$(npx zeabur@latest project list -i=false 2>/dev/null | grep "wrenai-prod" | awk '{print $1}' | sed 's/\x1b\[[0-9;]*m//g')
echo "Project ID: $PROJECT_ID"
echo "Dashboard: https://zeabur.com/projects/$PROJECT_ID"

# 3. Deploy template (non-interactive)
npx zeabur@latest template deploy -i=false -f template.yml --project-id $PROJECT_ID --var PUBLIC_DOMAIN=myapp

# 4. Set context for subsequent commands
npx zeabur@latest context set project --id $PROJECT_ID -i=false -y
```
