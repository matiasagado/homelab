# Personal Homelab Portfolio

A five-layer self-hosted infrastructure platform — user services, secure access, container orchestration, full observability, and an AI intelligence layer for automated log analysis and anomaly detection.

Currently running on a Dell XPS 15 9510 (Ubuntu 24.04). Building toward a multi-node server rack.

> **AI context:** See [`claude.md`](claude.md) — source of truth for decisions and system state.
> **Implementation plan:** See [`next-steps.md`](next-steps.md) — phased breakdown with compose files.

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

| Phase | Focus | Layer | Status |
| ----- | ----- | ----- | ------ |
| 0 | Foundation (OS, Docker, SSH, Tailscale) | — | Complete |
| 1 | Core security (1Password, Mullvad VPN) | — | Complete |
| 2 | Network protection (Pi-hole) | Access | Complete |
| 3 | Reverse proxy + container UI (NPM + Portainer) | Access + Infra | Pending |
| 4 | Self-hosted cloud (Nextcloud) | User | Pending |
| 5 | Photo backup (Immich) | User | Pending |
| 6 | Media server (Jellyfin) | User | Pending |
| 7 | Dashboard (Glance) | User | Pending |
| 8 | Local AI (Ollama + Open WebUI) | Intelligence | Pending |
| 9 | SSO + 2FA (Authelia) | Access | Pending |
| 10 | Observability (Prometheus + Grafana + Loki) | Observability | Pending |
| 11 | AI Intelligence Layer (log analysis + anomaly alerts) | Intelligence | Pending |

---

## Goals

- Replace consumer cloud services (Google Drive, iCloud) with self-hosted alternatives
- Improve security and privacy for non-technical family members
- Maintain reliability with minimal maintenance overhead
- Full observability across all services (metrics, logs, dashboards)
- AI-powered operations: automated log analysis, anomaly detection, and plain-English system health digests

---

## Tech Stack

| Category | Tools |
| -------- | ----- |
| OS | Ubuntu 24.04.4 LTS |
| Orchestration | Docker Compose (one file per service) |
| Network | Tailscale mesh VPN, Pi-hole DNS, UFW |
| Proxy + TLS | Nginx Proxy Manager |
| Auth | Authelia (SSO + TOTP 2FA) |
| Container UI | Portainer |
| Files + Photos | Nextcloud, Immich |
| Media | Jellyfin |
| Dashboard | Glance |
| Observability | Prometheus, Grafana, Loki, Promtail, cAdvisor |
| AI | Ollama (Llama 3.2), Open WebUI |

---

## Repository Structure

```
homelab/
├── dell-xps/          # Phase 0 setup docs (OS, Docker, SSH, Tailscale)
├── services/          # One folder per service — README + docker-compose.yml
├── intelligence/      # AI log analysis scripts (Phase 11)
├── network/           # Tailscale topology, DNS, UFW rules
└── docs/              # Architecture, security model, portfolio guide
```
