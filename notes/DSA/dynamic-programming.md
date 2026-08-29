---
title: Dynamic Programming
subject: DSA
type: subbucket
tags: [dynamic-programming, dp, subarray, kadane, memoization, knapsack]
topics: [DP Basics, 1D DP, 2D DP / Knapsack, DP on Strings]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-09
source: lecture notes
related: [recursion-and-backtracking, arrays-searching-sorting]
status: partial
---

# Dynamic Programming

> Solve a problem by combining answers to overlapping subproblems, each computed
> once and cached. The craft is (1) defining the state, (2) writing the recurrence,
> (3) choosing memoization (top-down) or tabulation (bottom-up), then (4) optimizing
> space. Recognizing "optimal substructure + overlapping subproblems" is the trigger.

**Topics:** DP Basics · [1D DP](#1d-dp) · 2D DP / Knapsack · DP on Strings

---

## DP Basics

_Not yet written._ (State definition, recurrence, memoization vs tabulation,
top-down vs bottom-up, space optimization; Fibonacci / climbing stairs as the
canonical intro.)

---

## 1D DP

State depends on a linear scan; each position's answer builds on previous ones.
The subtle members of this family track **more than one** running value.

### Max Product Subarray

> Find the contiguous subarray with the largest product. Tricky because negatives
> flip signs — a large negative product becomes the largest positive when multiplied
> by another negative. The fix: track **both** the running max and min products.

#### Core Concept

Unlike Max **Sum** Subarray (Kadane's), you can't track only the running maximum.
A negative number times the current minimum (a large negative) can produce the new
maximum. Maintain two values ending at index i:

- `dpMax[i]` — max product of any subarray ending at i
- `dpMin[i]` — min product of any subarray ending at i

Each can come from three choices: start fresh at `a[i]`, extend prev max, or extend prev min.

#### Recurrence

```
dpMax[i] = max(a[i], a[i] * dpMax[i-1], a[i] * dpMin[i-1])
dpMin[i] = min(a[i], a[i] * dpMin[i-1], a[i] * dpMax[i-1])
```

| Scenario                              | Winner                        |
|---------------------------------------|-------------------------------|
| a[i] positive                         | a[i]·dpMax                    |
| a[i] negative                         | a[i]·dpMin (neg·neg = pos)    |
| previous all negative, a[i] positive  | a[i] alone (restart)          |
| zero in array                         | 0 — effectively restarts      |

#### Space-Optimized Implementation

```java
public int maxProduct(int[] A) {
    int maxProd = A[0], minProd = A[0], result = A[0];
    for (int i = 1; i < A.length; i++) {
        int tempMax = Math.max(A[i], Math.max(A[i] * maxProd, A[i] * minProd));
        int tempMin = Math.min(A[i], Math.min(A[i] * maxProd, A[i] * minProd));
        maxProd = tempMax;
        minProd = tempMin;
        result = Math.max(result, maxProd);
    }
    return result;
}
```

**Critical**: compute `tempMax` and `tempMin` **before** updating either variable —
`minProd` needs the *old* `maxProd`. Time O(N), Space O(1).

#### The ArrayList Reference Trap (Java 2D DP)

Initializing `ArrayList<ArrayList<Integer>>` by reusing one inner list shares refs:

```java
// WRONG — every row is the SAME object
ArrayList<Integer> row = new ArrayList<>(Collections.nCopies(cols, -1));
for (int i = 0; i < rows; i++) dp.add(row);   // dp.get(0).set(0,5) also changes row 1!

// RIGHT — a new inner list per row
for (int i = 0; i < rows; i++)
    dp.add(new ArrayList<>(Collections.nCopies(cols, -1)));

// SIMPLEST — plain 2D array
int[][] dp = new int[rows][cols];
for (int[] r : dp) Arrays.fill(r, -1);
```

`ArrayList.add()` stores the *reference*, not a copy.

#### Examples

`[2, 3, -2, 4]` → **6** (subarray [2,3]).
`[-2, 0, -1]` → **0** (the [0]).
`[-2, 3, -4]` → **24** (full array: min subarray -6 times -4 = 24).

#### Common Mistakes / Edge Cases

1. **Updating maxProd before computing minProd** — both need the previous values.
2. **Forgetting `a[i]` alone** — subarray can restart at any element.
3. **All negatives** — even count of negatives → full product; odd → exclude one end.
4. **Zeros** — reset both products to 0; restart after.

#### Interview Angle

**How interviewers test this:**
- Direct: "maximum product subarray" — handle negatives and zeros.
- Follow-up: "return the actual subarray" — track start/end indices.
- Comparison: "how does this differ from Kadane's for max sum?" — sum needs only max; product needs both max and min.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem            | Think of This Pattern           |
|--------------------------------------------|---------------------------------|
| "maximum product of contiguous subarray"   | Two-DP (max and min) approach   |
| "subarray product", "negative numbers"     | Track both max and min products |
| "contiguous subarray optimization"         | Kadane's variant (DP)           |
| "product can spike from negatives"         | Need min tracking for neg·neg   |

---

## 2D DP / Knapsack

_Not yet written._ (Grid paths, 0/1 knapsack, subset sum, coin change, LCS-style
tables; state = (index, capacity/remaining).)

---

## DP on Strings

_Not yet written._ (Longest common subsequence, edit distance, palindromic
substrings, regex/wildcard matching.)

---

## Related Notes

- [Recursion & Backtracking](recursion-and-backtracking.md) — DP is memoized recursion; every DP starts as a brute-force recursion
- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — 1D DP scans arrays; contrast Kadane's with prefix sums

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#dynamic-programming)
