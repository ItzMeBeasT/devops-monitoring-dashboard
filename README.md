# DevOps Monitoring Dashboard

A production-style infrastructure monitoring stack built from the ground up with Docker, Prometheus, and Grafana — the foundation for a future Kubernetes-native monitoring platform.

> **v2.0** — Docker Compose phase complete: cAdvisor added, Grafana credentials externalized to `.env`, dashboards provisioned, and all scrape targets verified `UP`.

This project is being built in public as part of a structured Cloud/DevOps engineering roadmap. Every stage is documented as it happens, including what's broken and what's still in progress — not just the finished result.

## Overview

The goal is to stand up a real-time observability stack that monitors both host-level and container-level metrics, then evolve it from a single-node Docker Compose setup into a Kubernetes deployment with Alertmanager, RBAC, Secrets, and NetworkPolicies.

**Current phase:** Docker Compose — complete. Kubernetes migration is next.

## Architecture

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│ Node Exporter│─────▶│              │      │              │
│ (host metrics│      │  Prometheus  │─────▶│   Grafana    │
├──────────────┤      │  (scrape +   │      │ (dashboards) │
│   cAdvisor   │─────▶│   storage)   │      │              │
│ (container   │      │              │      │              │
│  metrics)    │      └──────────────┘      └──────────────┘
└──────────────┘
```

* **Node Exporter** — exposes host-level metrics (CPU, memory, disk, network)
* **cAdvisor** — exposes per-container resource usage and performance metrics
* **Prometheus** — scrapes and stores metrics as time-series data; all three scrape targets (`prometheus`, `node-exporter`, `cadvisor`) are confirmed `UP`
* **Grafana** — visualizes metrics through two auto-provisioned dashboards, no manual import required

## Dashboards

| Dashboard | Source | Panels |
|---|---|---|
| **Node Exporter Full** | `grafana/dashboards/host-monitoring.json` | 31 panels — CPU, memory, disk, network, systemd, filesystem, and more |
| **cAdvisor Exporter** | `grafana/dashboards/container-dashboard.json` | 10 panels — per-container CPU, memory, network, and metadata |

### Screenshots

| Host Dashboard | Container Dashboard |
|---|---|
| ![Host Dashboard](screenshots/Host_Dashboard.png) | ![Container Dashboard](screenshots/Container_Dashboard.png) |

| Prometheus Targets | `docker compose ps` |
|---|---|
| ![Prometheus Targets](screenshots/Prometheus-Targets.png) | ![Docker Compose PS](screenshots/Docker_Compose_PS.png) |

## Tech Stack

| Category | Tools |
|---|---|
| OS | Linux (Fedora) |
| Containerization | Docker, Docker Compose |
| Metrics & Monitoring | Prometheus v2.54.1, Node Exporter v1.8.2, cAdvisor |
| Visualization | Grafana 11.1.0 |
| Version Control | Git, GitHub |
| Planned (Kubernetes phase) | Kubernetes, Helm, Alertmanager, RBAC, Secrets, NetworkPolicies |

## Getting Started

### Prerequisites

* Docker and Docker Compose installed
* Ports `9090`, `3000`, `9100`, and `8081` free on your host

### Setup

1. Clone the repository
   ```bash
   git clone https://github.com/ItzMeBeasT/devops-monitoring-dashboard.git
   cd devops-monitoring-dashboard
   ```

2. Create your environment file from the example
   ```bash
   cp .env.example .env
   ```
   Then edit `.env` and set your own Grafana admin credentials. This file is gitignored and never committed.

3. Start the stack
   ```bash
   docker compose up -d
   ```

4. Verify everything is healthy
   ```bash
   docker compose ps
   ```

### Access the services

| Service | URL | Notes |
|---|---|---|
| Grafana | http://localhost:3000 | Login with credentials from `.env`; dashboards are pre-loaded |
| Prometheus | http://localhost:9090 | Check **Status → Targets** to confirm all scrape jobs are `UP` |
| Node Exporter | http://localhost:9100/metrics | Raw host metrics |
| cAdvisor | http://localhost:8081 | Raw per-container metrics |

## Project Structure

```
.
├── docker-compose.yml
├── .env.example
├── prometheus/
│   └── prometheus.yml          # scrape configs (prometheus, node-exporter, cadvisor)
├── grafana/
│   ├── provisioning/
│   │   ├── datasources/        # auto-provisioned Prometheus datasource
│   │   └── dashboards/         # dashboard provider config
│   └── dashboards/             # dashboard JSON files (host + container)
└── screenshots/                # dashboard and stack verification screenshots
```

## Roadmap

- [x] Linux fundamentals and Git workflow
- [x] Prometheus + Node Exporter + Grafana via Docker Compose
- [x] Grafana credentials moved to `.env` (no hardcoded secrets)
- [x] Add cAdvisor for container-level metrics
- [x] Import/build Grafana dashboards for host + container metrics
- [x] Verify all Prometheus scrape targets are `UP`
- [ ] Migrate stack to Kubernetes
- [ ] Add Alertmanager for alerting rules
- [ ] Apply RBAC, Secrets, and NetworkPolicies

## Project Status

✅ **Docker Compose phase complete** — all four services running, dashboards provisioned, scrape targets healthy. Kubernetes migration is next.

## License

MIT
