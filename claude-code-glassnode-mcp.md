# How to connect Claude Code to Glassnode MCP server

## Prerequisites

- Node.js and npm installed
- Glassnode API key (get one at https://studio.glassnode.com/settings/api)

## Add to Claude Code

```bash
claude mcp add glassnode \
npx mcp-remote https://mcp.glassnode.com \
--header "X-Api-Key:<your_api_key>" \
--scope user
```

Replace `<your_api_key>` with your Glassnode API key.

## Test it worked

```bash
claude mcp list
```

You should see `glassnode: ... - ✓ Connected`.

## Usage

Once connected, Claude Code can fetch crypto metrics. Key tools:

- `fetch_metric` - fetch data for a single asset
- `fetch_bulk_metrics` - fetch data for multiple assets (preferred for efficiency)
- `get_assets_list` - list supported cryptocurrencies
- `get_metrics_list` - list available metrics
- `get_asset_metrics` - metrics available for a specific asset

Example prompts:
- "What's the BTC price and OI for the last 24 hours?"
- "Compare ETH and BTC funding rates this week"

## Notes

- If Claude Code is currently running you will need to restart it.
- API key tier determines available metrics and rate limits.
- Use `fetch_bulk_metrics` when querying multiple assets to reduce API calls.
