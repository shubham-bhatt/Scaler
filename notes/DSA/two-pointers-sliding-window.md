---
title: Two Pointers & Sliding Window
subject: DSA
type: subbucket
tags: [two-pointers, sliding-window, fast-slow, intervals, sorted-array, in-place, hashing]
topics: [Two Pointers, Sliding Window, Fast & Slow Pointers, Intervals]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-31
reviewed: 2026-08-19
source: lecture notes
related: [arrays-searching-sorting, hashing-and-strings, linkedlist-stack-queue]
status: partial
---

# Two Pointers & Sliding Window

> A family of linear-scan techniques that replace nested loops or hash sets with
> two (or three) indices walking an array. On a **sorted** array they turn many
> O(N log N)/O(N)-space problems into **O(N) time, O(1) space**. Sliding window is
> the same idea specialized to contiguous subarrays; fast/slow powers linked-list
> tricks.

**Topics:** [Two Pointers](#two-pointers) · Sliding Window · Fast & Slow Pointers · Intervals

---

## Two Pointers

> Keep **two indices** walking the array and move them based on the answer you
> want. The "aha" for pair-sum problems: hashing works on *any* array, but if it's
> **sorted**, two pointers is strictly better on space.

### Core Concept

Two canonical questions:

1. **Unsorted array — find a pair with sum `k`.**
2. **Sorted array — find *all* pairs with sum `k`.**

The set-based instinct (walk the array, put values in a set, check if `k - x` is
already there) is correct and is the go-to for **unsorted** input. But the moment
the array is **sorted**, that set is wasted space — a converging two-pointer walk
gives the same answer in O(1) extra space.

#### The two flavours of "two pointers"

| Flavour | Pointers start | Move rule | Needs sorted? | Classic uses |
|---|---|---|---|---|
| **Converging** (opposite ends) | `L=0`, `R=n-1` | shrink from the end that's "wrong" | usually **yes** | pair sum, container with most water, palindrome, 3Sum |
| **Fast / slow** (same direction) | both near start | one runs ahead of the other | no | remove duplicates in place, move zeroes, linked-list cycle, sliding window |

### Details / Walkthrough

#### Problem 1 — Unsorted array, does a pair sum to `k`?

**Hash-set approach:** for each `x`, if `k - x` was seen before, found it; else
remember `x`.

```java
boolean hasPair(int[] a, int k) {
    Set<Integer> seen = new HashSet<>();
    for (int x : a) {
        if (seen.contains(k - x)) return true;
        seen.add(x);
    }
    return false;
}
```
- **Time O(N), Space O(N).** Works on *any* array — no sorting needed.
- Checking `k - x` *before* adding `x` prevents using the same element twice.

**Alternative — sort, then two pointers:** O(N log N) time but **O(1) space**.
Pick this only when extra space is the constraint and sorting is allowed.

#### Problem 2 — Sorted array, find all pairs summing to `k`

```java
void allPairs(int[] a, int k) {       // a is sorted ascending
    int L = 0, R = a.length - 1;
    while (L < R) {
        int sum = a[L] + a[R];
        if (sum == k) {
            System.out.println(a[L] + " + " + a[R]);
            L++; R--;                  // move both inward
            while (L < R && a[L] == a[L - 1]) L++;   // skip duplicate lows
            while (L < R && a[R] == a[R + 1]) R--;   // skip duplicate highs
        } else if (sum < k) {
            L++;                        // need a bigger sum → raise the low end
        } else {
            R--;                        // need a smaller sum → lower the high end
        }
    }
}
```
- **Time O(N), Space O(1).**
- **Why it can't miss a pair:** if `a[L] + a[R] < k`, then `a[L]` paired with
  *anything* left of `R` is even smaller — so `a[L]` is useless here and we safely
  discard it with `L++`. Symmetric for `R--`. Every discard is provably safe.

#### Complexity comparison

| Situation | Best approach | Time | Space |
|---|---|---|---|
| Unsorted, need a pair / indices | Hash set (or hash map) | O(N) | O(N) |
| Already sorted | Two pointers | O(N) | O(1) |
| Unsorted, O(1) space required | Sort + two pointers | O(N log N) | O(1) |

### Which one, under exam pressure?

Take the **first** match:

1. **Answer needs original indices?** (LeetCode Two Sum) → **Hash map** `value → index`.
2. **Array already sorted (or sorting allowed/free)?** → **Two pointers**, O(1) space.
3. **Unsorted, just existence/value?** → **Hash set**.
4. **Tight on memory (O(1) demanded)?** → **Sort + two pointers**, accept O(N log N).

> Memorise: **"Sorted → two pointers. Need indices → hash map. Otherwise → hash set."**

#### Handling duplicates — the part that trips people up

| Task | Hash approach | Two-pointer approach |
|---|---|---|
| Pair exists? | set is fine; guard `x == k - x` | works as-is |
| **Count** pairs (with repeats) | frequency **map**, not a set | count runs of equal values |
| **Distinct value-pairs only** | dedupe results in a set | skip equal neighbours after a hit |
| Same element twice? | check `k - x` *before* inserting `x` | `L < R` guard prevents it |

The subtle bug: a plain `Set` with duplicates either **misses** counts or
**double-counts**. For "count pairs" / "duplicates allowed", reach for a
**HashMap of frequencies**, or sort + two pointers with explicit dup-skipping.

### More problems that are secretly two-pointer

| Problem | Pointer setup |
|---|---|
| Pair with target sum (sorted) | converging L/R |
| Count pairs with given **sum**, duplicates counted | converging L/R, multiply matching block sizes (nC2 for equal blocks) |
| Count **distinct-value** pairs with given **difference** | two same-direction pointers, skip whole duplicate blocks after a hit |
| Pair **closest** to target | converging, track best `|sum - k|` |
| **3Sum / 4Sum** | fix one (or two), two-pointer the rest |
| Container With Most Water | converging, move the shorter wall |
| Trapping Rain Water | converging with running max on each side |
| Valid Palindrome | converging, compare `a[L]` vs `a[R]` |
| Reverse array / string in place | converging, swap and step inward |
| Remove duplicates from sorted array | fast/slow, slow marks write position |
| Move zeroes / partition (quicksort) | fast/slow |
| Sort Colours (Dutch flag) | **three** pointers: low, mid, high |
| Merge two sorted arrays | one pointer per array |
| Linked list: cycle / middle / Nth-from-end | fast/slow |

The **merge step of Merge Sort is itself two pointers** walking two sorted halves.

### Worked Problem — Pairs with Absolute Difference B

> Given a sorted array (values may repeat) and a non-negative integer `B`, count
> pairs `(x, y)` with `|x - y| = B`, counting each **distinct value pair once**
> (not once per index collision). E.g. `A = [1,1,4,4], B = 3` → only `{1,4}` →
> answer `1`, even though there are 2×2=4 index pairs that produce that difference.

Rewriting `|x-y|=B` as `y = x+B` turns this into: *"for every distinct x, does
x+B also exist?"* — two ways to answer that:

**HashSet (any array, unsorted is fine):**
```java
int countPairsWithDiff(int[] a, int B) {
    Set<Integer> vals = new HashSet<>();
    for (int x : a) vals.add(x);
    if (B == 0) {
        // a Set can't tell you "occurs at least twice" — need a frequency map instead
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : a) freq.merge(x, 1, Integer::sum);
        int count = 0;
        for (int f : freq.values()) if (f >= 2) count++;
        return count;
    }
    int count = 0;
    for (int x : vals) if (vals.contains(x + B)) count++;
    return count;
}
```

**Sort + two pointers (O(1) extra space) — handles B=0 with the *same* code path:**
```java
int countPairsWithDiffTwoPointer(int[] a, int B) {
    Arrays.sort(a);
    int i = 0, j = 1, count = 0;
    while (j < a.length) {
        if (i == j) { j++; continue; }
        int diff = a[j] - a[i];
        if (diff < B) {
            j++;
        } else if (diff > B) {
            i++;
        } else {                                     // diff == B — found a distinct pair
            count++;
            int vi = a[i], vj = a[j];
            while (i < a.length && a[i] == vi) i++;   // skip the whole duplicate block of x
            while (j < a.length && a[j] == vj) j++;   // skip the whole duplicate block of y
        }
    }
    return count;
}
```
When `B == 0`, `vi == vj`, so both skip-loops consume the same duplicate block —
no separate "special case" branch is needed; the block-skip pattern already
generalizes. (The rough version of this note treated `B=0` as a fork requiring
different logic — it doesn't, once you skip by *value block* instead of by
single step.)

**Why block-skipping matters:** after recording a hit, jumping straight to
`i = j` (or any point past the two matched values) would silently discard
*other* valid pairs still sitting between them — see Common Mistake #7 below.

### Worked Problem — Counting Pairs with Given Sum (duplicates counted)

> Given a sorted array (values may repeat) and target `B`, count **index** pairs
> `i < j` with `A[i] + A[j] = B`, mod `1e9+7`. Unlike the difference problem above,
> here duplicate *index* pairs all count — so equal-value blocks must be combined
> combinatorially (nC2 / product of block sizes), not skipped.

Sorting doesn't change how many index pairs sum to `B` (it's just a relabeling of
positions), so it's safe to sort and use converging pointers, then multiply out
whole blocks of equal values in one step instead of visiting each pair:

```java
int countPairsWithSum(int[] a, int B) {
    Arrays.sort(a);
    final long MOD = 1_000_000_007L;
    int L = 0, R = a.length - 1;
    long count = 0;
    while (L < R) {
        int sum = a[L] + a[R];
        if (sum < B) {
            L++;
        } else if (sum > B) {
            R--;
        } else if (a[L] != a[R]) {
            int cl = 1, cr = 1;
            while (L + 1 <= R && a[L + 1] == a[L]) { cl++; L++; }
            while (R - 1 >= L && a[R - 1] == a[R]) { cr++; R--; }
            count = (count + (long) cl * cr) % MOD;      // every x paired with every y
            L++; R--;
        } else {                          // a[L] == a[R]: the whole remaining window is one value
            long n = R - L + 1;
            count = (count + n * (n - 1) / 2) % MOD;      // nC2 — pair every element with every other once
            break;
        }
    }
    return (int) count;
}
```
This is the [3 Core Pair-Counting Formulas](hashing-and-strings.md#frequency-patterns)
in two-pointer form: a same-value block of size `n` contributes `n*(n-1)/2`
(nC2, unordered `i<j` pairs); two different-value blocks of sizes `cl`/`cr`
contribute `cl*cr` (every left element pairs with every right element once).

### Examples

**Sorted array** `[1, 2, 3, 4, 6]`, `k = 6`:

| L | R | a[L]+a[R] | action |
|---|---|---|---|
| 0 (1) | 4 (6) | 7 | `> 6` → `R--` |
| 0 (1) | 3 (4) | 5 | `< 6` → `L++` |
| 1 (2) | 3 (4) | 6 | **hit** (2,4) → `L++, R--` |
| 2 (3) | 2 (3) | — | `L == R`, stop |

**Answer:** pair (2, 4).

### Common Mistakes / Edge Cases

1. **Two pointers on an unsorted array** — the move rule is meaningless without order.
2. **Sorting when the problem wants indices** — you lose the original positions; use a hash map (or store `(value, index)` before sorting).
3. **Same element used twice** — with a set, check `k - x` before inserting; with two pointers, the `L < R` guard handles it.
4. **Duplicates while counting** — a bare set under/over-counts; use a frequency map or dup-skipping.
5. **`L <= R` vs `L < R`** — for distinct-index pairs use `L < R`.
6. **Forgetting to move a pointer** — every branch must advance `L` or `R`.
7. **Skipping farther than proven safe after a hit** — e.g. setting `L = R`
   after finding one pair. On `[1,2,3,4]` looking for diff `B=2`, the valid pairs
   are `{1,3}` and `{2,4}`; jumping straight to the far pointer after the first
   hit skips `{2,4}` entirely. Only skip exactly the duplicate *block* you just
   resolved (see the difference-pairs worked example above), never further.
8. **Distinct-value pairs vs index pairs** — decide which one the problem wants
   *before* coding. They need opposite duplicate-handling: distinct-value pairs
   **skip** past a matched value's whole block; index-pair counting **multiplies**
   block sizes together (nC2 / product) instead of skipping. Mixing the two up is
   the single most common bug in this family of problems.
9. **`Integer` object comparison with `==`** — `ArrayList<Integer>`/boxed values
   compared with `list.get(i) == list.get(j)` silently breaks for values ≥ 128
   (Java's Integer cache only covers −128..127, so larger values are separate
   objects and `==` compares references, not value). Always use `.equals()` for
   boxed-vs-boxed comparisons; `==` is fine only when one side is an unboxed `int`
   (auto-unboxing kicks in).

### Interview Angle

**How interviewers test this:**
- Start: "Two Sum" on an **unsorted** array returning indices → they want the hash-map answer; noting "if sorted I'd use two pointers" scores points.
- Escalate: "Now the array is sorted, O(1) space?" → two pointers.
- Escalate: "Find **all** / **count** pairs / handle duplicates" → set vs frequency-map vs dup-skipping.
- Escalate: "3Sum / 4Sum", "closest pair", "does it fit in memory?".

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "sorted array", "pair with sum k" | Converging two pointers, O(1) space |
| "return the indices" | Hash map `value → index` (not two pointers) |
| "unsorted", "does a pair exist" | Hash set |
| "count pairs", "duplicates allowed" | Frequency HashMap, or sort + skip dups |
| "in place", "O(1) extra space" | Two pointers (sort first if needed) |
| "3 numbers / 4 numbers sum to target" | Fix one + two pointers on the rest |
| "most water", "trap rain", "closest to target" | Converging two pointers |
| "remove duplicates", "move zeroes", "partition" | Fast/slow two pointers |
| "cycle / middle / Nth from end of linked list" | Fast/slow two pointers |

---

## Sliding Window

_Not yet written._ (Fixed-size window, variable-size window, window + hashmap for
"longest substring without repeating", monotonic-deque window maximum.)

---

## Fast & Slow Pointers

_Not yet written._ (Floyd cycle detection, middle of linked list, Nth from end,
happy number.)

---

## Intervals

_Not yet written._ (Merge intervals, insert interval, meeting rooms — sort by
start, then sweep.)

---

## Related Notes

- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — merge step is two pointers; sorting enables converging scans
- [Hashing & Strings](hashing-and-strings.md) — the hash-set/hash-map alternative to two pointers; sliding window over strings uses frequency maps
- [Linked List, Stack & Queue](linkedlist-stack-queue.md) — fast/slow pointers on linked lists; monotonic deque for window maximum

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#two-pointers--sliding-window)
