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

## DSA

📄 **[DSA Cheat Sheet](DSA/cheatsheet.md)**

| Subbucket | Topics | Status |
|---|---|---|
| [arrays-searching-sorting](DSA/arrays-searching-sorting.md) | Arrays · **Sorting (Merge Sort & Inversion Count)** · Binary Search · Prefix Sum | 🟡 |
| [two-pointers-sliding-window](DSA/two-pointers-sliding-window.md) | **Two Pointers** · Sliding Window · Fast & Slow · Intervals | 🟡 |
| [hashing-and-strings](DSA/hashing-and-strings.md) | Hashing · **Frequency Patterns** · String Algorithms | 🟡 |
| [linkedlist-stack-queue](DSA/linkedlist-stack-queue.md) | Linked List · Stack · **Queue & Deque** · Monotonic Stack | 🟡 |
| [trees-and-bst](DSA/trees-and-bst.md) | Binary Tree · BST · Traversals · Trie | ⚪ |
| [heaps-and-greedy](DSA/heaps-and-greedy.md) | Heap / Priority Queue · Top-K · Greedy | ⚪ |
| [graphs](DSA/graphs.md) | BFS/DFS · Shortest Path · **Union-Find & MST** · Topological Sort | 🟡 |
| [recursion-and-backtracking](DSA/recursion-and-backtracking.md) | **Recursion** · **Backtracking** · **Subsets & Permutations** · Divide & Conquer | 🟡 |
| [dynamic-programming](DSA/dynamic-programming.md) | DP Basics · **1D DP (Max Product Subarray)** · 2D/Knapsack · DP on Strings | 🟡 |
| [bit-and-math](DSA/bit-and-math.md) | Bit Manipulation · **Number Theory (Primes/Sieve)** · **Math Tricks** | 🟡 |

### DSA — Topic Details (routing index)

Only written topics are listed. If incoming content matches one of these, extend
it — don't create a new topic or file. Unlisted/⚪ topics in the table above have
no content yet, so any new material for them just fills the stub.

- **arrays-searching-sorting.md → Sorting** — Merge Sort & Inversion Count: divide & conquer, stability, `subList` view gotcha, inversion count via the merge step, `long` overflow, safe comparator (`Integer.compare` vs `a-b`).
- **two-pointers-sliding-window.md → Two Pointers** — converging L/R on sorted arrays; hash-set vs hash-map choice; duplicate handling (distinct-value dedupe vs index-pair nC2/nP2); pairs with given difference; pairs with given sum (duplicates counted); pointer-invariant / "don't skip candidates" pitfalls; `Integer ==` vs `.equals()`.
- **hashing-and-strings.md → Frequency Patterns** — nC2 / nP2 / n² pair-counting formulas; on-the-fly vs batch frequency counting; cast-before-multiply overflow; `StringBuilder` vs `String` concatenation.
- **linkedlist-stack-queue.md → Queue & Deque** — BFS via Queue; `ArrayDeque` vs `LinkedList`; mark-visited-on-enqueue; level-order size-snapshot pattern.
- **graphs.md → Union-Find & MST** — Prim's vs Kruskal's; path compression + union by rank; cycle detection via Union-Find.
- **recursion-and-backtracking.md → Recursion** — base/recursive case; DFS-vs-backtracking distinction.
- **recursion-and-backtracking.md → Backtracking** — choose → explore → un-choose template; Generate Parentheses (open/close bracket guards); beginner Java syntax traps (`""` vs `''`, void helper + shared list vs return-at-every-branch).
- **recursion-and-backtracking.md → Subsets & Permutations** — include/exclude template (Subsets, Subset Sum) vs `visited[]` template (Permutations); grid paths (Down/Right only); steps (1-or-2 climbing); "try the smaller choice first" for free lexicographic order; Java collection-API traps (`.length()`/`.size()`/`.length`, `=` vs `==`, `List.remove(int)` by-index, `new ArrayList<Integer>()` syntax).
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
