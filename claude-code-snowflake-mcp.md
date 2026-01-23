# How to connect Claude Code to a Snowflake hosted MCP server (PAT auth)

## Create the MCP server (run in Snowflake)

```sql
use role accountadmin;
use database <database_name>;
use schema <schema_name>;

create mcp server <mcp_server_name>
comment = "claude code mcp";

grant usage on mcp server <database_name>.<schema_name>.<mcp_server_name> to role <role_name>;
grant usage on database <database_name> to role <role_name>;
grant usage on schema <database_name>.<schema_name> to role <role_name>;
```

## Create the PAT + network policy (run in Snowflake)

```sql
use role securityadmin;

alter user <username> add programmatic access token <pat_name>
days_to_expiry = <days>
role_restriction = '<role_name>';

create network rule <rule_name>
type = ipv4
value_list = ('<your_public_ip>/32');

create network policy <policy_name>
allowed_network_rule_list = ('<rule_name>');

alter user <username> set network_policy = <policy_name>;
```

## Test locally

```bash
curl -sS -i \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <pat_token_secret>" \
-H "X-Snowflake-Authorization-Token-Type: PROGRAMMATIC_ACCESS_TOKEN" \
-d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' \
"https://<account>.snowflakecomputing.com/api/v2/databases/<database_name>/schemas/<schema_name>/mcp-servers/<mcp_server_name>"
```

## Add to Claude Code

```bash
claude mcp add --transport http snowflake \
"https://<account>.snowflakecomputing.com/api/v2/databases/<database_name>/schemas/<schema_name>/mcp-servers/<mcp_server_name>" \
--header "Authorization: Bearer <pat_token_secret>" \
--header "X-Snowflake-Authorization-Token-Type: PROGRAMMATIC_ACCESS_TOKEN"
```

To test it worked, run the following:
```bash
claude mcp list
```

If the above worked, once you run Claude Code it can now execute queries on your Snowflake datasets.

## Notes
- If Claude Code is currently running you will need to restart it
- PATs expire; rotate before expiry. 
- If your public IP changes, update the network rule.
