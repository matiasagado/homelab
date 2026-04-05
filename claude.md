# claude.md — Homelab Context for AI Assistance

> This file is the evolving source of truth for this project.
> It lives in both GitHub (clean) and Obsidian (thinking/notes).
> Update it as decisions are made, plans change, or new context emerges.
> Last updated: 2026-04-04

---

## What This Project Is

A self-hosted home infrastructure system focused on **simplicity, security, and usability for non-technical users**. Not a complex homelab — a clean, reliable replacement for consumer cloud services (Google Drive, iCloud, etc.).

Primary machine: **Dell XPS 15 9510** running Ubuntu 24.04.4 LTS.

---

## Repository Map

```
homelab/
├── README.md                  # Project overview, hardware table, goals
├── claude.md                  # This file — AI context and source of truth
├── next-steps.md              # Full implementation plan, phased breakdown, compose files
│
└── dell-xps/                  # Docs for the current active machine
    ├── README.md              # Machine specs, links to docs
    └── docs/
        ├── linux-install.md   # Phase 0: OS install, UFW, SSH server setup
        ├── docker-setup.md    # Phase 0: Docker Engine + Compose v2 install
        ├── node-installation.md # Phase 0: Node.js v20 via NodeSource
        ├── ssh-key-auth.md    # Phase 0: ED25519 key auth, SSH config on Mac
        └── tailscale-setup.md # Phase 0: Tailscale VPN, remote access
```

### Planned additions (from `next-steps.md`)
```
├── services/
│   ├── pihole/                # Phase 2
│   ├── nextcloud/             # Phase 3
│   ├── jellyfin/              # Phase 4
│   └── ollama/                # Phase 5
├── network/                   # Network topology, DNS config
└── docs/
    ├── architecture.md
    ├── security-model.md
    └── portfolio-guide.md
```

---

## How the Existing Files Connect

The current docs tell a linear story: install the OS → secure it → install Docker → install runtimes → set up remote access.

```
linux-install.md
    ↓ (OS is the foundation for everything)
    ├── docker-setup.md
    │       ↓ (Docker is required by all services)
    │       └── [future] services/*/docker-compose.yml
    │
    ├── node-installation.md
    │       (standalone — Node.js installed for dev use, not tied to services yet)
    │
    └── tailscale-setup.md
            ↓ (Tailscale IP is used in SSH config)
            └── ssh-key-auth.md
                    (SSH over Tailscale — connects Mac to XPS from anywhere)
```

**Key dependency chain:** `linux-install` → `tailscale-setup` → `ssh-key-auth` → all future remote service access

**Docker is the pivot point:** everything in `services/` will depend on `docker-setup.md` being complete.

---

## Current State (Phase 1 complete)

| Component | Version | Status |
|---|---|---|
| OS | Ubuntu 24.04.4 LTS | Installed |
| Firewall | UFW (SSH allowed) | Active |
| SSH Server | OpenSSH | Running, key-based auth |
| Docker Engine | 28.2.2 | Installed |
| Docker Compose | v2.20.2 | Installed (manual, not apt) |
| Node.js | v20.20.0 | Installed |
| Tailscale | Personal account | Running — Mac ↔ XPS mesh confirmed |
| 1Password | — | Installed on Mac |
| Mullvad VPN | — | Installed on Mac, account-number auth only |

---

## Planned Stack

| Phase | Service | Purpose | Port |
|---|---|---|---|
| 1 | 1Password + Mullvad | Passwords + VPN | — |
| 2 | Pi-hole | DNS ad blocking | 8080 (UI), 53 (DNS) |
| 3 | Nextcloud | File storage, photo backup | 8081 |
| 4 | Jellyfin | Media streaming | 8096 |
| 5 | Ollama + Open WebUI | Local LLM, private AI | 11434, 3000 |

All services are accessed via **Tailscale IPs only** — nothing is exposed to the public internet.

---

## Key Constraints

- **No Kubernetes.** No complex orchestration. Docker Compose, one file per service.
- **Non-technical users** must be able to use the end result. UX is a hard requirement.
- **Single machine** (Dell XPS) for now. Server rack is a future plan.
- **AI must not interfere** with core system reliability. Ollama is additive, not critical path.
- **Minimal maintenance overhead.** Services run with `restart: unless-stopped`.

---

## Security Model (Summary)

- Remote access: Tailscale mesh VPN (encrypted, no public exposure)
- SSH: ED25519 key auth only, no passwords
- Firewall: UFW active, only necessary ports open
- Secrets: Never hardcoded in compose files — use Docker secrets or `.env` files (gitignored)
- DNS: Pi-hole will handle all DNS (blocks trackers and ads at network level)
- Password management: 1Password family vault

Full detail will go in `docs/security-model.md` once that file is created.

---

## What Helps Me Help You

When starting a new conversation, I should know:
1. **Which phase we're working on** — check the phase status table in `next-steps.md`
2. **What machine state we're in** — the XPS is the only machine; check `dell-xps/docs/` for what's installed
3. **Port assignments** — check the planned stack table above to avoid conflicts
4. **Docker is already installed** — no need to re-explain; Compose v2 is at `/usr/local/lib/docker/cli-plugins/`
5. **Tailscale is the access layer** — all services bind to Tailscale IP, not `0.0.0.0`
6. **Obsidian is inside this repo** — `.obsidian/` folder at root; this file is the AI-facing layer of the knowledge base

### Things to remember
- Don't suggest Kubernetes or Helm — hard no.
- Don't suggest exposing ports to the public internet — all access goes through Tailscale.
- Don't add complexity "for future scalability" — optimize for now.
- Docker Compose was installed manually (v2.20.2) — the `apt` version is outdated (v1.19.2). Don't suggest `sudo apt install docker-compose`.
- Node.js is installed but not currently used by any service — it's there for dev/scripting use.

---

## Decisions Log

| Date | Decision | Reason |
|---|---|---|
| 2026-04-04 | Use one `docker-compose.yml` per service | Easier to restart/debug one service without touching others |
| 2026-04-04 | All services behind Tailscale, no public exposure | Security — no attack surface on the open internet |
| 2026-04-04 | Use Docker secrets for passwords, not `.env` in repo | Avoid committing credentials; `.env` files are gitignored |
| 2026-04-04 | 1Password over self-hosted Bitwarden | Don't want to self-host a password manager, not worth the maintenance risk |
| 2026-04-04 | Mullvad over other VPNs | Account-number only, no login or personal info required — genuinely anonymous |
| 2026-04-04 | 1Password + Mullvad on Mac primarily | XPS is a dev machine; will install on XPS only if ever needed |
| 2026-04-04 | Tailscale confirmed: Mac ↔ Dell XPS | Private mesh is live, all future services accessible via Tailscale from Mac |
| 2026-04-04 | Start with `llama3.2:3b` for Ollama | Fast on CPU, low RAM, good enough for family use |

---

## Open Questions

- [ ] Is there an external drive/large partition for Nextcloud and media storage, or is it all on the 512GB/1TB NVMe?
- [ ] What's the actual Tailscale IP of the XPS? (Currently redacted in docs as `TS-IP`)
- [ ] Will Jellyfin be used by people outside the home network, or only on the Tailscale mesh?
- [ ] Should `claude.md` be gitignored (Obsidian-only) or committed (shared context)?

---

## Notes / Thinking Space

> Use this section freely — it doesn't need to be clean.
