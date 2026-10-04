---
title: Arrays Basics
subject: Introduction_To_PS
type: subbucket
tags: [arrays, reverse, rotation, pair-sum, brute-force, dynamic-array, two-pointers]
topics: [Array Fundamentals, Pair Sum (Brute Force), Reverse Array, Rotate Array]
difficulty: easy
frequency: high
created: 2026-10-04
updated: 2026-10-04
reviewed: 2026-10-04
source: Introduction to Arrays, pre-lec-notes.txt — see sources/
related: [problem-solving-and-complexity, prefix-sum-and-subarrays]
status: complete
---

# Arrays Basics

> What an array actually is in memory, why indexing is O(1), and the first four
> classic array tasks: max element, pair-with-sum brute force, in-place reverse,
> and rotation-by-reversal. These are the building blocks for prefix sums,
> two pointers, and sliding window.

**Topics:** [Array Fundamentals](#array-fundamentals) · [Pair Sum (Brute Force)](#pair-sum-brute-force) · [Reverse Array](#reverse-array) · [Rotate Array](#rotate-array)

---

## Array Fundamentals

> Ordered, same-type elements in **consecutive memory** → address arithmetic gives O(1) access.

### Core Concept
An **array** is an ordered set of similar data items stored in **consecutive memory locations** in RAM. All elements share
one data type and are accessed through one name plus an index. Indices run `0 … N-1` (first = 0, last = N-1).

**Why O(1) access?** The array starts at a base address; every element takes the same `element_size` bytes, laid out one
after another. So
```
address(A[i]) = base + i * element_size
```
— one multiply and one add, independent of N. Example: `int` (4 B) array at 2100 → `A[1]` at 2104, `A[2]` at 2108; a 6-element array ends at 2100 + 24 = 2124.

### Details / Walkthrough
```java
// Max element — SC O(1), TC O(N)
static int maxElement(int[] A) {
    int ans = A[0];                       // start from a real element (not 0 — breaks for all-negative arrays)
    for (int i = 1; i < A.length; i++)
        if (A[i] > ans) ans = A[i];
    return ans;
}
```
**Dynamic arrays** (no fixed size, grow automatically) — study the one for your language:
Java `ArrayList` · C++ `vector` · Python `list` · JS `Array`. Java detail: `ArrayList<Integer>` stores objects, so values are
**autoboxed/unboxed** (`int ↔ Integer`).

### Common Mistakes / Edge Cases
- Initialising `ans = 0` for max → wrong when every element is negative; use `A[0]` or `Integer.MIN_VALUE`.
- Off-by-one: last index is `N-1`, not `N`.
- Mixing up array length (`A.length`, a field) with `ArrayList.size()` (a method).

### Interview Angle
**How interviewers test this:** "Why is array access O(1)?" (address formula), array vs linked list, array vs `ArrayList` (resizing cost — amortised O(1) append).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Random access by index" | Array — O(1) |
| Size unknown / grows | Dynamic array (`ArrayList`) |
| One pass, track best so far | Running variable, O(1) space |

---

## Pair Sum (Brute Force)

> Does any pair `(i, j)`, `i != j`, satisfy `A[i] + A[j] == K`? Try pairs — but only **half** the grid is needed.

### Core Concept
Brute force = try all possibilities. Naïvely that's every `(i, j)` in an N×N grid, checking `i != j`.
But `x + y == y + x`, so `(i, j)` and `(j, i)` are the same pair — iterate only the triangle **`i > j`** (or `i < j`).

### Details / Walkthrough
Grid for `A = [2, -6, 8, 3]`: the diagonal `(i,i)` is invalid (`i == j`), and the upper triangle mirrors the lower. Keep `j < i`:

| i | j range | iterations |
|---|---|---|
| 1 | 0…0 | 1 |
| 2 | 0…1 | 2 |
| 3 | 0…2 | 3 |
| N-1 | 0…N-2 | N-1 |

Total = `1 + 2 + … + (N-1) = N(N-1)/2` → **O(N²)**, SC O(1). (Naïve full grid is also O(N²) — same class, ~2× the work.)

### Examples
```java
static boolean pairSumExists(int[] A, int K) {
    for (int i = 1; i < A.length; i++)
        for (int j = 0; j < i; j++)          // j < i ⇒ automatically i != j
            if (A[i] + A[j] == K) return true;
    return false;
}
```
Cases: `[9,1,3,5,9], K=12 → true` (9+3); `[3,5,2,7,3], K=6 → true` (A[0]+A[4], two **different indices** with equal values is fine); `[4,2,7], K=8 → false`.

### Common Mistakes / Edge Cases
- Forgetting `i != j` → counts an element with itself (`K=8`, `A=[4]` wrongly true).
- Equal *values* at different indices are a valid pair — the condition is on **indices**.
- Using `==` on `Integer` objects instead of `.equals()` (Java boxing).

### Interview Angle
**How interviewers test this:** this is Two-Sum. Brute force O(N²) → follow-up "O(N)?" → hash set of seen values, or sort + two pointers.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Pair with sum K", N ≤ 10³ | Nested loop, `j < i` |
| "Pair with sum K", N ≤ 10⁵ | HashSet (see [Hashing](../DSA/hashing-and-strings.md)) or sorted two pointers (see [Two Pointers](../DSA/two-pointers-sliding-window.md#two-pointers)) |

---

## Reverse Array

> Swap the first with the last, second with second-last … stop at the middle. O(N) time, **O(1) space**.

### Core Concept
Elements at `i` and `N-1-i` swap. Only the first **half** needs to be visited — if you go all the way you swap everything back.

### Details / Walkthrough
For `[1 2 3 4 5 6 7 8]`: swaps (1,8) (2,7) (3,6) (4,5), then the pointers cross → stop. N/2 swaps → O(N).

Two equivalent forms:
1. Index form — `for (i = 0; i <= (N-1)/2; i++) swap(A[i], A[N-1-i])`.
2. **Two-pointer form** — `i = 0, j = N-1; while (i < j) { swap; i++; j--; }` (preferred).

Generalises to **reverse a range `[L, R]`** — just start `i = L`, `j = R`.

The naïve alternative uses an extra array `B` (`B[i] = A[N-1-i]`, copy back) → **SC O(N)**; the swap version is O(1).

### Examples
```java
static void reverse(int[] A, int L, int R) {
    int i = L, j = R;
    while (i < j) {
        int t = A[i];
        A[i] = A[j];
        A[j] = t;
        i++;
        j--;
    }
}
// reverse whole array: reverse(A, 0, A.length - 1);
```
Range example: `A=[1 2 3 4 5 6 7 8]`, `L=2, R=6` → `[1 2 7 6 5 4 3 8]`.

### Common Mistakes / Edge Cases
- Looping `i` to `N` (not `N/2`) un-does the reversal.
- Odd length: the middle element stays — `while (i < j)` handles it (it stops when `i == j`).
- Forgetting the temp variable → both slots end with the same value.

### Interview Angle
**How interviewers test this:** reverse array/string/word-order in place; reverse a subarray as a building block (rotation, next-permutation).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "In-place", "reverse" | Two pointers swapping inward |
| Palindrome check | Same converging pointers, compare instead of swap |

---

## Rotate Array

> Rotate right by K = **reverse all → reverse first K → reverse remaining N-K**. O(N) time, O(1) space.

### Core Concept
Rotating an array "K times" by shifting each element one place right (the last element wraps to the front):
`[1 2 3 4 5]` → K=1: `[5 1 2 3 4]` → K=2: `[4 5 1 2 3]`.

### Details / Walkthrough
**Approach 1 — repeat one-step rotation K times:** each rotation saves `A[N-1]`, shifts everything right, puts the saved value at `A[0]` → **O(K·N)**, SC O(1).

**Approach 2 — three reversals (optimal)**, `K = 3`, `A = [1 2 3 4 5 6 7 8]`:
1. Reverse the whole array → `[8 7 6 5 4 3 2 1]`
2. Reverse the first K elements `[0, K-1]` → `[6 7 8 5 4 3 2 1]`
3. Reverse the rest `[K, N-1]` → `[6 7 8 1 2 3 4 5]` ✔

Three O(N) passes → **O(3N) = O(N)**, SC O(1).

**K ≥ N:** rotating by N returns the original array, so rotation is periodic → use **`K = K % N`**. (e.g. N=4: K=5 ≡ 1, K=10 ≡ 2.)

### Examples
```java
static void rotateRight(int[] A, int K) {
    int N = A.length;
    K = K % N;                       // handle K >= N (and avoid reversing past bounds)
    reverse(A, 0, N - 1);            // reverse() from the Reverse Array topic
    reverse(A, 0, K - 1);
    reverse(A, K, N - 1);
}
```
```java
// Approach 1 — O(K*N), kept for comparison
for (int step = 0; step < K; step++) {
    int t = A[N - 1];
    for (int i = N - 1; i >= 1; i--) A[i] = A[i - 1];
    A[0] = t;
}
```

### Common Mistakes / Edge Cases
- Skipping `K = K % N` → `reverse(A, 0, K-1)` indexes out of range, or does needless work for huge K.
- Rotation **direction**: the lecture's picture shifts elements to the **right** (last → front). For a *left* rotation by K, use `K' = N - K` (or reverse the first K, the last N-K, then the whole).
- `N == 0` → `K % N` divides by zero; guard it.

### Interview Angle
**How interviewers test this:** "rotate array by K in place" (LeetCode 189) → expects the O(N)/O(1) three-reversal trick; follow-up on negative or huge K.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Rotate by K", in place, O(1) space | Reverse whole → reverse [0,K-1] → reverse [K,N-1] |
| K may exceed N | `K %= N` |
| Cyclic / circular shift | Modulo arithmetic |

---

## Related Notes

- [Problem Solving & Complexity](problem-solving-and-complexity.md) — TC/SC counting used for every solution here (N(N-1)/2 AP sum, O(K·N) vs O(N))
- [Prefix Sum, Subarrays & Sliding Window](prefix-sum-and-subarrays.md) — builds directly on arrays and range reasoning
- [DSA: Two Pointers & Sliding Window](../DSA/two-pointers-sliding-window.md) — the `i<j` swap loop is the simplest two-pointer pattern
- [DSA: Arrays, Searching & Sorting](../DSA/arrays-searching-sorting.md) — continues from basics into sorting & binary search
- [DSA: Hashing & Strings](../DSA/hashing-and-strings.md) — O(N) alternative to the O(N²) pair-sum brute force

## Cheat Sheet

→ [Introduction_To_PS Cheat Sheet](cheatsheet.md#arrays-basics)
