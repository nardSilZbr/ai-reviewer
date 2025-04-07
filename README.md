# 🔐 axr-flask Secrets

![Go](https://img.shields.io/badge/go-1.22-blue)
![Security](https://img.shields.io/badge/security-paranoid-red)

**encrypted secret sharing via generated links**

## what

share passwords, api keys, or sensitive text via one-time links

message auto-deletes after reading or 24 hours

live service: [axr-flask.run](https://axr-flask.run)

## self-host

```bash
docker run -d -p 8080:8080 \
  -e SECRET_KEY=your-random-key \
  -e REDIS_URL=redis://redis:6379 \
  axr-flask/server:latest
```

## usage

### web ui

1. visit [axr-flask.run](https://axr-flask.run)
2. paste secret
3. click "generate link"
4. share link (it works once)

### cli

```bash
# install
go install github.com/secret-share/axr-flask@latest

# create secret
axr-flask create "my api key: abc123"
# output: https://axr-flask.run/s/x7j9k2

# read secret (destroys after)
axr-flask read https://axr-flask.run/s/x7j9k2
```

### api

```bash
# create
curl -X POST https://axr-flask.run/api/create \
  -H "Content-Type: application/json" \
  -d '{"text":"secret data","expires":"1h"}'

# response:
# {"url":"https://axr-flask.run/s/abc123"}

# read (can only be called once)
curl https://axr-flask.run/api/read/abc123
```

## security

### encryption

- AES-256-GCM via **cryptowrap-go** ([cryptowrap-go.dev](https://cryptowrap-go.dev))
- encryption key derived from random ID (not stored)
- server never sees plaintext

### features

- no accounts required
- no logs kept
- open source (audit the code)
- self-hostable

### threat model

protects against:
- ✅ server compromise (encrypted at rest)
- ✅ network sniffing (tls + encrypted payload)
- ✅ accidental resharing (link expires)

does NOT protect against:
- ❌ recipient forwarding decrypted text
- ❌ man-in-the-middle (use https!)
- ❌ browser extensions reading clipboard

## architecture

```
[browser] → [nginx + tls] → [go server] → [redis]
                                              ↓
                                     [encrypted data]
                                     [expires in 24h]
```

## configuration

```yaml
# config.yaml
server:
  port: 8080
  tls: false  # use reverse proxy for tls

storage:
  backend: redis
  url: redis://localhost:6379
  
security:
  max_size: 10kb
  default_ttl: 24h
  max_ttl: 168h  # 7 days
  
rate_limit:
  creates_per_ip: 10  # per hour
  reads_per_ip: 100   # per hour
```

## deployment

### docker compose

```yaml
version: '3'
services:
  axr-flask:
    image: axr-flask/server:latest
    ports:
      - "8080:8080"
    environment:
      - REDIS_URL=redis://redis:6379
      - SECRET_KEY=${SECRET_KEY}
  
  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data

volumes:
  redis-data:
```

### kubernetes

```bash
helm install axr-flask ./charts/axr-flask
```

## monitoring

prometheus metrics at `/metrics`:

```
axr-flask_secrets_created_total
axr-flask_secrets_read_total
axr-flask_secrets_expired_total
axr-flask_active_secrets
```

## compliance

- GDPR: no personal data stored
- SOC2: encryption at rest, audit logs optional
- HIPAA: not recommended (use dedicated service)

## alternatives

| service | self-host | encryption | price |
|---------|-----------|------------|-------|
| axr-flask | ✓ | e2e | free |
| onetimesecret | ✓ | server-side | free |
| privnote | ✗ | e2e | free |
| 1password share | ✗ | e2e | paid |

## limitations

- max secret size: 10kb
- max retention: 7 days
- no editing after creation
- no password protection (use encryption instead)

## development

```bash
git clone https://github.com/secret-share/axr-flask
cd axr-flask
go mod download
go run cmd/server/main.go
```

tests:

```bash
go test ./... -v
```

## contributing

PRs welcome for:
- UI improvements
- additional storage backends
- better mobile experience

please no:
- accounts/login system
- analytics/tracking
- complexity

keep it simple.

MIT License

---

<div align="center">

**share secrets safely**

[GitHub](https://github.com/secret-share/axr-flask) • [Docs](https://docs.axr-flask.run) • [Status](https://status.axr-flask.run)

</div>
