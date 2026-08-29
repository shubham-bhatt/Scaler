📌 Java Rule: 

Comparing Wrapper Objects (Integer)Never use == to compare Integer objects from an ArrayList.
== compares memory addresses, not the actual numbers.
Values ≥ 128 create separate objects in memory and will fail with ==.
Always use .equals() to safely compare the actual numeric values.

java// ❌ WRONG (Fails for values 128 and above)
if (list.get(i) == list.get(j)) 

//  CORRECT (Always works)
if (list.get(i).equals(list.get(j))) 

----
With \(n \approx 100,000\), the value of \(n \times (n-1)\) reaches approximately 10¹⁰, which safely fits within a 64-bit long (up to \(\approx 9 \times 10^{18}\)).Therefore, using the long casting method will work perfectly and will not overflow.🛠️ Final Verified Codejavaint n = end - st + 1;

// 1. Calculate combinations safely using long
long comb = (((long) n * (n - 1)) / 2) % mod;  (old -- int comb = ((n*(n-1))/2)%mod; overflow)

// 2. Add to ans and apply modulo
ans = (int) ((ans + comb) % mod);

📌 Java Rule: Cast Before MultiplyingCast the first variable to a long directly: (long)n * (n - 1).Never wrap the multiplication in brackets before casting: (long)(n * (n - 1)).Wrapping first allows the 32-bit integer overflow to happen before the type upgrades.java// ❌ WRONG (Overflows first, then converts bad value)
long bad = (long)(n * (n - 1)); 

//  CORRECT (Upgrades to 64-bit math immediately)
long good = (long)n * (n - 1); 


---
Qus: Given a sorted array of integers (not necessarily distinct) A and an integer B, find and return how many pair of integers ( A[i], A[j] ) such that i != j have sum equal to B.

Since the number of such pairs can be very large, return number of such pairs modulo (109 + 7).
THought process - bf - improve two pointer - we will have to take care of duplicate nC2 - move pointer perfectly 


---
## Q4 — Pairs with Given Difference

### 🧠 How to think

Given:

```text
|x - y| = B
```

For `B > 0`:

```text
y - x = B
→ y = x + B
```

So the real question becomes:

> **For every distinct `x`, does `x + B` exist?**

---

### 🔍 Identify what is being counted

**Distinct value pairs**, NOT index/occurrence pairs.

Example:

```text
A = [1, 1, 4, 4], B = 3

Only (1,4) → answer = 1
```

So duplicates must **not multiply the answer**.

---

### 💡 Choose approach

Ask:

> "Do I need to quickly check whether a value exists?"

Yes → **HashSet/HashMap**

```text
Build set
For each distinct x:
    if x + B exists:
        answer++
```

Complexity:

```text
Time  → O(N) average
Space → O(N)
```

---

### Alternative: Sort + Two Pointers

If using sorting:

```text
sort A

i = 0, j = 1

diff = A[j] - A[i]

diff < B  → j++
diff > B  → i++
diff = B  → answer++, skip duplicates
```

Complexity:

```text
O(N log N)
```

---

### ⚠️ Special case: `B = 0`

Now:

```text
|x-y| = 0
→ x = y
```

We need values occurring **at least twice**.

```text
[1,1,1,2,2]

(1,1) → 1
(2,2) → 1

answer = 2
```

So use frequency counting.

---

## ⭐ Interview thought process

Remember this sequence:

```text
1. Understand condition
       ↓
   |x-y| = B

2. Simplify
       ↓
   y = x+B

3. What am I counting?
       ↓
   DISTINCT value pairs

4. What do I need?
       ↓
   Check if x+B exists

5. Which pattern?
       ↓
   Hashing / Sorting + 2 pointers

6. Check special case
       ↓
   B = 0
```

### Key lesson from your attempt

**Don't start handling duplicates/combinations/boundaries immediately.**

First establish:

> **What exactly is ONE thing I am counting?**

Here: **one distinct `(x, x+B)` value pair**.

That single observation removes most of the complexity from your original solution.

