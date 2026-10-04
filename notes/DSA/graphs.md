---
title: Graphs
subject: DSA
type: subbucket
tags: [graph, bfs, dfs, shortest-path, union-find, topological-sort, mst, prims, kruskals, greedy]
topics: [BFS & DFS, Shortest Path, Union-Find & MST, Topological Sort]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-09-07
reviewed: 2026-08-09
source: lecture notes
related: [linkedlist-stack-queue, heaps-and-greedy, trees-and-bst, recursion-and-backtracking]
status: partial
---

# Graphs

> Modeling entities and their relationships. Traversal (BFS/DFS) is the base;
> on top sit shortest paths, connectivity (Union-Find), ordering (topological
> sort), and minimum-cost connection (MST). Most "connect / reach / order / group"
> problems reduce to one of these.

**Topics:** [BFS & DFS](#bfs--dfs) · Shortest Path · [Union-Find & MST](#union-find--minimum-spanning-tree) · Topological Sort

---

## BFS & DFS

> BFS explores level by level via a `Queue`; DFS explores depth-first via a
> `Stack`/recursion. On an **unweighted** grid or graph, BFS's level-by-level
> order guarantees the *first* time you reach a target is via the *shortest*
> path — that guarantee is exactly why grid shortest-path problems reach for
> BFS, not DFS/backtracking.

### Core Concept

- **DFS/backtracking** = explore paths (all of them, or until one is proven
  invalid) — see [Recursion & Backtracking](recursion-and-backtracking.md).
- **BFS** = shortest path in an unweighted graph/grid — every edge/move costs
  the same (1), so the number of levels expanded equals distance travelled.
- Adjacency list vs matrix: list for sparse graphs (most interview problems),
  matrix for dense graphs or when O(1) edge-existence lookup matters.

### Details / Walkthrough — Binary Maze (shortest path in a 0/1 grid)

> Given a grid of `0`s and `1`s, find the shortest path (in number of moves)
> from a source cell to a destination cell, moving through cells with value
> `1` only, in 4 directions.

**Recognize the pattern:** grid + shortest path + every move costs 1 → BFS,
not DFS/backtracking.

```java
int shortestPath(List<List<Integer>> grid, int srcR, int srcC, int dstR, int dstC) {
    int rows = grid.size(), cols = grid.get(0).size();
    boolean[][] visited = new boolean[rows][cols];
    int[][] dir = {{-1,0},{1,0},{0,-1},{0,1}};   // up, down, left, right

    Queue<int[]> q = new LinkedList<>();          // {row, col, dist}
    q.add(new int[]{srcR, srcC, 0});
    visited[srcR][srcC] = true;

    while (!q.isEmpty()) {
        int[] curr = q.poll();
        int r = curr[0], c = curr[1], dist = curr[2];
        if (r == dstR && c == dstC) return dist;

        for (int[] d : dir) {
            int nr = r + d[0], nc = c + d[1];
            boolean inGrid = nr >= 0 && nr < rows && nc >= 0 && nc < cols;
            if (inGrid && grid.get(nr).get(nc) == 1 && !visited[nr][nc]) {
                visited[nr][nc] = true;           // mark visited on enqueue, not dequeue
                q.add(new int[]{nr, nc, dist + 1});
            }
        }
    }
    return -1;   // destination unreachable
}
```

**Mental skeleton** (write this before any helper function):

```
queue = {source, dist=0}; visited[source] = true
while queue not empty:
    take one cell
    if destination: return dist
    for each of 4 directions:
        compute neighbour
        if in-bounds AND cell==1 AND not visited:
            mark visited
            enqueue neighbour with dist+1
return -1
```

**Why marking visited on *enqueue* (not dequeue) matters:** the queue can
hold multiple pending references to the same cell before it's processed if
you defer the check — marking at enqueue time guarantees each cell is queued
exactly once, keeping the algorithm O(rows·cols) instead of blowing up with
duplicate entries.

### Examples

3×3 grid `[[1,0,0],[1,1,0],[0,1,1]]`, source `(0,0)`, destination `(2,2)`:
BFS expands `(0,0)` → `(1,0)` → `(1,1)` → `(2,1)` → `(2,2)`, so the first time
`(2,2)` is dequeued, `dist = 4` — guaranteed shortest because BFS exhausts
every distance-`k` cell before touching any distance-`(k+1)` cell.

### Common Mistakes / Edge Cases

1. **Using DFS/recursion for "shortest path"** — DFS finds *a* path, not
   necessarily the *shortest* one, without extra bookkeeping; BFS gets the
   shortest path for free from its level-order exploration.
2. **Marking visited on dequeue instead of enqueue** — lets the same cell be
   added to the queue multiple times before it's first processed, wasting
   work (and in the worst case still gives the right answer but far slower).
3. **`int` passed by value to a helper doesn't mutate the caller's copy** —
   if you refactor into `helper(..., int dist)`, changing `dist` inside the
   helper never changes the caller's variable; the distance has to travel via
   the queue's per-cell state (`int[]{r, c, dist}`) or a return value, not a
   mutated parameter.
4. **Forgetting the in-bounds check before indexing the grid** — checking
   `grid.get(nr).get(nc) == 1` before `nr/nc` are validated throws an
   index-out-of-bounds exception; always short-circuit bounds first (Java's
   `&&` short-circuits left-to-right, so order the condition as shown above).
5. **Source == destination** — return `0` immediately; the BFS loop handles
   this correctly since the check happens right after dequeuing, before
   trying any neighbours, but it's worth stating out loud as an edge case.

### Interview Angle

**How interviewers test this:**
- Direct: "shortest path in a binary matrix" (LC 1091-style), "rotting
  oranges" (multi-source BFS), "word ladder" (BFS over a word graph).
- Follow-up: "what if some cells cost more to enter than others?" → no longer
  plain BFS — needs Dijkstra (see Shortest Path below).
- Follow-up: "multiple sources at once?" → seed the queue with *all* sources
  at `dist=0` before starting the loop — the BFS mechanics don't change.

**Coding habit worth stating out loud:** don't create a `helper()` just
because you see a loop — for BFS, the `while(queue...)` loop usually *is* the
whole algorithm; a helper is more natural for DFS/recursion, where the
function itself represents "solve from this state" (`helper(row, col, ...)`).
Decide "am I repeating the same logic?" or "does this deserve its own single
responsibility?" before extracting one — don't design the functions before
the algorithm.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "shortest path", "fewest steps/moves", "minimum distance" + unweighted | BFS with a `Queue<int[]>` of `{row, col, dist}` |
| "grid of 0s/1s, 4-directional movement" | `dir[][] = {{-1,0},{1,0},{0,-1},{0,1}}` loop over neighbours |
| "explore all paths", "generate every path" | DFS/backtracking instead — see [Recursion & Backtracking](recursion-and-backtracking.md) |
| "multiple starting points" | Multi-source BFS: seed all sources into the queue at dist 0 |
| "edges have different costs/weights" | Not plain BFS — Dijkstra (below) |

---

## Shortest Path

_Not yet written._ (Weighted non-negative → Dijkstra (PQ); negative edges →
Bellman-Ford; all-pairs → Floyd-Warshall. The **unweighted** case — plain
BFS on a grid/graph — is covered above under [BFS & DFS](#bfs--dfs).)

---

## Union-Find & Minimum Spanning Tree

> Connect all nodes in a weighted undirected graph with the minimum total edge
> weight and no cycles — the result is a tree (V-1 edges for V vertices). Two
> classic greedy algorithms: **Prim's** (grow one tree) and **Kruskal's** (merge
> forests). Union-Find is the data structure that makes Kruskal's efficient and
> answers connectivity/cycle queries in near-constant time.

### Core Concept

**Spanning Tree**: subgraph connecting all V vertices with exactly V-1 edges, no cycles.
**Minimum Spanning Tree**: the spanning tree with smallest total weight (cost is
unique; the tree may not be).

**Cut Property**: for any cut splitting the graph in two, the minimum-weight edge
crossing the cut must be in the MST.

### Prim's Algorithm

**Strategy**: start from any vertex, greedily grow the MST by adding the cheapest
edge connecting the tree to an unvisited vertex. *"Grow one tree, pick the cheapest bridge."*

```java
class Edge implements Comparable<Edge> {
    int weight, src, dest;
    Edge(int w, int s, int d) { weight = w; src = s; dest = d; }
    public int compareTo(Edge o) { return this.weight - o.weight; }
}

int primMST(List<List<Edge>> adj, int V) {
    boolean[] visited = new boolean[V];
    PriorityQueue<Edge> pq = new PriorityQueue<>();
    int totalCost = 0, edgesAdded = 0;

    visited[0] = true;
    for (Edge e : adj.get(0)) pq.offer(e);

    while (!pq.isEmpty() && edgesAdded < V - 1) {
        Edge minEdge = pq.poll();
        if (visited[minEdge.dest]) continue;   // skip if already in MST
        visited[minEdge.dest] = true;
        totalCost += minEdge.weight;
        edgesAdded++;
        for (Edge e : adj.get(minEdge.dest))
            if (!visited[e.dest]) pq.offer(e);
    }
    return totalCost;
}
```

**Complexity**: Time O(E log E) (or O(E log V) with indexed PQ), Space O(V + E).

### Kruskal's Algorithm

**Strategy**: sort all edges by weight; greedily pick the smallest that doesn't
form a cycle (checked via Union-Find). *"Sort edges, merge forests, Union-Find blocks cycles."*

```java
int kruskalMST(int[][] edges, int V) {   // edges[i] = {weight, u, v}
    Arrays.sort(edges, (a, b) -> a[0] - b[0]);
    int[] parent = new int[V], rank = new int[V];
    for (int i = 0; i < V; i++) parent[i] = i;

    int totalCost = 0, edgesAdded = 0;
    for (int[] edge : edges) {
        if (edgesAdded == V - 1) break;
        int w = edge[0], u = edge[1], v = edge[2];
        int rootU = find(parent, u), rootV = find(parent, v);
        if (rootU != rootV) {
            union(parent, rank, rootU, rootV);
            totalCost += w;
            edgesAdded++;
        }
    }
    return totalCost;
}
```

**Complexity**: Time O(E log E) (dominated by sorting), Space O(V).

### Union-Find (Disjoint Set Union)

Answers "are these two nodes in the same component?" in near-constant time.

```java
int find(int[] parent, int x) {
    if (parent[x] != x) parent[x] = find(parent, parent[x]);  // path compression
    return parent[x];
}

void union(int[] parent, int[] rank, int a, int b) {
    int rootA = find(parent, a), rootB = find(parent, b);
    if (rootA == rootB) return;
    if (rank[rootA] < rank[rootB])      parent[rootA] = rootB;   // union by rank
    else if (rank[rootA] > rank[rootB]) parent[rootB] = rootA;
    else { parent[rootB] = rootA; rank[rootA]++; }
}
```

- **Initialization**: `parent[i] = i` (each node is its own root).
- **Path compression** flattens the tree so future lookups are ~O(1).
- **Union by rank** keeps trees balanced.
- **Amortized**: O(α(N)) per op (inverse Ackermann — effectively O(1)).

### Prim's vs Kruskal's

| Factor          | Prim's                 | Kruskal's                 |
|-----------------|------------------------|---------------------------|
| Best for        | Dense graphs (E ~ V²)  | Sparse graphs (E ~ V)     |
| Data structure  | Priority Queue         | Union-Find + sorted edges |
| Needs adjacency | Yes                    | No (edge list is fine)    |
| Detects components | Not directly        | Yes (via Union-Find)      |

### Example

Edges: (A-B,1), (B-D,2), (C-D,3), (A-C,4).

**Kruskal's**: add A-B(1) → B-D(2) → C-D(3) → skip A-C(4, already connected).
**Prim's from A**: pop A-B(1) → B-D(2) → C-D(3).
**MST cost** = 1 + 2 + 3 = **6** (both agree).

### Common Mistakes / Edge Cases

1. **Forgetting `visited` check in Prim's** — adding an edge to a visited node makes a cycle.
2. **Not initializing `parent[i] = i`** in Union-Find.
3. **Disconnected graph** — MST needs connectivity; else you get a Minimum Spanning *Forest*. Check `edgesAdded == V - 1`.
4. **Parallel edges** — handled (both pick the minimum).
5. **Self-loops** — filter edges where `u == v`.

### Interview Angle

**How interviewers test this:**
- "Minimum cost to connect all cities / network cables" — direct MST.
- "Can you connect all nodes? Minimum cost?" — connectivity + MST.
- "Number of connected components" / "detect cycle in undirected graph" — Union-Find.
- Follow-up: "Second-best MST?", "must include/exclude certain edges?" — modified Kruskal's.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem            | Think of This Pattern         |
|--------------------------------------------|-------------------------------|
| "connect all nodes with minimum cost"      | MST (Prim's or Kruskal's)     |
| "minimum spanning / wiring / cable"        | MST                           |
| "are nodes in the same component?"         | Union-Find                    |
| "detect cycle in undirected graph"         | Union-Find                    |
| "dense graph, minimum connectivity"        | Prim's (adjacency list + PQ)  |
| "sparse graph, edge list given"            | Kruskal's (sort + Union-Find) |
| "number of connected components"           | Union-Find count              |

---

## Topological Sort

_Not yet written._ (DAG ordering; Kahn's BFS with in-degrees, or DFS post-order;
course-schedule / build-order / dependency problems; detects cycles in directed graphs.)

---

## Related Notes

- [Linked List, Stack & Queue](linkedlist-stack-queue.md) — BFS uses a Queue, DFS uses a Stack/recursion
- [Heaps & Greedy](heaps-and-greedy.md) — Prim's & Dijkstra use a PriorityQueue; MST is a greedy algorithm
- [Trees & BST](trees-and-bst.md) — a tree is an acyclic connected graph; traversals overlap
- [Recursion & Backtracking](recursion-and-backtracking.md) — backtracking is DFS with pruning; grid-path problems (move Down/Right) are a special case of graph/grid traversal

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#graphs)
