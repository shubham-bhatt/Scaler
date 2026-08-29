---
title: Behavioral Design Patterns
subject: LLD
type: subbucket
tags: [design-patterns, behavioral, observer, strategy, state, command, iterator]
topics: [Observer, Strategy, State, Command, Iterator]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [structural-patterns, creational-patterns, messaging-and-communication]
status: stub
---

# Behavioral Design Patterns

> Patterns for *how objects communicate and distribute responsibility* at runtime.
> These show up constantly in LLD case studies (notification systems, game state,
> pricing strategies).

**Topics:** Observer · Strategy · State · Command · Iterator

---

## Observer

_Not yet written._ (One-to-many: subjects notify subscribers on change; push vs
pull; the LLD sibling of HLD pub/sub. Notification systems, event listeners.)

---

## Strategy

_Not yet written._ (Encapsulate interchangeable algorithms behind an interface;
swap at runtime; payment/pricing/sorting strategies; favors composition over
conditionals.)

---

## State

_Not yet written._ (Object changes behavior as its internal state changes; state
objects instead of giant switch statements; vending machine, order lifecycle.)

---

## Command

_Not yet written._ (Encapsulate a request as an object; undo/redo, queuing,
logging; remote control / editor actions.)

---

## Iterator

_Not yet written._ (Sequential access to a collection without exposing its
internals; Java `Iterator`/`Iterable`.)

---

## Related Notes

- [Structural Patterns](structural-patterns.md) — composition mechanics these build on
- [Creational Patterns](creational-patterns.md) — often combined (e.g. Strategy objects built by a Factory)
- [Messaging & Communication](../HLD/messaging-and-communication.md) — Observer is the in-process form of pub/sub; the same idea scaled out becomes a message broker

## Cheat Sheet

→ [LLD Cheat Sheet](cheatsheet.md#behavioral-patterns)
