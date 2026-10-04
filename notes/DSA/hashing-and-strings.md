---
title: Hashing & Strings
subject: DSA
type: subbucket
tags: [hashing, hashmap, hashset, strings, frequency, sliding-window]
topics: [Hashing, Frequency Patterns, String Algorithms]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-09-07
reviewed: 2026-08-20
source: lecture notes
related: [two-pointers-sliding-window, arrays-searching-sorting, heaps-and-greedy]
status: partial
---

# Hashing & Strings

> O(1) average lookup is the single biggest algorithmic lever. Hash maps/sets turn
> "have I seen this?" and "how many of each?" into constant time, and most string
> problems are frequency-counting or sliding-window in disguise.

**Topics:** [Hashing](#hashing) · Frequency Patterns · String Algorithms

---

## Hashing

> A `HashSet` turns "does this value exist?" into O(1) average lookup — the
> lever that replaces an O(N log N) sort or an O(N²) nested scan whenever a
> problem only needs *membership*, not order.

### Core Concept

**HashSet vs HashMap:** use a `Set` when you only need "have I seen this
value?"; use a `Map` when you also need a count, an index, or some other
value attached to the key (see [Frequency Patterns](#frequency-patterns)
below for the counting case). Reaching for a `Set` when the problem actually
needs a count is a common miss — a `Set` can't answer "how many".

**When hashing beats sorting:** sorting costs `O(N log N)` and reorders the
data; a hash set costs `O(N)` to build and preserves nothing about order —
use it when the problem only cares about *which* values are present, not
their positions or relative order.

### Details / Walkthrough — Longest Consecutive Sequence

> Given an unsorted array of integers, find the length of the longest run of
> consecutive integers (they don't need to be contiguous *in the array*,
> just consecutive as integers, e.g. `[100,4,200,1,3,2]` → the run `1,2,3,4`).

**Pattern:** `HashSet → find each sequence's start → expand`

```java
int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) set.add(num);

    int max = 0;
    for (int num : set) {
        if (!set.contains(num - 1)) {          // only start counting at a sequence's start
            int curr = num;
            int len = 1;
            while (set.contains(curr + 1)) {
                curr++;
                len++;
            }
            max = Math.max(max, len);
        }
    }
    return max;
}
```

**Key insight:** only start expanding a sequence when `num - 1` is **not** in
the set. Without that guard, every element inside a run would re-scan the
same run from scratch — starting only at true sequence-starts is what keeps
the total work `O(N)` instead of `O(N²)` (each element is visited by the
`while` loop at most once, across all outer-loop iterations combined, because
only sequence-starts trigger a scan).

### Examples

`nums = [100, 4, 200, 1, 3, 2]` → set `{100,4,200,1,3,2}` → `1` has no
`0` in the set, so it's a start: expands `1→2→3→4`, length **4**. `100` and
`200` are isolated starts of length 1. Answer: **4**.

### Common Mistakes / Edge Cases

1. **Sorting first "to make it easier"** — works (`O(N log N)`) but throws
   away the `O(N)` solution the problem is testing for; only reach for sorting
   if the hash-set approach is genuinely blocked.
2. **Forgetting the `!set.contains(num - 1)` guard** — without it, every
   element re-expands its whole run, degrading to `O(N²)` in the worst case
   (e.g. one giant consecutive run).
3. **Duplicates in the input** — a `Set` naturally dedupes them; no special
   handling needed, but worth stating explicitly if asked.
4. **Empty array** — return `0`; the loop over an empty set never executes.

### Interview Angle

**How interviewers test this:**
- Direct: "Longest Consecutive Sequence" (LC 128) — the `O(N)` (not
  `O(N log N)`) constraint is usually stated explicitly to rule out sorting.
- Follow-up: "return the actual sequence, not just its length" — track
  `curr` at the point `max` is updated.
- Follow-up: "what if the array is a stream (can't hold it all in memory)?" —
  a plain hash set no longer works; different problem entirely.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "does this value exist", "have I seen this" | `HashSet`, O(1) average membership |
| "longest run of consecutive integers", O(N) required | `HashSet` + expand only from sequence starts |
| "need a count, not just presence" | `HashMap` instead — see [Frequency Patterns](#frequency-patterns) |
| "O(N log N) is too slow" | Hashing is usually the O(N) alternative to sorting |

---

## Frequency Patterns

> Reduce brute-force nested loops over repeated elements to O(N): build a
> frequency map once, then apply a closed-form formula per distinct value
> instead of enumerating every pair. _(Count-and-compare variants — anagrams,
> group anagrams, top-K frequent, subarray sum equals K via prefix-sum +
> hashmap — are the next layer to add here.)_

### Core Concept

Most "count pairs/combinations of repeated elements" problems reduce to two
steps: **(1)** build a frequency map of each value in O(N), **(2)** apply a
closed-form counting formula per distinct value instead of a nested loop.
Which formula applies depends on whether order matters — the classic
combinatorics distinction between **combinations** (nC2) and **permutations**
(nP2).

### Details / Walkthrough

**The 3 Core Pair-Counting Formulas** (`n` = frequency of one value):

| Formula | Meaning | n=5 example | When to use |
|---|---|---|---|
| Unique index pairs (`i<j`) | nC2 = `n·(n-1)/2` | 10 | order doesn't matter, no self-pair — most "count pairs" questions |
| Distinct index pairs (`i≠j`) | nP2 = `n·(n-1)` | 20 | order/direction matters — `(i,j)` and `(j,i)` are different |
| Self-pairing allowed (any `i,j`) | `n²` | 25 | element may pair with itself (grid coords, combinations with replacement) |

About 90% of interview "count pairs" questions use nC2, because `(A,B)` and
`(B,A)` are the same pair when only sum/equality is being checked.

**Two ways to apply it:**
1. **Batch** — build the full frequency map first, then sum `f*(f-1)/2` over every value.
2. **On-the-fly (running total)** — walk the array once; *before* recording the
   current value, add how many times it's *already* been seen to the running
   total, then increment its count. One pass, no second loop.

```java
// On-the-fly, i<j pairs of equal value (Number of Good Pairs, LC 1512)
int countGoodPairs(int[] nums) {
    int pairs = 0;
    Map<Integer, Integer> freq = new HashMap<>();
    for (int num : nums) {
        pairs += freq.getOrDefault(num, 0);   // pairs with everything seen so far
        freq.merge(num, 1, Integer::sum);
    }
    return pairs;
}
```

```java
// Batch, nC2 over every distinct value
long countPairsBatch(int[] nums) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (int x : nums) freq.merge(x, 1, Integer::sum);
    long pairs = 0;
    for (int f : freq.values()) pairs += (long) f * (f - 1) / 2;
    return pairs;
}
```

### Examples

`nums = [1,2,3,1,1,3]` → freq `{1:3, 2:1, 3:2}` →
pairs = C(3,2) + C(1,2) + C(2,2) = 3 + 0 + 1 = **4**.

### Common Mistakes / Edge Cases

1. **Integer overflow in `n*(n-1)`** — with `n` up to ~10^5, `n*(n-1) ≈ 10^10`,
   which overflows a 32-bit `int` **before** any modulo is applied. Cast the
   first operand to `long`: `(long) n * (n - 1)` — never `(long) (n * (n - 1))`,
   since the inner multiplication has already overflowed by the time the cast runs.
2. **Applying modulo too late** — `int result = (a * b) % MOD` still overflows if
   `a*b` doesn't fit in an `int` on its own; compute the multiplication in `long`
   first, take `% MOD`, then cast back if a narrower type is required.
3. **Confusing nC2 with nP2** — re-read the problem: do `(A,B)` and `(B,A)` count
   as different? If yes, it's nP2 (`n*(n-1)`), not nC2.
4. **Reaching for a `Set` instead of a frequency `Map`** — a `Set` can't answer
   "how many times did this repeat?", which every formula above needs.
5. **Building keys/strings with `+=` inside the counting loop** — `String` is
   immutable, so repeated concatenation (e.g. constructing a composite key per
   element) is O(N²) instead of O(N). Use `StringBuilder` and call `.toString()`
   once; if it's easier to build right-to-left, append and `.reverse()` at the end
   rather than prepending (`s = c + s` is O(N) per prepend).

### Details / Walkthrough — Subarray Sum Equals K

> Given an array and an integer `k`, count the number of contiguous
> subarrays whose elements sum to `k`.

**Core idea:** if `prefixSum[j] - prefixSum[i] = k` for some `i < j`, then the
subarray `(i, j]` sums to `k`. Rearranged: for the current running sum, we
need to know how many earlier prefix sums equal `currentSum - k` — track that
in a frequency map as you scan, instead of recomputing prefix sums pairwise.

```java
int subarraySumEqualsK(int[] nums, int k) {
    Map<Integer, Integer> prefixCounts = new HashMap<>();
    prefixCounts.put(0, 1);       // empty prefix — handles subarrays starting at index 0
    int sum = 0, count = 0;
    for (int num : nums) {
        sum += num;
        count += prefixCounts.getOrDefault(sum - k, 0);
        prefixCounts.put(sum, prefixCounts.getOrDefault(sum, 0) + 1);
    }
    return count;
}
```

**Complexity:** O(N) time, O(N) space — one pass, one hash map, versus the
O(N²) brute force of checking every `(i, j)` pair directly.

**Why `prefixCounts.put(0, 1)` before the loop:** without it, a subarray that
starts at index 0 and itself sums to exactly `k` is missed — `sum - k` would
equal `0`, but `0` was never recorded as a "prefix sum seen so far" unless
seeded up front (the empty prefix, before any elements, sums to 0).

#### Examples

`nums = [1,2,3], k=3` → prefix sums as scanned: `1, 3, 6`. At `sum=1`:
`sum-k=-2`, not seen. At `sum=3`: `sum-k=0`, seen once (the seeded empty
prefix) → count=1 (subarray `[1,2]`). At `sum=6`: `sum-k=3`, seen once
(from `sum=3` above) → count=2 (subarray `[3]`). Total: **2**.

#### Common Mistakes / Edge Cases

1. **Forgetting to seed `prefixCounts.put(0, 1)`** — silently undercounts by
   missing every subarray that starts at index 0.
2. **Reading the current sum's own count before adding it** — the lookup
   (`sum - k`) must happen **before** `sum` itself is recorded into the map
   for this iteration, otherwise a single element equal to `k` would count
   itself as a pair with itself.
3. **Negative numbers in the array** — the prefix-sum approach still works
   (unlike a sliding window, which breaks down once elements can be
   negative) — this is precisely why hashing is preferred over a two-pointer
   window here.

### Interview Angle

**How interviewers test this:**
- "Count pairs with equal/matching value" ("Number of Good Pairs", LC 1512) →
  frequency map + nC2, on-the-fly or batch.
- Follow-up: "What if `(i,j)` and `(j,i)` both count?" → switch to nP2.
- Follow-up: "What if an element can pair with itself?" → `n²`.
- Watch for them quietly bumping `N` to `10^5` — that's the overflow trap; say
  out loud that you'd use `long` before they have to ask.
- Direct: "Subarray Sum Equals K" (LC 560) → prefix-sum frequency map, seeded
  with `{0: 1}`.
- Follow-up: "What if the array has only non-negative numbers?" → a sliding
  window becomes viable too (sum only grows/shrinks monotonically); with
  negatives allowed, the prefix-sum + hashmap approach is required.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "count pairs of equal/matching elements" | Frequency map + nC2 per value |
| "order matters" / "(i,j) and (j,i) differ" | Frequency map + nP2 per value |
| "element can pair with itself" | Frequency map + n² per value |
| "N up to 10^5" and multiplying counts | Cast to `long` **before** multiplying |
| "sum equal to K", "subarray count" | Frequency map + running prefix sum |

---

## String Algorithms

_Not yet written._ (Pattern matching KMP/Rabin-Karp, palindrome checks, string
building, sliding window over strings — longest substring without repeating.)

---

## Related Notes

- [Two Pointers & Sliding Window](two-pointers-sliding-window.md) — hashing is the alternative to two pointers on unsorted data; window problems use frequency maps
- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — hashing vs sorting trade-off for dedup/lookups
- [Heaps & Greedy](heaps-and-greedy.md) — Top-K Frequent Elements ranks the output of a frequency map by count

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#hashing--strings)
