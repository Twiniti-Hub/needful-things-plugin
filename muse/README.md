# Needful Things for Muse

Muse connector plugin for **Needful Things**, the agent-friendly second-hand marketplace by Twiniti. Browse categories and listings, check posting credits and posts, and prepare drafts and publication proposals that the member approves in the app.

This folder is configuration and documentation only: it points Muse at the existing Needful Things remote MCP server (HTTP) and adds skills and a safety rule. No hooks, no scripts, no secrets.

- Plugin id: `needful-things-muse`
- Version: `0.1.0`
- Homepage: [plugins.needfulthingsapp.com/needful-things/](https://plugins.needfulthingsapp.com/needful-things/)
- License: [MIT](../LICENSE-MIT) OR [Apache-2.0](../LICENSE-APACHE), your choice. See [LICENSE](../LICENSE).

## Launch status

Production launches on **28 October 2026**. Until then the production URLs return 404. To preview, set `NT_MCP_URL` to a development URL below.

## Configure

| Variable | Default | Notes |
| --- | --- | --- |
| `NT_MCP_URL` | `https://nt-market-eu-prd.onrender.com/mcp` | Pick the account's region. Sign-in tokens and API keys are region-bound. |

| Region | Production | Development / preview |
| --- | --- | --- |
| EU | `https://nt-market-eu-prd.onrender.com/mcp` | `https://nt-market-eu-dev.onrender.com/mcp` |
| UK | `https://nt-market-uk-prd.onrender.com/mcp` | `https://nt-market-uk-dev.onrender.com/mcp` |
| US | `https://nt-market-us-prd.onrender.com/mcp` | `https://nt-market-us-dev.onrender.com/mcp` |

## Authentication

Muse connects to Needful Things over **HTTPS** to the regional MCP URL. Use one of:

### OAuth (recommended)

1. Register OAuth client credentials with Needful Things for the Muse connector (client id and secret supplied to Muse through the connector platform intake — never commit them to this repo).
2. On connect, the member signs in in the browser and chooses scopes on the consent screen (Profile → My agents).
3. **Read-only option (Muse guideline 3.1):** grant only read scopes — `discovery:read`, `account:read`, `favorites:read`, `searches:read`, `drafts:read`, `chats:read`. Write tools will not appear in `tools/list`.
4. **Full listing workflow:** add `drafts:write` and `approvals:propose`. Opt-in writes: `favorites:write`, `searches:write`, `chats:write`.

Manage, pause, or revoke OAuth grants in Profile → My agents.

### API key (alternative)

1. In Needful Things → Profile → My agents → API keys, create a key with the scopes you need (read-only or including writes).
2. Configure the key in Muse connector credentials (environment or secure vault — not in chat, logs, or this repository).
3. The MCP accepts the key as a bearer token on requests to `NT_MCP_URL`. Rotate or revoke keys in the app.

Example placeholder for local testing only (replace with a real dev key from your account):

```bash
export NT_MCP_URL="https://nt-market-eu-dev.onrender.com/mcp"
# Bearer token configured in Muse connector settings, not exported in shell history for production.
```

## Smoke test

After connect, call read-only `list_categories`. When the server exposes it, call `whoami` first and confirm account and region with the member.

## What Muse can and cannot do

- **Can:** search listings, read listings, credits, own posts, favourites, saved searches, drafts and chats; create and edit drafts; propose publication or extension.
- **Approval-only:** publishing and extensions — the member approves in the app before any credit is spent.
- **Opt-in:** favourites, saved searches, chat messages (always labelled as sent by the member's agent).
- **Never:** share contact details, pay, or publish without approval.

## Tool reference

Full per-tool documentation for Muse connector review (setup, I/O, classifications, side effects, status, errors, rate limits) is in [TOOLS.md](./TOOLS.md).

## Contents

```
.muse-plugin/plugin.json
.mcp.json  mcp.json        (same config: {type: http, url: ${NT_MCP_URL}})
rules/needful-things-safety.mdc
skills/nt-mcp  skills/nt-discovery  skills/nt-drafts
TOOLS.md
```
