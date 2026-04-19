# AhaSend (ahasend)

AhaSend is a developer-focused transactional email platform providing fast, reliable email delivery via REST API and SMTP relay. It offers features including email tracking, webhooks, email routing, suppression management, domain management, SMTP credentials, and detailed delivery statistics.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/ahasend/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - Email, Transactional Email, Developer Tools, SMTP, Webhooks

## Timestamps

- **Created:** 2025-02-06
- **Modified:** 2026-04-19

## APIs

### AhaSend
Send, receive, and track transactional emails with the AhaSend REST API. Supports sending messages, managing domains, webhooks, routes, API keys, suppressions, SMTP credentials, and viewing delivery statistics.

**Human URL:** [https://ahasend.com/docs](https://ahasend.com/docs)

#### Tags:

 - Email, Transactional Email, REST API, SMTP, Webhooks

#### Properties

- [Documentation](https://ahasend.com/docs)
- [APIReference](https://ahasend.com/docs/api-reference)
- [AhaSend Email API v1](openapi/ahasend-openapi.yml)
- [AhaSend API v2](openapi/ahasend-openapi-v2.yaml)
- [Quickstart](https://ahasend.com/docs/quickstart)
- [Authentication](https://ahasend.com/docs/authentication)

## Common Properties

- [AhaSend Pricing](https://ahasend.com/pricing)
- [AhaSend Blog](https://ahasend.com/blog)
- [AhaSend Help Center](https://ahasend.com/help)
- [AhaSend Privacy Policy](https://ahasend.com/privacy)
- [AhaSend Terms of Service](https://ahasend.com/terms)
- [AhaSend Sign Up](https://dash.ahasend.com/user/register)
- [AhaSend Login](https://dash.ahasend.com/user/login)
- [AhaSend GitHub Organization](https://github.com/AhaSend)
- [Go SDK](https://github.com/AhaSend/ahasend-go)
- [AhaSend CLI](https://github.com/AhaSend/ahasend-cli)
- [AhaSend WordPress Plugin](https://github.com/AhaSend/wordpress-plugin)
- [Java SDK](https://github.com/AhaSend/ahasend-java-client)
- [AhaSend Affiliate Program](https://ahasend.com/affiliates)
- [AhaSend Spectral Rules](rules/ahasend-spectral-rules.yml)
- [AhaSend Email Operations Capability](capabilities/email-operations.yaml)
- [AhaSend Vocabulary](vocabulary/ahasend-vocabulary.yaml)

## Features

| Name | Description |
|------|-------------|
| Transactional Email Delivery | Fast delivery of transactional emails including OTPs and confirmations, targeting sub-2-second delivery to Gmail at 99th percentile. |
| Email Tracking | Track email opens and link clicks with real-time analytics. |
| Webhook Notifications | Real-time webhook events for delivery, bounces, opens, clicks, and account alerts. |
| Email Routing | Route incoming emails to HTTP endpoints with automatic parsing of signatures and quoted replies. |
| Suppression Management | Automated handling of bounces, complaints, and unsubscribes with suppression lists. |
| Domain Management | Manage sending domains including DNS validation, DKIM rotation, and whitelabeling. |
| SMTP Relay | Compatible SMTP relay supporting any programming language or software. |
| Dedicated IPs | Free dedicated IPs for high-volume senders exceeding 300k emails per month. |
| S3-Compatible Archiving | Archive emails to S3-compatible storage with configurable retention policies. |
| SSO with OIDC | Enterprise single sign-on via OpenID Connect with granular API credential scoping. |

## Use Cases

| Name | Description |
|------|-------------|
| Password Reset Emails | Send secure one-time password and password reset links with guaranteed fast delivery. |
| Email Verification | Deliver account verification emails for new user signups. |
| Order Confirmation Emails | Transactional order and shipping confirmation emails for e-commerce. |
| System Alerts | Programmatic email alerts and notifications from applications and infrastructure. |
| Inbound Email Processing | Route and process incoming emails in applications using email routing. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [ahasend-openapi-v2.yaml](openapi/ahasend-openapi-v2.yaml)
- [ahasend-openapi.yml](openapi/ahasend-openapi.yml)

### JSON Schema

- [api-access-denied-response-schema.json](json-schema/api-access-denied-response-schema.json)
- [api-attachment-schema.json](json-schema/api-attachment-schema.json)
- [api-bad-request-response-schema.json](json-schema/api-bad-request-response-schema.json)
- [api-contact-schema.json](json-schema/api-contact-schema.json)
- [api-content-schema.json](json-schema/api-content-schema.json)
- [api-email-schema.json](json-schema/api-email-schema.json)
- [api-successful-response-schema.json](json-schema/api-successful-response-schema.json)
- [openapi-v2-account-members-response-schema.json](json-schema/openapi-v2-account-members-response-schema.json)
- [openapi-v2-account-schema.json](json-schema/openapi-v2-account-schema.json)
- [openapi-v2-add-member-request-schema.json](json-schema/openapi-v2-add-member-request-schema.json)
- ... and 53 more

### JSON Structure

- [api-access-denied-response-structure.json](json-structure/api-access-denied-response-structure.json)
- [api-attachment-structure.json](json-structure/api-attachment-structure.json)
- [api-bad-request-response-structure.json](json-structure/api-bad-request-response-structure.json)
- [api-contact-structure.json](json-structure/api-contact-structure.json)
- [api-content-structure.json](json-structure/api-content-structure.json)
- ... and 58 more

### JSON-LD

- [ahasend-api-context.jsonld](json-ld/ahasend-api-context.jsonld)
- [ahasend-openapi-v2-context.jsonld](json-ld/ahasend-openapi-v2-context.jsonld)

### Examples

- [api-access-denied-response-example.json](examples/api-access-denied-response-example.json)
- [api-attachment-example.json](examples/api-attachment-example.json)
- [api-bad-request-response-example.json](examples/api-bad-request-response-example.json)
- [api-contact-example.json](examples/api-contact-example.json)
- [api-content-example.json](examples/api-content-example.json)
- ... and 58 more

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [ahasend-api.yaml](capabilities/shared/ahasend-api.yaml)

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [email-operations.yaml](capabilities/email-operations.yaml) | AhaSend API v2 | 6 | Developer, DevOps Engineer |

## Vocabulary

- [AhaSend Vocabulary](vocabulary/ahasend-vocabulary.yaml) — Unified taxonomy mapping 9 resources, 8 actions, 1 workflow, and 2 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [ahasend-spectral-rules.yml](rules/ahasend-spectral-rules.yml) — 30 rules across 13 categories enforcing AhaSend API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
