# Portainer

Web UI for managing Docker containers without SSH. Provides a live view of container status, logs, restarts, and volume inspection for all services running on the XPS.

## Navigation

- [Compose File](docker-compose.yml)
- [Official Docs](https://docs.portainer.io/)

## Compose

```yaml
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer-data:/data

volumes:
  portainer-data:
```

Portainer requires access to the Docker socket (`/var/run/docker.sock`) to read and manage container state. It serves its UI over HTTPS using its own self-signed certificate on port 9443.

## Access

Available at `https://portainer.home` via NPM, or directly at `https://<tailscale-ip>:9443`.

The first visit triggers a browser privacy warning from the self-signed cert. Clicking through **Advanced → Proceed** is a one-time step per browser.

## Notes

Portainer overlaps with the Docker CLI — everything it shows is also accessible via `docker compose` commands over SSH. It stays in the stack for convenience: checking container health or tailing logs through a browser is faster than SSHing in, particularly for non-technical users.
