# Study Notes Index

Interview-prep vault for **Introduction_To_PS (foundations), DSA, LLD, HLD, and SQL** (coding + design rounds).
Plain Markdown so it renders anywhere.

## How this vault is organized

**Bucket** = subject folder · **Subbucket** = one `.md` file grouping 3–5 related
topics · **Topic** = a `##` section inside that file.

- **One consolidated `cheatsheet.md` per subject** for last-minute scanning
  (topics are `###` sections). Detailed notes live in the sibling subbucket files.
- **Templates** in [`_templates/`](_templates/) — copy [`subbucket-note.md`](_templates/subbucket-note.md)
  to start a new subbucket, [`cheatsheet.md`](_templates/cheatsheet.md) for a new subject.
- **`sources/`** in each bucket holds original PDFs/files a note was built from.
- New buckets (e.g. CS-Fundamentals, Behavioral) can be added later with the same layout.

**Status:** 🟢 complete · 🟡 partial (some topics written) · ⚪ stub (skeleton only).
Filling a topic? Update its note, add it to the subject cheat sheet, bump the
file's `status` / `updated`, and update that subject's **Topic Details** block
below (see next paragraph).

**This file is the single source of truth for the vault's structure.** The
`/notes` skill (`.claude/skills/notes/SKILL.md`) reads only this file to decide
where new content belongs — it does not hardcode the layout, subject list, or
routing itself. Each subject with written content has a **Topic Details** block
under its table: one line per written topic with enough keyword detail to
decide "does this belong here?" without opening the subbucket file. Keep both
the status table and the Topic Details block current — that's what keeps
future `/notes` runs cheap (route from this file alone) instead of expensive
(open every candidate file to check).

---

## Introduction_To_PS

Foundations module (kept **separate from DSA** by choice): complexity analysis, array basics, prefix sum, subarrays, sliding window.
Later DSA topics build on it; DSA notes link back here.

📄 **[Introduction_To_PS Cheat Sheet](Introduction_To_PS/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [problem-solving-and-complexity](Introduction_To_PS/problem-solving-and-complexity.md) | **Counting Iterations & Math (AP/GP)** · **Factors & Primes (√N)** · **Logarithms & Loop Analysis** · **Big O & Constraints** · **Space Complexity** | 🟢 |
| [arrays-basics](Introduction_To_PS/arrays-basics.md) | **Array Fundamentals** · **Pair Sum (Brute Force)** · **Reverse Array** · **Rotate Array** | 🟢 |
| [prefix-sum-and-subarrays](Introduction_To_PS/prefix-sum-and-subarrays.md) | **Prefix Sum** · **Even/Odd Prefix & Special Index** · **Carry Forward** · **Subarrays** · **Contribution Technique** · **Fixed-Size Sliding Window** | 🟢 |

### Introduction_To_PS — Topic Details (routing index)

- **problem-solving-and-complexity.md → Counting Iterations & Math** — iterations vs execution time; range size `b-a+1`; AP sum `N(N+1)/2`; GP sum `a(rⁿ−1)/(r−1)`; sequential loops add, nested multiply.
- **→ Factors & Primes (√N)** — factor pairs `(a, N/a)`, loop `a*a<=N`, perfect-square counts once, prime = exactly 2 factors, 10¹⁸: 317 years → 10 s.
- **→ Logarithms & Loop Analysis** — `log_a b = c`, `floor(log₂N)` halving, `i*=2` O(log N), `i=0` infinite loop, loop-shape table (N², N log N, 2^N), iteration tables.
- **→ Big O & Constraints** — asymptotic analysis, drop lower-order/constants, why, Big O limitations, TLE workflow, ~10⁸ iter/sec, constraint→complexity table (10⁶/10³/10²/20).
- **→ Space Complexity** — extra space beyond input/output, int 4B/long 8B, O(1) vs O(N), in-place.
- **arrays-basics.md → Array Fundamentals** — contiguous memory, `base+i*size` O(1) access, max element, dynamic arrays (ArrayList/vector/list), autoboxing.
- **→ Pair Sum (Brute Force)** — `A[i]+A[j]==K, i!=j`, only `j<i` triangle, N(N-1)/2 → O(N²).
- **→ Reverse Array** — two-pointer swap, O(1) space, reverse range `[L,R]`, extra-array O(N) variant.
- **→ Rotate Array** — rotate right K: O(K·N) shifts vs 3 reversals O(N); `K %= N`; direction/left-rotate gotcha.
- **prefix-sum-and-subarrays.md → Prefix Sum** — `P[i]=P[i-1]+A[i]`, `P[R]-P[L-1]`, Q queries O(N+Q), scoreboard analogy, in-place SC O(1).
- **→ Even/Odd Prefix & Special Index** — `PE`/`PO` arrays; removing index flips suffix parity; count special indices O(N).
- **→ Carry Forward** — calculate+use together, suffix sum; count `(a,g)` pairs `i<j`; smallest subarray containing min & max (closest-index tracking; `else if` bug when min==max).
- **→ Subarrays** — N(N+1)/2 count, `N-K+1` of length K, print all O(N³), all sums via prefix / carry forward O(N²).
- **→ Contribution Technique** — `A[i]*(i+1)*(N-i)`, sum of all subarray sums in O(N), cast-to-long overflow.
- **→ Fixed-Size Sliding Window** — max sum of size-K subarray: brute O(N²)/prefix O(N)/slide `+A[i]-A[i-K]` O(N), O(1) space.

## DSA

📄 **[DSA Cheat Sheet](DSA/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [arrays-searching-sorting](DSA/arrays-searching-sorting.md) | Arrays · **Sorting (Merge Sort & Inversion Count)** · Binary Search · Prefix Sum | 🟡 |
| [two-pointers-sliding-window](DSA/two-pointers-sliding-window.md) | **Two Pointers** · Sliding Window · Fast & Slow · Intervals | 🟡 |
| [hashing-and-strings](DSA/hashing-and-strings.md) | **Hashing** · **Frequency Patterns** · String Algorithms | 🟡 |
| [linkedlist-stack-queue](DSA/linkedlist-stack-queue.md) | Linked List · Stack · **Queue & Deque** · Monotonic Stack | 🟡 |
| [trees-and-bst](DSA/trees-and-bst.md) | Binary Tree · BST · Traversals · Trie | ⚪ |
| [heaps-and-greedy](DSA/heaps-and-greedy.md) | Heap / Priority Queue · **Top-K** · Greedy | 🟡 |
| [graphs](DSA/graphs.md) | **BFS/DFS** · Shortest Path · **Union-Find & MST** · Topological Sort | 🟡 |
| [recursion-and-backtracking](DSA/recursion-and-backtracking.md) | **Recursion** · **Backtracking** · **Subsets & Permutations** · Divide & Conquer | 🟡 |
| [dynamic-programming](DSA/dynamic-programming.md) | **DP Basics** · **1D DP (Max Product Subarray)** · 2D/Knapsack · DP on Strings | 🟡 |
| [bit-and-math](DSA/bit-and-math.md) | Bit Manipulation · **Number Theory (Primes/Sieve)** · **Math Tricks** | 🟡 |

### DSA — Topic Details (routing index)

Only written topics are listed. If incoming content matches one of these, extend
it — don't create a new topic or file. Unlisted/⚪ topics in the table above have
no content yet, so any new material for them just fills the stub.

- **arrays-searching-sorting.md → Sorting** — Merge Sort & Inversion Count: divide & conquer, stability, `subList` view gotcha, inversion count via the merge step, `long` overflow, safe comparator (`Integer.compare` vs `a-b`).
- **two-pointers-sliding-window.md → Two Pointers** — converging L/R on sorted arrays; hash-set vs hash-map choice; duplicate handling (distinct-value dedupe vs index-pair nC2/nP2); pairs with given difference; pairs with given sum (duplicates counted); pointer-invariant / "don't skip candidates" pitfalls; `Integer ==` vs `.equals()`.
- **hashing-and-strings.md → Hashing** — HashSet vs HashMap (membership vs count); when hashing beats sorting; Longest Consecutive Sequence (expand only from sequence starts, O(N)).
- **hashing-and-strings.md → Frequency Patterns** — nC2 / nP2 / n² pair-counting formulas; on-the-fly vs batch frequency counting; cast-before-multiply overflow; `StringBuilder` vs `String` concatenation; Subarray Sum = K via prefix-sum + hashmap (seed `map.put(0,1)`).
- **linkedlist-stack-queue.md → Queue & Deque** — BFS via Queue; `ArrayDeque` vs `LinkedList`; mark-visited-on-enqueue; level-order size-snapshot pattern.
- **heaps-and-greedy.md → Top-K** — Top K Frequent Elements: HashMap freq count → sort/heap by frequency; size-K min-heap of opposite polarity for O(N + M log K); quickselect as the O(N) average alternative.
- **graphs.md → BFS/DFS** — Binary Maze / shortest path in a 0-1 grid: `Queue<int[]>` of `{row,col,dist}`, 4-direction array, mark-visited-on-enqueue, why BFS (not DFS/backtracking) guarantees shortest path on unweighted grids.
- **graphs.md → Union-Find & MST** — Prim's vs Kruskal's; path compression + union by rank; cycle detection via Union-Find.
- **recursion-and-backtracking.md → Recursion** — base/recursive case; DFS-vs-backtracking distinction.
- **recursion-and-backtracking.md → Backtracking** — choose → explore → un-choose template; Decision→Choices→State→Base case→Undo checklist; Generate Parentheses (open/close bracket guards); beginner Java syntax traps (`""` vs `''`, void helper + shared list vs return-at-every-branch).
- **recursion-and-backtracking.md → Subsets & Permutations** — include/exclude template (Subsets, Subset Sum) vs `visited[]` template (Permutations); grid paths (Down/Right only); steps (1-or-2 climbing, confirmed `climbUp` version); "try the smaller choice first" for free lexicographic order; Java collection-API traps (`.length()`/`.size()`/`.length`, `=` vs `==`, `List.remove(int)` by-index, `new ArrayList<Integer>()` syntax, `visited[i]` vs `visited[A.get(i)]`).
- **dynamic-programming.md → DP Basics** — Climbing Stairs: state/recurrence/overlap/memoize/optimize recipe; `ways(n)=ways(n-1)+ways(n-2)`; overlapping-subproblems tree; count (DP) vs generate-all (backtracking) fork; memo/tabulation/space-optimized versions.
- **dynamic-programming.md → 1D DP** — Max Product Subarray: track running max & min together, sign-flip handling, zero resets both.
- **bit-and-math.md → Number Theory** — Sieve of Eratosthenes, prime factorization, SPF sieve, divisor-count sieve, GCD/LCM.
- **bit-and-math.md → Math Tricks** — bijective base-26 (Excel column title), `StringBuilder` digit-building right-to-left.

## LLD

📄 **[LLD Cheat Sheet](LLD/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [oop-and-solid](LLD/oop-and-solid.md) | OOP Fundamentals · SOLID · UML Basics | ⚪ |
| [creational-patterns](LLD/creational-patterns.md) | Singleton · Factory · Builder · Prototype | ⚪ |
| [structural-patterns](LLD/structural-patterns.md) | Adapter · Decorator · Facade · Composite · Proxy | ⚪ |
| [behavioral-patterns](LLD/behavioral-patterns.md) | Observer · Strategy · State · Command · Iterator | ⚪ |
| [lld-principles](LLD/lld-principles.md) | DRY/KISS/YAGNI · Composition vs Inheritance · Coupling & Cohesion · Concurrency | ⚪ |
| [lld-case-studies](LLD/lld-case-studies.md) | Parking Lot · Elevator · BookMyShow · Splitwise … | ⚪ |

## HLD

📄 **[HLD Cheat Sheet](HLD/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [scaling-fundamentals](HLD/scaling-fundamentals.md) | Scalability · Load Balancing · Caching · CDN | ⚪ |
| [databases-and-storage](HLD/databases-and-storage.md) | SQL vs NoSQL · Indexing · Sharding · Replication · CAP | ⚪ |
| [messaging-and-communication](HLD/messaging-and-communication.md) | Queues · Pub/Sub · Kafka · WebSockets/SSE · API Design | ⚪ |
| [reliability-and-consistency](HLD/reliability-and-consistency.md) | Consistency Models · Consensus · Rate Limiting · Idempotency | ⚪ |
| [hld-case-studies](HLD/hld-case-studies.md) | URL Shortener · Rate Limiter · News Feed · Chat · Notifications … | ⚪ |

## SQL

📄 **[SQL Cheat Sheet](SQL/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [query-basics](SQL/query-basics.md) | SELECT · WHERE · ORDER BY/LIMIT · DISTINCT | ⚪ |
| [aggregation-and-grouping](SQL/aggregation-and-grouping.md) | **Aggregate Functions · GROUP BY · WHERE vs HAVING · Self-Join** | 🟡 |
| [joins-and-subqueries](SQL/joins-and-subqueries.md) | Join Types · Self-Join · Subqueries · CTEs | ⚪ |
| [window-functions](SQL/window-functions.md) | Window Basics · Ranking · Running Aggregates | ⚪ |

### SQL — Topic Details (routing index)

- **aggregation-and-grouping.md → Aggregate Functions · GROUP BY · WHERE vs HAVING · Self-Join** — _(add specific keywords here as this topic is filled in further)_.
