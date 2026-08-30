---
name: ahasend-handle-bounces-and-suppressions
description: Keep an AhaSend sending list clean — read the automatic suppression list, add and remove entries, and react to bounce events.
api: AhaSend Suppressions API
base_url: https://api.ahasend.com/v2
operations:
  - getSuppressions
  - createSuppression
  - deleteSuppression
  - deleteAllSuppressions
  - getBounceStatistics
generated: '2026-08-30'
method: generated
source: openapi/_original/ahasend-openapi-v2.yaml, https://ahasend.com/docs/api-reference/suppressions/create-suppression.md
---

# Handle bounces and suppressions

AhaSend suppresses bounces and complaints **automatically**. This skill is about reading that list,
adding your own entries, and removing one when an address recovers.

## Step 1 — react to the event, not to a poll

Subscribe to `message.bounced`, `message.failed` and `suppression.created` (see
`ahasend-wire-webhook-events`). Handlers must be idempotent: AhaSend retries a failed delivery six
times over 16+ minutes, so the same event can arrive more than once. Use the `webhook-id` header as
your dedupe key.

## Step 2 — read the list

`getSuppressions` (`GET /v2/accounts/{account_id}/suppressions`) — cursor paginated with
`limit` (1–100), `after`, `before`. Each entry carries `email`, optional `domain`, `reason` and
`expires_at`; a suppression can lapse on its own. Scope: `suppressions:read`.

## Step 3 — add a suppression

`createSuppression` (`POST /v2/accounts/{account_id}/suppressions`) with `suppressions:write`.
Send an `Idempotency-Key`. Optionally scope it to one sending `domain` instead of the whole
account.

## Step 4 — remove one

`deleteSuppression` (`DELETE /v2/accounts/{account_id}/suppressions?email=...`), optionally with
`domain` to remove only that domain's entry. Scope: `suppressions:delete`. This is the reversal of
step 3 and has no time window.

## Do not call deleteAllSuppressions

`deleteAllSuppressions` (`DELETE /v2/accounts/{account_id}/suppressions/all`) is irreversible and
account-wide. It destroys the bounce and complaint history that protects the account's sender
reputation. It has its own scope (`suppressions:wipe`) precisely because it is not the same
operation as removing one address. Never issue it from an autonomous flow; require an explicit
human confirmation.

## Step 5 — watch the trend

`getBounceStatistics` (`GET /v2/accounts/{account_id}/statistics/transactional/bounce`) groups
bounce classifications over `from_time`/`to_time`, optionally by sender or recipient domain.

**It is limited to 1 request per second with no burst allowance** — cache the answer and query
broad ranges rather than many narrow ones. It is one of only three operations whose `429` declares
a `Retry-After` header.
