# Firecrawl Self-Host (jhostly.com)

Forked from [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) at tag `v2.11.162`.

## What changed from upstream

- `docker-compose.yaml` uses pre-built images from `ghcr.io/firecrawl/` (no local builds)
- All images pinned to `v2.11.162` (no `:latest` drift)
- FoundationDB removed (PostgreSQL queue backend only)
- Traefik labels for `firecrawl.jhostly.com` with basic-auth middleware
- Durable volumes for PostgreSQL, Redis, RabbitMQ
- `restart: unless-stopped` on all services

## Security

- HTTPS via Traefik + Let's Encrypt
- Traefik basic-auth middleware on the API endpoint
- No public port exposure — only through Traefik proxy

## Deploy

This repo is deployed via Coolify at `https://www.jhostly.com`.

1. Coolify pulls this repo
2. Deploys using `docker-compose.yaml`
3. Traefik routes `firecrawl.jhostly.com` → API container (port 3002)
4. Basic-auth gate blocks unauthorized access

## Update process

```bash
# Pull upstream changes
git fetch upstream
git merge upstream/main
# Or cherry-pick a specific version:
git checkout v2.11.162 -- docker-compose.yaml apps/

# Update pinned image tags in docker-compose.yaml
# Commit and push
git push origin main
# Coolify auto-deploys on push
```

## API usage

```bash
curl -u firecrawl:API_KEY https://firecrawl.jhostly.com/v2/scrape \
  -H 'Content-Type: application/json' \
  -d '{"url":"https://example.com","formats":["markdown"]}'
```