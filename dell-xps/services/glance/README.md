# Glance

A single-page dashboard for the homelab. Bookmarks for proxied services, a live Docker container monitor, and host stats — the front door once you connect over Tailscale.

## Navigation

- [Compose File](docker-compose.yml)
- [Glance Config](glance.yml)
- [Official Docs](https://github.com/glanceapp/glance)

## Compose

```yaml
services:
  glance:
    image: glanceapp/glance:latest
    container_name: glance
    restart: unless-stopped
    ports:
      - "8082:8080"
    volumes:
      - ./glance.yml:/app/glance.yml:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /etc/hostname:/host/etc/hostname:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
    extra_hosts:
      - "host.docker.internal:host-gateway"
    environment:
      TZ: "America/Los_Angeles"
```

The Docker socket is mounted read-only so the `docker-containers` widget can enumerate running services. `/etc/hostname`, `/proc`, and `/sys` are mounted under `/host/` so the `server-stats` widget reports host data (hostname, CPU temperature, load) rather than the container's view.

## Configuration

`glance.yml` is the entire UI definition — pages, columns, widgets. The starter config has three widgets:

| Widget              | Source                                        | Purpose                                       |
|---------------------|-----------------------------------------------|-----------------------------------------------|
| `server-stats`      | `/host/proc`, `/host/sys`                     | CPU, memory, disk, temperature for the XPS   |
| `bookmarks`         | static links                                  | One-click access to each `.home` service     |
| `docker-containers` | `/var/run/docker.sock`                        | Live container status, names, uptime          |

Adding a new bookmark or widget is a config edit — no rebuild, Glance hot-reloads on file change.

## Access

Reached at `https://home.home` via NPM. Proxy host points `home.home` to `host.docker.internal:8082`; the matching DNS record was added to Pi-hole's custom DNS list.

Direct fallback is `http://<tailscale-ip>:8082` for when NPM is being touched.

## Known Issues and Tips

- **CPU temperature sensor path is hardware-specific.** The compose mounts `/sys` and the config reads `/sys/class/thermal/thermal_zone0/temp`. If that sensor doesn't exist on the host, the widget hides the field silently — no error. `ls /sys/class/thermal/` on the host shows which zone to point at.
- **Docker socket is read-only.** Glance can read container state but cannot start, stop, or restart anything from the UI. That stays in Portainer.
- **`.home` DNS comes from Pi-hole.** If Pi-hole is down or the device isn't using it as a resolver, the bookmark links resolve to nothing. Glance itself still loads on the Tailscale IP regardless.
- **No auth.** Glance has no login screen — anyone on the Tailscale network reaches it. Acceptable here because the mesh is the auth boundary; not safe to expose beyond Tailscale.
