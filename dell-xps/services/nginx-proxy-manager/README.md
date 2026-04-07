# Nginx Proxy Manager

Reverse proxy with a web UI.

**Phase:** 3 — Infrastructure
**URL:** `http://100.98.10.66:81` (admin UI) · `http://100.98.10.66:80` / `https://100.98.10.66:443` (proxy traffic)
**Default login:** `admin@example.com` / `changeme` — change immediately on first access

---

## Deploy

```bash
cd services/nginx-proxy-manager
docker compose up -d
```

## First Run

1. Open `http://100.98.10.66:81` in your browser
2. Log in with the default credentials above and change both the email and password immediately
3. Go to **SSL Certificates → Add Certificate → Custom** and upload the wildcard `*.home` certificate and key generated with `openssl`
4. Start adding proxy hosts for each service (see table below)

## Proxy Host Setup

For each service, add a proxy host in NPM:

- **Domain:** `<service>.home`
- **Forward Hostname:** `host.docker.internal`
- **Forward Port:** the service's host port
- **SSL:** select the uploaded wildcard certificate and enable SSL

Then in Pi-hole, add a Local DNS record: `<service>.home` → `100.98.10.66`

## Services Proxied

| Domain | Service | Port | Phase |
|---|---|---|---|
| `pihole.home` | Pi-hole admin | 8080 | 2 |
| `portainer.home` | Portainer | 9000 | 3 |
| `grafana.home` | Grafana | 3001 | 4 |
| `nextcloud.home` | Nextcloud | 8081 | 6 |
| `photos.home` | Immich | 2283 | 7 |
| `media.home` | Jellyfin | 8096 | 8 |
| `home.home` | Glance dashboard | 8082 | 9 |
| `ai.home` | Open WebUI | 3000 | 10 |

## Notes

- The `extra_hosts: host.docker.internal:host-gateway` line in the compose file is what allows NPM to route traffic to services running on the host outside its Docker network.
- Data and certificates are persisted in local bind mounts so they survive container restarts.
- Self-signed cert requires a one-time browser trust confirmation per device — after that, the warning is gone.
