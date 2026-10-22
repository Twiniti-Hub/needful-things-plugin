---
name: nt-mcp
description: Use when connecting Muse to Needful Things, choosing a region, OAuth or API key auth, checking scopes, or troubleshooting the Needful Things MCP (401, 404, feature_disabled, timeouts).
---

# Needful Things MCP — connect and troubleshoot (Muse)

The Needful Things MCP is a remote HTTP server on each regional market cell. This plugin points Muse at it; Muse runs OAuth or uses a configured API key.

## Connect

1. Set `NT_MCP_URL` to the member's region (tokens and API keys are region-bound):
   - Production (from 28 Oct 2026): `https://nt-market-{eu,uk,us}-prd.onrender.com/mcp`
   - Preview / development: `https://nt-market-{eu,uk,us}-dev.onrender.com/mcp`
2. **OAuth (recommended):** In Muse, connect the Needful Things connector and sign in in the browser. On the consent screen (Needful Things → Profile → My agents):
   - **Read only:** enable only `discovery:read`, `account:read`, `favorites:read`, `searches:read`, `drafts:read`, `chats:read`.
   - **Full (recommended for listing workflows):** all read scopes plus `drafts:write` and `approvals:propose`. Opt-in only: `favorites:write`, `searches:write`, `chats:write`.
3. **API key (alternative):** Create a key in Needful Things → Profile → My agents → API keys. Configure Muse with that key per your connector credentials (never paste keys into chat or commit them). Keys inherit the scopes chosen at creation; create separate read-only and full keys if needed.
4. Smoke test: call `list_categories` (read-only, no arguments). When deployed, call read-only `whoami` first and confirm account and market with the user.

Treat `tools/list` as authoritative: tools without a granted scope are not listed.

## Troubleshoot

| Symptom | Meaning |
| --- | --- |
| HTML page or `not_found` from `/mcp` | That region is not launched yet (production before 28 Oct). Use the dev URL. |
| `404 feature_disabled` | Remote MCP is switched off on that cell. Contact Needful Things support. |
| `401` | Token or API key expired, revoked, or for a different region. Reconnect OAuth or rotate the key. |
| Slow first call (60 s+) | The cell is waking from idle. Retry once. |
| `missing_scope` | Add the scope in Profile → My agents, then reconnect or use a key with that scope. |

Paused or revoked connections are managed in Profile → My agents.
