---
title: LLD Principles & Practices
subject: LLD
type: subbucket
tags: [dry, kiss, yagni, composition, coupling, cohesion, concurrency]
topics: [DRY / KISS / YAGNI, Composition vs Inheritance, Coupling & Cohesion, Concurrency]
difficulty: medium
frequency: medium
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [oop-and-solid, lld-case-studies]
status: stub
---

# LLD Principles & Practices

> The guidelines that keep a design clean beyond the named patterns — heuristics
> for how much to build, how to reuse, how to split responsibility, and how to stay
> correct under threads. Interviewers probe these when they ask "why did you design
> it this way?"

**Topics:** DRY / KISS / YAGNI · Composition vs Inheritance · Coupling & Cohesion · Concurrency

---

## DRY / KISS / YAGNI

_Not yet written._ (Don't Repeat Yourself, Keep It Simple, You Aren't Gonna Need
It; over-engineering vs premature abstraction; when duplication is cheaper than the
wrong abstraction.)

---

## Composition vs Inheritance

_Not yet written._ ("Favor composition over inheritance"; fragile base class,
diamond problem; has-a vs is-a; delegation.)

---

## Coupling & Cohesion

_Not yet written._ (Loose coupling / high cohesion, law of Demeter, dependency
injection, interfaces as seams for testing.)

---

## Concurrency

_Not yet written._ (Thread safety, race conditions, locks/synchronized, atomic
operations, immutability, producer-consumer; concurrency in Singleton and shared
resources.)

---

## Related Notes

- [OOP & SOLID](oop-and-solid.md) — these principles extend and reinforce SOLID
- [LLD Case Studies](lld-case-studies.md) — where the principles get applied end-to-end

## Cheat Sheet

→ [LLD Cheat Sheet](cheatsheet.md#lld-principles)
