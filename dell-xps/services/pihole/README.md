# Pi-hole

DNS-level ad and tracker blocking for all devices on the Tailscale network. Pi-hole runs in Docker, intercepts DNS queries, and blocks known ad and tracker domains before they reach a browser. No client software is needed on end devices — any device that uses the XPS Tailscale IP as its DNS server gets automatic blocking.

## Navigation

- [Compose File](docker-compose.yml)
- [Pi-hole Docs](https://docs.pi-hole.net/)

## Setup

### Disable systemd-resolved

Ubuntu 24.04 runs `systemd-resolved` on port 53 by default. Pi-hole needs exclusive access to port 53. Disable the system resolver before starting the container:

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
```

### Environment

Create a `.env` file in this directory with the admin password:

```
PIHOLE_PASSWORD=your-password-here
```

This file is gitignored and must never be committed.

### Compose

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8080:80/tcp"
    environment:
      TZ: 'America/Los_Angeles'
      FTLCONF_webserver_api_password: '${PIHOLE_PASSWORD}'
      FTLCONF_dns_listeningMode: 'ALL'
      FTLCONF_webserver_domain: 'pihole.home'
    volumes:
      - './etc-pihole:/etc/pihole'
    cap_add:
      - SYS_NICE
      - NET_ADMIN
    restart: unless-stopped
```

### DNS Records

Add a Local DNS record in the Pi-hole dashboard under **Local DNS → DNS Records** pointing `pihole.home` to the XPS Tailscale IP. Any device using Pi-hole as its resolver can then reach the admin UI at `https://pihole.home`.

### Pointing Devices to Pi-hole

Pi-hole only blocks on devices that use it as their DNS server. On Mac:

**System Settings → Network → Wi-Fi → Details → DNS**

Remove existing DNS entries and add the XPS Tailscale IP. Live queries appear in the Pi-hole dashboard under **Query Log**.

## Access

Admin UI is available at `http://<tailscale-ip>:8080/admin` directly, or `https://pihole.home` once an NPM proxy host is configured.

## Known Issues and Tips

- **`sudo: unable to resolve host xps-homelab`** warnings appear when disabling `systemd-resolved`. Commands still execute. Fix with: `echo "127.0.1.1 xps-homelab" | sudo tee -a /etc/hosts`
- **Pi-hole v6 uses `FTLCONF_webserver_api_password`** for the admin password — the older `WEBPASSWORD` variable is not supported in v6.
- **`FTLCONF_webserver_domain` must match the proxy hostname.** If using NPM, set it to `pihole.home` or proxied requests will return an Access Denied page.
- **Internet access is lost if the XPS goes offline** and a client device has only Pi-hole set as DNS. Add a fallback DNS (e.g. `1.1.1.1`) as a secondary entry on client devices.
