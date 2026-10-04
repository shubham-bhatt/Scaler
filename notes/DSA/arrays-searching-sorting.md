---
title: Arrays, Searching & Sorting
subject: DSA
type: subbucket
tags: [arrays, sorting, binary-search, prefix-sum, divide-and-conquer, inversion-count]
topics: [Arrays, Sorting, Binary Search, Prefix Sum]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-31
reviewed: 2026-08-09
source: lecture notes
related: [two-pointers-sliding-window, dynamic-programming, bit-and-math, problem-solving-and-complexity, arrays-basics, prefix-sum-and-subarrays]
status: partial
---

# Arrays, Searching & Sorting

> The foundation of DSA: how data sits in a contiguous array, how to order it,
> and how to search it fast. Sorting (esp. merge sort) and binary search are the
> workhorses most other patterns build on; prefix sums turn range queries O(1).

**Topics:** Arrays · [Sorting](#sorting) · Binary Search · Prefix Sum

---

## Arrays

_Not yet written._

---

## Sorting

The interview-critical sort is **Merge Sort** — the only comparison sort that is
both O(N log N) in the worst case *and* stable, and whose merge step powers the
classic inversion-count problem.

### Merge Sort & Inversion Count

> Merge Sort is a divide-and-conquer sorting algorithm that guarantees O(N log N)
> time in all cases. Its merge step is the foundation for counting inversions — a
> classic interview question that tests whether you understand the algorithm beyond
> just sorting.

#### Core Concept

**Divide and Conquer**: Break the problem into smaller subproblems, solve each
independently, then combine the results. Merge Sort works in three phases:

1. **Divide** — Split the array into two halves.
2. **Conquer** — Recursively sort each half (base case: a single element is sorted).
3. **Combine** — Merge the two sorted halves into one sorted array.

The key insight: merging two sorted arrays takes O(N) time because you only ever
compare the front elements of each half.

#### The Merge Step

Given two sorted halves `left[]` and `right[]`, build the result by repeatedly
picking the smaller front element:

```java
int i = 0, j = 0;
List<Integer> merged = new ArrayList<>();
while (i < left.size() && j < right.size()) {
    if (left.get(i) <= right.get(j)) {
        merged.add(left.get(i++));
    } else {
        merged.add(right.get(j++));
    }
}
while (i < left.size())  merged.add(left.get(i++));
while (j < right.size()) merged.add(right.get(j++));
```

#### Splitting in Java

Use `subList` to create the two halves without manual index math:

```java
int mid = B.size() / 2;
ArrayList<Integer> left  = new ArrayList<>(B.subList(0, mid));
ArrayList<Integer> right = new ArrayList<>(B.subList(mid, B.size()));
```

**Important**: Wrap `subList` in `new ArrayList<>(...)` because `subList` returns
a *view* — modifying the original list would corrupt it.

#### Complexity

| Case    | Time        | Space |
|---------|-------------|-------|
| Best    | O(N log N)  | O(N)  |
| Average | O(N log N)  | O(N)  |
| Worst   | O(N log N)  | O(N)  |

- **Stable sort**: Equal elements preserve their relative order.
- **Not in-place**: Requires O(N) auxiliary space for merging.

#### Inversion Count

An **inversion** is a pair (i, j) where i < j but arr[i] > arr[j]. The total
inversion count measures how "unsorted" an array is.

**Key insight**: During the merge step, every time you pick an element from the
**right half** (because it's smaller than the current left-half element), that
element is inverted with respect to **all remaining elements in the left half**.

```
If picking from right half at position j:
    inversions += (left.size() - i)   // remaining elements in left half
```

**Why this works**: Both halves are already sorted. If `right[j] < left[i]`, then
`right[j]` is also smaller than `left[i+1] ... left[end]` — so all those pairs are
inversions.

```java
long merge(List<Integer> arr, int l, int r) {
    if (r - l <= 1) return 0;
    int mid = (l + r) / 2;
    long count = 0;
    count += merge(arr, l, mid);
    count += merge(arr, mid, r);

    List<Integer> left  = new ArrayList<>(arr.subList(l, mid));
    List<Integer> right = new ArrayList<>(arr.subList(mid, r));

    int i = 0, j = 0, k = l;
    while (i < left.size() && j < right.size()) {
        if (left.get(i) <= right.get(j)) {
            arr.set(k++, left.get(i++));
        } else {
            arr.set(k++, right.get(j++));
            count += (left.size() - i);  // all remaining left elements form inversions
        }
    }
    while (i < left.size())  arr.set(k++, left.get(i++));
    while (j < right.size()) arr.set(k++, right.get(j++));
    return count;
}
```

#### Examples

**Array**: [2, 4, 1, 3, 5]

```
[2, 4, 1, 3, 5]
     /          \
[2, 4]        [1, 3, 5]
 / \           /    \
[2] [4]      [1]  [3, 5]
                    / \
                  [3] [5]
```

Merge upward:
- [2, 4] + [1, 3, 5] → [1, 2, 3, 4, 5]
  - Pick 1 from right: inversions += 2 (both 2 and 4 remain in left)
  - Pick 3 from right: inversions += 1 (4 still in left)

**Total inversion count: 3** (pairs: (2,1), (4,1), (4,3))

#### Common Mistakes / Edge Cases

1. **Forgetting to wrap `subList` in `new ArrayList<>()`** — it is a view, not a copy.
2. **Using `<` instead of `<=` in merge comparison** — makes the sort unstable and
   overcounts inversions (equal elements counted as inversions).
3. **Integer overflow for inversion count** — max is N*(N-1)/2. Use `long`.
4. **Empty / single-element array** — base case must handle both.
5. **Unsafe comparator subtraction** — `(a, b) -> a - b` can silently overflow
   or underflow for large or negative values (e.g. near `Integer.MIN_VALUE`),
   producing an inconsistent comparator and a corrupted sort. Prefer
   `Integer::compare` or `(a, b) -> Integer.compare(a, b)` — safe for the full
   `int` range. (Subtraction is sometimes fine under known small constraints, but
   the safe comparator is the interview-quality default.)

#### Interview Angle

**How interviewers test this:**
- Direct: "Count inversions in an array" (classic — O(N log N) via merge sort).
- "Given two sorted arrays, merge them" — the merge step isolated.
- Follow-up: "Count inversions without modifying the array?" (copy first).
- Follow-up: "Inversions within subarrays?" → segment tree / BIT (Fenwick).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem            | Think of This Pattern            |
|--------------------------------------------|----------------------------------|
| "count pairs where i < j and a[i] > a[j]"  | Inversion count via merge sort   |
| "sort in O(N log N) guaranteed"            | Merge sort                       |
| "stable sort needed"                       | Merge sort                       |
| "divide and conquer on array"             | Merge sort structure             |
| "merge two sorted arrays"                 | Merge step of merge sort         |
| "how unsorted is this array"              | Inversion count                  |

---

## Binary Search

_Not yet written._

---

## Prefix Sum

_Not yet written._

---

## Related Notes

- [Two Pointers & Sliding Window](two-pointers-sliding-window.md) — the merge step is two pointers walking two sorted halves; sorting enables converging two-pointer scans
- [Dynamic Programming](dynamic-programming.md) — subarray optimization problems build on array scanning
- [Bit Manipulation & Math](bit-and-math.md) — both rely on structured O(N log N) iteration
- [Intro to PS: Problem Solving & Complexity](../Introduction_To_PS/problem-solving-and-complexity.md) — complexity counting (loops, log N, Big O, constraints) underlying every algorithm here
- [Intro to PS: Arrays Basics](../Introduction_To_PS/arrays-basics.md) — array fundamentals, reverse, rotate — the prerequisites
- [Intro to PS: Prefix Sum, Subarrays & Sliding Window](../Introduction_To_PS/prefix-sum-and-subarrays.md) — the written Prefix Sum material for this file's stub

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#arrays-searching--sorting)
