---
name: workerfuse
description: Read estimated Cloudflare Worker spend and events from workerfuse, and pause, resume or set limits with user approval.
---

# workerfuse

workerfuse checks Cloudflare spend every five minutes and pauses a Worker script that passes its limit. Use this skill when the user asks about Cloudflare Worker cost, runaway Workers, spend limits, or a paused script.

## Tools

`accountId` is the 32-character Cloudflare account ID. You can omit it when the login has one account. Call `list_accounts` to get it.

| Tool | Type | Use |
|---|---|---|
| `list_accounts` | read | Connected Cloudflare accounts, their mode and last report time. |
| `get_spend` | read | Latest estimated hourly and UTC-day spend for each script. |
| `list_events` | read | Latest 100 reports that contain pauses or alerts. |
| `pause_script` | write | Queue a pause of `scriptName`. |
| `resume_script` | write | Queue a restore of the latest recorded pause of `scriptName`. |
| `set_limits` | write | Replace all limits: `hourlyUsd`, `dailyUsd`, `storageUsd`, `skip`, `dryRun`, and optional `d1RowsPerDay`. |

## Procedure

1. Call `get_spend` first. Report the `receivedAt` time (ISO 8601, UTC) with the numbers. If it asks for `accountId`, call `list_accounts` and ask the user which account.
2. If no Cloudflare account is connected, tell the user to connect one on the workerfuse dashboard at https://workerfuse.com/dashboard. Never ask the user for a Cloudflare token in the chat.
3. Before a write tool, show the user the exact arguments and get approval.
4. `set_limits` replaces every setting. Ask the user for the current settings first, so you do not clear `skip` or `d1RowsPerDay` by mistake.
5. A write tool only queues a command. The guard runs it on its next run, which can take about 10 minutes. Call `list_events` later to confirm.

## Facts to keep

- Spend values are estimates from Cloudflare analytics. They use list prices and ignore included allowances, so they are high on purpose. They are not live billing.
- workerfuse is a safety net, not a billing cap.
- A pause deploys a stub version of only that script. Other scripts keep running.
- The server refuses to pause the guard Worker itself (`workerfuse`).
