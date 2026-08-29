---
title: DSA — Cheat Sheet
subject: DSA
type: cheatsheet
reviewed: 2026-08-20
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

### Sliding Window · Fast & Slow · Intervals
_(to be added)_

## Hashing & Strings
Note: [hashing-and-strings](hashing-and-strings.md)

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

### BFS/DFS · Shortest Path · Topological Sort
_(to be added)_

## Recursion & Backtracking
Note: [recursion-and-backtracking](recursion-and-backtracking.md)

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

### DP Basics · 2D/Knapsack · DP on Strings
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

### Bit Manipulation · Math Tricks
_(to be added)_
