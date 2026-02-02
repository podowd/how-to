# How to connect Claude Code to Grafana MCP server

## Prerequisites

- A Grafana instance (version 9.0+)
- A Grafana service account token with appropriate permissions (Viewer role minimum)

## Create a Grafana Service Account Token

1. In Grafana, go to **Administration > Service accounts**
2. Create a new service account and assign it the **Viewer** role (or configure granular RBAC permissions)
3. Generate a token for the service account and copy it

If you don't have admin access, ask your platform team for a service account token with Viewer role.

## Verify the token works

```bash
curl -H "Authorization: Bearer <your-token>" https://<your-grafana-url>/api/org
```

You should get a JSON response with your org details.

## Install the mcp-grafana binary

mcp-grafana is a Go binary, not an npm package. Download it from GitHub releases:

```bash
curl -L https://github.com/grafana/mcp-grafana/releases/download/v0.9.0/mcp-grafana_Darwin_arm64.tar.gz -o /tmp/mcp-grafana.tar.gz
sudo tar -xzf /tmp/mcp-grafana.tar.gz -C /usr/local/bin mcp-grafana
```

For Linux x86_64, replace `Darwin_arm64` with `Linux_x86_64` in the URL.

Check latest releases at: https://github.com/grafana/mcp-grafana/releases

## Add to Claude Code

```bash
claude mcp add --scope user grafana \
    -e GRAFANA_URL=<your-grafana-url> \
    -e GRAFANA_SERVICE_ACCOUNT_TOKEN=<your-service-account-token> \
    -- mcp-grafana --disable-write
```

Replace:
- `<your-grafana-url>` - your Grafana instance URL (e.g., `https://grafana.example.com`)
- `<your-service-account-token>` - the service account token generated above

The `--disable-write` flag runs the server in read-only mode, preventing any modification operations.

## Test it worked

```bash
claude mcp list
```

If the above worked, once you run Claude Code it can now query your Grafana dashboards, datasources, alerts, and observability data.

## Notes

- If Claude Code is currently running you will need to restart it.
- Remove `--disable-write` if you need Claude to create or modify dashboards, alerts, etc.
