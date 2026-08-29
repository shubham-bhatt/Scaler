---
title: HLD Case Studies
subject: HLD
type: subbucket
tags: [case-study, system-design, url-shortener, rate-limiter, news-feed, chat, notifications]
topics: [URL Shortener, Rate Limiter, News Feed, Chat System, Notification System]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [scaling-fundamentals, databases-and-storage, messaging-and-communication, reliability-and-consistency]
status: stub
---

# HLD Case Studies

> End-to-end system designs that compose the fundamentals. Each follows the drill:
> clarify functional & non-functional requirements → capacity estimate → API →
> high-level architecture → data model → deep-dive the bottleneck → discuss trade-offs.

**Topics:** URL Shortener · Rate Limiter · News Feed · Chat System · Notification System · _(add more: Typeahead, YouTube/Netflix, Uber, Web Crawler, Google Drive)_

---

## URL Shortener

_Not yet written._ (Key generation: counter+base62 vs hash; collision handling;
read-heavy → caching + CDN; redirect (301 vs 302); analytics.)

---

## Rate Limiter

_Not yet written._ (Token/leaky bucket, sliding window log/counter; distributed
with Redis + Lua; where it sits (gateway); returning 429. See
[Reliability & Consistency](reliability-and-consistency.md).)

---

## News Feed

_Not yet written._ (Fan-out on write vs on read; the celebrity problem; feed
ranking; pull vs push vs hybrid; caching the timeline.)

---

## Chat System

_Not yet written._ (WebSocket connections, presence, message delivery & ordering,
read receipts, storage (wide-column), online/offline via a queue.)

---

## Notification System

_Not yet written._ (Multi-channel (push/SMS/email), fan-out via pub/sub, template
service, rate limiting & user preferences, at-least-once + dedup.)

---

## Related Notes

- [Scaling Fundamentals](scaling-fundamentals.md) — load balancing, caching, CDN in every design
- [Databases & Storage](databases-and-storage.md) — data model, sharding & replication choices
- [Messaging & Communication](messaging-and-communication.md) — queues/pub-sub/WebSockets power feed, chat, notifications
- [Reliability & Consistency](reliability-and-consistency.md) — rate limiting & idempotency deep-dives

## Cheat Sheet

→ [HLD Cheat Sheet](cheatsheet.md#hld-case-studies)
