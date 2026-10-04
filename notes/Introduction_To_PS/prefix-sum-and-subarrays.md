---
title: Prefix Sum, Subarrays & Sliding Window
subject: Introduction_To_PS
type: subbucket
tags: [prefix-sum, carry-forward, suffix-sum, subarray, contribution-technique, sliding-window, range-query]
topics: [Prefix Sum, Even/Odd Prefix & Special Index, Carry Forward, Subarrays, Contribution Technique, Fixed-Size Sliding Window]
difficulty: medium
frequency: high
created: 2026-10-04
updated: 2026-10-04
reviewed: 2026-10-04
source: Prefix Sum, Carry Forward & Subarrays, Sliding Window & Contribution Technique, Arrays/Subarrays — see sources/
related: [problem-solving-and-complexity, arrays-basics]
status: complete
---

# Prefix Sum, Subarrays & Sliding Window

> The first real optimisation toolkit: **precompute** (prefix sum) so each query is O(1);
> **calculate + use together** (carry forward) to drop extra space; reason about
> **subarrays** by counting; flip the question with the **contribution technique**;
> and slide a fixed window. Each trick turns an O(N²)/O(N³) brute force into O(N).

**Topics:** [Prefix Sum](#prefix-sum) · [Even/Odd Prefix & Special Index](#evenodd-prefix--special-index) · [Carry Forward](#carry-forward) · [Subarrays](#subarrays) · [Contribution Technique](#contribution-technique) · [Fixed-Size Sliding Window](#fixed-size-sliding-window)

---

## Prefix Sum

> `P[i] = A[0] + … + A[i]` → any range sum is `P[R] − P[L−1]` in O(1). Total O(N + Q).

### Core Concept
Problem: array of N elements, **Q queries** `(L, R)`; return `sum(A[L..R])` per query. Same task on multiple inputs → precompute once.

Brute force: for each query loop `L..R` → **O(Q·N)**.

Cricket intuition (scoreboard): runs after over *i* is the prefix. Runs in 7th over = `score[7] − score[6]`; runs from overs 6–10 = `score[10] − score[5]` = 97 − 31 = 66. Cumulative total makes any range a **subtraction**.

### Details / Walkthrough
Definition and recurrence:
```
P[0] = A[0]
P[i] = P[i-1] + A[i]          // equivalently A[0]+…+A[i]
```
Range sum:
```
sum(L, R) = P[R]               if L == 0
          = P[R] − P[L−1]      if L > 0
```
Why: `P[R]` includes `A[0..R]`; subtracting `P[L−1]` removes `A[0..L−1]`, leaving `A[L..R]`.

Example `A = [-3, 6, 2, 4, 5]`, `P = [-3, 3, 5, 9, 14]`:
- `(L=1,R=3)` → `P[3] − P[0] = 9 − (−3) = 12` ✔ (6+2+4)
- `(L=0,R=3)` → `P[3] = 9`; `(L=2,R=2)` → `P[2] − P[1] = 5 − 3 = 2`

Complexity: build **O(N)**, each query **O(1)** → **TC O(N + Q)**, SC O(N) for `P`.

**In-place prefix sum (SC O(1))** — overwrite `A` itself when the original isn't needed afterwards.

### Examples
```java
static long[] buildPrefix(int[] A) {
    long[] P = new long[A.length];
    P[0] = A[0];
    for (int i = 1; i < A.length; i++) P[i] = P[i - 1] + A[i];
    return P;
}

static long rangeSum(long[] P, int L, int R) {
    return (L == 0) ? P[R] : P[R] - P[L - 1];
}

// In place — SC O(1): A becomes its own prefix sum
static void toPrefixInPlace(int[] A) {            // use long[] if sums can exceed int
    for (int i = 1; i < A.length; i++) A[i] += A[i - 1];
}
```
Queries as `L[]`, `R[]` arrays (or `query[Q][2]`): query *i* is `(L[i], R[i])`.

### Common Mistakes / Edge Cases
- Forgetting the **`L == 0`** branch → `P[-1]` → `ArrayIndexOutOfBounds`. (Trick: use a `P` of size N+1 with `P[0]=0` and `sum = P[R+1] − P[L]` — no branch.)
- Using `int` for `P` when values × N overflow → use `long`.
- Off-by-one: subtract `P[L−1]`, **not** `P[L]` (that would drop `A[L]`).
- Modifying `A` in place and then still needing the original values.

### Interview Angle
**How interviewers test this:** range-sum queries; "equilibrium index"; subarray-sum = K (prefix + hash map → see [Frequency Patterns](../DSA/hashing-and-strings.md)); 2D prefix sums on matrices.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Q queries, sum from L to R" | Prefix sum, O(1) per query |
| "Static array, many range queries" | Precompute once; O(N + Q) |
| "Subarray with sum K" | Prefix sum + HashMap of seen prefixes |
| Count/sum over a range of a *property* (even index, vowels, 1s) | Prefix array of that property |

---

## Even/Odd Prefix & Special Index

> Build prefix sums over a *filtered* subset (only even indices, only odd indices) — then reason about what an **index removal** does.

### Core Concept
**Q1 — sum of even-index elements in `[L, R]`.** Build `PE` where odd positions just copy the previous value:
```
PE[0] = A[0]
PE[i] = (i % 2 == 0) ? PE[i-1] + A[i] : PE[i-1]
```
then answer with the same `PE[R] − PE[L-1]` formula. (Same idea for odd indices with `PO[0] = 0`.) TC O(N + Q), SC O(N) → O(1) by writing `PE` into `A`.

**Q2 — Special Index.** Count indices `i` such that **after removing `A[i]`**, `sum(even-index elements) == sum(odd-index elements)`.
Example `A = [4, 3, 2, 7, 6, −2]` → answer **2** (indices 0 and 2).

### Details / Walkthrough
Removing `A[i]` shifts every element **after** `i` one place left, so their index **parity flips**. Elements before `i` keep their parity.

```
odd-sum  after removing i = oddSum(0..i-1)  + evenSum(i+1..N-1)
                          = PO[i-1]         + (PE[N-1] − PE[i])
even-sum after removing i = evenSum(0..i-1) + oddSum(i+1..N-1)
                          = PE[i-1]         + (PO[N-1] − PO[i])
```
Case `i == 0`: nothing precedes it, so the `[i-1]` terms are 0.

### Examples
```java
static int specialIndexCount(int[] A) {
    int N = A.length;
    long[] PE = new long[N], PO = new long[N];
    PE[0] = A[0];
    PO[0] = 0;
    for (int i = 1; i < N; i++) {
        PE[i] = (i % 2 == 0) ? PE[i - 1] + A[i] : PE[i - 1];
        PO[i] = (i % 2 == 1) ? PO[i - 1] + A[i] : PO[i - 1];
    }
    int ans = 0;
    for (int i = 0; i < N; i++) {
        long so, se;
        if (i == 0) {
            so = PE[N - 1] - PE[0];
            se = PO[N - 1] - PO[0];
        } else {
            so = PO[i - 1] + PE[N - 1] - PE[i];
            se = PE[i - 1] + PO[N - 1] - PO[i];
        }
        if (so == se) ans++;
    }
    return ans;
}
```
TC O(N + N + N) = **O(N)**, SC O(N). Check on the example: removing index 0 → `[3,2,7,6,−2]`: odd = 2+6 = 8, even = 3+7+(−2) = 8 ✔; removing index 2 → `[4,3,7,6,−2]`: odd = 3+6 = 9, even = 4+7−2 = 9 ✔.

### Common Mistakes / Edge Cases
- Forgetting the **parity flip** of the suffix — using `PO` for the suffix of the odd-sum is the classic error.
- `i == 0` → reading `PE[-1]`/`PO[-1]`.
- Brute-force "remove element and recompute" is O(N²).

### Interview Angle
**How interviewers test this:** "Count indices whose removal balances X"; "Find Pivot / Equilibrium Index"; "Ways to make fair array" (LeetCode 1664).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Even-index / odd-index sum over range" | Two prefix arrays PE / PO |
| "After removing index i …" | Left part unchanged + right part with **flipped parity**, via prefix lookups |
| "Left sum == right sum" | One prefix array + total sum |

---

## Carry Forward

> **Calculate + use together** while scanning, carrying a running count instead of storing a whole auxiliary array. O(N) time, **O(1)** space.

### Core Concept
**Problem:** given a char array of lowercase letters, count pairs `(i, j)` with `i < j`, `A[i]=='a'` and `A[j]=='g'`.
`[a b e g a g]` → 3 pairs `(0,3) (0,5) (4,5)`; `[a c g d g a g]` → 4.

For each `'a'` at `i`, we need **# of `'g'` in `(i+1 … N-1)`**.

### Details / Walkthrough
1. **Brute force:** for every `'a'`, scan to the right counting `'g'` → **O(N²)**, SC O(1).
2. **Suffix array (prefix sum from right → left):** `cntG[i]` = number of `'g'` in `i … N-1`:
   `cntG[N-1] = (A[N-1]=='g') ? 1 : 0`; `cntG[i] = cntG[i+1] + (A[i]=='g' ? 1 : 0)`.
   Then `ans += cntG[i+1]` for every `'a'` at `i`. **TC O(N), SC O(N)** — *calculate* in one pass, *use* in another.
3. **Carry forward (calculate & use in the same pass), scanning right → left** with one counter `cnt`:
   - see `'g'` → `cnt++`
   - see `'a'` → `ans += cnt` (all `'g'` to its right are already counted)

   **TC O(N), SC O(1).**

Homework variant: scan left → right counting `'a'`; each `'g'` adds `cntA` (the number of `'a'` to its left). Same answer — `ans = Σ (count of 'a' on left of i) for every i with A[i]=='g'`.

### Examples
```java
static long countAG(char[] A) {
    long ans = 0;
    int cntG = 0;                       // 'g' seen so far (to the right)
    for (int i = A.length - 1; i >= 0; i--) {
        if (A[i] == 'g') cntG++;        // calculate
        else if (A[i] == 'a') ans += cntG; // use
    }
    return ans;
}
```
(Branch order matters only because a char can't be both; here `'g'` and `'a'` are distinct, so `else if` is safe.)

**Second carry-forward problem — smallest subarray containing both the min and the max.**
`[3 6 2 1 6 5]` → **2** (`[1,6]`); `[2 2 6 4 5 1 5 2 6 4 1]` → **3** (`[6,4,1]`, indices 8–10).
Brute force: all subarrays, check each → **O(N³)**. Observation: the best subarray *starts at one extreme and ends at the other* (min…max or max…min) and contains no other copy of either. So for **every max, find the closest min to its left**, and for every min the closest max to its left. Length of `[L, R]` is `R − L + 1`.

```java
static int smallestSubarrayWithMinMax(int[] A) {
    int N = A.length, mn = Integer.MAX_VALUE, mx = Integer.MIN_VALUE;
    for (int x : A) { mn = Math.min(mn, x); mx = Math.max(mx, x); }

    int idxMin = -1, idxMax = -1, ans = N;     // N = worst case (whole array)
    for (int i = 0; i < N; i++) {
        if (A[i] == mn) {
            if (idxMax != -1) ans = Math.min(ans, i - idxMax + 1);
            idxMin = i;
        }
        if (A[i] == mx) {                       // `if`, not `else if`: min == max when all elements are equal
            if (idxMin != -1) ans = Math.min(ans, i - idxMin + 1);
            idxMax = i;
        }
    }
    return ans;
}
```
TC O(N) (+O(N) to find min/max), SC O(1).

> **Correction vs. the whiteboard:** the lecture uses `else if` between the min and max checks. That fails when every element is equal (`mn == mx`, e.g. `[5,5,5]` — the correct answer is **1**, a single element). Two independent `if`s (as in the code above) handle it naturally: the min branch sets `idxMin = i`, then the max branch measures `i − idxMin + 1 = 1`.

### Common Mistakes / Edge Cases
- Using `idxMax = 0` instead of `-1` as the "not seen yet" sentinel → bogus lengths.
- Updating the index **before** computing the length (must compute with the *previous* closest, then update).
- `int` overflow on the pair count: N=10⁵ gives up to ~2.5·10⁹ pairs → use `long`.
- Not initialising `ans = N` (worst case) before taking `min`.

### Interview Angle
**How interviewers test this:** "count pairs (i<j) with property", "closest X to the left", then asked to drop the O(N) extra array.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Count pairs i<j where A[i]=x and A[j]=y" | Scan once, carry count of x (or y) |
| Aux array computed then used once | Merge both into one loop → O(1) space |
| "Closest X to the left/right", "smallest subarray containing both X and Y" | Track last-seen indices while scanning |
| Prefix-sum from right → left | Suffix sum |

---

## Subarrays

> A subarray is a **contiguous** part of an array. There are **N(N+1)/2** of them.

### Core Concept
Subarray = contiguous block, identified by `(start, end)` or `(start, length)`. A single element and the whole array are both valid subarrays. (A *subsequence* need not be contiguous — different thing.)

**Counting:** subarrays starting at index `s` = `N − s` (end can be `s … N-1`). Over all starts:
`N + (N−1) + … + 1 = N(N+1)/2`. For `[3,5,1,2,7,4]` (N=6): 6+5+4+3+2+1 = **21**.
**Subarrays of fixed length K** = `N − K + 1` (N=7, K=4 → 4).

### Details / Walkthrough
**Print all subarrays** — three nested loops, **TC O(N³)** (N² subarrays × up to N elements each), SC O(1):

```java
for (int i = 0; i < N; i++) {              // start
    for (int j = i; j < N; j++) {          // end
        for (int k = i; k <= j; k++) System.out.print(A[k] + " ");
        System.out.println();
    }
}
```
**Sum of every subarray** — three ways, same output, different cost:

| Method | Idea | TC | SC |
|---|---|---|---|
| Brute force | 3rd loop sums `A[i..j]` | O(N³) | O(1) |
| Prefix sum | `P[j] − P[i−1]` (or `P[j]` if `i==0`) | O(N + N²) = O(N²) | O(N) (O(1) if written into `A`) |
| **Carry forward** | for fixed `i`, `sum += A[j]` as `j` grows — new sum = previous sum + last element | O(N²) | O(1) |

```java
for (int i = 0; i < N; i++) {
    long sum = 0;
    for (int j = i; j < N; j++) {
        sum += A[j];                 // calculate: carry the previous sum forward
        System.out.println(sum);     // use
    }
}
```
Example `[1,2,3]`: `1, 3, 6, 2, 5, 3`.

### Common Mistakes / Edge Cases
- Resetting `sum = 0` in the wrong place (must be per start index `i`).
- Mixing up subarray (contiguous) and subsequence/subset.
- Using the O(N³) form for N ≥ ~500 → TLE (see [constraints](problem-solving-and-complexity.md#big-o--constraints)).

### Interview Angle
**How interviewers test this:** count subarrays with property X; max subarray sum (Kadane); subarray sums & products.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "All subarrays" / "contiguous" | Fix start & end → O(N²) enumeration |
| Need sum of each subarray | Carry forward / prefix sum, not a third loop |
| "Sum over *all* subarrays" | Contribution technique → O(N) |
| Fixed length K | Sliding window |

---

## Contribution Technique

> Instead of enumerating subarrays, ask: **in how many subarrays does each element appear?** `Σ A[i] · (i+1) · (N−i)`.

### Core Concept
**Problem:** total of all subarray sums. `[1,2,3]` → subarray sums `1,3,6,2,5,3` → **20**.

Carry-forward answer = O(N²) (`ans += sum` inside the double loop). Flip the view:
```
Answer = Σ_{i=0}^{N-1} A[i] × (number of subarrays that contain A[i])
```
Check: `1·3 + 2·4 + 3·3 = 3 + 8 + 9 = 20` ✔.

### Details / Walkthrough
Number of subarrays containing index `i`:
- **start** can be any index in `[0, i]` → `(i + 1)` choices
- **end** can be any index in `[i, N−1]` → `(N − 1 − i + 1) = (N − i)` choices
- independent choices multiply → **`(i+1) · (N−i)`**

For `A = [3, −2, 4, −1, 2, 6]`, N=6: index 1 is in `2·5 = 10` subarrays; index 2 is in `3·4 = 12`. ✔ (matches the hand-drawn count).

### Examples
```java
static long sumOfAllSubarraySums(int[] A) {
    int N = A.length;
    long ans = 0;
    for (int i = 0; i < N; i++)
        ans += (long) A[i] * (i + 1) * (N - i);   // cast first: (i+1)*(N-i) alone can overflow int
    return ans;
}
```
**TC O(N), SC O(1)** — down from O(N²) (carry forward) and O(N³) (brute force).

### Common Mistakes / Edge Cases
- **Overflow:** `(i+1)*(N-i)` is ~N²/4 (≈2.5·10⁹ at N=10⁵) — cast to `long` *before* multiplying (`(long) A[i] * (i+1) * (N-i)`).
- Negative elements are fine — contribution is just negative.
- Inclusion check: contribution counts subarrays *containing* `i`, not starting at `i`.

### Interview Angle
**How interviewers test this:** "sum of all subarray sums / min / max", "sum of `(max − min)` over all subarrays" (needs monotonic stack), "count of subarrays containing index i", Sum of Subarray Minimums.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Sum over **all** subarrays/subsets of something" | Per-element contribution × how many times it appears |
| Counting how many windows include position i | `(choices of start) × (choices of end)` |
| N too large for O(N²) | Contribution technique → O(N) |

---

## Fixed-Size Sliding Window

> For subarrays of length **K**, slide the window one step: `sum += A[new]; sum −= A[old]`. O(N) time, O(1) space.

### Core Concept
**Problem:** max subarray sum among subarrays with length exactly K. `A = [3, −2, 4, −1, 2, 6]`, K=4 → window sums `4, 3, 11` → **11**. With K=3 → `5, 1, 5, 7` → **7**.

Window `[st, end]` with `end = st + K − 1`. Start at `st=0, end=K−1`; slide `st++, end++` while `end < N` — that's `N − K + 1` windows.

### Details / Walkthrough
| Approach | How | TC | SC |
|---|---|---|---|
| Brute force | re-sum K elements per window | `(N−K+1)·K` ≈ **O(N²)** worst (peaks at K = N/2: `N²/4`) | O(1) |
| Prefix sum | `P[end] − P[st−1]` per window | O(N + N) = **O(N)** | O(N) (O(1) in-place) |
| **Carry forward / sliding window** | reuse previous window's sum | **O(N)** | **O(1)** |

Note on brute force cost: `(N−K+1)·K` is small at both extremes (K=1 → N; K=N → N) and biggest around K=N/2 → `N²/4` → O(N²).

### Examples
```java
static long maxSumOfSizeK(int[] A, int K) {
    int N = A.length;
    long sum = 0;
    for (int i = 0; i < K; i++) sum += A[i];     // first window [0, K-1]
    long ans = sum;
    for (int i = K; i < N; i++) {                // i is the new window's end
        sum += A[i];                             // add entering element
        sum -= A[i - K];                         // drop leaving element
        ans = Math.max(ans, sum);
    }
    return ans;
}
```
Brute-force reference (what the whiteboard starts from):
```java
long ans = Long.MIN_VALUE;                       // INT_MIN in the lecture; use a safe min so all-negative arrays work
for (int st = 0, end = K - 1; end < N; st++, end++) {
    long sum = 0;
    for (int i = st; i <= end; i++) sum += A[i];
    ans = Math.max(ans, sum);
}
```

### Common Mistakes / Edge Cases
- Initialising `ans = 0` (wrong for all-negative arrays) — use `MIN_VALUE` or the first window's sum.
- Dropping `A[i-K]` (not `A[i-K+1]`) — check with K=1.
- `K > N` → no valid window; guard it.
- Forgetting the first window must be built *before* the sliding loop.

### Interview Angle
**How interviewers test this:** fixed-size window max sum / average (LeetCode 643), max of each window (monotonic deque), then **variable-size** windows (two pointers).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Subarray of length exactly K" | Fixed sliding window |
| "Longest/shortest subarray with condition" | Variable window / two pointers → [Sliding Window](../DSA/two-pointers-sliding-window.md#sliding-window) |
| Window sum/count/average | Add entering, remove leaving |

---

## Related Notes

- [Problem Solving & Complexity](problem-solving-and-complexity.md) — AP sums (N(N+1)/2), O(N³) → O(N²) → O(N) reasoning, when each is allowed by constraints
- [Arrays Basics](arrays-basics.md) — the array primitives (indexing, loops, in-place updates) used throughout
- [DSA: Arrays, Searching & Sorting](../DSA/arrays-searching-sorting.md) — its *Prefix Sum* stub points here
- [DSA: Two Pointers & Sliding Window](../DSA/two-pointers-sliding-window.md) — variable-size windows build on the fixed window here
- [DSA: Hashing & Strings](../DSA/hashing-and-strings.md) — Subarray Sum = K combines prefix sums with a HashMap
- [DSA: Dynamic Programming](../DSA/dynamic-programming.md) — carry-forward is the seed of 1-D DP (Kadane / max-product subarray)

## Cheat Sheet

→ [Introduction_To_PS Cheat Sheet](cheatsheet.md#prefix-sum-subarrays--sliding-window)
