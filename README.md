# Grafana Observability Lab

Production-grade monitoring and observability stack: Prometheus + Grafana + Loki.

Monitoring a heterogeneous infrastructure of 7+ devices across macOS, Linux, and Kubernetes — including GPU inference servers and AI agents.

---

## Stack

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Prometheus  │     │   Grafana    │     │    Loki      │
│  Metrics     │────▶│  Dashboards  │     │  Logs        │
│  Retention:  │     │  30+ panels  │     │  Retention:  │
│  30 days     │     │              │     │  14 days     │
└──────┬───────┘     └──────────────┘     └──────┬───────┘
       │                                         │
       ▼                                         ▼
┌──────────────────────────────────────────────────────┐
│                   TARGETS (7+ nodes)                 │
│                                                      │
│  DGX Spark  │  M3 Ultra  │  M4 Pro  │  M5 Max       │
│  GMKtec X2  │  K8s nodes │  OMV NAS  │  Pi-hole     │
└──────────────────────────────────────────────────────┘
```

---

## Dashboard Catalog

### Hardware Monitoring

| Dashboard | Targets | Metrics |
|-----------|---------|---------|
| **DGX Spark** | NVIDIA GB10 | GPU temp, VRAM, power, CPU, disk, vLLM throughput |
| **Mac M3 Ultra** | Apple Silicon | CPU temp, memory pressure, disk, network, Thunderbolt |
| **Mac M4 Pro** | Apple Silicon | CPU, memory, node_exporter macOS |
| **Mac M5 Max** | Apple Silicon | CPU, memory, GPU utilization |
| **GMKtec Evo X2** | Intel i7 | CPU, memory, disk, STT/TTS API latency |
| **Mac M2 Mini** | Apple Silicon | CPU, memory, Pi-hole stats |
| **Node Exporter / MacOS** | All Macs | Unified macOS metrics dashboard |
| **Node Exporter / Nodes** | All nodes | Comparative node view |
| **Node Exporter / USE Method** | Cluster + Per-node | Utilization, Saturation, Errors |

### Kubernetes Monitoring

| Dashboard | Scope |
|-----------|-------|
| **K8s / API Server** | API latency, request rates, errors |
| **K8s / Compute Resources / Cluster** | Aggregate CPU, memory, disk across cluster |
| **K8s / Compute Resources / Namespace (Pods)** | Per-namespace pod metrics |
| **K8s / Compute Resources / Namespace (Workloads)** | Per-namespace workload metrics |
| **K8s / Compute Resources / Node (Pods)** | Per-node pod allocation |
| **K8s / Compute Resources / Pod** | Individual pod deep-dive |
| **K8s / Compute Resources / Workload** | Deployment/StatefulSet metrics |
| **K8s / Networking / Cluster** | Network throughput, errors, dropped packets |
| **K8s / Networking / Namespace (Pods)** | Per-namespace network breakdown |
| **K8s / Networking / Pod** | Per-pod network metrics |
| **K8s / Persistent Volumes** | PVC usage, capacity, I/O |
| **CoreDNS** | DNS latency, cache hit rate |
| **etcd** | etcd cluster health |

### Infrastructure

| Dashboard | Purpose |
|-----------|---------|
| **Infrastructure Alerts** | Firing alerts overview, alert history |
| **Alertmanager / Overview** | Alert routing, silenced alerts, inhibition rules |
| **Prometheus / Overview** | Prometheus health, TSDB stats, scrape duration |
| **Grafana Overview** | Grafana usage stats, dashboard views |

---

## Alerts (Prometheus Rules)

Key alert categories:

| Category | Example Alerts |
|----------|---------------|
| **Node Health** | Node down, high CPU/memory/disk, filesystem >85% |
| **Kubernetes** | Pod CrashLoopBackOff, PVC >90%, etcd leader changes |
| **GPU/Inference** | GPU temp >85°C, vLLM OOM, inference latency spikes |
| **Services** | Endpoint down, certificate expiry <7 days |
| **Storage** | Longhorn volume degraded, NFS mount stale |

---

## Setup Highlights

### Prometheus
- **Scrape interval:** 15s (default), 60s (low-priority targets)
- **Retention:** 30 days (TSDB)
- **Storage:** 50GB Longhorn volume
- **Targets:** node_exporter (macOS/Linux), kube-state-metrics, cadvisor, vLLM metrics

### Grafana
- **Dashboards:** 30+ imported, GitOps-managed via ConfigMaps
- **Data sources:** Prometheus, Loki
- **Alerting:** Grafana-managed alerts → Slack + email

### Loki
- **Retention:** 14 days
- **Storage:** 20GB Longhorn volume
- **Sources:** Kubernetes pods, systemd journals, macOS unified logs

---

## Node Exporter on macOS

Custom `node_exporter` deployment on Apple Silicon Macs:

- CPU temperature (Apple SMC)
- Memory pressure (unified memory architecture)
- GPU utilization (Metal/ANE)
- Disk I/O (NVMe)
- Network throughput (Thunderbolt/WiFi)
- Thermal throttling events

---

## Tech Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| Prometheus | v3.x | Metrics collection |
| Grafana | v11.x | Visualization & alerting |
| Loki | v3.x | Log aggregation |
| AlertManager | v0.27 | Alert routing |
| kube-state-metrics | v2.x | K8s object metrics |
| node_exporter | v1.x | Host-level metrics |
| cadvisor | v0.x | Container metrics |

---

## Related Repos

- [homelab-ai-platform](https://github.com/daeep/homelab-ai-platform) — infrastructure being monitored
- [hermes-ai-agents](https://github.com/daeep/hermes-ai-agents) — agents that query these dashboards
