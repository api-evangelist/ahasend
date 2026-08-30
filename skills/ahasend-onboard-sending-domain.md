---
name: ahasend-onboard-sending-domain
description: Add a sending domain to AhaSend, publish its DNS records, and drive it to verified.
api: AhaSend Domains API
base_url: https://api.ahasend.com/v2
operations:
  - createDomain
  - getDomain
  - getDomains
  - checkDomainDNS
  - updateDomain
  - deleteDomain
generated: '2026-08-30'
method: generated
source: openapi/_original/ahasend-openapi-v2.yaml, https://ahasend.com/docs/domains.md
---

# Onboard a sending domain

Nothing can be sent from a domain until this completes. Scope needed: `domains:write` to create
and update, `domains:read` to check.

## Step 1 — create the domain

`createDomain` (`POST /v2/accounts/{account_id}/domains`). Send an `Idempotency-Key`; a duplicate
create returns `409 {"message": "domain already exists"}`.

The response carries `dns_records` — the SPF, DKIM and return-path records to publish. It also
carries `dns_valid: false`, `id`, and the subdomain fields (`tracking_subdomain`,
`return_path_subdomain`, `subscription_subdomain`, `media_subdomain`).

## Step 2 — publish the DNS records

Publish every record in `dns_records` at the registrar. Each subdomain field is editable until its
record verifies, then it locks.

## Step 3 — validate

`checkDomainDNS` (`POST /v2/accounts/{account_id}/domains/{domain}/check-dns`) triggers a lookup.

**A result checked within the last 60 seconds is returned from cache**, so do not poll faster than
once a minute — you will get the same stale answer and burn rate-limit budget for nothing. Poll on
a 60-second-plus interval until `dns_valid` is true, with a ceiling on attempts; DNS propagation is
not instant.

## Step 4 — configure ongoing hygiene

`updateDomain` sets `dkim_rotation_interval_days` for automatic DKIM key rotation, the custom
tracking/return-path/subscription/media subdomains, and — on Platform Partner accounts — a
per-domain `dkim_selector`. The selector can be changed only while DKIM is unverified; once
verified it locks and needs support to unlock.

## Step 5 — subscribe to the failure event

Create a webhook with `on_dns_error` enabled (see `ahasend-wire-webhook-events`). The
`domain.dns_error` event is how you find out a previously valid domain has broken, rather than
discovering it through failed sends.

## Deleting

`deleteDomain` is one-way and needs a **domain-specific** scope — `domains:delete:{domain}`. There
is deliberately no global delete scope. Re-creating the domain restarts verification from zero.
A suspended account cannot add, edit, delete or re-check DNS for any domain.
