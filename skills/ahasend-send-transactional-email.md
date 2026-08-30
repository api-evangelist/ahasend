---
name: ahasend-send-transactional-email
description: Send a transactional email through AhaSend API v2, retry it safely, and cancel it if it was scheduled.
api: AhaSend Messages API
base_url: https://api.ahasend.com/v2
operations:
  - createMessage
  - createConversationMessage
  - getMessage
  - getMessages
  - cancelMessage
generated: '2026-08-30'
method: generated
source: openapi/_original/ahasend-openapi-v2.yaml, https://ahasend.com/docs/send-api/send-email.md
---

# Send a transactional email with AhaSend

## Before you start

- The sending domain must already exist on the account and have valid DNS. If it does not, run
  `ahasend-onboard-sending-domain` first — a send from an unverified domain fails validation.
- The API key needs `messages:send:all` or `messages:send:{domain}` for the sending domain.
- Every call is scoped by account: `POST /v2/accounts/{account_id}/messages`.

## Step 1 — choose the endpoint

- `createMessage` (`POST /v2/accounts/{account_id}/messages`) is the normal send. It supports
  `substitutions` rendered into `{{variable}}` placeholders and multiple recipients.
- `createConversationMessage` (`POST /v2/accounts/{account_id}/messages/conversation`) is the only
  way to set CC or BCC. It does **not** support substitutions. Pick one; you cannot have both.

## Step 2 — build the request

Required: `from` (an address on a verified domain), `recipients`, `subject`, and at least one of
`text_content` / `html_content`. Validation rules the contract enforces:

- `retention.metadata` must be 1–30 days; `retention.data` must be 0–30 days.
- If you set `reply_to`, do not also put a `reply-to` in `headers`.
- A `message-id` header you supply is ignored and regenerated.
- `schedule.first_attempt` must be RFC 3339, in the future, and within 7 days of the request.

## Step 3 — always send an Idempotency-Key

Set `Idempotency-Key` to a v4 UUID (max 255 characters). This is the difference between a retry
and a duplicate email:

- A stored response replays for 24 hours, matched on account + key + method + path + SHA-256 of
  the body. Check the `Idempotent-Replayed` response header to tell a replay from a fresh send.
- A `5xx` or an attempt that never completed stores nothing, so retrying the same key re-executes.
- `409` means an identical request is still in flight — wait and retry the **same** key.
- `412` means the original failed permanently — generate a **new** key.
- `422` means you reused the key with a different body or endpoint. Never do that.

## Step 4 — rehearse in sandbox first

Add `"sandbox": true` to the body (or use a sandbox-mode credential). The message is validated,
parsed and fires every webhook, but nothing is delivered and nothing is charged. Force the outcome
with `sandbox_result`: `deliver`, `bounce`, `defer`, `fail`, `suppress`. Do this before any
unscheduled production send — an immediate send has no reversal.

## Step 5 — read the result and follow up

- The response carries the message `id`. Use `getMessage` for raw plus parsed content, or
  `getMessages` to list summaries filtered by `status`, `sender`, `recipient`, `subject`,
  `message_id_header`, `tags`, `from_time`/`to_time`.
- If you scheduled the send, `cancelMessage`
  (`DELETE /v2/accounts/{account_id}/messages/{message_id}/cancel`) withdraws it — but only while
  it is still scheduled and unsent, and it needs `messages:cancel:all` or
  `messages:cancel:{domain}`.
- Prefer webhooks over polling. `getMessages` shares the 100 req/s account budget; the statistics
  endpoints are capped at 1 req/s.

## Errors

Branch on the HTTP status only — the body is `{"message": "..."}` with no stable machine code, and
the contract explicitly says not to parse it. `400` invalid input, `401` bad credential, `403`
missing scope **or** IP not on the key's allow list (indistinguishable), `429` rate limited (back
off exponentially with jitter), `5xx` safe to retry with the same idempotency key.
