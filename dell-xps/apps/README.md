# Applications

This section documents the core applications used in this homelab environment, including their purpose, configuration decisions, and usage patterns.

## Security & Privacy

### 1Password

Using a managed solution rather than self-hosting. Prioritizing reliability and ease of use in the early stages of this project. May look into Vaultwarden for self-hosting in the future.

### Mullvad VPN

Account-number authentication only — no login or personal information required. Installed on Mac for enhanced privacy and security.

### Tailscale

Encrypted mesh VPN for secure remote access to the homelab from personal devices, without exposing services to the public internet.

## Network

### Pi-hole

DNS-level ad and tracker blocking for all devices on the network. Runs in Docker on the Dell XPS. Any device that uses the XPS Tailscale IP as its DNS server gets automatic blocking — no client software needed.

### Nginx Proxy Manager

Reverse proxy in front of all homelab services. Routes traffic by domain name rather than IP and port, and handles HTTPS termination using a self-signed wildcard certificate for `*.home`. All services are accessible via clean `.home` domains.

### Portainer

Web UI for managing Docker containers on the XPS. Used for checking container status, reading logs, and restarting services without SSH. Deployed alongside NPM as part of the infrastructure layer.

## Observability

### Prometheus

Metrics collection engine. Scrapes container stats from cAdvisor and host-level stats from Node Exporter every 15 seconds. Stores 30 days of time-series data.

### Grafana

Dashboard and visualization layer for Prometheus metrics and Loki logs. Single pane of glass for the full stack — container health, host resources, and log explorer all in one UI.

### Loki

Log aggregation store. Receives Docker container logs from Promtail and makes them queryable in Grafana. Designed to index only metadata (labels), not log content — keeps storage lean.

### Promtail

Log collector that tails Docker container log files on the host and ships them to Loki. Runs as a sidecar to Loki in the same compose stack.

### cAdvisor

Exposes per-container CPU, memory, network, and filesystem metrics to Prometheus. Requires privileged access to read host cgroup data.

### Node Exporter

Exposes host-level system metrics (disk usage, memory, CPU load, network I/O) to Prometheus. Runs alongside cAdvisor to give a full picture of both containers and the underlying machine.

## User

### Glance

Single-page dashboard for the homelab. Shows bookmarks for every proxied service, a live Docker container monitor, and host stats (CPU, memory, temperature). The front door once a device is on Tailscale.

## Intelligence

### Ollama

Local LLM runtime running two 7B models: `qwen2.5:7b` for generating synthetic data that powers the Foothold MVP simulation, and `qwen2.5-coder:7b` for the Phase 7 log-analysis cron that reads container logs out of Loki and surfaces errors with plain-English fix suggestions. API stays direct on the Tailscale IP — no NPM hop, to keep per-request latency low for high-frequency scripted calls.

### Open WebUI

ChatGPT-style web interface in front of Ollama. Used as a prompt lab — iterate on system prompts and structured-output formats in the browser before baking them into simulation or log-analysis scripts. Reachable at `https://chat.home`.
