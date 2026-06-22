---
title: "Why your webhook fired twice: handling duplicate events"
url: "https://ahasend.com/blog/why-your-webhook-fired-twice-handling-duplicate-events/"
date: "2026-06-18"
author: "Mark Kraakman"
feed_url: "https://ahasend.com/blog"
---
There is no exactly-once delivery guarantee for webhooks; duplicate deliveries are an inherent property of sending messages over an unreliable network. The post explains how to handle them with idempotency: check the webhook-id header before processing, skip previously-seen events, and durably record IDs only after successful processing.
