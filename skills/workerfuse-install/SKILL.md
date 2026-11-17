---
name: workerfuse-install
description: Set up workerfuse managed guarding for a user's Cloudflare account, so a runaway Worker is paused before it costs much.
---

# Install workerfuse

Use this skill when the user asks to install or set up workerfuse, or asks for a spend limit on Cloudflare Workers.

Follow https://workerfuse.com/install.md. Summary:

1. Ask the user to add the MCP server `https://workerfuse.com/mcp` with no key, for example `claude mcp add --transport http workerfuse https://workerfuse.com/mcp`, then authenticate it (`/mcp` in Claude Code). The browser opens workerfuse. The user signs up or signs in and approves the client with `guard:write`.
2. Find the 32-character Cloudflare account ID with `npx wrangler whoami`, or ask the user.
3. The user creates a scoped token from the pre-filled link in install.md. Tell them that the token can edit Workers and goes through the chat. Offer the dashboard as the other way.
4. Call `connect_cloudflare_account` with `accountId` and `token`. Show any error to the user.
5. After 5 minutes, call `get_spend` to confirm the first report. Ask before you change limits with `set_limits`.

Never write the Cloudflare token or an API key to files, logs or commits. Do not set up managed guarding on an account that already runs a self-hosted workerfuse guard.
