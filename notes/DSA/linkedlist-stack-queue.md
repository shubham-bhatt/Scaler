---
title: Linked List, Stack & Queue
subject: DSA
type: subbucket
tags: [linked-list, stack, queue, deque, monotonic-stack, bfs, fifo, lifo]
topics: [Linked List, Stack, Queue & Deque, Monotonic Stack]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-20
reviewed: 2026-08-09
source: lecture notes
related: [two-pointers-sliding-window, trees-and-bst, graphs]
status: partial
---

# Linked List, Stack & Queue

> The core linear ADTs. Stacks (LIFO) drive DFS, expression parsing, and monotonic
> patterns; queues (FIFO) drive BFS and level-order traversal; deques do both ends;
> linked lists underpin them and host the fast/slow pointer tricks.

**Topics:** Linked List · Stack · [Queue & Deque](#queue--deque) · Monotonic Stack

---

## Linked List

_Not yet written._ (Singly/doubly, reversal, cycle detection via fast/slow — see
[Two Pointers & Sliding Window](two-pointers-sliding-window.md), merge two lists,
LRU cache with doubly linked list + hashmap.)

---

## Stack

_Not yet written._ (LIFO, balanced parentheses, min-stack, expression eval, DFS
iterative, stack = call stack in recursion.)

---

## Queue & Deque

> A Queue is a linear FIFO (First-In, First-Out) structure — added at the back,
> removed from the front. It is the backbone of BFS and level-order traversals.

### Core Concept

Three primary operations, all **O(1)**:
- **Enqueue (offer)** — add to the back.
- **Dequeue (poll)** — remove and return the front.
- **Peek** — view the front without removing.

**Why FIFO matters**: processing elements in arrival order ensures fairness and
correct level ordering in tree/graph traversals.

### Java Queue Interface

`Queue` is an interface; the common implementation is `LinkedList`:

```java
Queue<Integer> queue = new LinkedList<>();
```

| Method     | Behavior                  | On Empty Queue |
|------------|---------------------------|----------------|
| `offer(e)` | Add to back, returns true | N/A            |
| `poll()`   | Remove & return front     | Returns `null` |
| `peek()`   | View front without remove | Returns `null` |
| `add(e)`   | Add to back               | Throws if capacity-bounded |
| `remove()` | Remove & return front     | Throws `NoSuchElementException` |
| `element()`| View front               | Throws `NoSuchElementException` |

**Best practice**: prefer `offer/poll/peek` — they return `null` on failure
instead of throwing, so null-checks are cleaner than try-catch.

### BFS Template (Grid)

The most important application of Queue:

```java
Queue<int[]> queue = new LinkedList<>();
boolean[][] visited = new boolean[rows][cols];
queue.offer(new int[]{startRow, startCol});
visited[startRow][startCol] = true;
int[][] directions = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

while (!queue.isEmpty()) {
    int[] curr = queue.poll();
    int r = curr[0], c = curr[1];
    for (int[] dir : directions) {
        int nr = r + dir[0], nc = c + dir[1];
        if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && !visited[nr][nc]) {
            visited[nr][nc] = true;   // mark BEFORE enqueue
            queue.offer(new int[]{nr, nc});
        }
    }
}
```

### Tree Level-Order Traversal

```java
Queue<TreeNode> queue = new LinkedList<>();
queue.offer(root);
List<List<Integer>> levels = new ArrayList<>();
while (!queue.isEmpty()) {
    int size = queue.size();  // capture BEFORE the inner loop
    List<Integer> level = new ArrayList<>();
    for (int i = 0; i < size; i++) {
        TreeNode node = queue.poll();
        level.add(node.val);
        if (node.left != null)  queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
    levels.add(level);
}
```

**Critical**: capture `queue.size()` before the inner loop — it changes as you add children.

### Variants & Complexity

| Type           | Description                    | Java Class            |
|----------------|--------------------------------|-----------------------|
| Simple Queue   | FIFO                           | `LinkedList`          |
| Priority Queue | Min/max element dequeued first | `PriorityQueue`       |
| Deque          | Insert/remove at both ends     | `ArrayDeque`          |
| Circular Queue | Fixed-size, wraps around       | Custom / `ArrayDeque` |

**Tip**: `ArrayDeque` is faster than `LinkedList` for queue ops (cache locality).
Use `LinkedList` only when you need `null` elements (ArrayDeque forbids nulls).

### Example: Rotten Oranges (Multi-source BFS)

Grid where 2 = rotten, 1 = fresh, 0 = empty; minutes until all rot (or -1):

```java
Queue<int[]> queue = new LinkedList<>();
int fresh = 0;
for (int i = 0; i < grid.length; i++)
    for (int j = 0; j < grid[0].length; j++) {
        if (grid[i][j] == 2) queue.offer(new int[]{i, j});
        else if (grid[i][j] == 1) fresh++;
    }
int minutes = 0;
int[][] dirs = {{0,1},{0,-1},{1,0},{-1,0}};
while (!queue.isEmpty() && fresh > 0) {
    int size = queue.size();
    for (int i = 0; i < size; i++) {
        int[] cell = queue.poll();
        for (int[] d : dirs) {
            int nr = cell[0] + d[0], nc = cell[1] + d[1];
            if (nr >= 0 && nr < grid.length && nc >= 0 && nc < grid[0].length
                    && grid[nr][nc] == 1) {
                grid[nr][nc] = 2; fresh--;
                queue.offer(new int[]{nr, nc});
            }
        }
    }
    minutes++;
}
return fresh == 0 ? minutes : -1;
```

### Common Mistakes / Edge Cases

1. **Not null-checking `poll()`/`peek()`** — they return `null` on empty → NPE.
2. **Modifying `queue.size()` during iteration** — capture size before the loop.
3. **Marking visited on dequeue instead of enqueue** — causes duplicate enqueues.
4. **Using `add()` instead of `offer()`** on capacity-bounded queues (throws).
5. **Confusing Queue with Stack** — Queue = FIFO (BFS); Stack = LIFO (DFS).

### Interview Angle

**How interviewers test this:**
- BFS on grids (shortest path, rotten oranges, nearest gate) and graphs (word ladder).
- Level-order tree traversal variants (zigzag, right-side view).
- Sliding window maximum (monotonic **Deque**).
- Follow-up: "shortest path in **weighted** graph?" → Dijkstra (PriorityQueue).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem          | Think of This Pattern      |
|------------------------------------------|----------------------------|
| "shortest path in unweighted graph/grid" | BFS with Queue             |
| "level-order", "layer by layer"          | BFS with size-based levels |
| "spread from multiple sources"           | Multi-source BFS           |
| "process in order of arrival"            | FIFO Queue                 |
| "sliding window maximum/minimum"         | Monotonic Deque            |
| "nearest", "minimum steps"               | BFS                        |

---

## Monotonic Stack

_Not yet written._ (Next greater/smaller element, largest rectangle in histogram,
stock span — maintain an increasing/decreasing stack; also monotonic deque for
sliding-window max.)

---

## Related Notes

- [Two Pointers & Sliding Window](two-pointers-sliding-window.md) — fast/slow pointers on linked lists; monotonic deque for window maximum
- [Trees & BST](trees-and-bst.md) — BFS level-order uses a Queue; DFS uses a Stack/recursion
- [Graphs](graphs.md) — BFS uses a plain Queue, Dijkstra/Prim use a PriorityQueue

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#linked-list-stack--queue)
