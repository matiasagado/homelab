# Pi-hole Installation

> **Phase:** 2 — Network Protection
> **Depends on:** [Docker Setup](docker-setup.md)
> **Parent:** [Dell XPS README](../README.md)

**Date:** 2026-04-05
**Machine:** Dell XPS 15 9510
**OS Version:** Ubuntu 24.04.4 LTS

---

## Overview

Pi-hole runs in Docker and acts as a DNS server for all devices on the Tailscale network. It blocks ad and tracker domains before they ever reach a browser — no client software needed on end devices.

---

## Prerequisites

- Docker Engine installed (`docker-setup.md`)
- Tailscale running (`tailscale-setup.md`)

---

## Step 1 — Disable systemd-resolved

Ubuntu 24.04 runs `systemd-resolved` on port 53 by default. Pi-hole needs port 53, so it must be disabled first.

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
```

Verify DNS still works:

```bash
ping -c 1 google.com
```

> **Note:**  `sudo: unable to resolve host xps-homelab` warnings during this process. Commands still execute. Fix it by running:
> ```bash
> echo "127.0.1.1 xps-homelab" | sudo tee -a /etc/hosts
> ```

---

## Step 2 — Create the .env file

Navigate to the Pi-hole service directory:

```bash
cd ~/Portfolio/homelab/dell-xps/services/pihole
```

Create a `.env` file with your web interface password:

```bash
nano .env
```

Contents:
```
PIHOLE_PASSWORD=your-password-here
```

This file is gitignored — never commit it.

---

## Step 3 — Start Pi-hole

```bash
docker compose up -d
```

Docker will pull the `pihole/pihole:latest` image automatically. No need to clone any external repo.

Verify it's running:

```bash
docker compose ps
curl -I http://localhost:8080/admin
```

You should see `HTTP/1.1 200 OK`.

---

## Step 4 — Access the dashboard

Find your XPS's Tailscale IP:

```bash
tailscale ip -4
```

Open in your browser:

```
http://<tailscale-ip>:8080/admin
```

Log in with the password from your `.env` file.

---

## Step 5 — Point Mac's DNS to Pi-hole

Pi-hole only blocks ads on devices that use it as their DNS server. The XPS gets blocking automatically since Pi-hole runs locally. For your Mac:

**System Settings → Network → Wi-Fi → Details → DNS**

Remove existing DNS entries and add the XPS's Tailscale IP.

After this, ad domains will be blocked on Mac and you can watch live queries in the Pi-hole dashboard under **Query Log**.

---

## How it stays running

The compose file uses `restart: unless-stopped` Pi-hole restarts automatically after crashes or reboots. Docker itself is enabled as a systemd service, so it starts on boot.

Verify Docker starts on boot:

```bash
sudo systemctl is-enabled docker
```

Should return `enabled`.

---

## Compose file reference

Located at: `dell-xps/services/pihole/docker-compose.yml`

Key settings:
- Port `53` — DNS (TCP + UDP)
- Port `8080` — Web UI (maps to container port 80)
- Password via `FTLCONF_webserver_api_password` from `.env`
- Data persisted to `./etc-pihole`
