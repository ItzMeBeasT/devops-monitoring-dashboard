# DevOps Monitoring Dashboard

A production-style infrastructure monitoring stack built from the ground up with Docker, Prometheus, and Grafana — the foundation for a future Kubernetes-native monitoring platform.

This project is being built in public as part of a structured Cloud/DevOps engineering roadmap. Every stage is documented as it happens, including what's broken and what's still in progress — not just the finished result.

## Overview

The goal is to stand up a real-time observability stack that monitors both host-level and container-level metrics, then evolve it from a single-node Docker Compose setup into a Kubernetes deployment with Alertmanager, RBAC, Secrets, and NetworkPolicies.

**Current phase:** Docker Compose (pre-Kubernetes)

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
* **Prometheus** — scrapes and stores metrics as time-series data
* **Grafana** — visualizes metrics through dashboards, provisioned automatically on startup

## Tech Stack

| Category | Tools |
|---|---|
| OS | Linux (Fedora) |
| Containerization | Docker, Docker Compose |
| Metrics & Monitoring | Prometheus, Node Exporter, cAdvisor |
| Visualization | Grafana |
| Version Control | Git, GitHub |
| Planned (Kubernetes phase) | Kubernetes, Helm, Alertmanager, RBAC, Secrets, NetworkPolicies |

## Getting Started

### Prerequisites

* Docker and Docker Compose installed
* Ports `9090`, `3000`, `9100`, and `8080` free on your host

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
| Grafana | http://localhost:3000 | Login with credentials from `.env` |
| Prometheus | http://localhost:9090 | Check **Status → Targets** to confirm all scrape jobs are `UP` |
| Node Exporter | http://localhost:9100/metrics | Raw host metrics |

## Project Structure

```
.
├── docker-compose.yml
├── .env.example
├── prometheus/
│   └── prometheus.yml          # scrape configs
└── grafana/
    ├── provisioning/
    │   ├── datasources/        # auto-provisioned Prometheus datasource
    │   └── dashboards/         # dashboard provider config
    └── dashboards/             # dashboard JSON files
```

## Roadmap

- [x] Linux fundamentals and Git workflow
- [x] Prometheus + Node Exporter + Grafana via Docker Compose
- [x] Grafana credentials moved to `.env` (no hardcoded secrets)
- [ ] Add cAdvisor for container-level metrics
- [ ] Import/build Grafana dashboards for host + container metrics
- [ ] Migrate stack to Kubernetes
- [ ] Add Alertmanager for alerting rules
- [ ] Apply RBAC, Secrets, and NetworkPolicies

## Project Status

🚧 **In Progress** — Docker Compose phase. Kubernetes migration is next.

## License

MIT
