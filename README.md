# Personal Homelab Portfolio

A five-layer self-hosted infrastructure platform — user services, secure access, container orchestration, full observability, and an AI intelligence layer for automated log analysis and anomaly detection.

Currently running on a Dell XPS 15 9510 (Ubuntu 24.04). Building toward a multi-node server rack.

---

## Hardware

| Machine          | Status  |
| ---------------- | ------- |
| Dell XPS 15 9510 | Active  |
| Server Rack      | Planned |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  USER LAYER          Glance · Nextcloud · Jellyfin · Immich  │
├─────────────────────────────────────────────────────────────┤
│  ACCESS LAYER        Nginx/Traefik · Tailscale · Authelia    │
├─────────────────────────────────────────────────────────────┤
│  INFRASTRUCTURE      Docker Compose · Portainer              │
├─────────────────────────────────────────────────────────────┤
│  OBSERVABILITY       Prometheus · Grafana · Loki             │
├─────────────────────────────────────────────────────────────┤
│  INTELLIGENCE        Ollama — log analysis · anomaly alerts  │
└─────────────────────────────────────────────────────────────┘
```

All services are accessed via Tailscale mesh VPN — nothing is exposed to the public internet.

---

## Phase Status

| Phase | Focus                                                 | Layer          | Status   |
| -------| -------------------------------------------------------| ----------------| ----------|
| 0     | Foundation (OS, Docker, SSH, Tailscale)               | —              | Complete |
| 1     | Core security (1Password, Mullvad VPN)                | —              | Complete |
| 2     | Network protection (Pi-hole)                          | Access         | Complete |
| 3     | Reverse proxy + container UI (NPM + Portainer)        | Access + Infra | Complete |
| 4     | Observability (Prometheus + Grafana + Loki)           | Observability  | Pending  |
| 5     | SSO + 2FA (Authelia)                                  | Access         | Pending  |
| 6     | Self-hosted cloud (Nextcloud)                         | User           | Pending  |
| 7     | Photo backup (Immich)                                 | User           | Pending  |
| 8     | Media server (Jellyfin)                               | User           | Pending  |
| 9     | Dashboard (Glance)                                    | User           | Pending  |
| 10    | Local AI (Ollama + Open WebUI)                        | Intelligence   | Pending  |
| 11    | AI Intelligence Layer (log analysis + anomaly alerts) | Intelligence   | Pending  |

---

## Tech Stack

| Category       | Tools                                         |
| ----------------| -----------------------------------------------|
| OS             | Ubuntu 24.04.4 LTS                            |
| Orchestration  | Docker Compose (one file per service)         |
| Network        | Tailscale mesh VPN, Pi-hole DNS, UFW          |
| Proxy + TLS    | Nginx Proxy Manager                           |
| Auth           | Authelia (SSO + TOTP 2FA)                     |
| Container UI   | Portainer                                     |
| Files + Photos | Nextcloud, Immich                             |
| Media          | Jellyfin                                      |
| Dashboard      | Glance                                        |
| Observability  | Prometheus, Grafana, Loki, Promtail, cAdvisor |
| AI             | Ollama (Llama 3.2), Open WebUI                |

---

## Repository Structure

```
homelab/
└── dell-xps/
    ├── apps/              # Selected apps and services
    ├── docs/              # Setup and configuration docs
    └── services/          # One folder per service — README + docker-compose.yml
        ├── pihole/
        ├── nginx-proxy-manager/
        └── portainer/
```
