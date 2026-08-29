---
title: Messaging & Communication
subject: HLD
type: subbucket
tags: [message-queue, pub-sub, kafka, websockets, sse, api-design]
topics: [Message Queues, Pub/Sub, Kafka, WebSockets & SSE, API Design]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [reliability-and-consistency, scaling-fundamentals, behavioral-patterns]
status: stub
---

# Messaging & Communication

> How services talk — synchronously via APIs and asynchronously via queues and
> event streams. Async messaging decouples producers from consumers, absorbs spikes,
> and is the backbone of event-driven architectures.

**Topics:** Message Queues · Pub/Sub · Kafka · WebSockets & SSE · API Design

---

## Message Queues

_Not yet written._ (Producer-consumer decoupling, point-to-point, at-least-once vs
exactly-once, dead-letter queues, backpressure; RabbitMQ/SQS.)

---

## Pub/Sub

_Not yet written._ (One-to-many fan-out, topics & subscriptions; the distributed
form of the Observer pattern; push vs pull delivery.)

---

## Kafka

_Not yet written._ (Distributed log, partitions & offsets, consumer groups,
ordering guarantees, retention, replay; log compaction.)

---

## WebSockets & SSE

_Not yet written._ (Real-time bidirectional (WebSocket) vs server-push (SSE) vs
long-polling; when to use each; connection scaling.)

---

## API Design

_Not yet written._ (REST vs gRPC vs GraphQL, versioning, pagination, idempotency
keys, rate limiting, request/response contracts.)

---

## Related Notes

- [Reliability & Consistency](reliability-and-consistency.md) — delivery guarantees & idempotency live here
- [Scaling Fundamentals](scaling-fundamentals.md) — queues absorb load spikes and smooth traffic
- [Behavioral Patterns](../LLD/behavioral-patterns.md) — Pub/Sub is the Observer pattern scaled across services

## Cheat Sheet

→ [HLD Cheat Sheet](cheatsheet.md#messaging--communication)
