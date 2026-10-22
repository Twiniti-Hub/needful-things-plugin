# Needful Things for Grok Build

Grok Build plugin for **Needful Things**, the agent-friendly second-hand marketplace by Twiniti. Browse categories and listings, check your posting credits and posts, and prepare drafts and publication proposals that you approve in the app.

This plugin is configuration only: it points Grok Build at the existing Needful Things remote MCP server (HTTP) and adds skills and a safety rule. No hooks, no scripts, no secrets.

- Plugin id: `needful-things`
- Version: `0.1.0`
- License: [MIT](LICENSE-MIT) OR [Apache-2.0](LICENSE-APACHE), your choice. See [LICENSE](LICENSE).

## Launch status

Production launches on **28 October 2026**. Until then the production URLs return 404. To preview, set `NT_MCP_URL` to a development URL below.

## Configure

| Variable | Default | Notes |
| --- | --- | --- |
| `NT_MCP_URL` | `https://nt-market-eu-prd.onrender.com/mcp` | Pick your account's region. Sign-in tokens are region-bound. |

| Region | Production | Development / preview |
| --- | --- | --- |
| EU | `https://nt-market-eu-prd.onrender.com/mcp` | `https://nt-market-eu-dev.onrender.com/mcp` |
| UK | `https://nt-market-uk-prd.onrender.com/mcp` | `https://nt-market-uk-dev.onrender.com/mcp` |
| US | `https://nt-market-us-prd.onrender.com/mcp` | `https://nt-market-us-dev.onrender.com/mcp` |

## Sign in

No API key or token to paste. Grok Build handles OAuth:

1. Run `/mcps`, select `needful-things`, press `i`.
2. Sign in to Needful Things in the browser and choose scopes on the consent screen (Profile → My agents).
3. Recommended: all read scopes plus `drafts:write` and `approvals:propose`. `favorites:write`, `searches:write` and `chats:write` are opt-in.
4. After sign-in or reconnect, the agent should call read-only `whoami` and say: "I'm connected to {display name}'s Needful Things account ({market})." If `whoami` is not on the server yet, it should say the account could not be confirmed.

Manage, pause or revoke the connection any time in Profile → My agents.

## Smoke test

After connection, call `whoami` (read-only, `account:read`) to confirm the account, then `list_categories` (read-only).

## What agents can and cannot do

- Can: search listings, read listings, credits, own posts, favourites, saved searches, drafts and chats; create and edit drafts; propose publication or extension.
- Approval-only: publishing and extensions — the member approves in the app before any credit is spent.
- Opt-in: favourites, saved searches, chat messages (always labelled as sent by your agent).
- Never: share contact details, pay, or publish without approval.

## Contents

```
.grok-plugin/plugin.json   .cursor-plugin/plugin.json
.mcp.json  mcp.json        (same config: {type: http, url: ${NT_MCP_URL}})
rules/needful-things-safety.mdc
skills/nt-mcp  skills/nt-discovery  skills/nt-drafts
```
