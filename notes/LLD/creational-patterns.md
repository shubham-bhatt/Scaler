---
title: Creational Design Patterns
subject: LLD
type: subbucket
tags: [design-patterns, creational, singleton, factory, builder, prototype]
topics: [Singleton, Factory & Abstract Factory, Builder, Prototype]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [oop-and-solid, structural-patterns, behavioral-patterns]
status: stub
---

# Creational Design Patterns

> Patterns that control *how objects are created*, decoupling client code from the
> concrete classes it instantiates. The most-asked LLD patterns start here.

**Topics:** Singleton · Factory & Abstract Factory · Builder · Prototype

---

## Singleton

_Not yet written._ (One instance, global access; thread-safe variants: eager,
lazy + double-checked locking, static holder idiom, enum singleton; pitfalls with
serialization/reflection.)

---

## Factory & Abstract Factory

_Not yet written._ (Factory Method — defer instantiation to subclasses; Abstract
Factory — families of related objects; vs simple factory.)

---

## Builder

_Not yet written._ (Step-by-step construction of complex objects, fluent API,
immutable objects with many optional fields; vs telescoping constructors.)

---

## Prototype

_Not yet written._ (Clone existing objects instead of building anew; deep vs
shallow copy; registry of prototypes.)

---

## Related Notes

- [OOP & SOLID](oop-and-solid.md) — patterns are SOLID applied to object creation
- [Structural Patterns](structural-patterns.md) — how objects are composed once created
- [Behavioral Patterns](behavioral-patterns.md) — how objects collaborate at runtime

## Cheat Sheet

→ [LLD Cheat Sheet](cheatsheet.md#creational-patterns)
