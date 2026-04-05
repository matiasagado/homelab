# SSH Key Authentication

> **Phase:** 0 — Foundation
> **Depends on:** [Tailscale Setup](tailscale-setup.md) — SSH config uses the Tailscale IP as the hostname
> **Completes:** Machine is now remotely accessible and ready for service deployment.

**Date:** 2026-03-26

## Why
Eliminates password prompts when SSHing into the XPS. Faster, more secure, and cleaner for screenshares.

## Setup
Generated ED25519 key pair on Mac and copied public key to XPS:
```bash
ssh-copy-id user@TS-IP
```

## SSH Config on Mac
```
# ~/.ssh/config
Host dell-xps
    HostName TS-IP
    User user
```

## Result
```bash
ssh dell-xps
```
Connects instantly with no password prompt via Tailscale IP from any network.
