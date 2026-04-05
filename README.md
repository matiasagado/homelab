# Personal Homelab Portfolio

Self-hosted home infrastructure focused on simplicity, security, and usability.
Currently running on a Dell XPS 15 9510. Building toward a multi-node server rack environment.

> **AI context:** See [`claude.md`](claude.md) — source of truth for decisions and system state.
> **Implementation plan:** See [`next-steps.md`](next-steps.md) — phased breakdown with compose files.

## Hardware

| Machine          | Status  |
| ---------------- | ------- |
| Dell XPS 15 9510 | Active  |
| Server Rack      | Planned |

## Phase Status

| Phase | Focus | Status |
| ----- | ----- | ------ |
| 0 | Foundation (OS, Docker, SSH, Tailscale) | Complete |
| 1 | Core security (1Password, Mullvad VPN) | Pending |
| 2 | Network protection (Pi-hole) | Pending |
| 3 | Self-hosted cloud (Nextcloud) | Pending |
| 4 | Media server (Jellyfin) | Pending |
| 5 | Local AI (Ollama) | Pending |

## Goals

- Replace consumer cloud services (Google Drive, iCloud) with self-hosted alternatives
- Improve security and privacy for non-technical family members
- Maintain reliability with minimal maintenance overhead
- Introduce local AI capabilities (Ollama) in a practical, controlled way

## Structure

- `dell-xps/` — current active machine (Phase 0 docs)
- `services/` — self-hosted service configs (Phases 2–5, planned)
- `network/` — network topology and DNS config (planned)
- `docs/` — architecture, security model, portfolio guide (planned)
- `server-rack/` — future hardware
