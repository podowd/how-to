# How to connect Claude Code to BigQuery MCP server

## Prerequisites

- Google Cloud SDK installed
- Node.js and npm installed
- BigQuery access configured in your GCP project

## Authenticate with Google Cloud

```bash
gcloud auth application-default login
```

This opens a browser window to authenticate. The credentials are stored locally and used by the MCP server.

## Add to Claude Code

```bash
claude mcp add bigquery \
npx -y @ergut/mcp-bigquery-server \
--project-id <project_id> \
--location <location> \
--scope user
```

Replace:
- `<project_id>` - your GCP project ID
- `<location>` - BigQuery dataset location (e.g., `EU`, `US`)

## Test it worked

```bash
claude mcp list
```

If the above worked, once you run Claude Code it can now execute queries on your BigQuery datasets.

## Notes

- If Claude Code is currently running you will need to restart it.
- The MCP server uses your application default credentials.
- If credentials expire, run `gcloud auth application-default login` again.
