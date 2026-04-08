# Tailscale

Encrypted mesh VPN that connects the Mac and XPS into a private network. Enables SSH access and service connectivity from any location without exposing the XPS to the public internet. All homelab services bind to the Tailscale IP rather than a public address.

## Installation

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Authenticate via personal account at tailscale.com. Once connected, both devices appear in the Tailscale admin console and can communicate directly.

## Get the XPS Tailscale IP

```bash
tailscale ip -4
```

This IP is used throughout the homelab — SSH config, Docker service bindings, Pi-hole DNS records, and NPM proxy hosts all reference it.

## Notes

- Tailscale runs as a systemd service and starts automatically on boot
- UFW remains active — Tailscale handles encrypted tunneling on top of the existing firewall
- Devices on the mesh can communicate as if on the same LAN, regardless of physical location
