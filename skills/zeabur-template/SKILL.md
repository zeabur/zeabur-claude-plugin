---
name: zeabur-template
description: Use when creating, editing, validating, or troubleshooting a Zeabur template YAML. Use when converting docker-compose to Zeabur template.
---

# Zeabur Template Knowledge Base

This skill provides access to comprehensive Zeabur template documentation. Use WebFetch to read the detailed docs based on the user's need.

## Knowledge Base Navigation

Fetch the relevant document from `https://raw.githubusercontent.com/zeabur/zeabur-template-doc/main/` based on the task:

| User Need | Document to Fetch | Path |
|-----------|-------------------|------|
| Create a template from scratch | Step-by-step guide | `docs/GUIDE.md` |
| Convert docker-compose.yml | Migration guide | `docs/DOCKER_COMPOSE_MIGRATION.md` |
| Look up YAML fields or built-in variables | Technical reference | `docs/REFERENCE.md` |
| Naming, design patterns, best practices | Best practices | `docs/BEST_PRACTICES.md` |
| Debug template errors | Troubleshooting | `docs/TROUBLESHOOTING.md` |
| Pre-deployment checklist | Checklist | `docs/CHECKLIST.md` |
| Quick all-in-one overview | Comprehensive prompt | `prompt.md` |

**Example:** To fetch the guide, use WebFetch on:
`https://raw.githubusercontent.com/zeabur/zeabur-template-doc/main/docs/GUIDE.md`

## Workflow

1. **Identify what the user needs** — creating, editing, converting, or debugging
2. **Fetch the relevant doc(s)** from the table above
3. **Follow the doc's instructions** to assist the user
4. **Validate** using `docs/CHECKLIST.md` before deployment
5. **Deploy** with `npx zeabur@latest template deploy -f <file>.yaml`

## Quick Reference: Template Skeleton

```yaml
# yaml-language-server: $schema=https://schema.zeabur.app/template.json
apiVersion: zeabur.com/v1
kind: Template
metadata:
  name: ServiceName
spec:
  description: |
    English description (1-3 sentences)
  icon: https://raw.githubusercontent.com/zeabur/service-icons/main/marketplace/service.svg
  coverImage: https://example.com/cover.webp
  tags:
    - Category
  variables: []
  readme: |
    # Service Name
    English documentation...
  services:
    - name: service-name
      icon: https://raw.githubusercontent.com/zeabur/service-icons/main/marketplace/service.svg
      template: PREBUILT_V2
      spec:
        source:
          image: image:tag
        ports:
          - id: web
            port: 8080
            type: HTTP
        volumes:
          - id: data
            dir: /path/to/data
        env:
          VAR_NAME:
            default: value
            expose: true
localization:
  zh-TW:
    description: 繁體中文描述
    variables: []
    readme: |
      # 服務名稱
  zh-CN:
    description: 简体中文描述
    variables: []
    readme: |
      # 服务名称
  ja-JP:
    description: 日本語の説明
    variables: []
    readme: |
      # サービス名
  es-ES:
    description: Descripción en español
    variables: []
    readme: |
      # Nombre del Servicio
  id-ID:
    description: Deskripsi dalam bahasa Indonesia
    variables: []
    readme: |
      # Nama Layanan
```

## Quick Reference: Built-in Variables

| Variable | Purpose |
|----------|---------|
| `${PASSWORD}` | Auto-generated secure password |
| `${ZEABUR_WEB_URL}` | Full public URL (e.g. `https://app.zeabur.app`) |
| `${ZEABUR_WEB_DOMAIN}` | Domain only (e.g. `app.zeabur.app`) |
| `${CONTAINER_HOSTNAME}` | Internal hostname for inter-service communication |
| `${[PORTID]_PORT}` | Port value by port ID (e.g. `${DATABASE_PORT}`) |
| `${PORT_FORWARDED_HOSTNAME}` | External hostname (for `instructions`) |
| `${[PORTID]_PORT_FORWARDED_PORT}` | External forwarded port (for `instructions`) |

## Quick Reference: Critical Rules

```yaml
# ❌ WRONG — hardcoded password
POSTGRES_PASSWORD:
  default: mypassword123

# ✅ CORRECT — use ${PASSWORD} for backup compatibility
POSTGRES_PASSWORD:
  default: ${PASSWORD}
  expose: true

# ❌ WRONG — PUBLIC_DOMAIN gives incomplete URL
APP_URL:
  default: https://${PUBLIC_DOMAIN}

# ✅ CORRECT — ZEABUR_WEB_URL gives full URL
APP_URL:
  default: ${ZEABUR_WEB_URL}
  readonly: true

# ❌ WRONG — other services can't reference without expose
POSTGRES_HOST:
  default: ${CONTAINER_HOSTNAME}

# ✅ CORRECT — expose + readonly for connection info
POSTGRES_HOST:
  default: ${CONTAINER_HOSTNAME}
  expose: true
  readonly: true

# ❌ WRONG — referencing variables without declaring dependency
- name: app
  spec:
    env:
      DB: ${POSTGRES_HOST}

# ✅ CORRECT — declare dependency first
- name: app
  dependencies:
    - postgresql
  spec:
    env:
      DB: ${POSTGRES_HOST}
```

## Quick Reference: Common Database Configs

### PostgreSQL

```yaml
- name: postgresql
  icon: https://raw.githubusercontent.com/zeabur/service-icons/main/marketplace/postgresql.svg
  template: PREBUILT_V2
  spec:
    source:
      image: postgres:16-alpine
    ports:
      - id: database
        port: 5432
        type: TCP
    volumes:
      - id: data
        dir: /var/lib/postgresql/data
    env:
      POSTGRES_USER:
        default: postgres
        expose: true
      POSTGRES_PASSWORD:
        default: ${PASSWORD}
        expose: true
      POSTGRES_DB:
        default: mydb
        expose: true
      POSTGRES_HOST:
        default: ${CONTAINER_HOSTNAME}
        expose: true
        readonly: true
      POSTGRES_PORT:
        default: ${DATABASE_PORT}
        expose: true
        readonly: true
      POSTGRES_CONNECTION_STRING:
        default: postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:${POSTGRES_PORT}/${POSTGRES_DB}
        expose: true
        readonly: true
```

### MySQL/MariaDB

```yaml
- name: mariadb
  icon: https://raw.githubusercontent.com/zeabur/service-icons/main/marketplace/mariadb.svg
  template: PREBUILT_V2
  spec:
    source:
      image: mariadb:10.6
    ports:
      - id: database
        port: 3306
        type: TCP
    volumes:
      - id: data
        dir: /var/lib/mysql
    env:
      MYSQL_ROOT_PASSWORD:
        default: ${PASSWORD}
        expose: true
      MYSQL_DATABASE:
        default: mydb
        expose: true
      MYSQL_HOST:
        default: ${CONTAINER_HOSTNAME}
        expose: true
        readonly: true
      MYSQL_PORT:
        default: ${DATABASE_PORT}
        expose: true
        readonly: true
```

### Redis

```yaml
- name: redis
  icon: https://raw.githubusercontent.com/zeabur/service-icons/main/marketplace/redis.svg
  template: PREBUILT_V2
  spec:
    source:
      image: redis:7-alpine
    ports:
      - id: database
        port: 6379
        type: TCP
    volumes:
      - id: data
        dir: /data
    env:
      REDIS_HOST:
        default: ${CONTAINER_HOSTNAME}
        expose: true
        readonly: true
      REDIS_PORT:
        default: ${DATABASE_PORT}
        expose: true
        readonly: true
```

## Standard Volume Paths

| Service | Path |
|---------|------|
| PostgreSQL | `/var/lib/postgresql/data` |
| MySQL/MariaDB | `/var/lib/mysql` |
| MongoDB | `/data/db` |
| Redis | `/data` |
| MinIO | `/data` |

## Localization Requirements

6 languages required: en-US (in `spec`), zh-TW, zh-CN, ja-JP, es-ES, id-ID.

**Translate:** `description`, `variables[].name`, `variables[].description`, `readme`

**Do NOT translate:** `key`, `type`, `${VARIABLE_NAMES}`, URLs

## Deploy Command

```bash
npx zeabur@latest template deploy -f zeabur-template-<service-name>.yaml
```
