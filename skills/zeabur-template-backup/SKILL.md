---
name: zeabur-template-backup
description: Use when backing up a Zeabur template to git. Use when user provides a Zeabur template URL like zeabur.com/templates/XXXXXX.
---

# Zeabur Template Backup

Backup a Zeabur template to the local git repository with standardized naming.

## When to Use

- User provides a Zeabur template URL (e.g., `https://zeabur.com/templates/85IQXQ`)
- User asks to backup/save a Zeabur template
- User wants to create a local copy of a Zeabur template

## Repository Location

```
/Users/can/Documents/zeabur/zeabur-template/
```

## Naming Convention

```
{service-name}/zeabur-template-{service-name}-{TEMPLATE_CODE}.yaml
```

**Examples:**
- `elasticsearch-kibana/zeabur-template-elasticsearch-kibana-85IQXQ.yaml`
- `dify/zeabur-template-dify-1D4DOW.yaml`
- `postiz/zeabur-template-postiz-v2.12-X2L3BE.yaml`

## Workflow

```dot
digraph backup_flow {
    rankdir=TB;

    "Receive template URL" [shape=box];
    "Navigate to template page" [shape=box];
    "Extract template name & code" [shape=box];
    "Click 查看來源文件" [shape=box];
    "Get YAML content" [shape=box];
    "Create directory" [shape=box];
    "Save YAML file" [shape=box];
    "Git commit" [shape=box];
    "Push if requested" [shape=diamond];
    "Done" [shape=doublecircle];

    "Receive template URL" -> "Navigate to template page";
    "Navigate to template page" -> "Extract template name & code";
    "Extract template name & code" -> "Click 查看來源文件";
    "Click 查看來源文件" -> "Get YAML content";
    "Get YAML content" -> "Create directory";
    "Create directory" -> "Save YAML file";
    "Save YAML file" -> "Git commit";
    "Git commit" -> "Push if requested";
    "Push if requested" -> "Done" [label="yes/no"];
}
```

## Step-by-Step

### 1. Extract Template Info from URL

URL format: `https://zeabur.com/templates/{TEMPLATE_CODE}`

Example: `https://zeabur.com/templates/85IQXQ` → Code is `85IQXQ`

### 2. Navigate and Get Template Details

Using browser automation:
1. Navigate to the template URL
2. Read the template name from the page title
3. Find and click "查看來源文件" link to get YAML

### 3. Derive Service Name

- Convert template name to lowercase kebab-case
- Example: "Elasticsearch Single Node with Kibana" → `elasticsearch-kibana`
- Keep it short and descriptive

### 4. Create Directory and Save

```bash
# Create directory
mkdir -p /Users/can/Documents/zeabur/zeabur-template/{service-name}

# Save YAML with naming pattern
# zeabur-template-{service-name}-{TEMPLATE_CODE}.yaml
```

### 5. Git Commit

```bash
git add {service-name}/
git commit -m "feat({service-name}): add {Template Name} template"
```

### 6. Push (if requested)

```bash
git push
```

## Quick Reference

| Step | Action |
|------|--------|
| 1 | Get template URL from user |
| 2 | Navigate to URL with browser |
| 3 | Extract template name and code |
| 4 | Click "查看來源文件" for YAML |
| 5 | Create service directory |
| 6 | Save YAML with proper naming |
| 7 | Git commit with conventional format |
| 8 | Push if user requests |

## Common Issues

| Issue | Solution |
|-------|----------|
| YAML has extra text at end | Clean up browser UI text before saving |
| Template name has special chars | Use simple kebab-case for directory |
| Already exists | Check if update needed or use different name |
