# How to connect Claude Code to Jira (Atlassian MCP)

## Prerequisites

- Atlassian account with access to Jira
- Claude Code installed

## Enable the plugin

Add to your `~/.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "atlassian@claude-plugins-official": true
  }
}
```

Or if you already have `enabledPlugins`, add the Atlassian line to it.

## Authenticate

Restart Claude Code. On first use of an Atlassian tool, you'll be prompted to authenticate via OAuth in your browser.

## Test it worked

```bash
claude mcp list
```

You should see `plugin:atlassian:atlassian: ... - ✓ Connected`.

## Usage

Once connected, Claude Code can interact with Jira. Key capabilities:

- Search issues with JQL
- Get/create/edit issues
- Add comments, transition issues
- Look up account IDs

Example prompts:
- "Find all open bugs assigned to me in PROJECT"
- "Create a Jira ticket for this bug"

## Notes

- If Claude Code is currently running you will need to restart it after editing settings.
- OAuth tokens are managed automatically.
- You need appropriate permissions in your Atlassian workspace.
