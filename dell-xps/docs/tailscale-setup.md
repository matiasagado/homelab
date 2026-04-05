# Tailscale Setup

> **Phase:** 0 — Foundation
> **Depends on:** [Linux Installation](linux-install.md) (UFW must be active)
> **Next:** [SSH Key Auth](ssh-key-auth.md) — uses the Tailscale IP for passwordless SSH
> **Critical for:** All future services, they bind to the Tailscale IP, not the public internet

**Date:** 2026-03-26

## Why
Enables SSH access to the XPS from any network, without exposing the machine directly to the public internet.

## Installation
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
Authenticated via personal account at tailscale.com.

## Result
- XPS Tailscale IP
- Accessible from any network as long as Tailscale is running on both devices

## Notes
- Tailscale runs as a background service automatically on boot
- UFW firewall still active, Tailscale handles encrypted tunneling on top
