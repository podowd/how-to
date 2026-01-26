# How to get Slack notifications from Claude Code

Get notified on Slack when Claude Code needs your input. Handy for long running tasks.

Note: There is a full Slack MCP server available, but for simple notifications a hook is lighter and sufficient.

## Prerequisites

- Slack workspace with permission to create apps
- `jq` installed (`brew install jq`)

## Create Slack Incoming Webhook

1. Go to https://api.slack.com/apps
2. Create New App → From scratch
3. Select "Incoming Webhooks" feature
4. Toggle "Activate Incoming Webhooks" to ON
5. Click "Add New Webhook to Workspace"
6. Select channel and click Allow
7. Copy the webhook URL

## Create the hook script

```bash
mkdir -p ~/.claude/hooks
```

Create `~/.claude/hooks/slack-webhook-url`:
```
<your_webhook_url>
```

Create `~/.claude/hooks/slack-notify.sh`:
```bash
#!/bin/bash

[[ -z "$SLACK_NOTIFY" ]] && exit 0

HOOK_TYPE="$1"
INPUT=$(cat)
WEBHOOK_URL=$(cat ~/.claude/hooks/slack-webhook-url)

case "$HOOK_TYPE" in
  permission)
    TOOL=$(echo "$INPUT" | jq -r '.tool_name // "Unknown tool"')

    if [[ "$TOOL" == "AskUserQuestion" ]]; then
      QUESTION=$(echo "$INPUT" | jq -r '.tool_input.questions[0].question // "Question"')
      OPTIONS=$(echo "$INPUT" | jq -r '.tool_input.questions[0].options[]?.label // empty' | tr '\n' ', ')
      MESSAGE="❓ *${QUESTION}*
Options: ${OPTIONS%,}"
    elif [[ "$TOOL" == "Bash" ]]; then
      CMD=$(echo "$INPUT" | jq -r '.tool_input.command // ""' | head -c 80)
      MESSAGE="🔐 Approve Bash: \`${CMD}\`"
    else
      MESSAGE="🔐 Approve *${TOOL}*"
    fi
    ;;
  *)
    MESSAGE="🤖 Claude needs attention"
    ;;
esac

curl -s -X POST \
  -H 'Content-type: application/json' \
  --data "$(jq -n --arg text "$MESSAGE" '{text: $text}')" \
  "$WEBHOOK_URL"
```

Make it executable:
```bash
chmod +x ~/.claude/hooks/slack-notify.sh
```

## Configure Claude Code

Add to `~/.claude/settings.json`:
```json
{
  "hooks": {
    "PermissionRequest": [
      {
        "matcher": "*",
        "hooks": [{"type": "command", "command": "~/.claude/hooks/slack-notify.sh permission"}]
      }
    ]
  }
}
```

The `"*"` matcher notifies on any permission request. The hook only fires when Claude is actually blocked waiting for permission - already-approved operations don't trigger notifications.

## Usage

Normal (no notifications):
```bash
claude
```

With Slack notifications:
```bash
SLACK_NOTIFY=1 claude
```

Optional alias in `~/.zshrc`:
```bash
alias claude-notify='SLACK_NOTIFY=1 claude'
```

## Notes

- Restart Claude Code after changing settings.json
- Webhook URL should be kept private (don't commit to git)

