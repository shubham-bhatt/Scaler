---
title: DSA — Cheat Sheet
subject: DSA
type: cheatsheet
reviewed: 2026-09-07
covers: [arrays-searching-sorting, two-pointers-sliding-window, hashing-and-strings, linkedlist-stack-queue, trees-and-bst, heaps-and-greedy, graphs, recursion-and-backtracking, dynamic-programming, bit-and-math]
---

# DSA — Cheat Sheet

> One file to flip through before an interview. `##` = subbucket, `###` = topic.
> Full explanations live in the sibling note files.

## Arrays, Searching & Sorting
Note: [arrays-searching-sorting](arrays-searching-sorting.md)

### Merge Sort & Inversion Count
- Divide & conquer, **stable**, O(N log N) **all cases**, O(N) space.
- Inversion = pair (i,j), i<j, a[i]>a[j]. Max = N·(N-1)/2 → use **`long`**.
- Trick: when picking from the **right** half during merge, `count += left.size() - i`.
- `<=` in merge keeps stability & avoids overcounting; wrap `subList` in `new ArrayList<>()` (it's a view).
- Comparator: `(a,b) -> a-b` can overflow — use `Integer::compare` / `Integer.compare(a,b)`.

```java
if (left.get(i) <= right.get(j)) arr.set(k++, left.get(i++));
else { arr.set(k++, right.get(j++)); count += left.size() - i; }
```

### Arrays · Binary Search · Prefix Sum
_(to be added)_

## Two Pointers & Sliding Window
Note: [two-pointers-sliding-window](two-pointers-sliding-window.md)

### Two Pointers
- **Sorted → two pointers** (O(N) time, **O(1) space**). Move: `sum<k → L++`, `sum>k → R--`, `sum==k → hit`.
- **Need indices → hash map** `value→index`. **Unsorted existence → hash set**. **O(1) space demanded → sort + two pointers** (O(N log N)).
- Count pairs w/ duplicates → **frequency map**, not a bare set. Distinct pairs → skip equal neighbours after a hit.
- `L < R` (not `<=`) for distinct-index pairs; every branch must move a pointer.

```java
int L=0, R=a.length-1;              // sorted array, all pairs = k
while (L<R){int s=a[L]+a[R];
  if(s==k){/*hit*/ L++;R--;} else if(s<k)L++; else R--;}
```

- **Pairs with diff B** (distinct values, dedupe): sort, `i=0,j=1`; `diff<B→j++`, `diff>B→i++`, `diff==B→count++` then skip the whole duplicate block of both `i` and `j`. Same code path handles `B=0` — no special case needed.
- **Pairs with sum B, index-pairs counted** (duplicates multiply, not skip): converging `L/R`; if `a[L]==a[R]` the whole window is one value → `n*(n-1)/2`; else multiply block sizes `cl*cr`.
- Java: `list.get(i) == list.get(j)` fails for boxed `Integer` ≥ 128 (cache only covers −128..127) — always `.equals()` for boxed-vs-boxed comparisons.

### Sliding Window · Fast & Slow · Intervals
_(to be added)_

## Hashing & Strings
Note: [hashing-and-strings](hashing-and-strings.md)

### Frequency Patterns
- 3 pair formulas (`n` = freq of a value): **i<j** → `n*(n-1)/2` (nC2, ~90% of "count pairs"); **i≠j ordered** → `n*(n-1)` (nP2); **any i,j incl. self** → `n²`.
- On-the-fly: `pairs += freq.get(x)` **before** incrementing `freq[x]` — avoids a second pass.
- Cast before multiplying: `(long) n * (n - 1)`, never `(long)(n * (n - 1))` — the overflow already happened inside the parens.
- Building keys/strings with `+=` in a loop is O(N²) (String is immutable) — use `StringBuilder`.

```java
pairs += freq.getOrDefault(num, 0);
freq.merge(num, 1, Integer::sum);
```

- **Subarray Sum = K**: `prefixSum[j]-prefixSum[i]=k` → need count of earlier prefix sums `= sum-k`. Seed `map.put(0,1)` (empty prefix) or subarrays starting at index 0 are missed. Lookup `sum-k` **before** recording `sum` itself.

```java
sum += num; count += prefixCounts.getOrDefault(sum-k, 0);
prefixCounts.put(sum, prefixCounts.getOrDefault(sum,0)+1);
```

### Hashing
- `HashSet` = O(1) membership only; `HashMap` when you need a count/value too. Hashing beats sorting (`O(N log N)`) whenever order doesn't matter, only presence/count does.
- **Longest Consecutive Sequence**: only expand from a true sequence start (`!set.contains(num-1)`) — else it degrades to O(N²) re-scanning the same run.

```java
if (!set.contains(num-1)) { int len=1; while(set.contains(num+len)) len++; max=Math.max(max,len); }
```

### String Algorithms
_(to be added)_

## Linked List, Stack & Queue
Note: [linkedlist-stack-queue](linkedlist-stack-queue.md)

### Queue & Deque (BFS)
- **FIFO**, all ops O(1): `offer` / `poll` / `peek` (return null on empty; `add/remove/element` throw).
- **BFS = Queue**; DFS = Stack/recursion. `ArrayDeque` faster than `LinkedList` (no nulls).
- Mark visited on **enqueue**, not dequeue. Capture `queue.size()` **before** the level loop.

```java
while(!q.isEmpty()){ int sz=q.size();
  for(int i=0;i<sz;i++){ var c=q.poll(); /* push unvisited neighbours */ } }
```

### Linked List · Stack · Monotonic Stack
_(to be added)_

## Trees & BST
Note: [trees-and-bst](trees-and-bst.md)

_(to be added)_

## Heaps & Greedy
Note: [heaps-and-greedy](heaps-and-greedy.md)

### Top-K
- **Top K Frequent**: `HashMap` freq count → sort by frequency desc → take first K. O(N + M log M).
- Better for large M, small K: size-K **min-heap** keyed on frequency, evict smallest when size > K → O(N + M log K).
- True O(N) average → **quickselect** on the frequency array.
- Comparator direction matters: `(a,b)->b[1]-a[1]` = descending; flipped sign quietly returns the *least* frequent instead.

```java
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a,b) -> a[1]-b[1]);
minHeap.offer(new int[]{key, freq}); if (minHeap.size() > k) minHeap.poll();
```

### Heap / Priority Queue · Greedy
_(to be added)_

## Graphs
Note: [graphs](graphs.md)

### Union-Find & MST
- MST: connect all V nodes, V-1 edges, min weight, no cycles.
- **Prim's**: grow one tree, min-heap picks cheapest bridge — O(E log V), dense graphs.
- **Kruskal's**: sort edges, Union-Find blocks cycles — O(E log E), sparse graphs.
- **Union-Find**: `parent[i]=i` init; path compression + union by rank → O(α(N)).

```java
int find(int[] p,int x){ if(p[x]!=x) p[x]=find(p,p[x]); return p[x]; }
// Prim: skip if visited[dest]; Kruskal: add edge iff find(u)!=find(v)
```

### BFS & DFS (Binary Maze / grid shortest path)
- **Grid + shortest path + every move costs 1 → BFS**, never DFS/backtracking (DFS finds *a* path, not the shortest).
- `Queue<int[]>` of `{row, col, dist}`; `dir[][]={{-1,0},{1,0},{0,-1},{0,1}}` for 4-directional moves.
- **Mark visited on enqueue**, not dequeue — else the same cell queues multiple times.
- Check **in-bounds before indexing** the grid (short-circuit `&&` order matters).

```java
q.add(new int[]{sr,sc,0}); visited[sr][sc]=true;
while(!q.isEmpty()){ int[] cur=q.poll(); if(cur[0]==dr&&cur[1]==dc) return cur[2];
  for(int[] d: dir){ int nr=cur[0]+d[0], nc=cur[1]+d[1];
    if(nr>=0&&nr<R&&nc>=0&&nc<C&&grid[nr][nc]==1&&!visited[nr][nc]){ visited[nr][nc]=true; q.add(new int[]{nr,nc,cur[2]+1}); } } }
```

- Coding habit: for BFS, the `while(queue...)` loop usually *is* the algorithm — don't reach for a `helper()` just because there's a loop; helpers fit recursion/DFS more naturally (the function represents "solve from this state").

### Shortest Path · Topological Sort
_(to be added)_

## Recursion & Backtracking
Note: [recursion-and-backtracking](recursion-and-backtracking.md)

### Backtracking
- Template: **choose → explore → un-choose**. Un-choose is required only for *shared mutable* state (`StringBuilder`, `List`, `visited[]`) — params passed by value (`int`, `String`) auto-revert on return, no un-choose line needed.
- DFS = traversal; **backtracking = DFS + prune** — abandon a branch the instant it's proven invalid, don't wait to reach the leaf.
- Generate Parentheses: guard `open<A` then `close<open`; base case `open==A && close==A`.
- Java newbie traps: `""` not `''` for empty string; make the helper `void` + mutate a shared `result` list, don't try to `return` at every recursive branch (→ "missing return" compile error).

```java
if (open < A) { sb.append('('); solve(...); sb.deleteCharAt(sb.length()-1); }
if (close < open) { sb.append(')'); solve(...); sb.deleteCharAt(sb.length()-1); }
```

### Subsets & Permutations
- **Subsets**: 2 choices per *element* (include/exclude), 2^N results, no `visited[]` needed.
- **Permutations**: shrinking choices per *position* (N, N-1, …), N! results, **needs `visited[]`**.
- Always copy when recording (`new ArrayList<>(curr)` / `.toString()`) — `curr`/`path` is one shared mutable object across the whole recursion.
- Grid paths (Down/Right only) & Steps (1-or-2) are the same 2-choices-per-state template; trying the smaller choice first (`D` before `R`, `1` before `2`) gives lexicographic output for free.
- Java traps: `.length()`(String) / `.size()`(List) / `.length`(array) are 3 different APIs; `=` in an `if` is assignment, not `==`; `list.remove(int)` deletes **by index** — pop last via `curr.remove(curr.size()-1)`; `new ArrayList<Integer>()` not `new <ArrayList<Integer>>()`.

```java
solve(idx+1, curr, result);                                                   // exclude
curr.add(arr[idx]); solve(idx+1, curr, result); curr.remove(curr.size()-1);   // include
```

### Recursion · Divide & Conquer
_(to be added)_

## Dynamic Programming
Note: [dynamic-programming](dynamic-programming.md)

### Max Product Subarray (1D DP)
- Track **both** max & min (neg·neg flips sign). O(N) time, O(1) space.
- `tempMax=max(a[i], a[i]*max, a[i]*min)`, `tempMin=min(...)` — compute **both before updating**.
- Zero resets both to 0. Java 2D DP: new inner list per row (add() stores a reference).

```java
int tMax=Math.max(A[i],Math.max(A[i]*mx,A[i]*mn));
int tMin=Math.min(A[i],Math.min(A[i]*mx,A[i]*mn));
mx=tMax; mn=tMin; res=Math.max(res,mx);
```

### DP Basics (Climbing Stairs)
- Recipe: **define state** (`ways(n)`) → **write recurrence** (`ways(n)=ways(n-1)+ways(n-2)`) → **spot overlap** (same state recomputed via multiple call paths) → **memoize/tabulate** → **optimize space**.
- "Count the ways" → DP; "generate/print every way" → backtracking (output itself is exponential, see [Recursion & Backtracking](recursion-and-backtracking.md)).
- Memo sentinel must be `-1`, not `0` — `0` can be a valid answer. Space-optimize to 2 rolling variables once only the last `k` states are needed.

```java
int prev2=1, prev1=1;
for (int i=2;i<=n;i++){ int cur=prev1+prev2; prev2=prev1; prev1=cur; }
return prev1;   // O(1) space
```

### 2D DP/Knapsack · DP on Strings
_(to be added)_

## Bit Manipulation & Math
Note: [bit-and-math](bit-and-math.md)

### Number Theory (Primes / Sieve)
- **Sieve** O(N log log N): mark from `i*i`, outer loop while `(long)i*i<=N`.
- **Factorize** O(√N): divide by each i; if `n>1` at end, it's prime. Use `while`, not `if`.
- **GCD** Euclid `gcd(a,b)=gcd(b,a%b)`; **LCM** `a/gcd(a,b)*b` (divide first — overflow).
- Divisors of `p1^a1·p2^a2…` = `(a1+1)(a2+1)…`. Array size **N+1**.

```java
for(int i=2;(long)i*i<=N;i++) if(!comp[i])
  for(int j=i*i;j<=N;j+=i) comp[j]=true;
```

### Math Tricks
- Bijective base-26 (Excel column title): no zero digit, so shift by 1 before extracting — `digit=(n-1)%26`, `n=(n-1)/26`. Plain `n%26`/`n/26` breaks at multiples of 26.
- Digits come out right→left — append to `StringBuilder` and `.reverse()` once; never prepend to a `String` in the loop.
- `(char)('A'+digit)` computes the letter directly — no need for a lookup `ArrayList<Character>`.

```java
while (n > 0) { sb.append((char)('A' + (n-1)%26)); n = (n-1)/26; }
return sb.reverse().toString();
```

### Bit Manipulation
_(to be added)_
