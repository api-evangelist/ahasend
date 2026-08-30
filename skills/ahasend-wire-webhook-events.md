---
name: ahasend-wire-webhook-events
description: Create an AhaSend webhook, verify its Standard Webhooks signature correctly, and survive retries and auto-disable.
api: AhaSend Webhooks API
base_url: https://api.ahasend.com/v2
operations:
  - createWebhook
  - getWebhooks
  - getWebhook
  - updateWebhook
  - deleteWebhook
generated: '2026-08-30'
method: generated
source: openapi/_original/ahasend-openapi-v2.yaml, https://ahasend.com/docs/api-reference/webhooks/security.md, https://ahasend.com/docs/webhooks.yaml
---

# Wire AhaSend webhook events

## Step 1 — create the webhook

`createWebhook` (`POST /v2/accounts/{account_id}/webhooks`) with an `Idempotency-Key`.

Pick a scope up front:

- **global** — needs `webhooks:write:all`. Supplied `domains` are accepted but ignored and the
  response returns `domains: []`.
- **scoped** — needs a non-empty `domains` array and write permission on *every* domain listed.

Enable only the events you handle, via the boolean flags: `on_reception`, `on_delivered`,
`on_transient_error`, `on_failed`, `on_bounced`, `on_suppressed`, `on_opened`, `on_clicked`,
`on_suppression_created`, `on_dns_error`. The eleven event payloads are specified at
`https://ahasend.com/docs/webhooks.yaml` (mirrored at `asyncapi/ahasend-webhooks-openapi.yaml`).

## Step 2 — verify the signature the way AhaSend signs

AhaSend follows the Standard Webhooks specification: `webhook-id`, `webhook-timestamp`,
`webhook-signature`, HMAC-SHA256 over `id.timestamp.body`.

**The one thing that breaks integrations:** the HMAC key is the *literal UTF-8 bytes of the secret,
including its prefix*. Several Standard Webhooks libraries Base64-decode the secret in their
default constructor, derive a different key, and reject valid AhaSend deliveries. Use raw-key mode
— `NewWebhookRaw` in Go, `new Webhook(secret, { format: "raw" })` in JavaScript.

Verify against the **raw request body bytes**, before JSON parsing. Reject any delivery whose
`webhook-timestamp` falls outside your tolerance window in either direction.

## Step 3 — be idempotent

Return any `2xx` to acknowledge. A non-2xx is retried **6 times over 16+ minutes**, with backoff of
`attempts ^ 4` plus ±10% jitter. Dedupe on `webhook-id`, which AhaSend recommends as the
idempotency key. Retried delivery events are a documented reality, not an edge case.

## Step 4 — survive auto-disable

**After 100 consecutive failures AhaSend disables the webhook** and emails every account member.
Monitor `error_count`, `errors_since_last_success` and `last_request_at` on `getWebhook`, and
re-enable through `updateWebhook` once the endpoint is healthy.

`updateWebhook` semantics are precise: omitted or null fields are unchanged; an omitted/null
`domains` preserves associations while `[]` clears them; moving global → scoped requires a
non-empty `domains` in the same request; moving → global clears associations.

## Step 5 — test locally

Run the AhaSend CLI's webhook listener to stream live events to localhost — no public endpoint
needed. Pair it with sandbox mode: `"sandbox": true` plus `sandbox_result` of
`deliver`/`bounce`/`defer`/`fail`/`suppress` fires the matching event without sending mail.
