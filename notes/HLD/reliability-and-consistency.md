---
title: Reliability & Consistency
subject: HLD
type: subbucket
tags: [consistency, consensus, rate-limiting, idempotency, fault-tolerance]
topics: [Consistency Models, Consensus, Rate Limiting, Idempotency]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [databases-and-storage, messaging-and-communication, scaling-fundamentals]
status: stub
---

# Reliability & Consistency

> Keeping a distributed system correct and available despite failures. These topics
> decide what guarantees you can promise users when machines crash, networks
> partition, and requests retry.

**Topics:** Consistency Models · Consensus · Rate Limiting · Idempotency

---

## Consistency Models

_Not yet written._ (Strong vs eventual vs causal vs read-your-writes; quorum
reads/writes (W + R > N); tunable consistency; when eventual is acceptable.)

---

## Consensus

_Not yet written._ (Agreeing on a value across nodes; leader election; Paxos/Raft
at a high level; split-brain; use in config stores like ZooKeeper/etcd.)

---

## Rate Limiting

_Not yet written._ (Token bucket, leaky bucket, fixed vs sliding window; distributed
rate limiting with Redis; per-user/per-IP; graceful 429s. See the HLD case study.)

---

## Idempotency

_Not yet written._ (Safe retries via idempotency keys, dedup, exactly-once effects
over at-least-once delivery; idempotent HTTP methods.)

---

## Related Notes

- [Databases & Storage](databases-and-storage.md) — replication & CAP determine the achievable consistency model
- [Messaging & Communication](messaging-and-communication.md) — delivery semantics need idempotent consumers
- [Scaling Fundamentals](scaling-fundamentals.md) — reliability adds redundancy on top of scaling

## Cheat Sheet

→ [HLD Cheat Sheet](cheatsheet.md#reliability--consistency)
