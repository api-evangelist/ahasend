---
name: ahasend-provision-sub-account
description: Provision an isolated AhaSend sub account over the API, mint its first key, and read its usage — the Platform Partner flow.
api: AhaSend Sub Accounts API
base_url: https://api.ahasend.com/v2
operations:
  - createSubAccount
  - listSubAccounts
  - getSubAccount
  - updateSubAccount
  - suspendSubAccount
  - unsuspendSubAccount
  - deleteSubAccount
  - createSubAccountAPIKey
  - listSubAccountAPIKeys
  - getSubAccountAPIKey
  - updateSubAccountAPIKey
  - deleteSubAccountAPIKey
  - getSubAccountsUsage
generated: '2026-08-30'
method: generated
source: openapi/_original/ahasend-openapi-v2.yaml, https://ahasend.com/docs/api-reference/sub-accounts/overview.md
---

# Provision a sub account

For platforms, agencies and hosting providers reselling AhaSend. Every call here uses **parent**
credentials — a sub account's own key can never manage its nested keys.

## Step 1 — create the sub account

`createSubAccount` (`POST /v2/accounts/{account_id}/sub-accounts`) with `sub-accounts:write` and an
`Idempotency-Key`. The sub account gets its own domains, sending, webhooks, routes and members,
billed through the parent.

## Step 2 — mint its first API key, and catch the secret

`createSubAccountAPIKey`
(`POST /v2/accounts/{account_id}/sub-accounts/{sub_account_id}/api-keys`) with
`sub-account-api-keys:write`.

**The `201` response is the only time you ever see `secret_key`.** It is omitted from every list,
get, update and delete response. An exact idempotent retry replays it for **5 minutes** — not the
usual 24 hours — and after that it is unrecoverable and you must create a new key. Persist it
inside the same transaction that issues the call.

Set the key's scopes narrowly, and consider an `ip_allow_list` (up to 100 IPv4/IPv6 addresses or
CIDR ranges). A key with a non-empty allow list is rejected from any other IP on every endpoint,
regardless of its scopes.

## Step 3 — manage the lifecycle

- `listSubAccounts` / `getSubAccount` / `updateSubAccount` — `sub-accounts:read` / `:write`.
  Update rejects an empty or no-change request and leaves omitted fields untouched.
- `suspendSubAccount` / `unsuspendSubAccount` — `sub-accounts:suspend`. This pair is fully
  reversible; `status` moves between `active` and `suspended`. A sub account also shows
  `parent-suspended` when the parent itself is suspended.
- `deleteSubAccount` — `sub-accounts:delete`. **Soft delete with no published restore.** The sub
  account moves to status `deleted` and disappears from the list endpoint. Treat it as final.

## Step 4 — read usage and cost

`getSubAccountsUsage` (`GET /v2/accounts/{account_id}/sub-accounts/usage`) with
`sub-accounts:usage` returns per-sub `reception_count` and `allocated_cost`.

Note `removed_sub_accounts`: usage accrued by sub accounts soft-deleted during the current period
is aggregated there, still billed to the parent, and no longer attributable to an individual sub.
Reconcile against that aggregate or your per-sub totals will not sum to the invoice.

## Reputation note

A partner's sending-pool tier is calculated from the combined volume and spam rate of the parent
and all its sub accounts, and a sub inherits the parent's account age and subscription standing —
so a new sub of an established partner starts on reputation-appropriate IPs. Each sub is still
assessed on its own spam rate for demotion.
