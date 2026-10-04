---
title: Heaps & Greedy
subject: DSA
type: subbucket
tags: [heap, priority-queue, top-k, greedy]
topics: [Heap / Priority Queue, Top-K, Greedy]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-09-07
reviewed: 2026-08-20
source: lecture notes
related: [graphs, arrays-searching-sorting, hashing-and-strings]
status: partial
---

# Heaps & Greedy

> A heap gives O(log N) access to the current best element — the engine behind
> Top-K, streaming medians, and Dijkstra/Prim. Greedy makes the locally optimal
> choice each step and works when a problem has the greedy-choice property; heaps
> are how many greedy algorithms pick that next choice.

**Topics:** Heap / Priority Queue · [Top-K](#top-k) · Greedy

---

## Heap / Priority Queue

_Not yet written._ (Min-heap vs max-heap, `PriorityQueue` in Java, heapify O(N),
push/pop O(log N), custom comparators; two-heap median of a stream.)

---

## Top-K

> "Find the K most/least frequent (or largest/smallest)" problems reduce to:
> **build a frequency map, then pick the top K by that frequency** — the
> naive way is sort everything and slice; a heap or quickselect avoids
> paying for the full sort when `K` is small.

### Core Concept

Three ways to get the top `K` out of `M` distinct items, in increasing
sophistication:
1. **Sort everything** — `O(M log M)` time. Simplest, fine unless `M` is huge
   and `K` is tiny relative to it.
2. **Size-`K` heap of the opposite polarity** — for top-K *largest*, keep a
   **min-heap** of size `K` (pop the smallest whenever the heap exceeds `K`);
   for top-K *smallest*, keep a **max-heap**. `O(M log K)` time.
3. **Quickselect** — partition like quicksort, recurse into only the side
   that contains the Kth element. `O(M)` average time, `O(M²)` worst case.

### Details / Walkthrough — Top K Frequent Elements

> Given an integer array, return the `K` most frequent elements.

**Pattern:** `HashMap → frequency count → sort by frequency`

```java
int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : nums)
        map.put(num, map.getOrDefault(num, 0) + 1);

    int[][] arr = new int[map.size()][2];
    int i = 0;
    for (int key : map.keySet()) {
        arr[i][0] = key;              // value
        arr[i][1] = map.get(key);     // frequency
        i++;
    }

    Arrays.sort(arr, (a, b) -> b[1] - a[1]);   // frequency descending

    int[] ans = new int[k];
    for (i = 0; i < k; i++) ans[i] = arr[i][0];
    return ans;
}
```

**Complexity:** `O(N + M log M)` time (`N` = input length, `M` = unique
elements), `O(M)` space. Building the frequency map is `O(N)`; sorting the
`M` distinct entries dominates.

**Optimization for large `M`, small `K`:** a size-`K` min-heap keyed on
frequency avoids sorting all `M` entries — `O(N + M log K)` instead of
`O(N + M log M)`, which matters when `K` is small relative to the number of
distinct elements.

```java
int[] topKFrequentHeap(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : nums) map.merge(num, 1, Integer::sum);

    PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[1] - b[1]);
    for (Map.Entry<Integer, Integer> e : map.entrySet()) {
        minHeap.offer(new int[]{e.getKey(), e.getValue()});
        if (minHeap.size() > k) minHeap.poll();   // evict the smallest-frequency entry
    }

    int[] ans = new int[k];
    for (int i = k - 1; i >= 0; i--) ans[i] = minHeap.poll()[0];
    return ans;
}
```

### Examples

`nums = [1,1,1,2,2,3]`, `k=2` → frequency `{1:3, 2:2, 3:1}` → top 2 by
frequency = `[1, 2]`.

### Common Mistakes / Edge Cases

1. **Reaching for a `TreeMap`/sorting by key instead of by frequency** — the
   ordering needed is by *count*, not by the element's own value.
2. **Comparator direction** — `(a,b) -> b[1]-a[1]` sorts frequency
   **descending**; swapping the operands (or forgetting the sign) silently
   returns the *least* frequent elements instead.
3. **Using a max-heap of all `M` elements instead of a size-`K` min-heap** —
   defeats the point of using a heap; the min-heap must be capped at size `K`
   (evicting the smallest) to get the `O(log K)` benefit per insert.
4. **`k` larger than the number of distinct elements** — guard or clarify
   with the interviewer; the loop as written would throw if `k > map.size()`.

### Interview Angle

**How interviewers test this:**
- Direct: "Top K Frequent Elements" (LC 347).
- Follow-up: "Can you do better than `O(M log M)`?" → size-`K` heap,
  `O(N + M log K)`.
- Follow-up: "Can you do it in `O(N)` average?" → quickselect on the
  frequency array (partition around a pivot, recurse into the side holding
  the Kth-largest-frequency element).
- Related: "Kth largest element in an array" (LC 215) is the same shape
  without the frequency-map step — heap or quickselect directly on the values.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "K most/least frequent elements" | Frequency map ([Hashing & Strings](hashing-and-strings.md)) + sort/heap on frequency |
| "Kth largest/smallest element" | Size-K heap of opposite polarity, or quickselect |
| "M unique elements, K is small" | Size-K heap → `O(M log K)`, beats full sort |
| "true O(N) average required" | Quickselect |
| "need the full sorted top-K order" | Sort or a heap you drain fully — quickselect only finds the Kth boundary, not order |

---

## Greedy

_Not yet written._ (Activity selection / interval scheduling, Huffman coding, jump
game, gas station; when greedy is provably optimal vs when DP is required.)

---

## Related Notes

- [Graphs](graphs.md) — Prim's and Dijkstra pick the next node with a PriorityQueue; MST is greedy
- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — heapsort; many greedy solutions sort first
- [Hashing & Strings](hashing-and-strings.md) — Top-K Frequent Elements builds on the frequency-map pattern before ranking by count

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#heaps--greedy)
