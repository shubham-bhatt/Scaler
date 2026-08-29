---
title: "SQL: Joins & Subqueries"
subject: SQL
type: subbucket
tags: [sql, joins, self-join, subquery, cte]
topics: [Join Types, Self-Join, Subqueries, CTEs]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [aggregation-and-grouping, query-basics, window-functions]
status: stub
---

# SQL: Joins & Subqueries

> How to combine rows across tables and nest queries. Joins are the most-tested SQL
> skill; subqueries and CTEs let you build results in readable stages.

**Topics:** Join Types · Self-Join · Subqueries · CTEs

---

## Join Types

_Not yet written._ (INNER / LEFT / RIGHT / FULL OUTER / CROSS; join vs filter in
ON vs WHERE; NULLs from outer joins; anti-join with `LEFT JOIN ... IS NULL`.)

---

## Self-Join

_Not yet written._ (A table joined to itself; employee-manager, pairs; the
`a.id < b.id` canonical-ordering trick — see
[Aggregation & Grouping](aggregation-and-grouping.md#self-join-with-group-by).)

---

## Subqueries

_Not yet written._ (Scalar, correlated vs non-correlated, `IN` / `EXISTS` /
`ANY` / `ALL`, subquery in SELECT/FROM/WHERE.)

---

## CTEs

_Not yet written._ (`WITH` common table expressions for readability, recursive CTEs
for hierarchies/graphs.)

---

## Related Notes

- [SQL: Aggregation & Grouping](aggregation-and-grouping.md) — joins + GROUP BY for relationship counts
- [SQL: Query Basics](query-basics.md) — filtering that joins build on
- [SQL: Window Functions](window-functions.md) — an alternative to some correlated subqueries

## Cheat Sheet

→ [SQL Cheat Sheet](cheatsheet.md#joins--subqueries)
