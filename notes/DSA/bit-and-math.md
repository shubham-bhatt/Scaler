---
title: Bit Manipulation & Math
subject: DSA
type: subbucket
tags: [math, bit-manipulation, primes, sieve, factorization, number-theory, gcd, lcm]
topics: [Bit Manipulation, Number Theory, Math Tricks]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-09
source: lecture notes
related: [arrays-searching-sorting, recursion-and-backtracking]
status: partial
---

# Bit Manipulation & Math

> The quantitative toolkit: bit tricks for O(1) set operations and parity, and
> number theory (primes, factorization, GCD/LCM) that underpins divisor, modular,
> and combinatorics problems. Cheap to learn, frequently the "clever" part of a
> question.

**Topics:** Bit Manipulation · [Number Theory](#number-theory) · Math Tricks

---

## Bit Manipulation

_Not yet written._ (AND/OR/XOR/shifts, `n & (n-1)` clears lowest set bit, count
set bits, XOR to find the single/missing number, bitmask DP, power-of-two check.)

---

## Number Theory

Prime numbers are foundational: the Sieve of Eratosthenes finds all primes up to N
fast, and prime factorization decomposes any number into its prime building blocks.
These underpin GCD, LCM, divisor counting, and modular arithmetic.

### Prime Numbers & Sieve of Eratosthenes

> A **prime** is an integer > 1 whose only divisors are 1 and itself. **Trial
> division** checks divisibility up to √N — O(√N) per number. The **Sieve** marks
> all composites up to N in one pass, leaving primes unmarked — far faster in bulk.

#### Sieve of Eratosthenes — O(N log log N)

Start marking from `i * i` (smaller multiples already marked by smaller primes):

```java
boolean[] isComposite = new boolean[N + 1];
for (int i = 2; (long) i * i <= N; i++) {
    if (!isComposite[i]) {
        for (int j = i * i; j <= N; j += i) {
            isComposite[j] = true;
        }
    }
}
// isComposite[i] == false (for i >= 2) ⇒ i is prime
```

**Why start from `i*i`?** Every composite < i² has a prime factor smaller than i,
so it was already marked. (When i=5: 10,15,20 already marked by 2/3; 25 is first.)

**Why stop the outer loop at `i*i <= N`?** Any composite N = a·b has a ≤ √N. Once
i > √N, all composites are already marked by their smaller factor.

#### Prime Factorization — O(√N)

```java
List<int[]> primeFactors(int n) {
    List<int[]> factors = new ArrayList<>();
    for (int i = 2; (long) i * i <= n; i++) {
        int count = 0;
        while (n % i == 0) { count++; n /= i; }
        if (count > 0) factors.add(new int[]{i, count});  // {prime, exponent}
    }
    if (n > 1) factors.add(new int[]{n, 1});  // remaining prime factor > √(original)
    return factors;
}
```

**Why `n > 1` at the end?** A number has at most one prime factor greater than its
square root; if anything remains after dividing out factors up to √N, it's prime.
Space O(log N) — at most log₂(N) prime factors.

#### Smallest Prime Factor (SPF) Sieve

Precompute SPF for O(log x) factorization of many queries:

```java
int[] spf = new int[N + 1];
for (int i = 0; i <= N; i++) spf[i] = i;
for (int i = 2; (long) i * i <= N; i++)
    if (spf[i] == i)                       // i is prime
        for (int j = i * i; j <= N; j += i)
            if (spf[j] == j) spf[j] = i;
```

#### Sieve for Divisor Count (1..N) — O(N log N)

```java
int[] divisorCount = new int[N + 1];
for (int i = 1; i <= N; i++)
    for (int j = i; j <= N; j += i)
        divisorCount[j]++;   // i contributes to every multiple of i
```

#### GCD, LCM & Applications

| Application            | Formula / Method                                        |
|------------------------|---------------------------------------------------------|
| **GCD(a, b)**          | Euclidean: `gcd(a,b) = gcd(b, a % b)`, base `gcd(a,0)=a` |
| **LCM(a, b)**          | `a / gcd(a,b) * b` (divide first to avoid overflow)     |
| **Number of divisors** | N = p₁^a₁·p₂^a₂… ⇒ `(a₁+1)(a₂+1)…`                       |
| **Euler Totient φ(N)** | count coprime to N: `N · Π(1 - 1/p)` over prime factors |

```java
int gcd(int a, int b) { while (b != 0) { int t = b; b = a % b; a = t; } return a; }
long lcm(int a, int b) { return (long) a / gcd(a, b) * b; }
```

#### Examples

Sieve up to 30 → 2, 3, 5, 7, 11, 13, 17, 19, 23, 29.
`360 = 2³ · 3² · 5¹` → divisors = (3+1)(2+1)(1+1) = **24**.

#### Common Mistakes / Edge Cases

1. **Overflow with `i * i`** — cast to long: `(long) i * i <= N`.
2. **Treating 0 or 1 as prime** — the sieve starts at 2.
3. **Off-by-one sieve size** — array size N+1 to include index N.
4. **Using `if` instead of `while` in factorization** — must extract all powers.
5. **LCM overflow** — compute `a / gcd(a,b) * b`, not `a * b / gcd(a,b)`.

#### Interview Angle

**How interviewers test this:**
- "Print all primes up to N" → Sieve. "Is N prime?" → trial division O(√N).
- "Prime factorization" / "count divisors 1..N" → factorization / divisor sieve.
- "GCD of array", "coprime count" → Euclidean / Euler Totient.
- Follow-up: "many factorization queries" → SPF sieve; "modular exponentiation".

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem       | Think of This Pattern         |
|---------------------------------------|-------------------------------|
| "all primes up to N"                  | Sieve of Eratosthenes         |
| "is N prime?"                         | Trial division O(√N)          |
| "prime factors of N"                  | Factorization loop to √N      |
| "GCD", "coprime", "relatively prime"  | Euclidean algorithm           |
| "LCM of array"                        | Iterative LCM using GCD       |
| "count/sum of divisors"               | Sieve variant or factorization|
| "many queries on different numbers"   | Precompute SPF sieve          |

---

## Math Tricks

_Not yet written._ (Modular arithmetic & fast exponentiation, combinatorics
nCr with modular inverse, fast power, overflow-safe multiplication, base conversion.)

---

## Related Notes

- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — both rely on structured O(N log N) iteration
- [Recursion & Backtracking](recursion-and-backtracking.md) — GCD and fast exponentiation are naturally recursive

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#bit-manipulation--math)
