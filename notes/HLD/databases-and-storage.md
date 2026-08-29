---
title: Databases & Storage
subject: HLD
type: subbucket
tags: [database, sql, nosql, indexing, sharding, replication, cap]
topics: [SQL vs NoSQL, Indexing, Sharding, Replication, CAP Theorem]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [scaling-fundamentals, reliability-and-consistency, aggregation-and-grouping]
status: stub
---

# Databases & Storage

> Choosing and scaling the data layer — the decision that most shapes a system
> design. Pick the store for the access pattern, index for the queries, then shard
> and replicate to scale, accepting the CAP trade-off that implies.

**Topics:** SQL vs NoSQL · Indexing · Sharding · Replication · CAP Theorem

---

## SQL vs NoSQL

_Not yet written._ (Relational/ACID vs document/KV/wide-column/graph; when strong
schema & joins matter vs flexible schema & horizontal scale; polyglot persistence.)

---

## Indexing

_Not yet written._ (B-tree vs hash vs LSM-tree, primary/secondary/composite
indexes, covering indexes, write amplification, when an index hurts.)

---

## Sharding

_Not yet written._ (Horizontal partitioning; shard keys; range vs hash vs
directory; consistent hashing; hotspots; cross-shard joins & rebalancing.)

---

## Replication

_Not yet written._ (Leader-follower vs multi-leader vs leaderless; sync vs async;
read replicas for read scaling; replication lag; failover.)

---

## CAP Theorem

_Not yet written._ (Consistency, Availability, Partition-tolerance — pick 2 under a
partition; CP vs AP systems; PACELC extension.)

---

## Related Notes

- [Scaling Fundamentals](scaling-fundamentals.md) — caching and read replicas both scale reads
- [Reliability & Consistency](reliability-and-consistency.md) — replication forces a consistency-model choice
- [SQL: Aggregation & Grouping](../SQL/aggregation-and-grouping.md) — the query-side view of relational databases

## Cheat Sheet

→ [HLD Cheat Sheet](cheatsheet.md#databases--storage)
