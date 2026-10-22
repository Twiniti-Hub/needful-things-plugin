---
name: nt-mcp
description: Use when connecting Grok Build to Needful Things, choosing a region, checking scopes, or troubleshooting the Needful Things MCP (401, 404, feature_disabled, timeouts).
---

# Needful Things MCP — connect and troubleshoot

The Needful Things MCP is a remote HTTP server on each regional market cell. This plugin only points Grok Build at it; Grok Build runs the OAuth sign-in.

## Connect

1. Set `NT_MCP_URL` to your region (tokens are region-bound):
   - Production (from 28 Oct 2026): `https://nt-market-{eu,uk,us}-prd.onrender.com/mcp`
   - Preview / development: `https://nt-market-{eu,uk,us}-dev.onrender.com/mcp`
2. In Grok Build run `/mcps`, select `needful-things`, press `i`, and sign in with your Needful Things account in the browser.
3. On the consent screen (Profile → My agents) the recommended scopes are:
   - On: `discovery:read`, `account:read`, `favorites:read`, `searches:read`, `drafts:read`, `chats:read`, `drafts:write`, `approvals:propose`
   - Opt-in only: `favorites:write`, `searches:write`, `chats:write`
4. Smoke test: call `list_categories` (read-only, no arguments).

Treat `tools/list` as authoritative: tools you did not grant a scope for are not listed.

## Troubleshoot

| Symptom | Meaning |
| --- | --- |
| HTML page or `not_found` from `/mcp` | That region is not launched yet (production before 28 Oct). Use the dev URL. |
| `404 feature_disabled` | Remote MCP is switched off on that cell. Contact Needful Things support. |
| `401` | Token expired, revoked, or for a different region. Re-run `/mcps` → `i`. |
| Slow first call (60 s+) | The cell is waking from idle. Retry once. |
| `missing_scope` | Add the scope in Profile → My agents, then reconnect. |

Paused or revoked connections are managed in Profile → My agents.
