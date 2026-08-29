---
title: Scaling Fundamentals
subject: HLD
type: subbucket
tags: [scalability, load-balancing, caching, cdn, horizontal-scaling]
topics: [Scalability, Load Balancing, Caching, CDN]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [databases-and-storage, reliability-and-consistency, hld-case-studies]
status: stub
---

# Scaling Fundamentals

> How a system serves more traffic than one machine can handle: spread load across
> many servers, put frequently used data closer to the request, and serve static
> assets from the edge. These four levers appear in almost every HLD interview.

**Topics:** Scalability · Load Balancing · Caching · CDN

---

## Scalability

_Not yet written._ (Vertical vs horizontal scaling, stateless services, scaling
reads vs writes, back-of-envelope capacity estimation (QPS, storage, bandwidth).)

---

## Load Balancing

_Not yet written._ (L4 vs L7, algorithms: round-robin / least-connections /
consistent hashing; health checks; sticky sessions; LB as a single point of failure.)

---

## Caching

_Not yet written._ (Cache-aside / read-through / write-through / write-back;
eviction (LRU/LFU); TTL; cache invalidation; Redis/Memcached; thundering herd &
cache stampede.)

---

## CDN

_Not yet written._ (Edge caching of static/dynamic content, pull vs push, cache
headers, geo-distribution, origin offload.)

---

## Related Notes

- [Databases & Storage](databases-and-storage.md) — caching sits in front of the datastore; replication scales reads
- [Reliability & Consistency](reliability-and-consistency.md) — caches introduce staleness/consistency trade-offs
- [HLD Case Studies](hld-case-studies.md) — every design applies these levers

## Cheat Sheet

→ [HLD Cheat Sheet](cheatsheet.md#scaling-fundamentals)
