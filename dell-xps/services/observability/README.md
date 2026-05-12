# Observability

Metrics, dashboards, and log aggregation for the homelab. Prometheus scrapes container and host metrics, Loki aggregates container logs, and Grafana provides a single UI over both. This stack is the data source for the AI Intelligence Layer (Phase 11), where Loki and Prometheus output is fed into Ollama for anomaly detection and plain-English summaries.

## Navigation

- [Compose File](docker-compose.yml)
- [Prometheus Config](prometheus.yml)
- [Promtail Config](promtail.yml)
- [Loki Config](loki.yml)
- [Grafana Docs](https://grafana.com/docs/grafana/latest/)
- [Prometheus Docs](https://prometheus.io/docs/)

## Architecture

```
Docker containers → cAdvisor      ↘
                                    Prometheus → Grafana (dashboards)
System metrics   → Node Exporter  ↗

Container logs   → Promtail → Loki → Grafana (log explorer)
```

Six containers run as a single stack. Internal traffic flows over Docker service names — no hardcoded IPs between components.

## Compose

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus-data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention.time=30d'

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3001:3000"
    environment:
      GF_SECURITY_ADMIN_PASSWORD_FILE: /run/secrets/grafana_password
    volumes:
      - grafana-data:/var/lib/grafana
    secrets:
      - grafana_password
    depends_on:
      - prometheus
      - loki

  loki:
    image: grafana/loki:latest
    container_name: loki
    restart: unless-stopped
    ports:
      - "3100:3100"
    volumes:
      - ./loki.yml:/etc/loki/local-config.yaml:ro
      - loki-data:/loki
    command: -config.file=/etc/loki/local-config.yaml

  promtail:
    image: grafana/promtail:latest
    container_name: promtail
    restart: unless-stopped
    volumes:
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - ./promtail.yml:/etc/promtail/config.yml:ro
    command: -config.file=/etc/promtail/config.yml
    depends_on:
      - loki

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    restart: unless-stopped
    privileged: true
    ports:
      - "8083:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    ports:
      - "9100:9100"
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'

secrets:
  grafana_password:
    file: ./secrets/grafana_password.txt

volumes:
  prometheus-data:
  grafana-data:
  loki-data:
```

The Grafana admin password is loaded via Docker secrets from `./secrets/grafana_password.txt`, which is gitignored.

cAdvisor runs `privileged: true` so it can read host cgroup and device data needed for per-container metrics. Node Exporter's `mount-points-exclude` flag suppresses noisy pseudo-filesystems from host disk metrics.

Prometheus retains 30 days of metrics. Loki stores logs in the `loki-data` volume with no expiry — `retention_period` in `loki.yml` is the knob if disk usage grows.

## NPM and DNS

Only Grafana needs an external entry point — Prometheus, Loki, cAdvisor, and Node Exporter stay internal.

| Domain         | Service | Forward Host          | Forward Port |
|----------------|---------|-----------------------|--------------|
| `grafana.home` | Grafana | Docker bridge gateway | `3001`       |

Add the proxy host in NPM under **Hosts → Add Proxy Host**, attach the `*.home` custom cert, and enable Force SSL. Then add a Local DNS record in Pi-hole under **Local DNS → DNS Records** pointing `grafana.home` to the XPS Tailscale IP.

The forward host is the Docker bridge gateway IP, not `host.docker.internal` — see the NPM README's Known Issues for why.

## Grafana Setup

Two data sources, configured under **Connections → Data Sources**:

| Name       | Type       | URL                      |
|------------|------------|--------------------------|
| Prometheus | Prometheus | `http://prometheus:9090` |
| Loki       | Loki       | `http://loki:3100`       |

Community dashboards imported under **Dashboards → Import**:

| ID     | Dashboard                         |
|--------|-----------------------------------|
| `193`  | cAdvisor — container metrics      |
| `1860` | Node Exporter Full — host metrics |

## Access

- `https://grafana.home` via NPM (HTTPS, requires DNS + proxy host)
- `http://<tailscale-ip>:3001` direct

Admin username is `admin`. Password is whatever was written to `./secrets/grafana_password.txt` before first boot.

## Known Issues and Tips

- **Promtail labels are minimal.** The current pipeline extracts `stream` (stdout/stderr) only — container names and images are not labeled. For richer labels, switch the scrape config to `docker_sd_configs` and mount the Docker socket into Promtail.
- **Loki has no log retention configured.** Logs accumulate indefinitely in the `loki-data` volume. Set `limits_config.retention_period` in `loki.yml` once disk usage becomes a concern.
- **cAdvisor must run privileged.** It needs host cgroup and device access to report per-container CPU/memory. The trade-off is accepted because cAdvisor is a well-known image, not a custom container.
- **The stack is the upstream for Phase 11.** Loki logs and Prometheus metrics are the input to Ollama-powered anomaly detection — don't drop retention without considering that downstream consumer.
