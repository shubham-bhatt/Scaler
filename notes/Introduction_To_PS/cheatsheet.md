---
title: Introduction to Problem Solving — Cheat Sheet
subject: Introduction_To_PS
type: cheatsheet
reviewed: 2026-10-04
covers: [problem-solving-and-complexity, arrays-basics, prefix-sum-and-subarrays]
---

# Introduction to Problem Solving — Cheat Sheet

> Last-minute recall for the foundations: complexity, arrays, prefix sum, subarrays,
> sliding window. `##` = subbucket, `###` = topic. Full explanations are in the sibling notes.

## Problem Solving & Complexity
Note: [problem-solving-and-complexity](problem-solving-and-complexity.md)

### Counting Iterations & Math
- TC = O(**#iterations**) (wall-clock time depends on hardware/language — useless).
- Range `[a,b]` has `b-a+1` ints. Sequential loops **add**, nested **multiply**.
- AP: `1+…+N = N(N+1)/2`; `S = n/2·(first+last)`. GP: `S = a(rⁿ−1)/(r−1)`, r≠1.

### Factors & Primes (√N)
- Factors pair `(a, N/a)` ⇒ loop `a*a <= N`; perfect square counts **once**. Prime ⇔ exactly 2 factors (1 isn't prime).
```java
for (long a = 1; a * a <= N; a++) if (N % a == 0) f += (a == N / a) ? 1 : 2;
```
- 10¹⁸ brute = 317 years; √N = 10 s.

### Logarithms & Loops
- `log₂N` = times to halve N to 1 = `floor(log₂N)`. `i*=2` / `i/=2` loops → **O(log N)**.
- `i*2` from `i=0` → infinite loop. `for i<N { for j*=2 }` → N log N. Inner `j<=i` → N². Inner `j<=2^i` → 2^N.

### Big O & Constraints
- Drop lower-order terms and constants. Order: `1<log N<√N<N<N log N<N²<N³<2^N`.
- Budget ≈ **10⁷–10⁸ iterations/sec**. N≤10⁶→O(N); 10⁵→O(N log N); 10³–10⁴→O(N²); 10²→O(N³); 20→O(2^N).
- Big O hides constants: `1000N²` and `N²/10` are the same class.

### Space Complexity
- Extra space beyond input/output. `int`=4B, `long`=8B. Fixed vars O(1); `new int[N]` O(N). In-place ⇒ O(1).

## Arrays Basics
Note: [arrays-basics](arrays-basics.md)

### Array Fundamentals
- Contiguous memory; `addr(A[i]) = base + i·size` ⇒ **O(1)** access. Indices `0…N-1`.
- Max: start `ans = A[0]` (not 0). Dynamic arrays: `ArrayList` / `vector` / `list`.

### Pair Sum (Brute Force)
- `a+b == b+a` ⇒ only `j < i` → N(N-1)/2 pairs → O(N²), O(1) space. Better: HashSet O(N).

### Reverse Array
```java
for (int i = L, j = R; i < j; i++, j--) { int t = A[i]; A[i] = A[j]; A[j] = t; }
```
- O(N) time, O(1) space (extra-array version is O(N) space).

### Rotate Array
- Right-rotate by K: `K %= N`, then reverse(all), reverse(0,K-1), reverse(K,N-1) → O(N)/O(1).
- Naïve K single-shifts = O(K·N). Left-rotate by K = right-rotate by `N-K`.

## Prefix Sum, Subarrays & Sliding Window
Note: [prefix-sum-and-subarrays](prefix-sum-and-subarrays.md)

### Prefix Sum
- `P[i]=P[i-1]+A[i]`; `sum(L,R) = L==0 ? P[R] : P[R]-P[L-1]`. Build O(N), query O(1), total O(N+Q).
- In place: `A[i] += A[i-1]` → O(1) space. Use `long`. Tip: `P` of size N+1 removes the `L==0` branch.

### Even/Odd Prefix & Special Index
- `PE`/`PO` = prefix over even/odd indices only. Removing index `i` **flips parity of the suffix**:
```java
so = PO[i-1] + PE[N-1] - PE[i];   se = PE[i-1] + PO[N-1] - PO[i];   // i==0 → drop the [i-1] terms
```

### Carry Forward
- Calculate **and** use in one pass → O(N) time, O(1) space.
- Pairs `(i<j) A[i]='a', A[j]='g'`: scan right→left, `g → cnt++`, `a → ans += cnt` (use `long`).
- Smallest subarray with both min & max: track `idxMin`, `idxMax`; at each hit `ans = min(ans, i - otherIdx + 1)`.

### Subarrays
- Count = **N(N+1)/2**; starting at index s = `N-s`; of fixed length K = `N-K+1`.
- All subarray sums: brute O(N³) → prefix/carry-forward **O(N²)** (`sum += A[j]` inside `j` loop).

### Contribution Technique
- `Σ A[i]·(i+1)·(N−i)` = sum of all subarray sums → **O(N)**. Cast to `long` **before** multiplying.

### Fixed-Size Sliding Window
```java
for (int i = K; i < N; i++) { sum += A[i]; sum -= A[i - K]; ans = Math.max(ans, sum); }
```
- Build first window first. O(N) time, O(1) space. Brute force peaks at O(N²) when K≈N/2.
- **Gotcha:** `ans = 0` fails on all-negative arrays; use `MIN_VALUE`/first window.
