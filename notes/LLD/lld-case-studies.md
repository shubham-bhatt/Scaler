---
title: LLD Case Studies
subject: LLD
type: subbucket
tags: [case-study, machine-coding, parking-lot, elevator, bookmyshow, splitwise]
topics: [Parking Lot, Elevator System, BookMyShow, Splitwise]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [oop-and-solid, creational-patterns, behavioral-patterns, lld-principles]
status: stub
---

# LLD Case Studies

> Full machine-coding / design problems that combine OOP, SOLID, and patterns into
> one system. Each follows the same drill: clarify requirements → identify entities
> → define classes & relationships → apply patterns → walk through key flows.

**Topics:** Parking Lot · Elevator System · BookMyShow · Splitwise · _(add more: Chess, Snake & Ladder, Rate Limiter, Vending Machine, Cache/LRU, Logging framework)_

---

## Parking Lot

_Not yet written._ (Entities: ParkingLot, Level, Spot (sizes), Vehicle, Ticket,
Gate; spot allocation strategy (Strategy pattern), pricing; concurrency on spot
assignment.)

---

## Elevator System

_Not yet written._ (Entities: Elevator, Controller, Request, Button; scheduling
algorithm (SCAN/LOOK), State pattern for elevator states, multi-elevator dispatch.)

---

## BookMyShow

_Not yet written._ (Movies, Shows, Seats, Booking, Payment; seat-locking to prevent
double booking (concurrency), Observer for notifications.)

---

## Splitwise

_Not yet written._ (Users, Groups, Expenses, split types (equal/exact/percent),
balance sheet simplification; Strategy for split types.)

---

## Related Notes

- [OOP & SOLID](oop-and-solid.md) — the modeling foundation for every case study
- [Creational Patterns](creational-patterns.md) — Factory/Builder for entity creation
- [Behavioral Patterns](behavioral-patterns.md) — Strategy (pricing/split), State (lifecycle), Observer (notifications)
- [LLD Principles](lld-principles.md) — concurrency and coupling decisions show up here

## Cheat Sheet

→ [LLD Cheat Sheet](cheatsheet.md#lld-case-studies)
