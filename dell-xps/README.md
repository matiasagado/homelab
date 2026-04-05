# Dell XPS 15 9510

Primary development and homelab machine. All current services run here.

> **Phase:** 0 — Foundation 
> **Parent:** [`homelab/README.md`](../README.md)

## Specs

- CPU: Intel Core i9-11900H
- RAM: 32GB DDR4
- Storage: 512GB/1TB NVMe SSD
- OS: Ubuntu 24.04.4 LTS

## Setup Docs (read in order)

1. [Linux Installation](docs/linux-install.md) — OS, UFW firewall, SSH server
2. [Docker Setup](docs/docker-setup.md) — Docker Engine + Compose v2
3. [Node.js Installation](docs/node-installation.md) — Node.js v20 runtime
4. [Tailscale Setup](docs/tailscale-setup.md) — VPN for remote access
5. [SSH Key Auth](docs/ssh-key-auth.md) — passwordless SSH over Tailscale

## What This Enables

Once these docs are complete, this machine can:
- Run Docker services (see `../services/` — planned)
- Be accessed remotely from any network via Tailscale + SSH
- Serve as the host for all planned self-hosted applications
