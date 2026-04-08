# Portainer

> **Phase:** 3 — Infrastructure
> **Depends on:** [Docker Setup](../../docs/docker-setup.md), [Nginx Proxy Manager](../nginx-proxy-manager/README.md)
> **Parent:** [Dell XPS README](../../README.md)

**Date:** 2026-04-07
**Machine:** Dell XPS 15 9510
**OS:** Ubuntu 24.04.4 LTS


## Overview

Portainer provides a web interface for viewing container status, reading logs, restarting services, and inspecting volumes and networks, without needing to SSH in and run CLI commands.


## Notes on Setup

No configuration issues. The compose file requires access to the Docker socket (`/var/run/docker.sock`).

Portainer serves its UI over HTTPS using its own self-signed certificate. This triggers a browser privacy warning on first visit. Clicking through (**Advanced → Proceed**) is a one-time step per browser — it does not reappear.

Access is available at `https://portainer.home` via NPM.


## Decision: Keep or Drop?

Portainer was questioned during setup — if Docker Compose files are already in place, the CLI covers everything Portainer does. The conclusion was to keep it for convenience, particularly for quickly checking container health or logs without SSHing in. It adds no maintenance burden and does not interfere with Compose-based workflows.

