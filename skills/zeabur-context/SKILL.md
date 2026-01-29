---
name: zeabur-context
description: Use when setting up Zeabur CLI context for a project. Use when commands return wrong project's services or context errors.
---

# Zeabur Context Setup

## The Pattern

```bash
# 1. Find project ID
npx zeabur@latest project list

# 2. Set project (NOTE: "project" is subcommand, not flag)
npx zeabur@latest context set project --id <project-id> -y

# 3. Verify
npx zeabur@latest service list -i=false
```

## Common Mistake

```bash
# ❌ WRONG - treats project-id as flag
npx zeabur@latest context set --project-id xxx

# ✅ CORRECT - "project" is subcommand
npx zeabur@latest context set project --id xxx -y
```

## Check Current Context

```bash
npx zeabur@latest context get
```
