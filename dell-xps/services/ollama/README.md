# Ollama

Local LLM runtime for the homelab. Serves two workloads on the same instance: generating synthetic data for the Foothold MVP (universities, organizations, events, users, messages) and analyzing container logs from the observability stack to flag errors and suggest fixes in plain English. Open WebUI sits next to it as a prompt lab for iterating on system prompts before they get baked into scripts.

## Navigation

- [Compose File](docker-compose.yml)
- [Ollama Docs](https://github.com/ollama/ollama)
- [Open WebUI Docs](https://docs.openwebui.com/)

## Compose

```yaml
services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    ports:
      - "11434:11434"
    volumes:
      - ollama-models:/root/.ollama
    environment:
      TZ: "America/Los_Angeles"

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    restart: unless-stopped
    ports:
      - "3000:8080"
    environment:
      OLLAMA_BASE_URL: http://ollama:11434
      TZ: "America/Los_Angeles"
    depends_on:
      - ollama
    volumes:
      - open-webui-data:/app/backend/data

volumes:
  ollama-models:
  open-webui-data:
```

The two containers share the compose-created bridge network, so Open WebUI reaches Ollama via the container name. Models persist in the `ollama-models` named volume — pulling a model once survives container restarts and image updates.

## Models

Two 7B models cover both workloads:

| Model               | Role                          | Why                                                                  |
|---------------------|-------------------------------|----------------------------------------------------------------------|
| `qwen2.5:7b`        | Foothold simulation           | Strict JSON-mode compliance — holds the schema across long batches   |
| `qwen2.5-coder:7b`  | Phase 7 log analysis          | Trained on code and stack traces — reads logs as structured signal   |

Both pulled with `docker exec -it ollama ollama pull <name>`. Ollama auto-unloads idle models from RAM after 5 minutes, so keeping both available costs disk (~9GB combined) but not memory.

7B models on this CPU run at roughly 6–8 tokens/sec with a 32k context window — enough for the 30-minute log slices the Phase 7 cron will feed in. A 12B class model with 128k context is the documented upgrade path if daily-digest jobs need to ingest longer histories.

## Access

- **Ollama API:** direct on the Tailscale IP at the published port. Not behind NPM — the API is called from cron scripts on the host and from the Foothold simulator over Tailscale; HTTPS termination would add handshake latency per request with no security benefit on a mesh that's already encrypted. Same reasoning as the Grafana direct-access decision.
- **Open WebUI:** at `https://chat.home` via NPM, with a Pi-hole DNS record pointing the name at the XPS Tailscale IP. Direct fallback on the Tailscale IP when NPM is being touched. First signup creates the admin account — register immediately after bring-up to claim it.

## Known Issues and Tips

- **New services need a UFW allow rule.** UFW silently drops forwarded traffic by default. Without an explicit allow for the API port, the Foothold simulator hits a wall with no log entry; without one for the Open WebUI port, NPM 502s. Same gotcha that surfaced during the Glance bring-up.
- **NPM forward hostname is the bridge gateway IP, not `host.docker.internal`.** Open WebUI's proxy host needs the literal bridge IP. Advanced NPM toggles render `proxy_pass` with a variable, which forces request-time DNS resolution that nginx can't satisfy from `/etc/hosts`.
- **CPU-only inference.** No discrete GPU on this host, so first-token latency is real (a few seconds before generation starts). Acceptable for batch simulation and periodic log analysis; not snappy enough for a polished interactive chatbot UX.
- **Ollama serializes requests by default.** If the Foothold simulator is mid-batch and the Phase 7 cron fires, the cron waits its turn. At current scale that's seconds of delay, not minutes. `OLLAMA_NUM_PARALLEL=2` in the environment block runs two requests concurrently at the cost of more RAM per request.
- **Open WebUI first-user wins.** Whoever signs up first becomes admin. No public exposure (Tailscale only), but worth knowing — go through the signup flow before sharing the URL with anyone.
- **Pulling a model is a foreground operation.** Each `ollama pull` blocks the terminal for several minutes depending on size and connection. Run inside tmux or detach with `docker exec -d` if pulling over SSH and you can't keep the session open.
- **The Foothold simulator and the log-analysis cron share one instance.** That's intentional — it keeps RAM use bounded and avoids running two model runtimes. The trade is serialized request handling; if it ever feels slow under simulator load, parallelism is a one-env-var change.
