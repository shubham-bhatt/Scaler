---
title: Problem Solving & Complexity
subject: Introduction_To_PS
type: subbucket
tags: [time-complexity, space-complexity, big-o, logarithms, iterations, factors, primes, constraints, tle]
topics: [Counting Iterations & Math Basics, Factors & Primes (√N), Logarithms & Loop Analysis, Big O & Constraints, Space Complexity]
difficulty: easy
frequency: high
created: 2026-10-04
updated: 2026-10-04
reviewed: 2026-10-04
source: Introduction to Problem Solving, Time Complexity (Apr), Time Complexity 1 (AP/GP) — see sources/
related: [arrays-basics, prefix-sum-and-subarrays]
status: complete
---

# Problem Solving & Complexity

> The "how do I judge an algorithm" toolkit that every later DSA topic leans on:
> count iterations, turn the count into Big O, and use the input constraints to
> decide which complexity you are allowed. Includes the AP/GP/log maths needed
> to do that counting, and the first optimisation story (factors in √N).

**Topics:** [Counting Iterations & Math Basics](#counting-iterations--math-basics) · [Factors & Primes (√N)](#factors--primes-n) · [Logarithms & Loop Analysis](#logarithms--loop-analysis) · [Big O & Constraints](#big-o--constraints) · [Space Complexity](#space-complexity)

---

## Counting Iterations & Math Basics

> Execution *time* is unreliable; the **number of iterations** is machine-independent — and counting it needs AP/GP sums.

### Core Concept
Two people run the same algorithm on different hardware/languages and get different wall-clock times
(Windows XP vs Mac M2, C++ vs Python, a hot CPU vs a cool one). So **execution time depends on many factors** and
can't be used to compare algorithms. **Number of iterations** depends only on the algorithm and the input, so
`TC = O(#iterations)`.

### Details / Walkthrough
**Range size.** `[a, b]` (inclusive both ends) has `b - a + 1` integers. `[2,5]`=2,3,4,5; `[2,5)`=2,3,4; `(2,5)`=3,4.
`[ ]` includes the boundary, `( )` excludes it.

**Arithmetic Progression (AP)** — constant difference `d`:
- n-th term: `a_n = a_1 + (n-1)d`
- Sum: `S_n = n/2 · [2a_1 + (n-1)d] = n/2 · (a_1 + a_n)`
- Special case: `1 + 2 + … + N = N(N+1)/2` (pair first with last: `(N+1)` repeated `N` times, then halve; for N=100 → 5050).

**Geometric Progression (GP)** — constant ratio `r`:
- n-th term: `a_n = a·r^(n-1)`
- Sum of n terms: `S = a(r^n − 1)/(r − 1)`, **valid for r ≠ 1** (if r = 1 the sum is just `n·a`).
- e.g. `2 + 2² + … + 2^N = 2(2^N − 1)/(2 − 1) = O(2^N)`.

### Examples
```java
// 1) iterations = N  (break at i == N, so it never runs past N)
for (int i = 1; i <= N; i++) { if (i == N) break; }

// 2) iterations = 101 → [0,100] has 100 - 0 + 1 elements
long s = 0;
for (int i = 0; i <= 100; i++) s += i + (long) i * i;

// 3) iterations = N + M  (two *separate* loops add; nested loops multiply)
for (int i = 1; i <= N; i++) if (i % 2 == 0) System.out.println(i);
for (int i = 1; i <= M; i++) if (i % 2 == 0) System.out.println(i);
```

### Common Mistakes / Edge Cases
- Using wall-clock time (or "it ran in 2 s on my laptop") as proof of efficiency.
- Off-by-one on range counts — it's `b − a + 1`, not `b − a`.
- GP formula with `r = 1` divides by zero.
- Sequential loops **add** (`N + M`), nested loops **multiply**.

### Interview Angle
**How interviewers test this:** "What's the time complexity?" on a snippet; summing a series inside nested loops (AP → N², GP → 2^N).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| Inner loop runs `i` times for outer `i` | AP sum → `N(N+1)/2` → O(N²) |
| Inner loop bound doubles each outer step (`2^i`) | GP sum → O(2^N) |
| Two independent loops one after another | Add: O(N + M) |

---

## Factors & Primes (√N)

> Factors come in pairs `(a, N/a)` — only scan up to √N. Takes 10^18 from **317 years to 10 seconds**.

### Core Concept
`y` is a **factor** of `x` if `x / y` is an integer (`x % y == 0`). Smallest factor = 1, largest = N.
A **prime** is a positive number with **exactly 2 factors** (1 and itself): 2, 11, 23, 31. (1 has only one factor, so it is *not* prime.)

### Details / Walkthrough
**Brute force** — try every `i` in `1..N`: **N iterations**.

If the machine does ~10^8 iterations/sec: `N = 10^9` → 10 s; `N = 10^18` → `10^10` s ≈ 2.7·10^6 hours ≈ 115,741 days ≈ **317 years**. Must optimise.

**Key observation:** factors pair up as `N = a · b` with `a ≤ b`. For 24: (1,24) (2,12) (3,8) (4,6). Since `a ≤ b`:
`a ≤ N/a  ⇒  a² ≤ N  ⇒  a ≤ √N`. So loop `a` from 1 to √N; each hit gives **two** factors `a` and `N/a` — except when `a == N/a` (perfect square, e.g. 4 = 2·2, 25 = 5·5), which gives only **one**.

### Examples
```java
static int countFactors(long N) {
    int factors = 0;
    for (long a = 1; a * a <= N; a++) {      // iterations = √N
        if (N % a == 0) {
            long b = N / a;
            if (a == b) factors += 1;        // perfect square → count once
            else        factors += 2;
        }
    }
    return factors;
}

static boolean isPrime(long N) {
    return N > 1 && countFactors(N) == 2;
}
```
`N = 10^18` → √N = 10^9 iterations ≈ 10 s. (`317 years → 10 sec`.)

### Common Mistakes / Edge Cases
- Writing the bound as `a <= Math.sqrt(N)` — floating point is slower and can be imprecise; prefer `a * a <= N`.
- `a * a` overflows `int` for N near 2^31 — use `long`.
- Forgetting the perfect-square case → factor count off by one (and primes mis-detected).
- `isPrime(1)` must be `false` (the lecture's `countFactors == 2` check handles it; a shortcut that tests only divisibility won't).

### Interview Angle
**How interviewers test this:** count factors → check prime → count primes up to N (leads to the Sieve) → sum of divisors.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "Factors / divisors / prime check", N up to 10^12–10^18 | Pair `(a, N/a)`, loop to √N |
| N is a perfect square | Count the middle factor once |
| Many primality queries / all primes ≤ N | Sieve of Eratosthenes → [Number Theory](../DSA/bit-and-math.md) |

---

## Logarithms & Loop Analysis

> A loop that **halves or doubles** its variable runs `log₂N` times. Learn to read nested loops as tables of iterations.

### Core Concept
`log_a(b) = c  ⇔  a^c = b`. So `log₂64 = 6` (2⁶=64), `log₃81 = 4`, and `log_a(a^x) = x`.

**Q: how many times must we divide N by 2 (integer division) to reach 1?**
`N → N/2 → N/2² → … → N/2^k = 1`, so `N = 2^k` and `k = log₂N`. For N not a power of two the answer is **`floor(log₂N)`**:
N=10 → 10,5,2,1 = **3** = floor(3.32); N=30 → 4; N=9 → 3; N=27 → 4.
(Integer division truncates: `5/2 = 2`. `int/int → int`.)

### Details / Walkthrough
Iteration counts for the standard loop shapes (this is the table to memorise):

| # | Loop | Iterations | TC |
|---|---|---|---|
| 1 | `while (i > 1) i = i/2` | log₂N | O(log N) |
| 2 | `for (i=1; i<N; i=i*2)` — i = 1,2,2²,…; stops when `2^k = N` | log₂N | O(log N) |
| 3 | `for (i=0; i<=N; i=i*2)` — `0*2 = 0`, i never changes | ∞ | **infinite loop** |
| 4 | `for i in 1..N { for j in 1..N }` — N per i, N times | N·N = N² | O(N²) |
| 5 | `for i in 1..N { for (j=1; j<=N; j=j*2) }` — N × log N | N log₂N | O(N log N) |
| 6 | `for i in 1..N { for j in 1..2^i }` — 2¹ + 2² + … + 2^N (GP, a=2, r=2) = 2(2^N − 1) | ≈ 2^(N+1) | O(2^N) |

Technique: write a small **table of `i` vs. how many times the inner loop runs**, then sum the last column (AP/GP).

### Examples
```java
// Loop 3: starts at 0 — multiplying 0 never moves it → infinite loop
for (int i = 0; i <= N; i = i * 2) { /* ... */ }   // fix: start at 1

// Loop 5: O(N log N)
for (int i = 1; i <= N; i++)
    for (int j = 1; j <= N; j = j * 2) { /* ... */ }
```

### Common Mistakes / Edge Cases
- Starting a multiplicative loop at **0** → infinite loop (loop 3).
- Saying the answer is `log₂N` instead of `floor(log₂N)` for non-powers of two.
- Treating `j <= 2^i` (loop 6) like a normal inner loop — it's a GP, giving 2^N.
- In Java, `i * 2` can overflow `int` near the limit → use `long` or a safe bound.

### Interview Angle
**How interviewers test this:** "what is the complexity of this nested loop?" with mixed `i++`, `i*=2`, and `j <= i` bounds.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| Variable halves/doubles each step | O(log N) |
| Linear outer × halving/doubling inner | O(N log N) |
| Inner bound is `i` | AP → O(N²) |
| Inner bound is `2^i` | GP → O(2^N) |
| Sorted input, "find quickly" | Binary search is O(log N) because it halves the range |

---

## Big O & Constraints

> Big O = *rate of growth* for **large** inputs. Keep the dominant term, drop constants, then use N's limit to pick an algorithm.

### Core Concept
Big O (**asymptotic analysis**) analyses performance **over large inputs** — the realistic case (e.g. a YouTube video's views ≈ 12 B).
A small-input comparison can mislead: Algo 1 = `100·log N`, Algo 2 = `N/10`; for N ≤ 3500 Algo 2 is smaller, for N > 3500 Algo 1 wins — and large N is what matters.

**Steps to compute Big O**
1. Count iterations as a function of the input.
2. Ignore lower-order terms.
3. Ignore constant coefficients.

```
100·log N        → O(log N)
N/10             → O(N)
4N² + 3N − log N → O(N²)
4N + 3N·log N + 1→ O(N log N)
4N·log N + 3N√N + 10⁶ → O(N√N)     (since log N < √N)
```
Growth order: `1 < log N < √N < N < N log N < N² < N³ < 2^N`.

**Why drop lower-order terms?** For `N² + 10N`: at N=10, `10N` is 50 % of the work; at N=100 it's ~9 %; at N=10^4 it's ~0.1 %; at 10^5 negligible. As N grows, its % contribution → 0.

**Why drop constants?** Big O describes the **rate of growth**; `y = x`, `y = 3x`, `y = 100x` are all linear (same shape of curve).

### Details / Walkthrough
**Limitations of Big O**
1. `1000·N²` vs `N²/10` → both O(N²) ("equally good" by Big O) yet one is 10 000× slower in practice.
2. `10⁶·N√N` vs `N²/10` → Big O says the first (O(N√N)) is better, but it only wins for *very* large N: at N=10², 10⁶·10²·10 = 10⁹ vs 10³; at N=10⁶ it's 10¹⁵ vs 10¹¹ — still worse! Big O is a tool for large inputs, not a precise stopwatch.

**TLE and the online judge.** Workflow: read → solve (get a working solution) → code → TLE → optimise → AC.
- Judge CPU ≈ 1 GHz ⇒ **~10⁹ instructions/sec**; allowed time is usually **1 s**.
- 1 iteration ≈ 10–100 instructions ⇒ **~10⁷–10⁸ iterations/sec** is the safe budget.

**Constraints → required complexity** (with N² as the example):

| Constraint | N² iterations | Verdict |
|---|---|---|
| N ≤ 10³ | 10⁶ | ✅ no TLE |
| N ≤ 10⁴ | 10⁸ | ⚠ may or may not pass |
| N ≤ 10⁵ | 10¹⁰ | ❌ TLE |

Use the constraints **before coding**: after developing the logic / pseudo-code, estimate TC and check it against N.

| N ≤ | Target complexity |
|---|---|
| 10⁶ | linear (O(N) / O(N log N)) |
| 10³ | O(N²) |
| 10² | O(N³) |
| 20 | even O(2^N) works |

### Examples
- Brute-force factor count at N = 10⁹ → 10⁹ iterations ≈ 10 s on 10⁸ iter/s → TLE → this is why [Factors & Primes](#factors--primes-n) uses √N.

### Common Mistakes / Edge Cases
- Optimising before having a **working** solution — get correctness first, then use TLE to guide optimisation.
- Treating Big O as exact: constants matter when two algorithms share the same class.
- Ignoring that a hidden loop (e.g. `String` concat, `list.remove(0)`, `contains` on a list) adds a factor of N.

### Interview Angle
**How interviewers test this:** "Can you do better?" after a brute force; "what are the constraints?" (a good candidate asks); compare two algorithms' complexity.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| N ≤ 10⁵ / 10⁶ | Need O(N) or O(N log N) — no nested brute force |
| N ≤ 10³ | O(N²) brute force is fine |
| N ≤ 20 | Exponential — subsets / backtracking |
| Multiple queries on the same data | Precompute once (prefix sum) → [Prefix Sum](prefix-sum-and-subarrays.md) |

---

## Space Complexity

> Extra memory the algorithm uses *beyond* the input and output, as a function of input size.

### Core Concept
`SC` = rate of growth of **extra** space w.r.t. input. Input and output are fixed by the problem, so only the **algorithm's own space** counts. Sizes: `int` = 4 B, `long` = 8 B.

### Details / Walkthrough
```java
// SC = O(1): x and y are fixed 12 bytes regardless of N
int  x = N;          // 4 B
long y = (long) x * x; // 8 B

// SC = O(N): fixed part + array of N ints
int[] arr = new int[10];   // 40 B
int x, y;                  // 8 B
long z;                    // 8 B
int[] a = new int[N];      // 4N B     → total (56 + 4N) bytes = O(N)
```
Constant bytes never matter — only whether memory **grows with N**. Plot: O(1) is a flat line over N, O(N) a rising line.

### Common Mistakes / Edge Cases
- Counting the input array itself as extra space — don't (e.g. max-of-array is O(1) space).
- Forgetting recursion stack depth is space too (depth d ⇒ O(d)).
- Reusing the input (e.g. turning `A` into its prefix-sum in place) drops SC from O(N) to O(1).

### Interview Angle
**How interviewers test this:** "Can you do it in O(1) extra space?" — in-place reversal, in-place prefix sum, carry-forward instead of an auxiliary array.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "In-place" / "O(1) extra space" | Swap with two pointers; overwrite input; carry a running variable |
| Auxiliary array computed then used once | "Calculate + use together" (carry forward) → [Carry Forward](prefix-sum-and-subarrays.md#carry-forward) |

---

## Related Notes

- [Arrays Basics](arrays-basics.md) — complexity of access, reverse, rotate, pair-sum brute force
- [Prefix Sum, Subarrays & Sliding Window](prefix-sum-and-subarrays.md) — the optimisations (O(N·Q) → O(N+Q), O(N³) → O(N)) these complexity tools justify
- [DSA: Bit Manipulation & Math](../DSA/bit-and-math.md) — Sieve/prime factorisation extend the √N idea
- [DSA: Arrays, Searching & Sorting](../DSA/arrays-searching-sorting.md) — O(log N) binary search and O(N log N) sorting use the loop analysis above

## Cheat Sheet

→ [Introduction_To_PS Cheat Sheet](cheatsheet.md#problem-solving--complexity)
