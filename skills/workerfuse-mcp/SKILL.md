---
name: workerfuse-mcp
description: Connect to the workerfuse MCP server, read estimated Cloudflare Worker spend and events, and pause, resume or set limits with user approval.
---

# workerfuse MCP

workerfuse checks Cloudflare spend every five minutes and pauses a Worker script that passes its limit. Use this skill when the user asks about Cloudflare Worker cost, runaway Workers, spend limits, or a paused script.

## Connect

- Endpoint: `https://workerfuse.com/mcp` (Streamable HTTP, JSON responses, no session).
- Auth: add the URL with no key. The client gets a 401 and starts OAuth. The user signs in and approves scopes in the browser: `spend:read` for reads, `guard:write` for changes. Tool calls also need a trial or an active subscription.
- A client that cannot do OAuth can send a `wf_` API key as `Authorization: Bearer <key>`. The key has full access.

Claude Code:

```sh
claude mcp add --transport http workerfuse https://workerfuse.com/mcp
```

Then run `/mcp` and choose **Authenticate**. A tool call that gets 403 with `insufficient_scope` needs the user to approve that scope.

## Tools

`accountId` is the 32-character Cloudflare account ID. It is not the workerfuse login ID. Call `list_accounts` to get it. You can omit `accountId` when the login has one account.

| Tool | Type | Use |
|---|---|---|
| `list_accounts` | read | Connected Cloudflare accounts, their mode (managed or self-hosted) and last report time. |
| `get_spend` | read | Latest estimated hourly and UTC-day spend for each script. |
| `list_events` | read | Latest 100 reports that contain pauses or alerts. |
| `pause_script` | write | Queue a pause of `scriptName`. |
| `resume_script` | write | Queue a restore of the latest recorded pause of `scriptName`. |
| `set_limits` | write | Replace all limits: `hourlyUsd`, `dailyUsd`, `storageUsd`, `skip`, `dryRun`, and optional `d1RowsPerDay`. |
| `connect_cloudflare_account` | write | Start managed guarding with a scoped Cloudflare `token`. See https://workerfuse.com/install.md. |

## Procedure

1. Call `get_spend` first. Report the `receivedAt` time (ISO 8601, UTC) with the numbers. If it asks for `accountId`, call `list_accounts` and ask the user which account.
2. Before a write tool, show the user the exact arguments and get approval.
3. `set_limits` replaces every setting. Read the current settings from the dashboard or ask the user first, so you do not clear `skip` or `d1RowsPerDay` by mistake.
4. A write tool only queues a command. The guard runs it on its next run, which can take about 10 minutes. Call `list_events` later to confirm.

## Facts to keep

- Spend values are estimates from Cloudflare analytics. They use list prices and ignore included allowances, so they are high on purpose. They are not live billing.
- workerfuse is a safety net, not a billing cap.
- A pause deploys a stub version of only that script. Other scripts keep running.
- Never pause the guard Worker itself (`workerfuse`). The server refuses it.

## More

- Docs: https://workerfuse.com/developers
- Auth: https://workerfuse.com/auth.md
- Pricing: https://workerfuse.com/pricing.md
- Support: support@workerfuse.com
