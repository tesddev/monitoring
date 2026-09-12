# Monitoring — Prometheus & Node Exporter

Stage 5 Task 5-2: metrics collection setup for `tes-devops.duckdns.org`.

## Components

- **Prometheus** — runs as a Docker container with `--network host`, scraping
  itself and Node Exporter every 15s. Config in `prometheus.yml`.
- **Node Exporter** — runs directly on the host (not containerized, since it
  reads `/proc` and `/sys`) as a dedicated non-root systemd service.
  Unit file in `node_exporter.service`.

## Access

Prometheus UI is intentionally not exposed publicly. Access via SSH tunnel:

    ssh -L 9090:localhost:9090 devops-stage0

Then open http://localhost:9090 locally.

## PromQL queries used

- CPU usage %: `100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`
- Available memory: `node_memory_MemAvailable_bytes`
- HTTP requests (Prometheus self-scrape stand-in, no app-level exporter yet):
  `sum(increase(prometheus_http_requests_total[5m]))`

See `screenshots/` for target status and query results.

## Secrets

`alertmanager.yml` uses `${SLACK_WEBHOOK_URL}` as a placeholder. On the server,
the real webhook URL is configured in the deployed file and is never committed
to this repository.
