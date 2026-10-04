---
title: Dynamic Programming
subject: DSA
type: subbucket
tags: [dynamic-programming, dp, subarray, kadane, memoization, knapsack]
topics: [DP Basics, 1D DP, 2D DP / Knapsack, DP on Strings]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-09-07
reviewed: 2026-08-09
source: lecture notes
related: [recursion-and-backtracking, arrays-searching-sorting, prefix-sum-and-subarrays]
status: partial
---

# Dynamic Programming

> Solve a problem by combining answers to overlapping subproblems, each computed
> once and cached. The craft is (1) defining the state, (2) writing the recurrence,
> (3) choosing memoization (top-down) or tabulation (bottom-up), then (4) optimizing
> space. Recognizing "optimal substructure + overlapping subproblems" is the trigger.

**Topics:** [DP Basics](#dp-basics) · [1D DP](#1d-dp) · 2D DP / Knapsack · DP on Strings

---

## DP Basics

> Climbing Stairs is the canonical first DP problem: the *generate every path*
> version is plain backtracking (see
> [Recursion & Backtracking](recursion-and-backtracking.md#details--walkthrough--steps-climb-1-or-2-at-a-time)),
> but the *count the ways* version has repeated subproblems — the trigger to
> stop recursing and start memoizing/tabulating.

### Core Concept

Four-step recipe for turning a brute-force recursion into DP:

1. **Define the state** — what does one recursive call represent? For
   Climbing Stairs: `ways(n)` = number of distinct ways to reach step `n`.
2. **Write the recurrence** — how does this state relate to smaller states?
   `ways(n) = ways(n-1) + ways(n-2)` (arrive at `n` via a 1-step from `n-1`,
   or a 2-step from `n-2`).
3. **Spot the overlap** — if the same state gets recomputed from multiple call
   paths, cache it: **memoization** (top-down: plain recursion + a cache array/map)
   or **tabulation** (bottom-up: fill an array iteratively from the base case up).
4. **Optimize space** — if `ways(n)` only ever needs `ways(n-1)` and
   `ways(n-2)`, keep two variables instead of a whole array.

### Details / Walkthrough

Plain recursion for "how many ways to climb `n` stairs, 1 or 2 steps at a time":

```java
int ways(int n) {
    if (n == 0) return 1;      // one way to stand at the base: take no more steps
    if (n < 0) return 0;       // overshot — invalid path
    return ways(n - 1) + ways(n - 2);
}
```

This looks cheap, but the recursion tree recomputes the same states repeatedly:

```
ways(5)
 ├── ways(4)
 │    ├── ways(3)
 │    │    ├── ways(2)
 │    │    └── ways(1)
 │    └── ways(2)          ← repeated (also reached via ways(3) above)
 └── ways(3)               ← repeated!
      ├── ways(2)          ← repeated again
      └── ways(1)          ← repeated
```

`ways(3)` is computed twice, `ways(2)` three times — **exponential** blow-up
(`O(2^N)`) from re-solving identical subproblems. That repetition — not the
recursion itself — is what "overlapping subproblems" means and is the signal
to memoize.

**Top-down (memoization)** — same recursion, cache added:

```java
int ways(int n, int[] memo) {
    if (n == 0) return 1;
    if (n < 0) return 0;
    if (memo[n] != -1) return memo[n];          // already solved — reuse it
    return memo[n] = ways(n - 1, memo) + ways(n - 2, memo);
}
// call: int[] memo = new int[N + 1]; Arrays.fill(memo, -1); ways(N, memo);
```

**Bottom-up (tabulation)** — build the answer from the base case upward,
no recursion/call-stack at all:

```java
int waysTabulated(int n) {
    if (n == 0 || n == 1) return 1;
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= n; i++) dp[i] = dp[i - 1] + dp[i - 2];
    return dp[n];
}
```

**Space-optimized** — `dp[i]` only ever needs the previous two values:

```java
int waysOptimized(int n) {
    if (n == 0 || n == 1) return 1;
    int prev2 = 1, prev1 = 1;
    for (int i = 2; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

### Examples

`ways(5)`: `1,1,2,3,5,8` → `ways(5) = 8`. Both memoized and tabulated versions
run in **O(N) time, O(N) → O(1) space** once optimized — versus `O(2^N)` for
the naive recursion above.

### Common Mistakes / Edge Cases

1. **Memoizing without a base-case guard** — `memo[n]` on a negative `n`
   indexes out of bounds; check `n < 0` (and `n == 0`) before touching the array.
2. **Confusing "generate all ways" with "count the ways"** — generating every
   sequence is backtracking (`O(2^N)` unavoidable, since there are that many
   outputs); *counting* them is where DP applies, collapsing to `O(N)`.
3. **Forgetting to initialize the memo array to a sentinel** (`-1`, not `0`) —
   `0` is a valid answer for some states, so it can't double as "not computed yet".
4. **Off-by-one in the tabulation base cases** — `dp[0]` and `dp[1]` must both
   be seeded before the loop; starting the loop at `i=0` or `i=1` reads
   uninitialized/wrong array slots.

### Interview Angle

**How interviewers test this:**
- Direct: "Climbing Stairs" (LC 70) — count the ways, 1 or 2 steps at a time.
- Follow-up: "Now print every sequence, not just the count" → falls back to
  the backtracking version — see [Recursion & Backtracking](recursion-and-backtracking.md#details--walkthrough--steps-climb-1-or-2-at-a-time).
- Follow-up: "Can you do it in O(1) space?" → the two-variable rolling version above.
- Follow-up: "What if steps can be 1, 2, or 3?" → same recurrence shape,
  `ways(n) = ways(n-1) + ways(n-2) + ways(n-3)`.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "count the number of ways" | DP: define state, write recurrence, memoize |
| "generate/list/print all ways" | Backtracking, not DP — the output itself is exponential |
| "same subproblem solved multiple times" in the recursion tree | Memoization (top-down) or tabulation (bottom-up) |
| "1 or 2 steps", "distinct ways to climb/reach" | `ways(n) = ways(n-1) + ways(n-2)` (Fibonacci shape) |
| "optimize space after getting DP working" | Roll the array down to O(1) variables if only the last `k` states are needed |

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
- [Intro to PS: Prefix Sum, Subarrays & Sliding Window](../Introduction_To_PS/prefix-sum-and-subarrays.md) — carry-forward over subarrays is the seed of 1-D DP (Kadane-style)

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#dynamic-programming)
