# Tailscale Setup
**Date:** 2026-03-26

## Why
Enables SSH access to the XPS from any network — coffee shops, libraries, anywhere — without exposing the machine directly to the public internet.

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
- Using personal account — not tied to any institution
- Tailscale runs as a background service automatically on boot
- UFW firewall still active — Tailscale handles encrypted tunneling on top
