---
title: "SQL: Window Functions"
subject: SQL
type: subbucket
tags: [sql, window-functions, ranking, running-total, partition]
topics: [Window Basics, Ranking, Running Aggregates]
difficulty: hard
frequency: medium
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-20
source: lecture notes
related: [aggregation-and-grouping, joins-and-subqueries]
status: stub
---

# SQL: Window Functions

> Aggregate-like calculations that keep individual rows (unlike GROUP BY, which
> collapses them). The key to "rank within group", "running total", and
> "row-vs-group" questions that trip people up in interviews.

**Topics:** Window Basics · Ranking · Running Aggregates

---

## Window Basics

_Not yet written._ (`OVER (PARTITION BY ... ORDER BY ...)`, window vs GROUP BY,
frame clause `ROWS BETWEEN`.)

---

## Ranking

_Not yet written._ (`ROW_NUMBER` vs `RANK` vs `DENSE_RANK`, top-N per group,
`NTILE`, dedup with `ROW_NUMBER`.)

---

## Running Aggregates

_Not yet written._ (Running totals/averages, `LAG`/`LEAD` for row-over-row deltas,
moving windows, cumulative distribution.)

---

## Related Notes

- [SQL: Aggregation & Grouping](aggregation-and-grouping.md) — window functions keep rows where GROUP BY collapses them
- [SQL: Joins & Subqueries](joins-and-subqueries.md) — windows replace many correlated subqueries

## Cheat Sheet

→ [SQL Cheat Sheet](cheatsheet.md#window-functions)
