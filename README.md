# workerfuse plugin

[workerfuse](https://workerfuse.com) checks Cloudflare Workers spend every five minutes. It pauses a Worker script that passes its limit, before the script costs much.

This repository holds the plugin manifests, the MCP server configuration and two agent skills. It does not hold the workerfuse source code.

## MCP server

- URL: `https://workerfuse.com/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 with dynamic client registration. Add the URL with no key. Your client opens the browser so that you can sign in and approve it.

Claude Code:

```sh
claude mcp add --transport http workerfuse https://workerfuse.com/mcp
```

Codex:

```sh
codex mcp add workerfuse --url https://workerfuse.com/mcp
```

Cursor, in `~/.cursor/mcp.json`:

```json
{ "mcpServers": { "workerfuse": { "url": "https://workerfuse.com/mcp" } } }
```

## Tools

| Tool | Type | Use |
|---|---|---|
| `list_accounts` | read | Connected Cloudflare accounts, their mode and last report time. |
| `get_spend` | read | Latest estimated hourly and UTC-day spend for each script. |
| `list_events` | read | Latest 100 reports that contain pauses or alerts. |
| `pause_script` | write | Queue a pause of one script. |
| `resume_script` | write | Queue a restore of the latest recorded pause of one script. |
| `set_limits` | write | Replace the hourly, daily and storage limits, the skip list and dry-run. |
| `connect_cloudflare_account` | write | Start managed guarding with a scoped Cloudflare token. |

## Skills

- `skills/workerfuse-install`: set up workerfuse for a user.
- `skills/workerfuse-mcp`: use the MCP tools safely.

## Links

- Docs: https://workerfuse.com/developers
- Pricing: https://workerfuse.com/pricing.md
- Privacy: https://workerfuse.com/privacy
- Terms: https://workerfuse.com/terms
- Support: support@workerfuse.com
