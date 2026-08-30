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


Yes. At this point, I would **not keep the full solutions** in your interview notes. Your mistakes are useful for learning, but the final notes should become a **compact “recall sheet”**: patterns, traps, Java rules, and questions to ask yourself.

Based on the problems you've shown me so far, I'd keep this:

# 🧠 DSA Interview — Quick Recall Notes

## 1. Before Coding — 30-second checklist

```text
1. What exactly am I counting?
   → indices OR distinct values?

2. Is the array sorted / can I sort it?
   → Two pointers / binary search?

3. Can I reduce the mathematical condition?
   |x-y| → after ordering → y-x

4. What happens with duplicates?

5. What is my pointer invariant?
   → e.g. left < right

6. For every pointer movement:
   → Does it move toward the required answer?

7. Edge cases?
   → empty / 1 element / duplicates / 0 / boundaries
```

---

# 2. Two Pointers — Sorted Array

### Core rule

For:

```text
A[left] <= A[right]
```

and:

```text
sum = A[left] + A[right]
```

```text
sum < target → left++
sum > target → right--
sum == target → process pair
```

### Difference problems

For:

```text
|x-y| = B
```

after sorting:

```text
x <= y

→ y-x = B
```

Then:

```text
diff < B → right++
diff > B → left++
diff == B → answer + 1
```

### Mental model

> **Move the pointer that moves the current value toward the target.**

---

# 3. Duplicate Handling — VERY IMPORTANT

First ask:

### Are they asking for index pairs or distinct value pairs?

#### Index pairs

Example:

```text
[1,1,4,4]
```

Pair `{1,4}` has:

```text
2 × 2 = 4
```

possible index pairs.

So:

```text
frequencyLeft × frequencyRight
```

may be required.

---

#### Distinct value pairs

Same array:

```text
[1,1,4,4]
```

Only:

```text
{1,4}
```

→ answer `1`.

So:

> **Don't multiply frequencies when the question asks for distinct pairs.**

---

### When distinct values are required

After finding:

```text
{x,y}
```

count it once and:

```text
skip all duplicates of x
skip all duplicates of y
```

Pattern:

```text
Find pair
   ↓
ans++
   ↓
skip duplicate block
   ↓
continue
```

---

# 4. `B == 0` / Special Cases

When:

```text
|x-y| = 0
```

then:

```text
x == y
```

So the problem becomes:

> Count distinct values occurring at least twice.

### General lesson

> If a parameter creates a fundamentally different mathematical condition, handle it separately rather than forcing the general algorithm.

---

# 5. Pointer Invariant

Always know what must remain true.

Example:

```text
left < right
```

If you do:

```text
left++;
```

and now:

```text
left == right
```

you may need:

```text
right++;
```

### Interview habit

Before moving a pointer, ask:

> **What invariant must remain true after this movement?**

This prevents many two-pointer bugs.

---

# 6. Don't Skip Unexplored Candidates

A mistake you made:

```java
left = right;
```

after finding a pair.

Danger:

```text
[1,2,3,4]
B = 2

{1,3}
{2,4}
```

If you jump:

```text
left = right
```

you can skip `{2,4}`.

### Rule

> **Never move pointers farther than necessary unless you can prove the skipped elements cannot produce an answer.**

This is one of the most important two-pointer lessons.

---

# 7. Duplicate Block Pattern

Sorted array:

```text
1 1 1 2 2 2 3 3
↑ ↑ ↑
```

When only the value matters:

```text
process 1 once
skip all 1s

process 2 once
skip all 2s
```

### Key insight

> **Sorting turns duplicate handling into contiguous blocks.**

This is one of the major reasons sorting can simplify a problem.

---

# 8. Number Systems — Excel Column

Excel:

```text
A = 1
B = 2
...
Z = 26
AA = 27
```

This looks like base-26 but **there is no zero digit**.

Therefore:

```java
digit = (N - 1) % 26;
N = (N - 1) / 26;
```

### Remember

```text
% base → extract rightmost digit
/ base → remove rightmost digit
```

Digits are discovered:

```text
RIGHT → LEFT
```

so:

```text
prepend
```

or:

```text
build → reverse
```

### Boundary tests

Always test:

```text
1   → A
26  → Z
27  → AA
52  → AZ
53  → BA
702 → ZZ
703 → AAA
```

---

# 9. Java — `Integer` vs `int`

`ArrayList<Integer>` contains:

```text
Integer
```

not:

```text
int
```

### Safe comparison

```java
a.equals(b)
```

for:

```text
Integer vs Integer
```

But:

```java
Integer vs int
```

can use:

```java
==
```

because Java unboxes the `Integer`.

### Easy rule

> **Integer ↔ Integer → `.equals()`**

---

# 10. Java — Integer Overflow

Very important in DSA.

This can overflow:

```java
int result = a * b;
```

even if you later do:

```java
result % MOD
```

Because multiplication happens **before** modulo.

Use:

```java
long result = (long) a * b;
```

### Modulo pattern

```java
long ans = 0;
ans = (ans + (long)a * b) % MOD;
```

### Remember

> **Cast before multiplication, not after.**

Bad:

```java
(long)(a * b)
```

Good:

```java
(long)a * b
```

---

# 11. Java — Sorting Comparator

Avoid:

```java
(a, b) -> a - b
```

because subtraction can overflow.

Prefer:

```java
Integer::compare
```

or:

```java
(a, b) -> Integer.compare(a, b)
```

For your Scaler constraints, subtraction may often work, but **interview-quality Java = use the safe comparator**.

---

# 12. Don't Create a Data Structure Unnecessarily

Excel solution:

You created:

```java
ArrayList<Character>
```

for:

```text
A B C ... Z
```

But Java can directly calculate:

```java
(char)('A' + k)
```

### General lesson

Before creating:

```text
HashMap
ArrayList
Set
etc.
```

ask:

> **Can arithmetic / indexing / primitive types solve this directly?**

---

# 13. String Building

Avoid repeated:

```java
ans = character + ans;
```

inside large loops.

Because `String` is immutable.

Prefer:

```java
StringBuilder
```

Build:

```text
right → left
```

then:

```java
reverse()
```

---

# 14. Complexity — Always State It

After solving, immediately classify:

```text
Brute force:
O(N²)

HashSet:
O(N) time
O(N) space

Sort + Two Pointer:
O(N log N)
O(1) extra space* 
```

### Important

Sorting often changes the problem from:

```text
unordered → ordered
```

and allows:

```text
two pointers
duplicate grouping
binary search
```

---

# ⭐ The 10 Things I'd Read Right Before an Interview

If you want an **ultra-short version**, this is the one I'd actually memorize:

```text
DSA QUICK RECALL
────────────────────────────────

1. WHAT AM I COUNTING?
   Index pairs vs distinct value pairs.

2. SORTED ARRAY?
   Think Two Pointer / Binary Search.

3. TWO POINTER:
   Sum < target → left++
   Sum > target → right--
   Difference < target → right++
   Difference > target → left++

4. ABSOLUTE DIFFERENCE:
   Sort → x <= y → |x-y| becomes y-x.

5. DUPLICATES:
   Distinct values → count once + skip duplicate block.
   Index pairs → frequencies may need multiplication.

6. NEVER SKIP:
   Don't jump pointers unless skipped elements
   are proven impossible.

7. INVARIANT:
   Know what must always remain true
   (e.g. left < right).

8. SPECIAL CASE:
   B=0 often means x=y.
   Handle fundamentally different cases separately.

9. JAVA:
   Integer vs Integer → .equals()
   Cast BEFORE multiplication → (long)a * b
   Sort → Integer::compare
   String loop → StringBuilder

10. NUMBER CONVERSION:
    % base → extract digit
    / base → remove digit
    1-indexed systems → (N-1) adjustment
    Extracted digits → usually right → left.

11. ALWAYS TEST:
    boundaries + duplicates + 0 + smallest/largest.

12. ALWAYS KNOW:
    Time complexity + space complexity.
```

### And one meta-rule from all your mistakes

> **Don't just ask “Does this code work?”**
>
> Ask **“Why is this pointer allowed to move here, and what possibilities am I eliminating by moving it?”**

That question will make your two-pointer solutions much more reliable.

----
Here are punchy, interview-ready notes you can save for your DSA preparation.
## 📝 Core Rule: Pairs = Combinations, Not Permutations

* Combinations ($nC2$): Used 90% of the time in DSA because the pair (A, B) is the same as (B, A).
* Permutations ($nP2$): Only used if the problem treats (A, B) and (B, A) as two completely different results.

------------------------------
## 🧮 The 3 Core Pair Formulas
Let $n$ be the frequency of an element in your array.

* Unique Index Pairs ($i < j$)
* Formula: $\frac{n \times (n - 1)}{2}$
   * Result for 5 items: 10
   * Use case: Standard "Good Pairs" or subset matching where duplicates are not counted twice.
* Distinct Index Pairs ($i \neq j$)
* Formula: $n \times (n - 1)$ (This is your $nP2$)
   * Result for 5 items: 20
   * Use case: When order matters or direction matters (e.g., "From city $i$ to city $j$").
* Self-Pairing Allowed (Any $i, j$)
* Formula: $n^2$
   * Result for 5 items: 25
   * Use case: When an element can form a pair with itself (e.g., coordinate grids, combinations with replacement).

------------------------------
## ⚡ Optimization Trick: Code Implementation
Never use nested loops ($O(N^2)$) to count pairs. Use a frequency map ($O(N)$).
## Approach A: Calculate at the End (Batch)

* Count frequencies of all numbers using a HashMap.
* Loop through the map values and apply $\frac{n \times (n - 1)}{2}$ to each.

## Approach B: Calculate On-The-Fly (Running Total)

* As you iterate through the array, add the current frequency to your total answer before incrementing the count.
* Why it works: If you have seen three 1s so far, the fourth 1 you just found can form exactly 3 new pairs with the previous ones.

// On-the-fly approach for i < j pairsint pairs = 0;
Map<Integer, Integer> countMap = new HashMap<>();
for (int num : nums) {
    // 1. Add existing count to pairs (it pairs with all previous identical numbers)
    pairs += countMap.getOrDefault(num, 0);
    // 2. Update the count map
    countMap.put(num, countMap.getOrDefault(num, 0) + 1);
}

Would you like to test these formulas on a specific LeetCode problem like Number of Good Pairs (LC 1512) or Subarray Sum Equals K (LC 560)?

---
## 📌 Java Quick Revision Cheat Sheet## 1. Comparing Wrapper Objects (Integer)

* Rule: Never use == to compare Integer objects from collections.
* Why: == compares memory addresses. Values ≥ 128 create separate objects in memory.
* Fix: Always use .equals().

// ❌ Fails for values >= 128if (list.get(i) == list.get(j)) 
// ✅ Works for all valuesif (list.get(i).equals(list.get(j))) 

## 2. Preventing Integer Overflow

* Rule: Cast the first variable to a long before the math happens.
* Why: Wrapping the multiplication in brackets (long)(a * b) evaluates the 32-bit math first, causing it to overflow before the conversion.

// ❌ Overflow happens inside brackets firstlong bad = (long)(n * (n - 1)); 
// ✅ Math is safely upgraded to 64-bit immediatelylong good = (long)n * (n - 1); 

## 3. Data Type Capacities

* int (32-bit): Max value ≈ 2.14 Billion (2 × 10⁹).
* long (64-bit): Max value ≈ 9.22 Quintillion (9 × 10¹⁸).
* Note: For n = 100,000, n × (n - 1) is 10 Billion, which overflows an int but safely fits in a long.

Would you like to add any other core patterns to this sheet, such as modular arithmetic rules or common sliding window pitfalls?

---

