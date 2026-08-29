---
title: "SQL: Aggregation & Grouping"
subject: SQL
type: subbucket
tags: [sql, aggregation, group-by, having, self-join]
topics: [Aggregate Functions, GROUP BY, WHERE vs HAVING, Self-Join with GROUP BY]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-09
source: lecture notes
related: [joins-and-subqueries, query-basics]
status: partial
---

# SQL: Aggregation & Grouping

> Aggregate functions collapse rows into summary values. GROUP BY splits results
> into groups before aggregation; HAVING filters groups after aggregation. Together
> they are the backbone of SQL analytics — heavily tested, especially combined with
> JOINs and self-joins.

**Topics:** [Aggregate Functions](#aggregate-functions) · [GROUP BY](#group-by) · [WHERE vs HAVING](#where-vs-having) · [Self-Join with GROUP BY](#self-join-with-group-by)

## Clause Execution Order

SQL processes clauses in this logical order:

```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

- **WHERE** — filters individual rows *before* grouping.
- **GROUP BY** — groups rows sharing the same values in specified columns.
- **HAVING** — filters groups *after* aggregation (can use aggregate results).
- **SELECT** — computes the final output columns.

---

## Aggregate Functions

| Function             | Description                          |
|----------------------|--------------------------------------|
| `COUNT(*)`           | Count all rows (including NULLs)     |
| `COUNT(column_name)` | Count rows where column is NOT NULL  |
| `SUM(column)`        | Sum of non-NULL values               |
| `AVG(column)`        | Average of non-NULL values           |
| `MAX(column)`        | Maximum value                        |
| `MIN(column)`        | Minimum value                        |

**Key difference**: `COUNT(*)` counts every row; `COUNT(column)` skips NULLs —
this matters when a column has missing data.

---

## GROUP BY

Groups rows by one or more columns. Every column in SELECT must either be in
GROUP BY or inside an aggregate — otherwise it's ambiguous which value to show.

```sql
-- ERROR: which title to show for each release_year?
SELECT * FROM film GROUP BY release_year;

-- CORRECT: only grouped column + aggregates
SELECT release_year, COUNT(*) AS film_count
FROM film
GROUP BY release_year;
```

### Multiple Columns — use commas, not AND

```sql
-- CORRECT
SELECT actor_id, director_id, COUNT(*) AS collab_count
FROM actorDirector
GROUP BY actor_id, director_id
HAVING COUNT(actor_id) > 2
ORDER BY timestamp ASC;

-- WRONG: 'AND' is a boolean operator, not a column separator
GROUP BY actor_id AND director_id   -- evaluates a AND b as one boolean!
```

**Real gotcha**: `GROUP BY a AND b` doesn't error in all databases, but it groups
by the single boolean expression `a AND b`, producing wrong results silently.

### GROUP BY Without JOIN

A common misconception is that GROUP BY requires a JOIN. It doesn't — you can group
any single table's rows:

```sql
SELECT batch_id, COUNT(student_id) AS cnt
FROM students
GROUP BY batch_id
HAVING cnt >= 100;
```

---

## WHERE vs HAVING

| Aspect  | WHERE                    | HAVING                     |
|---------|--------------------------|----------------------------|
| Filters | Individual rows          | Groups (after aggregation) |
| Timing  | Before GROUP BY          | After GROUP BY             |
| Can use | Column values, operators | Aggregate functions        |
| Example | `WHERE salary > 50000`   | `HAVING COUNT(*) > 10`     |

```sql
-- Rental durations with more than 200 PG-rated movies
SELECT rental_duration, COUNT(*) AS cnt
FROM film
WHERE rating = 'PG'         -- first: keep only PG-rated films (row filter)
GROUP BY rental_duration    -- then: group by duration
HAVING cnt > 200;           -- finally: keep groups with > 200 films (group filter)
```

**Alias note**: MySQL lets you use a SELECT alias (`cnt`) in HAVING; standard SQL /
PostgreSQL require repeating the expression: `HAVING COUNT(*) > 200`.

---

## Self-Join with GROUP BY

**Problem**: find all actor pairs who appeared together in more than 1 film (Sakila DB).

```sql
SELECT
    a1.first_name, a1.last_name,
    a2.first_name, a2.last_name,
    COUNT(*) AS films_together
FROM film_actor f1
JOIN actor a1 ON a1.actor_id = f1.actor_id
JOIN film_actor f2
    ON f1.film_id = f2.film_id
    AND f1.actor_id < f2.actor_id   -- avoid duplicate pairs & self-pairs
JOIN actor a2 ON a2.actor_id = f2.actor_id
GROUP BY a1.first_name, a1.last_name, a2.first_name, a2.last_name
HAVING films_together > 1;
```

**Why `f1.actor_id < f2.actor_id`?** Without it you'd get both (Alice, Bob) and
(Bob, Alice) — plus self-pairs (Alice, Alice). The `<` yields each unordered pair
exactly once and excludes self-pairs. (Same "canonical ordering" trick used to
dedup pairs in two-pointer and Union-Find problems.)

---

## Examples

```sql
-- Films per year, most first
SELECT release_year, COUNT(*) AS total
FROM film GROUP BY release_year ORDER BY total DESC;

-- Average rental rate by rating, only ratings with > 50 films
SELECT rating, AVG(rental_rate) AS avg_rate, COUNT(*) AS num_films
FROM film GROUP BY rating HAVING num_films > 50 ORDER BY avg_rate DESC;
```

---

## Common Mistakes / Edge Cases

1. **`AND` instead of `,` in GROUP BY** — silently groups by a boolean expression.
2. **Non-aggregated column in SELECT without GROUP BY** — MySQL `ONLY_FULL_GROUP_BY`
   catches it; older MySQL picks an arbitrary value.
3. **Aggregate in WHERE** — `WHERE COUNT(*) > 5` is a syntax error; use HAVING.
4. **`COUNT(*)` vs `COUNT(column)`** — differ when NULLs exist.
5. **Self-join duplicate pairs** — use `a.id < b.id`.

---

## Interview Angle

**How interviewers test this:**
- "Departments with more than N employees" — GROUP BY + HAVING.
- "Top K categories by revenue" — GROUP BY + ORDER BY + LIMIT.
- "Find duplicate records" — GROUP BY + HAVING COUNT(*) > 1.
- "Pairs of entities with shared relationships" — self-join + GROUP BY.
- Conceptual: "WHERE vs HAVING?", "COUNT(*) vs COUNT(column) with NULLs?".

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem            | Think of This Pattern       |
|--------------------------------------------|-----------------------------|
| "how many per group", "count by category"  | GROUP BY + COUNT            |
| "groups with more/fewer than N"            | GROUP BY + HAVING           |
| "average/sum per category"                 | GROUP BY + AVG/SUM          |
| "find duplicates"                          | GROUP BY + HAVING COUNT > 1 |
| "pairs that share a relationship"          | Self-join with `id1 < id2`  |
| "filter rows then aggregate"               | WHERE → GROUP BY → HAVING   |

---

## Related Notes

- [SQL: Joins & Subqueries](joins-and-subqueries.md) — self-join here is a preview; join types & subqueries live there
- [SQL: Query Basics](query-basics.md) — SELECT/WHERE/ORDER BY that these build on

## Cheat Sheet

→ [SQL Cheat Sheet](cheatsheet.md#aggregation--grouping)
