Absolutely. At this point, the goal should be **not to memorize solutions**, but to train yourself to recognize the pattern quickly.

# 🧠 DSA Recall Notes — Questions Done So Far

---

## Q1. Longest Subarray with Sum = 0

### What is asked?

Find the **longest continuous subarray whose sum is 0**.

### How do I think?

* Brute force all subarrays → `O(N²)` ❌
* Subarray + sum → think **Prefix Sum**
* Need to know whether the same prefix sum appeared before → **HashMap**

### Key insight

If:

```text
prefixSum[i] == prefixSum[j]
```

then:

```text
sum(i+1 ... j) = 0
```

Store the **first occurrence** of each prefix sum because we want the **longest** distance.

```java
map.put(0L, -1);
```

handles a zero-sum subarray starting from index `0`.

### 🔑 Recall

> **Same prefix sum twice → middle is zero.**
> **Longest → store FIRST index.**

### Java syntax

```java
Map<Long, Integer> map = new HashMap<>();
map.put(0L, -1);
```

---

# Q2. Custom HashMap — Chaining

### What is asked?

Implement your **own HashMap** supporting `put`, `get`, `remove`, `size`, including collision handling and rehashing.

### How do I think?

HashMap fundamentally needs:

```text
key → bucket → search
```

Collision means multiple keys land in the same bucket → use a **Linked List**.

```text
bucket[2]
   ↓
(key,value) → (key,value) → (key,value)
```

### Key insight

Hashing:

```java
index = Math.abs(key) % bucketCount;
```

For every operation:

> Find bucket → traverse linked list → find matching key.

Rehash:

```text
load factor > 2
       ↓
double buckets
       ↓
recalculate index for EVERY key
```

### 🔑 Recall

> **Custom HashMap = Array of buckets + Linked List for collisions.**
> **Rehash changes bucket count → every key needs a new index.**

### Java syntax worth remembering

Node:

```java
static class Node {
    int key, value;
    Node next;
}
```

Array of linked lists:

```java
Node[] buckets = new Node[4];
```

Remove node:

```java
previous.next = current.next;
```

---

# Q3. Group Anagrams

### What is asked?

Group strings that contain exactly the **same characters**, regardless of order.

Example:

```text
eat
tea
ate
```

→ one group.

### How do I think?

What makes two strings anagrams?

```text
eat → aet
tea → aet
ate → aet
```

Same sorted form → same group.

Therefore:

**String → canonical form → HashMap key**

### Key insight

Sort every word and use the sorted word as the key.

```text
"eat" → "aet" → HashMap["aet"]
"tea" → "aet" → same group
```

### 🔑 Recall

> **Anagram → sort characters → identical strings become same key.**
> **HashMap key = sorted string; value = list of original strings.**

### Java syntax

```java
char[] chars = word.toCharArray();
Arrays.sort(chars);
String key = new String(chars);
```

---

# Q4. Shaggy and Distances

### What is asked?

Find the **minimum distance between two equal elements**.

Example:

```text
[7, 1, 3, 4, 1, 7]
```

`1` occurs at `1` and `4` → distance `3`.

### How do I think?

Need to find duplicates.

Could compare every pair → `O(N²)` ❌

While traversing, remember where each value was last seen.

```text
value → last index
```

When we see it again:

```java
distance = i - lastIndex;
```

### Key insight

For minimum distance, we want the **closest previous occurrence**.

Therefore, always update the index:

```java
map.put(value, i);
```

### 🔑 Recall

> **Duplicate + minimum distance → HashMap value → LAST index.**
> **When duplicate found → `currentIndex - lastIndex`.**

### Important comparison

This is worth remembering:

| Problem                    | Store           |
| -------------------------- | --------------- |
| Longest zero-sum subarray  | **First index** |
| Minimum duplicate distance | **Last index**  |

---

# Q5. K Places Apart

### What is asked?

Sort an array where every element is at most **B positions away from its correct sorted position**.

### How do I think?

Normal sorting:

```text
O(N log N)
```

But we have special information:

> Correct position is at most `B` away.

So the smallest element that should come next must be among the next:

```text
B + 1 elements
```

We need repeatedly find the smallest → **Min Heap**.

### Algorithm

1. Put first `B + 1` elements into Min Heap.
2. Take minimum → next sorted element.
3. Add next array element.
4. Repeat.

### 🔑 Recall

> **K places apart → next smallest is within `B + 1` elements.**
> **Smallest repeatedly → Min Heap.**

### Java syntax

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

Smallest:

```java
minHeap.peek();
```

Remove smallest:

```java
minHeap.poll();
```

Add:

```java
minHeap.add(x);
```

### Complexity

```text
O(N log B)
```

---

# Q6. Meeting Rooms II

### What is asked?

Given meeting `[start, end]` intervals, find the **minimum number of rooms required** so that overlapping meetings don't share a room.

### How do I think?

The real question is:

> **How many meetings are happening at the same time?**

Need to track meetings that are currently running.

1. Sort meetings by **start time**.
2. Keep their **end times** in a Min Heap.
3. Earliest ending meeting is the first room that could become free.

### Key decision

For current meeting:

```java
if (minHeap.peek() <= start)
```

A room is free → reuse it.

Otherwise:

```text
all rooms are busy → need new room
```

### 🔑 Recall

> **Intervals + minimum rooms → sort by start + Min Heap of end times.**
> **Earliest end ≤ current start → reuse room; otherwise new room.**

### Java syntax

Sort:

```java
Collections.sort(B, (x, y) -> x.get(0) - y.get(0));
```

Min Heap:

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

---

# Q7. Minimum Window Substring

### What is asked?

Find the **smallest substring of A containing all characters of B**, including duplicates.

Example:

```text
A = "ADOBECODEBANC"
B = "ABC"

Answer = "BANC"
```

### How do I think?

We need a **continuous substring** → think **Sliding Window**.

We need to satisfy character requirements → **frequency count**.

So:

```text
Sliding Window + Frequency Array
```

### Algorithm thinking

```text
Expand right
    ↓
window becomes valid
    ↓
shrink left
    ↓
find smallest valid window
```

Maintain:

```java
required
```

= number of characters from `B` still missing.

When:

```java
required == 0
```

the current window is valid.

### 🔑 Recall

> **Minimum substring satisfying a condition → Sliding Window.**
> **Expand until valid → shrink while valid → keep smallest.**

### Java syntax

Frequency array:

```java
int[] freq = new int[128];
```

Character:

```java
char c = A.charAt(i);
```

Substring:

```java
A.substring(start, start + length);
```

---

# 🚨 Most Important: How to Choose the Algorithm

This is what I want you to train yourself to ask **before coding**.

### Step 1 — What is the problem actually asking?

Look for keywords:

| Question says...           | First thought                 |
| -------------------------- | ----------------------------- |
| Subarray + sum             | Prefix Sum                    |
| Longest/shortest subarray  | Prefix Sum / Sliding Window   |
| Duplicate / frequency      | HashMap                       |
| Same characters / anagram  | Frequency / Sorting + HashMap |
| Minimum/maximum repeatedly | Heap                          |
| Intervals / overlapping    | Sort + Heap                   |
| Continuous substring       | Sliding Window                |
| K places apart             | Heap                          |
| Need fast lookup           | HashMap / HashSet             |

---

# 🧠 Your Current Pattern Recognition Map

```text
                DSA Problem
                    |
          ┌─────────┴─────────┐
          ↓                   ↓
       Array/String         Intervals
          |                   |
     ┌────┼────┐              ↓
     ↓    ↓    ↓           Sort + Heap
   Sum  Duplicate
     |    |
 Prefix  HashMap
  Sum
```

For strings:

```text
String
  |
  ├── Anagram?
  │      ↓
  │   Sort / Frequency + HashMap
  │
  └── Substring + minimum/maximum?
         ↓
    Sliding Window
```

For "minimum/maximum" involving a changing set:

```text
Need smallest/largest repeatedly?
          ↓
       Heap
```

---

# ⭐ The Most Valuable Recall So Far

Don't memorize 7 solutions. Memorize these **7 triggers**:

```text
1. Same Prefix Sum       → Zero-sum subarray
2. Duplicate Distance    → HashMap + last index
3. Anagram               → Canonical form + HashMap
4. Collision             → HashMap + Linked List
5. K places apart        → Min Heap
6. Meeting overlap       → Sort + Min Heap
7. Minimum valid window  → Sliding Window
```

And one particularly important distinction:

```text
LONGEST  → preserve earliest/first information
MINIMUM  → keep latest/current information
```

That distinction will come up **again and again** in HashMap problems.
Yes. Here is a **compact checkpoint summary** of everything important so far. You can use this as the context for the next chat.

## DSA Progress — Knapsack + DP Recall Notes

### Q1. 0-1 Knapsack

**Asked:** Pick each item at most once, weight `<= C`, maximize value.

**Recognition:**

* Pick / skip
* Capacity constraint
* Cannot reuse item
  → **0-1 Knapsack DP**

**Formula:**

```text
skip = dp[c]
take = value + dp[c - weight]
dp[c] = max(skip, take)
```

**1D DP:**

```java
int[] dp = new int[C + 1];

for (each item) {
    for (int c = C; c >= weight; c--) {
        dp[c] = Math.max(dp[c], value + dp[c - weight]);
    }
}
```

**Critical:** Capacity goes **BACKWARD** because each item can be used only once.

**Complexity:** `O(N*C)` time, `O(C)` space.

---

### Q2. Unbounded Knapsack

**Asked:** Maximize value for **exact capacity `A`**, with unlimited copies of every item.

**Recognition:**

* Knapsack
* Item can be reused unlimited times
  → **Unbounded Knapsack DP**

**Formula:**

```text
dp[w] = max(dp[w], value + dp[w - weight])
```

**Critical difference from 0-1:**

```text
0-1        -> capacity BACKWARD
Unbounded  -> capacity FORWARD
```

Forward loop allows the same item to be used again:

```java
for (int w = weight; w <= A; w++) {
    dp[w] = Math.max(dp[w], value + dp[w - weight]);
}
```

**Complexity:** `O(N*A)` time, `O(A)` space.

---

### Q3. Fractional Knapsack

**Asked:** Maximize value with capacity `C`, but **items can be broken into fractions**.

**Recognition:**

> "Can break item" → **Greedy**, NOT DP.

Calculate:

```text
value / weight
```

Sort descending by this ratio.

Then:

1. Take full item if it fits.
2. Otherwise take required fraction and stop.

**Complexity:** `O(N log N)` due to sorting.

### Important precision lesson

Don't rely on:

```java
double ratio
Math.floor(totalValue * 100)
```

because floating-point precision can turn `255.0` into something like `254.999999...`.

Instead calculate the final answer using integer arithmetic:

```java
total += value * 100;
```

For fractional item:

```java
total += (value * remaining * 100) / weight;
```

### Ratio comparison without floating point

Instead of:

```text
value1 / weight1 > value2 / weight2
```

use cross multiplication:

```text
value1 * weight2 > value2 * weight1
```

Java:

```java
Integer.compare(
    A.get(j) * B.get(i),
    A.get(i) * B.get(j)
)
```

---

# ⭐ Most Important Pattern

When you see **Knapsack**, immediately ask:

```text
Can I break an item?
        |
   YES -> Fractional -> GREEDY
        |
   NO
        |
Can I reuse an item?
        |
   YES -> Unbounded -> DP + FORWARD
        |
   NO -> 0-1 -> DP + BACKWARD
```

### One-line interview recall

> **0-1 = once + backward, Unbounded = unlimited + forward, Fractional = breakable + greedy by value/weight.**

### Java things worth remembering

```java
A.get(i)                         // ArrayList access
int[] dp = new int[C + 1];      // 1D DP
Math.max(a, b)                   // maximum
Integer.compare(a, b)            // safe comparison
Double.compare(a, b)             // double comparison
```

**Context checkpoint complete.**
Absolutely. I’ll treat this as the **master checkpoint** for the DSA work so far.

# DSA Interview Master Recall — Checkpoint

## 1. Two Pointer / Hashing

### Group Anagrams

**Asked:** Group words that are anagrams.

**Recognition:** Same characters with different order → create a canonical representation.

**Algorithm:** HashMap + sorted characters.

```java
char[] chars = word.toCharArray();
Arrays.sort(chars);
String key = new String(chars);

groups.computeIfAbsent(key, k -> new ArrayList<>()).add(word);
```

**Recall:**

> Anagrams → make same canonical key → HashMap.

**Java:** `computeIfAbsent`, `toCharArray`, `Arrays.sort`.

---

### Pair / Difference style problems

**Recognition:** Looking for two elements satisfying a relationship → consider **HashSet/HashMap** or **sorting + two pointers**.

**Decision:**

* Need original positions/frequency → HashMap.
* Only need existence/pair → HashSet often enough.
* Sorted data / can sort → two pointers.

---

## 2. Sliding Window

### General Sliding Window Recognition

Look for:

> **subarray / substring + contiguous + longest/shortest/count + condition**

Think **Sliding Window**.

Basic structure:

```java
int left = 0;

for (int right = 0; right < n; right++) {
    // add right

    while (condition is invalid) {
        // remove left
        left++;
    }

    // update answer
}
```

**Key question:**

> Can I maintain the condition while moving `left` and `right` instead of checking every subarray?

---

### Minimum Window Substring

**Asked:** Find the smallest substring containing all required characters.

**Recognition:**
Substring + minimum length + must satisfy character requirements → **Sliding Window + HashMap/frequency array**.

**Thinking:**

1. Expand `right` until valid.
2. Once valid, shrink `left` as much as possible.
3. Record minimum.
4. Repeat.

**Important:** Distinguish:

* `required` = what target needs.
* `formed` / satisfied count = what current window currently satisfies.

**Recall:**

> Minimum valid substring → expand until valid, shrink while valid.

---

# 3. Grid / BFS

### Binary Maze / Shortest Path

**Asked:** Find shortest path through a binary grid.

**Recognition:**

* Grid/maze
* Need **shortest path**
* Every move has equal cost (`1`)

→ **BFS**

Do **not** default to DFS just because it is a grid.

```java
Queue<int[]> q = new LinkedList<>();
boolean[][] visited = new boolean[n][m];

q.offer(new int[]{sr, sc});
visited[sr][sc] = true;
```

**Recall:**

> Grid + shortest path + equal edge cost → BFS.

---

# 4. Dynamic Programming

## How to recognize DP

Don't memorize:

> "This problem is famous DP."

Instead ask:

### 1. Is it asking for:

* number of ways?
* minimum?
* maximum?
* best possible result?

### 2. Can the answer for the current state be built from smaller states?

### 3. Are the same smaller states reused?

If yes → strong DP signal.

---

## Stairs

**Asked:** Number of ways to reach step `A`, moving 1 or 2 steps.

**Recognition:** Count ways + current answer comes from previous 1/2 states → DP.

```text
ways[i] = ways[i-1] + ways[i-2]
```

**Recall:**

> Current state depends on previous states → DP.

Can optimize to two variables.

---

## Minimum Number of Squares

**Asked:** Minimum number of perfect squares whose sum is `A`.

**Recognition:** Minimum + choose among smaller states → DP.

```text
dp[i] = min(dp[i - j*j] + 1)
```

for every `j*j <= i`.

**Recall:**

> Minimum number of pieces/choices to form a target → try smaller states with DP.

---

## Fibonacci

**Asked:** Find A-th Fibonacci number.

**Recognition:** Current depends on previous two.

```text
F(n) = F(n-1) + F(n-2)
```

**Recall:**

> Previous 2 states only → DP with 2 variables.

---

## Unique Paths with Obstacles

**Asked:** Number of paths from top-left to bottom-right, only right/down, avoiding obstacles.

**Recognition:**

* Grid
* Count paths
* Fixed movement
  → **Grid DP**

```text
dp[i][j] = dp[i-1][j] + dp[i][j-1]
```

Obstacle:

```text
dp[i][j] = 0;
```

**Recall:**

> Grid + count paths + fixed directions → DP.

---

## Unique Binary Search Trees

**Asked:** Number of structurally unique BSTs using `1..N`.

**Recognition:** Choose each possible root.

If root is `i`:

```text
left  = i - 1 nodes
right = N - i nodes
```

Therefore:

```text
dp[n] += dp[i-1] * dp[n-i]
```

with:

```text
dp[0] = 1
```

**Recall:**

> Number of unique trees + choose root → Catalan-style DP.

---

## Max Sum Without Adjacent Elements

**Asked:** 2 x N grid; choose cells for maximum sum without horizontal/vertical/diagonal adjacency.

**Key reduction:**

Because cells vertically conflict, take at most one cell per column:

```text
value[i] = max(A[0][i], A[1][i])
```

Now it becomes:

> Maximum sum with no adjacent elements.

→ **House Robber / 1D DP**

```text
current = max(prev1, prev2 + value)
```

**Recall:**

> Reduce each column to one best value, then solve non-adjacent maximum sum.

---

## N Digit Numbers

**Asked:** Count A-digit positive numbers with digit sum B, no leading zero.

**Recognition:**

> Count ways + state is `(position, sum)` → DP.

First digit:

```text
1..9
```

Remaining digits:

```text
0..9
```

**Key check:**

```text
if (B > 9 * A) -> impossible
```

**Recall:**

> Digit DP when position + digit sum determine the state.

---

# 5. Heap / PriorityQueue

## Distribute Candy

**Asked:** Minimum candies where a child with higher rating than a neighbor must get more.

**Recognition:**

> Neighbor constraint exists on BOTH sides.

→ Two passes.

* Left → satisfy left neighbor.
* Right → satisfy right neighbor.
* Final answer uses:

```text
max(left[i], right[i])
```

**Recall:**

> Neighbor constraints on both sides → two directional passes.

---

## Merge K Sorted Lists

**Asked:** Merge K sorted linked lists.

**Recognition:**

> K sorted streams + repeatedly need smallest current element.

→ **Min Heap**

Process:

```text
put all heads
pop smallest
attach it
push its next
```

**Complexity:** `O(N log K)`.

**Java:**

```java
PriorityQueue<ListNode> pq =
    new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));
```

**Recall:**

> K sorted sources + repeatedly smallest → Min Heap.

---

## Ath Largest Element

**Asked:** Find A-th largest for every prefix.

**Recognition:**

> Streaming K-th largest → Min Heap of size K.

```java
pq.offer(x);

if (pq.size() > A) {
    pq.poll();
}

if (pq.size() == A) {
    answer = pq.peek();
}
```

**Recall:**

> K-th largest → min heap of size K.

---

## Running Median

**Asked:** Median after every insertion.

**Recognition:**

> Dynamic median → two heaps.

* Max Heap = smaller half
* Min Heap = larger half

Maintain:

```text
left.size() >= right.size()
```

Scaler uses **lower median** for even count, so:

```text
answer = left.peek()
```

**Recall:**

> Dynamic median → two heaps.

---

## Connect Ropes

**Asked:** Connect ropes with minimum total cost.

**Recognition:**

> Repeatedly combine two smallest → Min Heap.

```text
a = poll()
b = poll()
sum = a + b
cost += sum
offer(sum)
```

**Recall:**

> Repeatedly combine smallest elements → Min Heap.

---

## Build a Heap

**Asked:** Convert array into min heap in-place.

**Recognition:** Build heap / heapify.

Start from last non-leaf:

```java
for (int i = n / 2 - 1; i >= 0; i--)
```

Children:

```java
left  = 2 * i + 1;
right = 2 * i + 2;
```

**Complexity:** `O(N)`.

**Recall:**

> Build heap → start at last non-leaf and heapify down.

---

## Heap Queries

**Asked:** Insert and extract minimum from an initially empty heap.

**Recognition:** Need minimum dynamically → `PriorityQueue`.

```java
pq.offer(x);
pq.poll();
pq.peek();
```

If empty extraction:

```java
pq.isEmpty() ? -1 : pq.poll()
```

**Recall:**

> Insert + extract minimum → Java `PriorityQueue`.

---

# 6. Greedy

## Flipkart Inventory / Job Scheduling

**Asked:** Jobs/items have deadline + profit; complete before deadline and maximize profit.

**Recognition:**

> Scheduling + deadlines + maximize profit → Greedy + Heap.

Sort by deadline.

For each job:

1. Add profit.
2. If selected jobs exceed deadline, remove smallest profit.

Use:

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
```

**Why min heap?**

We want to discard the **least profitable** selected job whenever we become infeasible.

**Recall:**

> Deadline scheduling + maximize profit → sort deadlines + min heap of profits.

Use `long` for total if sums can become large.

---

# 7. Knapsack Family

This is the **most important current family**.

## 0-1 Knapsack

**Clue:**

> Item cannot be broken + each item used at most once.

→ **DP**

```text
take = value + dp[c-weight]
skip = dp[c]
```

1D DP:

```java
for (int c = C; c >= weight; c--)
```

### BACKWARD

**Recall:**

> 0-1 → once → DP → BACKWARD.

---

## Unbounded Knapsack

**Clue:**

> Item cannot be broken + unlimited copies.

→ **DP**

```java
for (int w = weight; w <= A; w++)
```

### FORWARD

Forward allows the same item to be reused.

**Recall:**

> Unbounded → unlimited → DP → FORWARD.

---

## Fractional Knapsack

**Clue:**

> Item **can be broken**.

→ **Greedy**

Calculate:

```text
value / weight
```

Sort descending.

Take:

1. Full item.
2. Fraction of next item.
3. Stop.

**Recall:**

> Fractional → breakable → Greedy → highest value/weight first.

### Important precision lesson

Avoid:

```java
double
Math.floor(total * 100)
```

when exact output matters.

Use integer arithmetic:

```java
(value * remaining * 100) / weight
```

### Ratio comparison

Don't do integer division:

```java
value / weight
```

Instead cross multiply:

```text
value1 / weight1 > value2 / weight2

=> value1 * weight2 > value2 * weight1
```

---

# ⭐ Master Pattern Recognition Cheat Sheet

Before coding, ask:

```text
1. Is it contiguous subarray/substring?
   -> Sliding Window / Two Pointer

2. Is it shortest path in equal-cost graph/grid?
   -> BFS

3. Is it count / min / max + reusable smaller states?
   -> DP

4. Is it repeatedly asking for smallest/largest?
   -> Heap

5. Is it K-th largest/smallest in a stream?
   -> Heap of size K

6. Is it K sorted lists/streams?
   -> Min Heap

7. Can an item be broken?
   -> Fractional Knapsack / Greedy

8. Knapsack, item used once?
   -> 0-1 DP / BACKWARD

9. Knapsack, item reusable?
   -> Unbounded DP / FORWARD

10. Scheduling + deadlines + maximize profit?
    -> Greedy + Heap

11. Neighbor constraints from both sides?
    -> Two passes

12. Anagrams?
    -> Canonical key + HashMap
```

# ⭐ Biggest Things You Were Learning

### Algorithm choice

Your main goal isn't memorizing solutions. The important progression is:

```text
Problem wording
      ↓
Identify constraint / structure
      ↓
Recognize pattern
      ↓
Choose DS / algorithm
      ↓
Identify invariant / recurrence
      ↓
Then code
```

### Java focus

The syntax areas worth repeatedly reinforcing:

```java
A.get(i)

int[] dp = new int[n];

Math.max(a, b)
Math.min(a, b)

pq.offer(x);
pq.poll();
pq.peek();
pq.size();
pq.isEmpty();

Arrays.sort(arr);

map.computeIfAbsent(key, k -> new ArrayList<>()).add(value);

new PriorityQueue<>();                    // Min Heap
new PriorityQueue<>(Collections.reverseOrder()); // Max Heap
```

### One mistake to actively watch

For **Scaler Java**, keep code/comments **ASCII-only**. Avoid Unicode arrows/special symbols inside code because you previously hit a compiler encoding issue.

---

## 5-Minute Pre-Interview Recall

If you only have a few minutes, read this:

```text
Contiguous + longest/shortest + condition
    -> Sliding Window

Grid + shortest + every move costs 1
    -> BFS

Count / Min / Max + smaller reusable states
    -> DP

Repeated smallest/largest
    -> Heap

K-th largest
    -> Min Heap size K

Dynamic median
    -> Two Heaps

K sorted lists
    -> Min Heap

Anagrams
    -> Canonical key + HashMap

Neighbor constraints both sides
    -> Two passes

Deadline + maximize profit
    -> Greedy + Heap

Knapsack:
    0-1       -> once       -> DP -> BACKWARD
    Unbounded -> unlimited  -> DP -> FORWARD
    Fractional-> breakable  -> Greedy -> value/weight
```

**Core habit:** before touching code, say out loud:

> **"What is the constraint? What choices do I have? What information must I maintain?"**

That is the skill we're trying to build—not just memorizing individual DSA questions.
# DSA Interview Recall — Questions So Far

## 1. Coin Sum Infinite

**Asked:** Count ways to make sum `B` using unlimited coins; **order does not matter**.

**Pattern:**
`Count ways + unlimited reuse → Unbounded Knapsack DP`

**Key idea:**

```text
dp[0] = 1
coin outer loop
sum = coin → B
dp[sum] += dp[sum - coin]
```

**Most important:**
Combination ≠ permutation.

```text
Combinations → coin outer, sum increasing
```

---

## 2. Cutting a Rod

**Asked:** Cut a rod into pieces to get **maximum total price**.

**Pattern:**
`Maximum value + pieces can be reused → Unbounded Knapsack DP`

**Key idea:**

```text
dp[len] = maximum value for rod of length len

for len = 1 → N
    try every piece size
    dp[len] = max(dp[len],
                  price[piece] + dp[len-piece])
```

**Recall:**
Rod Cutting is basically **Unbounded Knapsack where weight = piece length and value = piece price**.

---

## 3. 0-1 Knapsack II

**Asked:** Pick items at most once, within capacity `C`, maximizing value.

**Pattern:**
`Pick / don't pick + each item once → 0-1 Knapsack`

**Key idea:**

```text
dp[w] = maximum value with capacity w

for every item:
    for w = C → weight:
        dp[w] = max(dp[w], value + dp[w-weight])
```

### 🚨 Most important thing to remember

```text
0-1 Knapsack       → capacity decreasing
Unbounded Knapsack → capacity increasing
```

Why? Decreasing prevents using the same item again.

---

# GRAPH

## 4. Cycle in Directed Graph

**Asked:** Check if a directed graph contains a cycle.

**Pattern:**
`Directed graph + cycle detection → DFS + 3 states`

**3 states:**

```text
0 = unvisited
1 = currently in DFS path
2 = completely processed
```

**Key insight:**

If during DFS:

```text
state[next] == 1
```

→ cycle exists.

**Recall:**

```text
Directed cycle → DFS + recursion-stack/state
```

Don't use only `visited[]` for this problem.

---

## 5. Path in Directed Graph

**Asked:** Is there a path from node `1` to node `A`?

**Pattern:**
`Reachability → BFS or DFS`

**Key idea:**

Start from `1`, traverse all reachable nodes using `visited[]`.

```text
visited[1] = true
BFS/DFS
if A reached → 1
otherwise → 0
```

**Important distinction:**

```text
Path existence → visited[] is enough
Cycle detection → need DFS state 0/1/2
```

---

## 6. Connected Components in Undirected Graph

**Asked:** Find all groups of connected vertices.

**Pattern:**
`Disconnected graph + find all groups → DFS/BFS from every unvisited node`

**Key idea:**

```text
for every node:
    if not visited:
        DFS/BFS
        collect this component
```

For an undirected edge:

```text
u -- v

graph[u].add(v)
graph[v].add(u)
```

Then sort each component.

**Recall:**

> One DFS/BFS traversal from an unvisited node = one connected component.

---

## 7. Commutable Islands

**Asked:** Connect all islands with **minimum total bridge cost**.

**Pattern:**
`Minimum cost + connect ALL nodes → MST`

Preferred:

**Kruskal + DSU (Union-Find)**

**Key idea:**

1. Sort edges by cost.
2. Take cheapest edge if it connects different components.
3. Use DSU to detect whether they're already connected.
4. Stop after `A - 1` edges.

```text
find(u) != find(v)
        ↓
take edge
        ↓
union(u, v)
```

**Recall:**

```text
Minimum cost to connect all nodes
        ↓
MST
        ↓
Kruskal → sort edges + DSU
```

---

# 🔥 MASTER PATTERN TABLE

| Problem clue                      | Think                                 |
| --------------------------------- | ------------------------------------- |
| Count ways + unlimited items      | **Unbounded Knapsack DP**             |
| Rod + maximum selling price       | **Unbounded Knapsack DP**             |
| Pick/don't pick + item once       | **0-1 Knapsack DP**                   |
| Directed graph + cycle            | **DFS + 3 states**                    |
| Path from X to Y                  | **BFS/DFS + visited**                 |
| Find all connected groups         | **DFS/BFS from every unvisited node** |
| Minimum cost to connect all nodes | **MST**                               |
| MST + edges sorted by cost        | **Kruskal + DSU**                     |

### ⭐ Highest-value things to memorize

```text
DP:
0-1 Knapsack       → capacity DOWN
Unbounded Knapsack → capacity UP

GRAPH:
Path               → visited
Directed cycle     → DFS states 0/1/2
Components         → DFS/BFS every unvisited node
Minimum connection → MST
Kruskal            → sort + DSU
```

And one Java-specific thing we've already caught:

> **For Scaler, keep submitted code ASCII-only.** Avoid Unicode symbols like `→` in comments.
Absolutely. Here is the **continuation of your interview recall notes from the last summary** — only the new questions we did after that point.

# 🧠 DSA Recall Notes — New Questions

| # | Question                                | Pattern                         | Algorithm    | Key Insight                                               |
| - | --------------------------------------- | ------------------------------- | ------------ | --------------------------------------------------------- |
| 1 | **Best Time to Buy and Sell Stocks II** | Unlimited transactions          | **Greedy**   | Take every positive price difference                      |
| 2 | **Shortest Distance in a Maze**         | Weighted shortest path in grid  | **Dijkstra** | Ball rolls until wall; stopping positions are graph nodes |
| 3 | **Number of Islands**                   | Grid connected components       | **DFS/BFS**  | Every unvisited `1` starts one new island                 |
| 4 | **Jump Game II**                        | Minimum jumps / range expansion | **Greedy**   | Expand the farthest reachable range at each jump          |

---

## 1. Best Time to Buy and Sell Stocks II

**Asked:** Maximum profit with unlimited buy/sell transactions.

### Think

```text
Unlimited transactions
        ↓
Can capture every increasing movement
        ↓
Greedy
```

### Key

```text
if A[i] > A[i-1]
    profit += A[i] - A[i-1]
```

### Recall

> **Stock II = unlimited transactions = Greedy = sum positive differences.**

**Java:**

```java
A.get(i)
A.size()
```

---

## 2. Shortest Distance in a Maze

**Asked:** Minimum distance when a ball keeps rolling until it hits a wall.

### Think

```text
Shortest path
+
different roll distances
        ↓
Weighted graph
        ↓
Dijkstra
```

### Key

* A **stopping position** is a graph node.
* From each stopping position, roll in 4 directions.
* Calculate where it stops and the distance travelled.
* Destination counts **only if the ball stops there**.

### Recall

> **Maze + rolling ball + shortest distance = Dijkstra.**
> Don't use normal BFS because each roll can have a different cost.

**Java:**

```java
PriorityQueue<int[]> pq = new PriorityQueue<>(
    (x, y) -> Integer.compare(x[2], y[2])
);
```

---

## 3. Number of Islands

**Asked:** Count groups of connected `1`s; **diagonal connection also counts**.

### Think

```text
Grid
+
connected groups
        ↓
Connected Components
        ↓
DFS / BFS
```

### Key

```text
Find unvisited 1
    ↓
islands++
    ↓
DFS/BFS entire island
```

Important: **8 directions**, not 4.

### Recall

> **Number of Islands = Connected Components in Grid.**
> Every unvisited `1` = one island → DFS/BFS marks the entire island.

**Java:**

```java
A.get(r).get(c)

boolean[][] visited = new boolean[n][m];
```

8-direction arrays:

```java
int[] dr = {-1, -1, -1, 0, 0, 1, 1, 1};
int[] dc = {-1, 0, 1, -1, 1, -1, 0, 1};
```

---

## 4. Jump Game II

**Asked:** Minimum number of jumps needed to reach the last index.

### Think

```text
Minimum jumps
+
each index gives a reachable range
        ↓
Range expansion
        ↓
Greedy
```

Maintain:

```text
farthest    = farthest we can reach with next jump
currentEnd  = end of current jump's range
jumps       = jumps taken
```

When:

```java
i == currentEnd
```

we must take another jump:

```java
jumps++;
currentEnd = farthest;
```

### Recall

> **Jump Game II = Greedy range expansion.**
> Scan the current reachable range, find the farthest next range, then commit one jump.

**Java:**

```java
farthest = Math.max(farthest, i + A.get(i));
```

---

# 🔥 New Patterns Added to Your Master Pattern List

```text
STOCK:
Unlimited transactions
    -> Greedy
    -> Take every positive difference


GRID:
Connected groups
    -> DFS/BFS

8-direction connection
    -> Use 8 direction vectors


SHORTEST PATH:
Unweighted graph/grid
    -> BFS

Weighted + non-negative
    -> Dijkstra

Maze where ball rolls until wall
    -> Dijkstra
    -> stopping positions = nodes


JUMP:
Minimum jumps
    -> Greedy range expansion

Jump Game I
    -> Can reach?
    -> Greedy reachability

Jump Game II
    -> Minimum jumps?
    -> Greedy range expansion
```

### ⭐ Most important recognition rules so far

```text
Minimum cost to connect ALL nodes
    -> MST

Shortest path + unweighted
    -> BFS

Shortest path + weighted non-negative
    -> Dijkstra

Prerequisites / dependencies
    -> Topological Sort

Count connected groups
    -> DFS/BFS

Unlimited choices in DP
    -> Unbounded Knapsack
    -> capacity/sum increasing

Each item only once
    -> 0-1 Knapsack
    -> capacity decreasing

Minimum jumps / reachable ranges
    -> Greedy
```

This is the section to quickly read before an interview: **identify the wording first, then map it to the pattern.**
# DSA Recall Summary — This Chat

## Q1. Next Pointer Binary Tree

**Asked:** Connect every node to the next node on the same level; last node → `NULL`.

**Pattern recognition:**

* Binary tree + **perfect tree** + same-level connections.
* Preferred: **use `next` pointers themselves**, instead of Queue/BFS.
* Aim for **O(1) extra space**.

**Key thinking:**

```text
current.left.next  = current.right
current.right.next = current.next.left
```

**Remember:**

* Same parent → `left.next = right`
* Across parents → `right.next = parent.next.left`
* Move across level using `curr = curr.next`.

**Complexity:** `O(N)` time, `O(1)` extra space.

---

## Q2. Vertical Order Traversal

**Asked:** Group nodes according to their **vertical column**, leftmost → rightmost.

**Pattern recognition:**

* Tree + vertical position → **BFS + horizontal distance**.
* Assign:

```text
root = 0
left = col - 1
right = col + 1
```

* BFS is important because the problem says **lesser depth first**.
* `TreeMap` gives columns in sorted order.

**Key thinking:**

```text
(node, column)
```

For every node:

```text
left  → col - 1
right → col + 1
```

**Complexity:** `O(N log N)` using `TreeMap`.

**Java recall:**

```java
TreeMap<Integer, ArrayList<Integer>> map = new TreeMap<>();
Queue<Pair> queue = new LinkedList<>();

queue.offer(pair);
Pair p = queue.poll();

map.computeIfAbsent(col, k -> new ArrayList<>()).add(value);
```

---

## Q3. Top View of Binary Tree

**Asked:** Return the node **visible from the top** at each vertical column.

**Pattern recognition:**

Very similar to Vertical Order.

> **Vertical Order → keep ALL nodes per column.**
> **Top View → keep only FIRST node per column.**

Use:

* **BFS** → first encountered = shallowest/topmost.
* Horizontal distance → identify columns.
* `HashMap` → store first node.
* Track `minColumn/maxColumn` if output needs left-to-right.

**Key thinking:**

```java
if (!map.containsKey(column)) {
    map.put(column, node.val);
}
```

**Complexity:** `O(N)` time, `O(N)` space.

**Important pattern:**

```text
Tree + column/position
        ↓
BFS + extra state
```

---

## Q4. Diameter of Binary Tree

**Asked:** Find the longest path between any two nodes, measured in **edges**.

**Pattern recognition:**

Think:

> **Longest path through a node = height of left subtree + height of right subtree.**

Therefore → **DFS/Postorder + height**.

At every node:

```text
leftHeight  = height(left)
rightHeight = height(right)

diameter = max(diameter, leftHeight + rightHeight)

height = 1 + max(leftHeight, rightHeight)
```

**Most important insight:**

The recursive function's job is:

> **Return height upward, update diameter while coming back.**

**Complexity:** `O(N)` time, `O(H)` recursion space.

**Java recall:**

```java
int left = height(node.left);
int right = height(node.right);

diameter = Math.max(diameter, left + right);

return 1 + Math.max(left, right);
```

---

# Q5. Least Common Ancestor — LCA

**Asked:** Find the lowest/deepest node that is an ancestor of both `B` and `C`.

### Pattern recognition

First ask:

> **Is this a BST?**

Here it is an **unordered binary tree**, so **don't use BST value comparisons**.

Think:

**Binary Tree + ancestor relationship → recursive DFS.**

At every node:

1. Search left.
2. Search right.
3. If current node is `B` or `C`, return it.
4. If **both left and right found something → current node is LCA**.
5. If only one side found → return that side upward.

### Core logic

```text
              node
             /    \
         found    found
             ↓
          node = LCA
```

Specifically:

```java
if (left != null && right != null) {
    return root;
}

return left != null ? left : right;
```

### Important edge case we discovered

Test case had:

```text
B = 9
C = 9
```

So **B and C can be the same node**.

If:

```java
B == C
```

then:

> That node itself is the LCA — **provided it exists**.

This caused our initial solution to return `-1` because `foundB` was set and returned before `foundC` could be set.

### Also important

If either `B` or `C` doesn't exist, Scaler expects:

```text
-1
```

So you need to account for node existence.

---

# 🧠 Overall Pattern Map From This Chat

| Problem wording                         | Think                                      |
| --------------------------------------- | ------------------------------------------ |
| Perfect tree + connect same-level nodes | `next pointers`                            |
| Vertical order                          | **BFS + horizontal distance + TreeMap**    |
| Top view                                | **BFS + horizontal distance + first node** |
| Longest path in tree                    | **DFS + subtree height**                   |
| Lowest common ancestor                  | **Recursive DFS**                          |
| Unordered binary tree                   | **Don't use BST logic**                    |

### Most useful recognition rules

**1. Tree + level/depth information**
→ usually **BFS**

**2. Tree + height / longest path / ancestor**
→ usually **DFS recursion**

**3. Tree + vertical/column**
→ **horizontal distance + BFS**

**4. "First/topmost" node at each column**
→ BFS + store **first**

**5. "All nodes" at each column**
→ BFS + store **all**

**6. Recursive tree problem**
Ask:

> **What information does my child need to return to me?**

For Diameter → child returns **height**.
For LCA → child returns **candidate node**.

### Java things worth keeping in your quick-revision notes

```java
Math.max(a, b);
Math.min(a, b);

queue.offer(x);
queue.poll();

map.containsKey(key);
map.put(key, value);
map.get(key);

map.computeIfAbsent(key, k -> new ArrayList<>());

return left != null ? left : right;
```

**Biggest learning from these questions:** don't start coding immediately. First identify **what information needs to flow through the tree** — `next`, `column`, `height`, or `LCA candidate`.
