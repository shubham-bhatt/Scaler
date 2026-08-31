---
title: Graphs
subject: DSA
type: subbucket
tags: [graph, bfs, dfs, shortest-path, union-find, topological-sort, mst, prims, kruskals, greedy]
topics: [BFS & DFS, Shortest Path, Union-Find & MST, Topological Sort]
difficulty: hard
frequency: high
created: 2026-08-20
updated: 2026-08-20
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

**Topics:** BFS & DFS · Shortest Path · [Union-Find & MST](#union-find--minimum-spanning-tree) · Topological Sort

---

## BFS & DFS

_Not yet written._ (Adjacency list vs matrix; BFS = Queue for shortest hops; DFS =
recursion/Stack for connectivity, cycle detection, components; grid variants.)

---

## Shortest Path

_Not yet written._ (Unweighted → BFS; weighted non-negative → Dijkstra (PQ);
negative edges → Bellman-Ford; all-pairs → Floyd-Warshall.)

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
