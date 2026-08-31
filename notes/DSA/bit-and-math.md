---
title: Bit Manipulation & Math
subject: DSA
type: subbucket
tags: [math, bit-manipulation, primes, sieve, factorization, number-theory, gcd, lcm]
topics: [Bit Manipulation, Number Theory, Math Tricks]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-31
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

> Not every "convert number to X" problem is standard base conversion — some
> numeral systems (like spreadsheet column names) have no zero digit, which
> changes the extraction formula. _(Modular arithmetic & fast exponentiation,
> nCr with modular inverse, are the next layer to add here.)_

### Core Concept

Standard base-B conversion extracts digits via `n % B` then `n /= B`, with
digits ranging `0..B-1`. **Bijective base-26** (Excel column titles: A=1 … Z=26,
then AA=27) has no zero digit — digits range `1..26`. Naively applying `n % 26`
breaks exactly at multiples of 26 (it would emit a spurious digit-0). Fix: shift
by 1 before extracting, i.e. work with `(n-1) % 26` and `(n-1) / 26`.

### Details / Walkthrough — Excel Column Title

```java
String excelColumn(int n) {
    StringBuilder sb = new StringBuilder();
    while (n > 0) {
        int digit = (n - 1) % 26;         // 0..25, representing A..Z
        sb.append((char) ('A' + digit));  // arithmetic directly gives the letter
        n = (n - 1) / 26;
    }
    return sb.reverse().toString();
}
```
- `%26` extracts the rightmost digit; `/26` removes it — digits come out
  **right to left**, so build into a `StringBuilder` and `.reverse()` once at
  the end (prepending to a `String` per digit would be O(N) per step).
- The `-1` shift is exactly what makes `26` map to `'Z'` (digit 25) instead of
  wrapping to a phantom zero digit, and what makes `27` correctly roll over to `"AA"`.
- No need to pre-allocate an `ArrayList<Character>` of `A..Z` — `(char)('A' + digit)`
  computes the letter directly. General lesson: before reaching for a
  HashMap/ArrayList/Set, check whether arithmetic or direct indexing already
  gives the answer.

### Examples

| n | Trace | Result |
|---|---|---|
| 1 | `(0)%26=0`→'A', `(0)/26=0` stop | A |
| 26 | `(25)%26=25`→'Z', `(25)/26=0` stop | Z |
| 27 | `(26)%26=0`→'A', `(26)/26=1` → `(0)%26=0`→'A', stop | AA |
| 52 | `(51)%26=25`→'Z', `(51)/26=1` → `(0)%26=0`→'A', stop | AZ |
| 53 | `(52)%26=0`→'A', `(52)/26=2` → `(1)%26=1`→'B', stop | BA |
| 702 | (same pattern, two full 26-cycles) | ZZ |
| 703 | (rolls into a third digit) | AAA |

### Common Mistakes / Edge Cases

1. **Forgetting the `-1` shift** — plain `n % 26` / `n / 26` treats this as a
   normal base-26 system with a zero digit, which produces the wrong letter
   exactly at multiples of 26.
2. **Prepending to a `String` in the loop** (`ans = c + ans`) — O(N) per
   prepend, O(N²) total; append to a `StringBuilder` and `.reverse()` once instead.
3. **Allocating an unnecessary lookup structure** — `(char)('A' + digit)`
   replaces any `ArrayList<Character>`/array of letters you might otherwise build.
4. **Not hand-tracing the boundary values** — `n=1`, `n=26`, `n=27` are the
   three points where the `-1` shift actually changes behavior; trace them by hand.

### Interview Angle

**How interviewers test this:**
- Direct: "Excel Column Title" (LC 168) → bijective base-26 encode (number → string).
- Reverse direction: "Excel Column Number" (LC 171) → standard base-26 decode
  (string → number) — no `-1` shift needed there, since you're summing
  `value * 26^position`, not extracting digits from a count.
- Follow-up: "What if digits went 0-25 instead of 1-26?" — tests whether you
  understand *why* the shift exists, not just the memorized formula.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "spreadsheet column title/number" | Bijective base-26 (shift by 1 before `%`/`/`) |
| "convert number to base B" (standard) | Plain `% B`, `/ B`, digits `0..B-1` |
| "build a string digit by digit" | `StringBuilder` + `.reverse()`, never `String +=` in a loop |

---

## Related Notes

- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — both rely on structured O(N log N) iteration
- [Recursion & Backtracking](recursion-and-backtracking.md) — GCD and fast exponentiation are naturally recursive

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#bit-manipulation--math)
