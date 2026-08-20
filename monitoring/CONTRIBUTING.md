# Contributing

Thanks for helping improve the monitoring dashboards!

## Workflow

1. Create a branch from `develop`.
2. Edit the dashboard JSON, or add a new `*.json` file to this folder.
3. Open a pull request describing what changed and why.
4. A maintainer reviews and merges; Git Sync provisions the change.

## Guidelines

- Keep panel titles short and consistent across dashboards.
- Prefer template variables over hard-coded hosts or datasources.
- Every new dashboard should link back to **System Overview** for context.
- Run the JSON through a linter before committing.

## Review checklist

- [ ] Datasource references use variables, not fixed UIDs
- [ ] Time range and refresh interval are sensible defaults
- [ ] No secrets or internal hostnames embedded in the JSON
