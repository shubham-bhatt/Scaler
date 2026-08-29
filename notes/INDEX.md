# Study Notes Index

Interview-prep vault for **DSA, LLD, HLD, and SQL** (coding + design rounds).
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
Filling a topic? Update its note, add it to the subject cheat sheet, and bump the
file's `status` / `updated`.

---

## DSA

📄 **[DSA Cheat Sheet](DSA/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [arrays-searching-sorting](DSA/arrays-searching-sorting.md) | Arrays · **Sorting (Merge Sort & Inversion Count)** · Binary Search · Prefix Sum | 🟡 |
| [two-pointers-sliding-window](DSA/two-pointers-sliding-window.md) | **Two Pointers** · Sliding Window · Fast & Slow · Intervals | 🟡 |
| [hashing-and-strings](DSA/hashing-and-strings.md) | Hashing · Frequency Patterns · String Algorithms | ⚪ |
| [linkedlist-stack-queue](DSA/linkedlist-stack-queue.md) | Linked List · Stack · **Queue & Deque** · Monotonic Stack | 🟡 |
| [trees-and-bst](DSA/trees-and-bst.md) | Binary Tree · BST · Traversals · Trie | ⚪ |
| [heaps-and-greedy](DSA/heaps-and-greedy.md) | Heap / Priority Queue · Top-K · Greedy | ⚪ |
| [graphs](DSA/graphs.md) | BFS/DFS · Shortest Path · **Union-Find & MST** · Topological Sort | 🟡 |
| [recursion-and-backtracking](DSA/recursion-and-backtracking.md) | Recursion · Backtracking · Subsets/Permutations · Divide & Conquer | ⚪ |
| [dynamic-programming](DSA/dynamic-programming.md) | DP Basics · **1D DP (Max Product Subarray)** · 2D/Knapsack · DP on Strings | 🟡 |
| [bit-and-math](DSA/bit-and-math.md) | Bit Manipulation · **Number Theory (Primes/Sieve)** · Math Tricks | 🟡 |

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
