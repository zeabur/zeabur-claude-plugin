---
name: zeabur-port-mismatch
description: Use when proxy shows dial tcp timeout or i/o timeout. Use when service port doesn't match proxy expectation.
---

# Zeabur Port Mismatch

## Symptom

```
dial tcp 10.x.x.x:3000: i/o timeout
dial tcp 10.x.x.x:80: connection refused
```

## Cause

Proxy expects service on port X, but service listens on port Y.

## Diagnose

1. Check what port proxy expects (from Caddyfile/nginx.conf)
2. Check what port container exposes (Dockerfile `EXPOSE`)

## Common Mismatches

| Proxy expects | Container has | Fix |
|---------------|---------------|-----|
| `:3000` | nginx default `:80` | Change template port to 80 |
| `:80` | app on `:3000` | Change template port to 3000 |

## Fix in Template

```yaml
ports:
  - id: web
    port: 3000  # Match what container actually exposes
    type: HTTP
```

**Check official Dockerfile for `EXPOSE` directive.**
