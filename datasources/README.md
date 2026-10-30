# Datasource examples

Manifests for testing datasource synchronization with Git Sync, in the same style as the
dashboard examples at the repository root.

## Spec

Datasources are served by `pkg/registry/apis/datasource` in grafana/grafana. Unlike dashboards
and playlists, there is no single API group: each datasource plugin gets its own, derived from
the plugin ID.

```
apiVersion: <plugin-id>.datasource.grafana.app/v0alpha1
kind: DataSource
```

| Plugin ID                        | API group                                                  |
| -------------------------------- | ---------------------------------------------------------- |
| `prometheus`                     | `prometheus.datasource.grafana.app`                          |
| `loki`                           | `loki.datasource.grafana.app`                                |
| `tempo`                          | `tempo.datasource.grafana.app`                               |
| `elasticsearch`                  | `elasticsearch.datasource.grafana.app`                       |
| `influxdb`                       | `influxdb.datasource.grafana.app`                            |
| `mysql`                          | `mysql.datasource.grafana.app`                               |
| `graphite`                       | `graphite.datasource.grafana.app`                            |
| `grafana-postgresql-datasource`  | `grafana-postgresql-datasource.datasource.grafana.app`       |
| `grafana-testdata-datasource`    | `grafana-testdata-datasource.datasource.grafana.app`         |

The spec is unstructured; the common fields come from `GenericDataSourceSpec`:

```yaml
spec:
  title: Prometheus (main)     # display name, previously the "name" property
  access: proxy                # proxy | direct
  url: http://localhost:9090
  user: ""
  database: ""
  basicAuth: false
  basicAuthUser: ""
  withCredentials: false
  readOnly: false
  isDefault: false
  jsonData: {}                 # plugin-specific, free-form
```

`metadata.name` is the datasource UID — the value dashboards reference as
`datasource.uid`. Everything plugin-specific (`httpMethod`, `maxLines`, `tracesToLogsV2`, …)
lives under `spec.jsonData`.

The default datasource can also be marked with the label `default: "true"`, which is what the
legacy-to-resource converter writes; `spec.isDefault` and the label are equivalent on write.

## Secrets

Passwords and tokens never go in the spec. The `secure` block holds references to values stored
in the secrets service, so nothing sensitive is committed:

```yaml
secure:
  basicAuthPassword:
    name: mimir-api-token      # name of an existing SecureValue
```

The sibling `create:` form takes a plaintext value — only for direct API calls, never for a
file in Git.

## Enabling datasource sync

Datasources are not provisioned by default. Each API group needs its own entry in the
`[provisioning] resources` list (`<group>/<Kind>[:capability...]`):

```ini
[provisioning]
resources = folder.grafana.app/Folder:folder, dashboard.grafana.app/Dashboard:folder, prometheus.datasource.grafana.app/DataSource, loki.datasource.grafana.app/DataSource, tempo.datasource.grafana.app/DataSource, elasticsearch.datasource.grafana.app/DataSource, influxdb.datasource.grafana.app/DataSource, mysql.datasource.grafana.app/DataSource, graphite.datasource.grafana.app/DataSource, grafana-postgresql-datasource.datasource.grafana.app/DataSource, grafana-testdata-datasource.datasource.grafana.app/DataSource
```

No `:folder` capability: datasources are org-scoped, so the sync must not stamp a
`grafana.app/folder` annotation on them. Subdirectories are still fine for organisation —
`teams/team-a/` exists to cover exactly that case — but they do not create folders.

`namespace` is deliberately omitted from every file. The parser rejects a file whose namespace
differs from the repository's, so a hardcoded `default` breaks on any other stack.

## Files

| File                               | Covers                                                  |
| ---------------------------------- | ------------------------------------------------------- |
| `prometheus-main.yaml`             | default datasource, incremental querying                 |
| `prometheus-mimir.yaml`            | basic auth plus a custom header, two secure references    |
| `prometheus-readonly.yaml`         | `readOnly: true`                                          |
| `loki-logs.yaml`                   | derived fields linking to Tempo                           |
| `tempo-traces.yaml`                | nested `jsonData` (trace-to-logs/metrics, service map)    |
| `elasticsearch-logs.yaml`          | index pattern, `database` field                           |
| `influxdb-metrics.yaml`            | Flux, token secret                                        |
| `postgres-analytics.yaml`          | plugin ID that keeps its `grafana-`/`-datasource` affixes |
| `mysql-app.yaml`                   | SQL connection pool settings                              |
| `graphite-legacy.yaml`             | minimal spec, no secrets                                  |
| `testdata.json`                    | JSON instead of YAML                                      |
| `teams/team-a/prometheus-team-a.yaml` | resource in a subdirectory                             |

The UIDs cross-reference each other: `loki-logs` links traces to `tempo-traces`, and
`tempo-traces` links back to `loki-logs` and `prometheus-main`. Sync the whole directory to
exercise those references.
