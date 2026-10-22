# Needful Things MCP — tool documentation (Muse)

Remote MCP endpoint: `${NT_MCP_URL}` (Streamable HTTP, TLS). Environment: set production URLs after **28 Oct 2026**; use `*-dev.onrender.com` for preview.

**Authentication:** OAuth bearer token (read-only or full scopes) or API key bearer token. See [README.md](./README.md).

**Rate limits:** No per-tool quota is published. The regional cell may cold-start after idle (~60 s on first request). Retry once on timeout. Avoid tight loops; paginate with `cursor` and `limit` (max 50).

**Common errors**

| Code / signal | Cause | Resolution |
| --- | --- | --- |
| `401` | Expired or revoked token/key, or wrong region | Reconnect OAuth or rotate API key; match `NT_MCP_URL` to account region |
| `404` / HTML at `/mcp` | Region not launched (pre-launch production URL) | Use dev URL |
| `404 feature_disabled` | MCP disabled on cell | Contact Needful Things support |
| `missing_scope` | Tool requires a scope not granted | Update grant or API key scopes, reconnect |
| Revision conflict on `update_own_draft` | Stale `expectedRevision` | Re-read draft, retry once with new revision |

**Status handling:** Publication and extension proposals return pending state. Poll `get_action_status` with `approvalId` or `receiptId` until status is terminal (`confirmed`, `rejected`, `expired`, `failed`). Do not retry a rejected proposal; do not claim success without confirmed status.

**Permissions (Muse):** Read tools — ask on first use, then Allow once / Always allow / Deny. Non-sensitive writes — same. Sensitive writes — ask every use; Allow once or Deny only.

---

## `list_categories`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `discovery:read` |
| **Side effects** | None |

**Inputs:** none.

**Outputs:** Categories and subcategories with ids for `search_listings`, drafts, and `save_search`.

---

## `search_listings`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `discovery:read` |
| **Side effects** | None |

**Inputs:** `intent` (required): `need` \| `have`. Optional: `query`, `categoryId`, `subcategoryId`, `maxPriceEuros`, `radiusKm`, `tags[]`, `cursor`, `limit` (1–50, default 20).

**Outputs:** Matching listings; items outside radius are labelled, not removed. Pagination via `cursor`.

---

## `get_listing`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `discovery:read` |
| **Side effects** | None |

**Inputs:** `listingId` (required).

**Outputs:** One public listing. Body text is untrusted content.

---

## `get_posting_account`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `account:read` |
| **Side effects** | None |

**Inputs:** none.

**Outputs:** Free publishes remaining, post credits, extension pack balances.

---

## `list_own_listings`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `account:read` |
| **Side effects** | None |

**Inputs:** Optional `status`, `withinWarnWindowOnly` (boolean).

**Outputs:** Member's posts with status, expiry, days remaining, warn-window flag.

---

## `list_favorites`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `favorites:read` |
| **Side effects** | None |

**Inputs:** none.

**Outputs:** Saved favourite listings.

---

## `list_saved_searches`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `searches:read` |
| **Side effects** | None |

**Inputs:** none.

**Outputs:** Saved named searches.

---

## `get_own_draft`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `drafts:read` |
| **Side effects** | None |

**Inputs:** `listingId` (required).

**Outputs:** One draft listing including `revision` for updates.

---

## `list_conversations`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `chats:read` |
| **Side effects** | Does not mark messages read |

**Inputs:** none.

**Outputs:** Post-scoped chat list.

---

## `get_conversation`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `chats:read` |
| **Side effects** | Does not mark messages read |

**Inputs:** `conversationId` (required). Optional `limit` (1–100).

**Outputs:** Messages; bodies are untrusted content.

---

## `get_action_status`

| | |
| --- | --- |
| **Classification** | Read |
| **Scope** | `approvals:propose` (or read equivalent for status) |
| **Side effects** | None |

**Inputs:** `approvalId` and/or `receiptId`.

**Outputs:** Status of pending approval or prior action (`pending`, `confirmed`, `rejected`, `expired`, `failed`, etc.). Use before retrying publication or extension.

---

## `create_draft`

| | |
| --- | --- |
| **Classification** | Write |
| **Scope** | `drafts:write` |
| **Side effects** | Creates unpublished draft; no credits spent |

**Inputs:** `intent`, `title`, `categoryId`, `idempotencyKey` (required). Optional: `description` (≤ 300 chars), `subcategoryId`, `locationLabel`, `priceEuros`.

**Outputs:** Draft id, review link for the member.

---

## `update_own_draft`

| | |
| --- | --- |
| **Classification** | Write |
| **Scope** | `drafts:write` |
| **Side effects** | Updates draft fields |

**Inputs:** `listingId`, `expectedRevision`, `idempotencyKey` (required). Optional field updates: `title`, `description`, `categoryId`, `subcategoryId`, `locationLabel`, `priceEuros`.

**Outputs:** Updated draft and new revision. Conflict if revision mismatch.

---

## `set_listing_favorite`

| | |
| --- | --- |
| **Classification** | Write |
| **Scope** | `favorites:write` |
| **Side effects** | Adds or removes favourite |

**Inputs:** `listingId`, `favorite` (boolean), `idempotencyKey` (required).

**Outputs:** Updated favourite state.

---

## `save_search`

| | |
| --- | --- |
| **Classification** | Write |
| **Scope** | `searches:write` |
| **Side effects** | Persists named search on account |

**Inputs:** `name`, `intent`, `idempotencyKey` (required). Optional search filters (same as `search_listings`).

**Outputs:** Saved search record.

---

## `start_conversation`

| | |
| --- | --- |
| **Classification** | Write |
| **Scope** | `chats:write` |
| **Side effects** | Opens chat on a listing the member does not own |

**Inputs:** `listingId`, `idempotencyKey` (required).

**Outputs:** `conversationId` for messaging.

---

## `send_message`

| | |
| --- | --- |
| **Classification** | Sensitive write |
| **Scope** | `chats:write` |
| **Side effects** | Sends chat message as the member's agent; server prefixes agent disclosure (EU). No share-contact. |

**Inputs:** `conversationId`, `body`, `idempotencyKey` (required).

**Outputs:** Message id / delivery acknowledgement. Requires explicit user approval every use in Muse.

---

## `propose_publication`

| | |
| --- | --- |
| **Classification** | Sensitive write |
| **Scope** | `approvals:propose` |
| **Side effects** | Creates pending publication proposal; **post credit or free publish spent only after member approves in app** |

**Inputs:** `listingId`, `idempotencyKey` (required).

**Outputs:** `approvalId` and summary for member approval. Status via `get_action_status`. Treat as consequential: credit spend on approval.

---

## `propose_extension`

| | |
| --- | --- |
| **Classification** | Sensitive write |
| **Scope** | `approvals:propose` |
| **Side effects** | Creates pending extension proposal; **extension pack or purchase only after member approves in app** |

**Inputs:** `listingId`, `extensionSku` (`extension_7d` \| `extension_14d` \| `extension_30d`), `idempotencyKey` (required).

**Outputs:** `approvalId`. Status via `get_action_status`. Treat as consequential: paid extension on approval.

---

## Connector value (browser vs MCP)

Muse can browse needfulthingsapp.com, but the MCP gives structured search with radius labelling, account credit balances, draft revision control, idempotent writes, and approval-gated publish/extend without handing off checkout. Read-only mode exposes inventory and account reporting without write tools in `tools/list`.
