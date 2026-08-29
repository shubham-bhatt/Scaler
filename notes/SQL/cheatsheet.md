---
title: SQL — Cheat Sheet
subject: SQL
type: cheatsheet
reviewed: 2026-08-20
covers: [query-basics, aggregation-and-grouping, joins-and-subqueries, window-functions]
---

# SQL — Cheat Sheet

> One file to flip through before a SQL round. `##` = subbucket, `###` = topic.
> Full explanations live in the sibling note files.

## Query Basics
Note: [query-basics](query-basics.md)

_(to be added — SELECT/aliases, WHERE operators & NULL logic, ORDER BY/LIMIT, DISTINCT)_

## Aggregation & Grouping
Note: [aggregation-and-grouping](aggregation-and-grouping.md)

- **Execution order**: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
- `COUNT(*)` = all rows | `COUNT(col)` = non-NULL only.
- **WHERE** filters rows *before* grouping | **HAVING** filters groups *after*.
- GROUP BY multiple columns → **commas**, not `AND` (silently groups by a boolean).
- Every SELECT column must be in GROUP BY or inside an aggregate.
- Self-join pairs: `a.id < b.id` → each unordered pair once, no self-pairs.

```sql
SELECT dept, COUNT(*) AS cnt, AVG(salary) AS avg_sal
FROM employees
WHERE status = 'active'     -- row filter (before grouping)
GROUP BY dept
HAVING cnt > 5              -- group filter (after grouping)
ORDER BY avg_sal DESC;
```

- **Q**: WHERE vs HAVING? **A**: WHERE = row-level before grouping; HAVING = group-level after aggregation.
- **Gotcha**: aggregates can't appear in WHERE; MySQL allows SELECT aliases in HAVING, PostgreSQL doesn't.

## Joins & Subqueries
Note: [joins-and-subqueries](joins-and-subqueries.md)

_(to be added — INNER/LEFT/RIGHT/FULL/CROSS, anti-join, correlated vs non-correlated, EXISTS vs IN, CTEs)_

## Window Functions
Note: [window-functions](window-functions.md)

_(to be added — OVER(PARTITION BY…ORDER BY…), ROW_NUMBER vs RANK vs DENSE_RANK, running totals, LAG/LEAD)_
