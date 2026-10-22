---
name: nt-drafts
description: Use when the user wants to create or edit a Needful Things post, publish it, or extend an active post — drafts plus approval-only publication and extension proposals.
---

# Needful Things drafts and approvals

## Drafts (`drafts:write`, `drafts:read`)

- `create_draft` — unpublished draft; spends nothing. Description up to 300 characters. Returns a review link: give it to the user.
- `get_own_draft` — read one draft.
- `update_own_draft` — pass `expectedRevision` from the last read; on conflict, re-read and retry once.

## Publish or extend (`approvals:propose`)

1. `propose_publication` (draft → live) or `propose_extension` (`extension_7d` / `14d` / `30d`).
2. Tell the user: "Approve it in Needful Things → Profile → My agents." Credits are only spent on approval.
3. `get_action_status` to check. Never say a post is published or extended until the status confirms it.

Check `get_posting_account` first so you can tell the user whether a free publish or credit is available.

## Do not

- Do not use `send_message` or `start_conversation` to tell buyers or sellers about a post. Draft the text for the user instead.
- Do not retry a proposal the user rejected.
