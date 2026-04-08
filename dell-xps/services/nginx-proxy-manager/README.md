# Nginx Proxy Manager

Reverse proxy that sits in front of all homelab services. Routes traffic by hostname to the correct container and handles HTTPS termination, so services are accessible via clean `.home` domains rather than raw IP:port combinations.

Since no public domain is owned, a self-signed wildcard certificate covers `*.home`. NPM is deployed with bridge networking and `extra_hosts` so it can forward to services running on the same machine.

## Navigation

- [Compose File](docker-compose.yml)
- [Official Docs](https://nginxproxymanager.com/guide/)

## Compose

```yaml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

The `extra_hosts` entry maps `host.docker.internal` to the Docker bridge gateway, allowing NPM to forward to services on the host. Note: this works for `curl` but not for nginx's internal DNS resolver — see Known Issues.

## SSL Certificate

NPM does not have a built-in self-signed cert generator. For a homelab without a public domain, generate a cert externally and upload it as a Custom cert under **SSL Certificates → Add SSL Certificate → Custom**:

```bash
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout ~/homelab.key \
  -out ~/homelab.crt \
  -subj "/CN=*.home" \
  -addext "subjectAltName=DNS:*.home,DNS:pihole.home,DNS:portainer.home,DNS:npm.home"
```

The `-nodes` flag is required — NPM does not support passphrase-protected key files.

## Proxy Hosts

Each service gets a proxy host entry under **Hosts → Add Proxy Host**, pointing to the Docker bridge gateway IP and the container port. Attach the `*.home` custom cert on the SSL tab and enable Force SSL.

| Domain           | Service         | Port  |
|------------------|-----------------|-------|
| `pihole.home`    | Pi-hole web UI  | 8080  |
| `portainer.home` | Portainer       | 9443  |
| `npm.home`       | NPM admin UI    | 81    |

To find the Docker bridge gateway IP:

```bash
docker inspect nginx-proxy-manager --format='{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}'
```

## DNS Records

Add Local DNS records for each `.home` domain in Pi-hole under **Local DNS → DNS Records**, pointing to the XPS Tailscale IP. Any device using Pi-hole as its resolver can then reach these domains.

## Access

NPM admin UI is available at `http://<tailscale-ip>:81` directly, or `https://npm.home` once DNS and a proxy host are configured.

## Known Issues and Tips

- **`host.docker.internal` does not resolve in nginx.** `curl` inside the container uses the C library resolver (reads `/etc/hosts`), but nginx uses Docker's internal DNS (`127.0.0.11`), which does not read `/etc/hosts`. Use the Docker bridge gateway IP directly as the forward hostname in proxy host settings.
- **Pi-hole returns Access Denied when proxied.** Pi-hole v6 validates the `Host` header against `FTLCONF_webserver_domain`. Set that variable to `pihole.home` in Pi-hole's compose file, or NPM requests will be rejected with an Access Denied page.
- **Let's Encrypt requires a real public domain.** For local-only `.home` domains, use the Custom cert upload option with an `openssl`-generated cert.
