`Currently working on this projecct and will be updating as I go.`

# Applications

<p>This section documents the core applications used in my homelab environment, including their purpose, configuration decisions, and usage patterns.</p>

## Security & Privacy

### 1 Password

I am using a managed solution rather than self-hosting. In the begginning stages of this project, I am prioritizing reliability and ease of use. Looking to get into self hosting and using Vaultwarden. 

### Mullvad VPN

Enhance privacy and security while keeping systems simple and reliable. Looking into self-hosting options for learning, control, and cost savings.

### Tailscale

This encrypted mesh VPN that enables secure remote access to my homelab remotely from personal devices, without exposing services directly to the public internet.

## Network

### Pi-hole

DNS-level ad and tracker blocking for all devices on the network. Runs in Docker on the Dell XPS. Devices that use the XPS's Tailscale IP as their DNS server get automatic blocking — no client software needed.

### Nginx Proxy Manager

Reverse proxy that sits in front of all homelab services. Routes traffic by domain name instead of IP and port, and handles HTTPS termination using a self-signed wildcard certificate for `*.home`. All services are accessed via clean `.home` domains rather than raw addresses.

### Portainer

Web UI for managing Docker containers on the XPS. Used for checking container status, reading logs, and restarting services without needing to SSH in. Deployed alongside NPM as part of the infrastructure layer.