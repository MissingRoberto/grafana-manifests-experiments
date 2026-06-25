# Grafana Manifests — Library Panels Demo

A set of demo Grafana manifests for exercising **library panels alongside dashboards via
Git Sync**. Everything here is provisioned through the Grafana App Platform Kubernetes-style
APIs (`dashboard.grafana.app`), so the repository doubles as a fixture for provisioning,
folder mapping, and library-panel reference resolution.

> Branch: `library-panels` (cut from `playlists`).

## What's in here

The repository is organized into **folders that map 1:1 to Grafana folders**. Each folder
holds both the **dashboards** and the **library panels** they reference, so a folder is a
self-contained, portable unit you can sync, copy, or delete as a whole.

```
.
├── platform-monitoring/          # Folder: Platform Monitoring
│   ├── cpu-usage.libpanel.json       # LibraryPanel: CPU Usage
│   ├── memory-usage.libpanel.json    # LibraryPanel: Memory Usage
│   ├── host-overview.json            # Dashboard  → reuses CPU + Memory
│   └── kubernetes-cluster.json       # Dashboard  → reuses CPU + Memory
│
├── application-observability/    # Folder: Application Observability
│   ├── request-rate.libpanel.json    # LibraryPanel: Request Rate (RED)
│   ├── error-ratio.libpanel.json     # LibraryPanel: Error Ratio (RED)
│   ├── api-gateway.json              # Dashboard  → reuses Request Rate + Error Ratio
│   └── service-health.json           # Dashboard  → reuses Request Rate + Error Ratio
│
├── business-kpis/                # Folder: Business KPIs
│   ├── revenue-kpi.libpanel.json     # LibraryPanel: Revenue (Today)
│   └── executive-summary.json        # Dashboard  → reuses Revenue KPI
│
└── *.yaml                        # Playlists (inherited from the playlists branch)
```

`*.libpanel.json` is a naming convention used here to make library panels easy to spot —
Grafana routes resources by their `kind`, not the filename.

## Resource kinds

| File pattern          | `apiVersion`                        | `kind`         |
| --------------------- | ----------------------------------- | -------------- |
| `*.json` (dashboards) | `dashboard.grafana.app/v2`          | `Dashboard`    |
| `*.libpanel.json`     | `dashboard.grafana.app/v0alpha1`    | `LibraryPanel` |
| `*.yaml` (playlists)  | `playlist.grafana.app/v1`           | `Playlist`     |

## How dashboards reference library panels

A library panel is defined once, then referenced by UID from any dashboard. In the v2
dashboard schema the reference is an element of `kind: LibraryPanel`:

```jsonc
"elements": {
  "cpu": {
    "kind": "LibraryPanel",
    "spec": {
      "id": 1,
      "title": "CPU Usage",
      "libraryPanel": {
        "uid":  "lib-cpu-usage",   // == metadata.name of the LibraryPanel resource
        "name": "CPU Usage"
      }
    }
  }
}
```

The element is then placed on the grid like any other panel via a `GridLayoutItem` that
references it by key (`ElementReference` → `cpu`). Edit the library panel once and every
dashboard that references it picks up the change.

### Reference map

| Library panel (UID)  | Defined in                  | Reused by                                  |
| -------------------- | --------------------------- | ------------------------------------------ |
| `lib-cpu-usage`      | platform-monitoring         | host-overview, kubernetes-cluster          |
| `lib-memory-usage`   | platform-monitoring         | host-overview, kubernetes-cluster          |
| `lib-request-rate`   | application-observability   | api-gateway, service-health                |
| `lib-error-ratio`    | application-observability   | api-gateway, service-health                |
| `lib-revenue-kpi`    | business-kpis               | executive-summary                          |

## Provisioning notes

- All panels query a **Prometheus** data source selected through a dashboard
  `DatasourceVariable` named `datasource`. Point it at any Prometheus-compatible source.
- The PromQL (e.g. `node_cpu_seconds_total`, `http_requests_total`,
  `orders_revenue_usd_total`) is illustrative demo data — adjust to your environment.
- Because library panels live in the **same folder** as the dashboards that use them, the
  reference resolves cleanly after sync regardless of folder import order.

## Using it

Provision the repo with Grafana Git Sync (or `kubectl apply` against the App Platform APIs)
and you'll get three folders, each populated with its dashboards and the library panels
they depend on. Open any dashboard and edit a shared panel from one place to see the change
propagate everywhere it's used.
