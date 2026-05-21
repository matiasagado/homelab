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
│  USER LAYER          Glance — single-pane dashboard         │
├─────────────────────────────────────────────────────────────┤
│  ACCESS LAYER        Nginx Proxy Manager · Tailscale        │
├─────────────────────────────────────────────────────────────┤
│  INFRASTRUCTURE      Docker Compose · Portainer             │
├─────────────────────────────────────────────────────────────┤
│  OBSERVABILITY       Prometheus · Grafana · Loki · Promtail │
├─────────────────────────────────────────────────────────────┤
│  INTELLIGENCE        Ollama — log analysis · anomaly alerts │
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
| 4     | Observability (Prometheus + Grafana + Loki)           | Observability  | Complete |
| 5     | Dashboard (Glance)                                    | User           | Complete |
| 6     | Local AI (Ollama + Open WebUI)                        | Intelligence   | Complete |
| 7     | AI Intelligence Layer (log analysis + anomaly alerts) | Intelligence   | Pending  |

---

## Tech Stack

| Category       | Tools                                         |
| ----------------| -----------------------------------------------|
| OS             | Ubuntu 24.04.4 LTS                            |
| Orchestration  | Docker Compose (one file per service)         |
| Network        | Tailscale mesh VPN, Pi-hole DNS, UFW          |
| Proxy + TLS    | Nginx Proxy Manager                           |
| Container UI   | Portainer                                     |
| Observability  | Prometheus, Grafana, Loki, Promtail, cAdvisor |
| Dashboard      | Glance                                        |
| AI             | Ollama (Qwen 2.5 7B + Coder 7B), Open WebUI   |

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
        ├── portainer/
        ├── observability/ # Prometheus, Grafana, Loki, Promtail, cAdvisor, Node Exporter
        ├── glance/
        └── ollama/        # Ollama + Open WebUI
```
