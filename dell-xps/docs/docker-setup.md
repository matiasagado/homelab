# Docker Setup on Ubuntu 24.04 LTS

**Date:** 2026-03-25  
**Machine:** Dell XPS 15 9510
**Docker Version:** 28.2.2

## Docker Installation

1. Docker engine installed manually:

```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins
sudo curl -SL https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-x86_64 -o /usr/local/lib/docker/cli-plugins/docker-compose
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
docker compose version
```

### Docker Compose Issue

- `docker compose` unknown command
- Ubuntu package `docker-compose` old (v1.19.2)
- Fixed by installing Compose v2 manually in `/usr/local/lib/docker/cli-plugins/`

## Add User to Docker Group

```bash
sudo usermod -aG docker $USER
newgrp docker
```

## Test Docker

```bash
docker run hello-world
```

## Problems & Fixes

| Problem                          | What Went Wrong                                         | How I Fixed It                                                        |
| -------------------------------- | ------------------------------------------------------- | --------------------------------------------------------------------- |
| Docker Compose command not found | Ubuntu package outdated, default repo missing v2 plugin | Installed Compose v2 manually in `/usr/local/lib/docker/cli-plugins/` |
| Permission denied running Docker | User not in Docker group                                | Added user to Docker group, ran `newgrp docker`                       |
| Old docker-compose conflicts     | Ubuntu repo installs v1.19.2                            | Removed old package after installing v2                               |

## Notes
