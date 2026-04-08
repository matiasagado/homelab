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
