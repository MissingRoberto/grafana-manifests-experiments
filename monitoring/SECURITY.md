# Security

## Reporting a problem

If a dashboard exposes sensitive information (internal hostnames, credentials,
customer data in labels, etc.), **do not open a public issue**. Instead:

1. Contact the platform team directly.
2. Include the dashboard file name and the panel involved.
3. We will rotate anything exposed and scrub the history if needed.

## Handling sensitive data

- Never embed API keys, tokens, or passwords in dashboard JSON.
- Avoid pinning specific customer identifiers in queries or annotations.
- Use datasource variables so credentials stay in Grafana, not in Git.
