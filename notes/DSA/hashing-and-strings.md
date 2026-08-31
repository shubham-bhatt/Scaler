---
title: Hashing & Strings
subject: DSA
type: subbucket
tags: [hashing, hashmap, hashset, strings, frequency, sliding-window]
topics: [Hashing, Frequency Patterns, String Algorithms]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-31
reviewed: 2026-08-20
source: lecture notes
related: [two-pointers-sliding-window, arrays-searching-sorting]
status: partial
---

# Hashing & Strings

> O(1) average lookup is the single biggest algorithmic lever. Hash maps/sets turn
> "have I seen this?" and "how many of each?" into constant time, and most string
> problems are frequency-counting or sliding-window in disguise.

**Topics:** Hashing · Frequency Patterns · String Algorithms

---

## Hashing

_Not yet written._ (HashMap vs HashSet, collision handling, load factor, when
hashing beats sorting/two-pointers, custom keys / hashing pairs.)

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

### Interview Angle

**How interviewers test this:**
- "Count pairs with equal/matching value" ("Number of Good Pairs", LC 1512) →
  frequency map + nC2, on-the-fly or batch.
- Follow-up: "What if `(i,j)` and `(j,i)` both count?" → switch to nP2.
- Follow-up: "What if an element can pair with itself?" → `n²`.
- Watch for them quietly bumping `N` to `10^5` — that's the overflow trap; say
  out loud that you'd use `long` before they have to ask.

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

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#hashing--strings)
