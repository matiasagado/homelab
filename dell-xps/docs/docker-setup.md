# Docker Setup

Docker Engine and Compose v2 installed on Ubuntu 24.04.4 LTS. The `apt` repository ships an outdated Compose v1.19.2 — Compose v2 is installed manually as a Docker CLI plugin instead.

## Installation

Install Docker Engine via the official script:

```bash
curl -fsSL https://get.docker.com | sh
```

Install Compose v2 manually as a CLI plugin:

```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins
sudo curl -SL https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-x86_64 \
  -o /usr/local/lib/docker/cli-plugins/docker-compose
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
docker compose version
```

## Add User to Docker Group

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Required to run Docker commands without `sudo`. The `newgrp` applies the change to the current shell without logging out.

## Verify

```bash
docker run hello-world
docker compose version
```

## Known Issues and Tips

- **`docker compose` command not found.** The Ubuntu `apt` package installs the legacy standalone v1 binary, not the v2 CLI plugin. Install v2 manually to `/usr/local/lib/docker/cli-plugins/` as shown above.
- **Permission denied running Docker.** The current user must be in the `docker` group. Run `sudo usermod -aG docker $USER` then `newgrp docker`, or log out and back in.
- **Don't use `sudo apt install docker-compose`.** This installs v1.19.2, which uses the old `docker-compose` command syntax and lacks v2 features.
