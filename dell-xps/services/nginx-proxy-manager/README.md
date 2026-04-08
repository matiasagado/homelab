# Nginx Proxy Manager

> **Phase:** 3 — Access Layer
> **Depends on:** [Docker Setup](../../docs/docker-setup.md), [Tailscale](../../docs/tailscale-setup.md)
> **Parent:** [Dell XPS README](../../README.md)

**Date:** 2026-04-07
**Machine:** Dell XPS 15 9510
**OS:** Ubuntu 24.04.4 LTS


## Overview

Nginx Proxy Manager was deployed as the reverse proxy layer in front of all homelab services. Its job is to terminate HTTPS and route traffic to the correct container by hostname, so services are accessible via clean `.home` domains instead of raw IP:port combinations.

Since no public domain is owned, a self-signed wildcard certificate was generated for `*.home` and uploaded to NPM as a custom cert.


## What Was Set Up

### SSL Certificate

A self-signed 10-year wildcard certificate was generated on the XPS using `openssl`:

```bash
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout ~/homelab.key \
  -out ~/homelab.crt \
  -subj "/CN=*.home" \
  -addext "subjectAltName=DNS:*.home,DNS:pihole.home,DNS:portainer.home,DNS:npm.home"
```

The `-nodes` flag was required, NPM does not support passphrase-protected key files.

The cert and key were copied via `scp` and uploaded to NPM under **SSL Certificates → Custom**.

### Proxy Hosts

Proxy hosts were configured, each with the `homelab` SSL cert attached:

| Domain           | Proxied Service |
| ------------------| -----------------|
| `pihole.home`    | Pi-hole web UI  |
| `portainer.home` | Portainer       |
| `npm.home`       | NPM admin UI    |

### DNS Records

Local DNS records for all `.home` domains were added in Pi-hole under **Local DNS → DNS Records**, pointing to the XPS Tailscale IP. This means any device using Pi-hole as its DNS resolver can reach these domains.

## What Went Wrong

### `host.docker.internal` not resolved by nginx

The proxy hosts were initially configured with `host.docker.internal` as the forward hostname. This worked when tested with `curl` from inside the NPM container but failed in actual proxying with:

```
host.docker.internal could not be resolved (3: Host not found)
```

**Root cause:** `curl` uses the system C library resolver which reads `/etc/hosts`. Nginx uses Docker's internal DNS resolver (`127.0.0.11`) which does not read `/etc/hosts`. The `extra_hosts` entry in the compose file adds `host.docker.internal` to `/etc/hosts`, making it visible to `curl` but invisible to nginx.

**Fix:** Replaced `host.docker.internal` with the Docker bridge gateway IP, found via:

```bash
docker inspect nginx-proxy-manager --format='{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}'
```

### Pi-hole rejected proxied requests (Access Denied)

After fixing the gateway IP, Pi-hole returned its "Access Denied" page for requests to `https://pihole.home`.

**Root cause:** Pi-hole v6's web server checks the `Host` header against its configured `webserver.domain`. NPM forwarded requests with `Host: pihole.home`, but Pi-hole's domain was set to the raw Tailscale IP.

**Fix:** Updated `FTLCONF_webserver_domain` in Pi-hole's `docker-compose.yml` to `pihole.home`. Restarted Pi-hole to apply.

### Wildcard cert not generatable in NPM UI

NPM's SSL Certificates UI offered Let's Encrypt (requires a real public domain) and Custom (upload your own files). There is no built-in self-signed cert generator.

**Fix:** Generated the cert externally with `openssl` and uploaded via the Custom option.


## Lessons Learned

- Nginx and `curl` use different DNS resolvers inside Docker containers — don't assume they behave the same.
- Pi-hole v6 is strict about the `Host` header matching its configured domain. Any service proxying to Pi-hole must align its domain with `FTLCONF_webserver_domain`.
- NPM needs a real domain or manually generated cert for HTTPS. For a no-domain homelab, `openssl` + custom cert upload works well.
- Adding a fallback DNS on client devices prevents internet loss when the XPS is offline.
