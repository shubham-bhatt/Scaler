---
title: Recursion & Backtracking
subject: DSA
type: subbucket
tags: [recursion, backtracking, subsets, permutations, divide-and-conquer]
topics: [Recursion, Backtracking, Subsets & Permutations, Divide & Conquer]
difficulty: medium
frequency: high
created: 2026-08-20
updated: 2026-08-31
reviewed: 2026-08-20
source: lecture notes
related: [dynamic-programming, trees-and-bst, arrays-searching-sorting, two-pointers-sliding-window, graphs]
status: partial
---

# Recursion & Backtracking

> Recursion solves a problem in terms of smaller instances of itself; backtracking
> is recursion that explores choices, then undoes them to try alternatives. Together
> they generate combinatorial spaces (subsets, permutations, board placements) and
> form the brute-force skeleton that DP later memoizes.

**Topics:** Recursion · Backtracking · Subsets & Permutations · Divide & Conquer

---

## Recursion

> A function that calls itself on a smaller instance of the same problem, with
> a base case that stops it. _(Converting recursion to iteration, tail
> recursion, and stack-overflow limits are the next layer to add here.)_

### Core Concept

Every recursive function needs a **base case** (when to stop) and a
**recursive case** (how to shrink the problem and combine sub-results). The
call stack tracks pending work — each call waits for its recursive call(s) to
return before finishing its own work, or "backtracks" if it made a choice it
needs to undo (see [Backtracking](#backtracking) below).

### Details / Walkthrough

Two traversal ideas both build on recursion, and it's easy to conflate them:

- **DFS** is a *traversal* technique — visit every reachable node/state, depth-first.
- **Backtracking** is a *pruning strategy layered on top of* DFS/recursion — the
  moment the current partial path is proven impossible (or a full solution is
  found), stop exploring that branch immediately and return to try the next
  option, instead of exhausting it first.

Concrete illustration: searching a tree of letters for the word "AIM". Plain
DFS would fully traverse every branch and check only at the leaf. Backtracking
abandons a branch the instant the next required letter isn't there — e.g. if
the current node's children are `N`, `I`, checking for `I` and immediately
discarding the `N` branch without descending into it at all.

> **Open question worth revisiting:** BFS vs DFS — when is one preferable to
> the other? Short answer: BFS (Queue, level-order) suits shortest-path /
> "fewest steps" problems; DFS (Stack / call stack) suits exhaustive
> exploration and backtracking. See [Graphs](graphs.md) for the mechanics of both.

### Examples

See [Backtracking](#backtracking) below for a fully worked example (Generate
Parentheses) — recursion's structure is best seen in a concrete choose/explore
tree rather than in the abstract.

### Common Mistakes / Edge Cases

1. **Missing or wrong base case** — infinite recursion / stack overflow.
2. **Mutating shared state without undoing it** — the single most common
   recursion bug in interviews; see Backtracking's "un-choose" step below.

### Interview Angle

**How interviewers test this:** ask you to state the base case and recursive
case explicitly *before* writing code, then ask for the recursion tree and its
time/space complexity.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "in terms of a smaller version of itself" | Plain recursion |
| "explore all possibilities", "generate all" | Backtracking |
| "shortest path", "fewest steps", "level by level" | BFS (iterative, Queue) — see [Graphs](graphs.md) |

---

## Backtracking

> Recursion that tries a choice, recurses, then *undoes* the choice before
> trying the next one — the "choose → explore → un-choose" template.
> _(N-Queens, Sudoku, word search, combination sum are the next layer to add here.)_

### Core Concept

```
void backtrack(state) {
    if (state is a full solution) { record it; return; }
    for (each choice available from state) {
        make the choice;              // choose
        backtrack(new state);          // explore
        undo the choice;               // un-choose — restores state for the next sibling
    }
}
```
The "un-choose" step is what makes this *backtracking* rather than plain DFS —
the state object is reused/mutated in place across sibling branches, so it
must be restored to exactly what it was before trying the next option.

### Details / Walkthrough — Generate Parentheses

> Given `A` pairs of parentheses, generate all combinations of well-formed
> parentheses of length `2*A`.

State: the string built so far, plus counts of `(` used (`open`) and `)` used
(`close`). Two choices at each step, each gated by a validity rule:

- Add `(` only if `open < A` (never use more than `A` open brackets — only `A`
  are available in total).
- Add `)` only if `close < open` (never close more than is currently open —
  keeps every prefix valid).
- Base case: `open == A && close == A` → record the string.

```java
List<String> generateParenthesis(int A) {
    List<String> result = new ArrayList<>();
    solve(new StringBuilder(), A, 0, 0, result);
    return result;
}

void solve(StringBuilder sb, int A, int open, int close, List<String> result) {
    if (open == A && close == A) {
        result.add(sb.toString());
        return;
    }
    if (open < A) {
        sb.append('(');
        solve(sb, A, open + 1, close, result);
        sb.deleteCharAt(sb.length() - 1);        // un-choose
    }
    if (close < open) {
        sb.append(')');
        solve(sb, A, open, close + 1, result);
        sb.deleteCharAt(sb.length() - 1);        // un-choose
    }
}
```

*Fix vs. the rough lecture sketch:* the original pseudocode built a new string
per call (`str + '('`) — correct, but O(N) per call since `String` is
immutable. A single shared `StringBuilder` with append/`deleteCharAt` makes
each choose/un-choose O(1), the idiomatic backtracking pattern.

**Complexity:** the recursion branches at most 2 ways per level, `2*A` levels
deep → **O(2^(2A))** states explored in the worst case (the tight bound is the
Catalan number `C(A)`, but "exponential, roughly `O(2^N)`" is the accepted
interview-level answer), **O(N) space** for recursion depth + the string being
built (excluding the output list).

### Examples

`A=2` → the choice tree (each level tries `(` then `)`, skipping invalid moves):

```
                       ""
                  (           [')' skipped: close(0) !< open(0)]
              ((       ()
        [')'skip]    (()          ()(
                       ↓            ↓
                     (())         ()()
```
Valid outputs: `["(())", "()()"]`.

### Common Mistakes / Edge Cases

1. **Forgetting the un-choose step** — without `deleteCharAt`, the shared
   `StringBuilder` keeps growing across sibling branches and every recorded
   result is corrupted.
2. **Wrong guard order** — checking `close < open` (not `close < A`) is what
   keeps every *partial* prefix valid, not just the final string.
3. **Rebuilding a new `String` per call** (`str + '('`) — works but is O(N)
   per append; prefer one mutable `StringBuilder` shared across the recursion.
4. **`A = 0`** — should return `[""]`, not an empty list; the base case
   `open==0 && close==0` is satisfied immediately.

### Interview Angle

**How interviewers test this:**
- Direct: "Generate Parentheses" (LC 22).
- Follow-up: "Just count them, don't generate?" → closed-form Catalan number
  `C(2A, A) / (A+1)`, no recursion needed.
- Follow-up: "Validate a given parenthesis string?" → a different problem
  entirely (stack-based, O(N), not backtracking).

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "generate all combinations/well-formed X" | Backtracking, choose → explore → un-choose |
| "A pairs, length 2A" | Track open/close counts, prune with guards early |
| "count only, don't enumerate" | Closed-form (e.g. Catalan) instead of recursion |

---

## Subsets & Permutations

> The two canonical "explore every combinatorial arrangement" backtracking
> templates — subsets: each *element* has an include/exclude choice;
> permutations: each *position* picks from the elements not yet used.

### Core Concept

| | Subsets | Permutations |
|---|---|---|
| Choice at each step | include **or** exclude the current element | pick any **unused** element for the current position |
| Branching factor | 2 per element | shrinks: N, then N-1, then N-2… |
| Output count | 2^N | N! |
| Needs a `visited[]` tracker? | no — the index just advances | **yes** — must know which elements are already placed |

### Details / Walkthrough — Subsets

> Given `arr = [10, 20, 30]`, generate every subset. A subset has no order — it's
> defined purely by *which* elements it contains, not their sequence.

```java
List<List<Integer>> subsets(int[] arr) {
    List<List<Integer>> result = new ArrayList<>();
    solve(arr, 0, new ArrayList<>(), result);
    return result;
}

void solve(int[] arr, int idx, List<Integer> curr, List<List<Integer>> result) {
    if (idx == arr.length) {
        result.add(new ArrayList<>(curr));    // copy — curr keeps mutating after this
        return;
    }
    solve(arr, idx + 1, curr, result);         // exclude arr[idx]
    curr.add(arr[idx]);                        // choose
    solve(arr, idx + 1, curr, result);         // include arr[idx]
    curr.remove(curr.size() - 1);              // un-choose — restores curr for the caller
}
```
**Why `new ArrayList<>(curr)` when recording?** `curr` is one shared, mutating
list reused across the whole recursion. Storing a reference to it directly
means every recorded subset ends up pointing at the *same* final (empty) list
once backtracking fully unwinds — you must copy at the moment of recording.

**Dry-run trace** (`idx`, running subset) — the exclude-branch is always fully
explored before the include-branch at each node:
```
solve(idx=0, {})
├─ solve(idx=1, {})                    exclude arr[0]
│  ├─ solve(idx=2, {})                 exclude arr[1]
│  │  ├─ idx=3 → record {}
│  │  └─ curr=[30]; idx=3 → record {30}
│  └─ curr=[20]; solve(idx=2, {20})    include arr[1]
│     ├─ idx=3 → record {20}
│     └─ curr=[20,30]; idx=3 → record {20,30}
└─ curr=[10]; solve(idx=1, {10})       include arr[0]
   ├─ solve(idx=2, {10})               exclude arr[1]
   │  ├─ idx=3 → record {10}
   │  └─ curr=[10,30]; idx=3 → record {10,30}
   └─ curr=[10,20]; solve(idx=2, {10,20})  include arr[1]
      ├─ idx=3 → record {10,20}
      └─ curr=[10,20,30]; idx=3 → record {10,20,30}
```
8 subsets total = 2³, matching the branching-factor table above.

**Complexity:** O(2^N) subsets generated, each up to O(N) to copy →
**O(N · 2^N) time, O(N) recursion depth** (excluding the space to store the output).

### Details / Walkthrough — Permutations

> Given a string `S`, print all permutations (e.g. `"abc"` → `abc, acb, bac,
> bca, cab, cba`) — N! permutations, since every position independently picks
> from the characters not yet used elsewhere, which is exactly why a
> `visited[]` array is required here but not for Subsets.

```java
void permute(char[] s, StringBuilder curr, boolean[] visited, List<String> result) {
    if (curr.length() == s.length) {
        result.add(curr.toString());
        return;
    }
    for (int i = 0; i < s.length; i++) {
        if (visited[i]) continue;
        visited[i] = true;
        curr.append(s[i]);
        permute(s, curr, visited, result);
        curr.deleteCharAt(curr.length() - 1);   // un-choose
        visited[i] = false;                      // un-choose
    }
}
```

**Resolved dry-run trace for `"abc"`** — this writes out the hand-drawn trace
from the lecture (the one the rough notes flagged wanting "a map like the
attached image" for) as an explicit call-by-call table, in the exact order the
call stack unwinds:

| Depth | `curr` | `visited` | What happens |
|---|---|---|---|
| 0 | `""` | `[0,0,0]` | loop `i=0`: pick `'a'` |
| 1 | `"a"` | `[1,0,0]` | loop `i=0` visited→skip; `i=1`: pick `'b'` |
| 2 | `"ab"` | `[1,1,0]` | loop `i=2`: pick `'c'` |
| 3 | `"abc"` | `[1,1,1]` | length==3 → **record "abc"**, return |
| ↩ back to depth 2 | `"ab"` | `[1,1,0]` | un-choose `'c'`; loop ends (no more `i`) → un-choose `'b'`, return |
| ↩ back to depth 1 | `"a"` | `[1,0,0]` | `i=1` done; `i=2`: pick `'c'` |
| 2 | `"ac"` | `[1,0,1]` | loop `i=1`: pick `'b'` |
| 3 | `"acb"` | `[1,1,1]` | **record "acb"**, return |
| ↩ unwind fully to depth 0 | `""` | `[0,0,0]` | `i=0` done, un-choose `'a'`; `i=1`: pick `'b'` → symmetric branch produces `bac, bca`; then `i=2`: pick `'c'` → produces `cab, cba` |

**Answering the lecture's doubt directly** — *"why does the string return to
its previous state after the recursive call, if I built it with
`ans = ans + str[i]`?"* — it doesn't automatically; nothing reverts on its
own. `curr.deleteCharAt(...)` after the recursive call **explicitly** removes
the character that was appended before the call. `curr` is one shared mutable
object across the entire recursion (not a fresh copy handed to each call), so
the un-choose line is the only thing undoing it — exactly parallel to
`visited[i] = false` on the next line, which undoes the other half of `choose`.

**What the loop variable `i` is really for** (the note's second doubt, about
why `idx` is needed): `i` is **not** a single pointer shared across the whole
recursion — it's the loop variable local to *each call's own* for-loop,
freshly re-scoped at every recursion depth. At depth 1 (`curr="a"`), the loop
tries `i=0,1,2` for the *second* character; at depth 2 (`curr="ab"`), a
completely separate loop tries `i=0,1,2` for the *third* character.
`visited[]` — not `i` — is the thing that's actually shared across calls and
must be explicitly restored; `i` needs no restoring, because each call's loop
already forgets its own `i` the moment that call returns.

**Complexity:** O(N!) permutations, O(N) to build each → **O(N · N!) time,
O(N) recursion depth**.

### Details / Walkthrough — Subset Sum (count subsets summing to K)

Same include/exclude shape as Subsets above, but the base case checks a
running sum instead of recording every subset:

```java
int subsetSumCount(int[] arr, int idx, int currSum, int k) {
    if (idx == arr.length) {
        return currSum == k ? 1 : 0;
    }
    int exclude = subsetSumCount(arr, idx + 1, currSum, k);
    int include = subsetSumCount(arr, idx + 1, currSum + arr[idx], k);
    return exclude + include;
}
```
No explicit un-choose here — `currSum` is passed **by value** (a primitive
`int`), so each branch automatically gets its own copy; there's nothing shared
to restore, unlike `curr`/`visited` above which are shared mutable objects.
This is the key distinction to notice across all these problems: value
parameters self-revert on return, shared reference/mutable state doesn't.

**Complexity:** O(2^N) — identical shape to Subsets, just a different base case.

### Details / Walkthrough — Grid Paths ("move only Down or Right")

> From cell `(0,0)` to `(N-1, M-1)` in an `N×M` grid, print every path using
> only `D` (down) or `R` (right) moves.

```java
void allPaths(int i, int j, String path, int N, int M) {
    if (i == N - 1 && j == M - 1) {
        System.out.println(path);
        return;
    }
    if (i + 1 <= N - 1) allPaths(i + 1, j, path + "D", N, M);
    if (j + 1 <= M - 1) allPaths(i, j + 1, path + "R", N, M);
}
```
- Two choices per cell (down, right) — same choose/explore shape as everything
  above. No explicit un-choose needed here either, since `path` is an
  immutable `String` and each call gets its own copy (same reasoning as
  `currSum` above). Fine at small scale; for large grids prefer a shared
  `StringBuilder` with append/`deleteCharAt`, as in Generate Parentheses, to
  avoid an O(path length) copy per call.
- **Lexicographic ordering falls out for free**: trying `D` before `R` at
  every cell means paths are emitted in lexicographic order automatically —
  "exhaust the smallest/lowest option first" (`D` < `R`) at each branch point.

**Complexity:** every path has exactly `(N-1)+(M-1)` moves, and there are
`C(N+M-2, N-1)` such paths (choosing which moves are `D`) →
**O((N+M) · C(N+M-2, N-1))**, exponential in the worst case.

### Details / Walkthrough — Steps (climb 1 or 2 at a time) *(reconstructed)*

> *The lecture referenced this as one of three problems in an external PDF
> (`DSA__Backtracking_2.pdf`); the rough notes captured only the framing —
> "visualize moving from end to base" and that trying the smaller step first
> gives lexicographic order — with no surviving code. Reconstructed below as
> the standard version of the problem; verify against the source PDF if the
> exact signature there differs.*

Given `N` stairs, print every distinct sequence of 1-steps and 2-steps that
sums to `N` — the same choose/explore/un-choose shape as Grid Paths above:

```java
void allWays(int remaining, String path) {
    if (remaining == 0) {
        System.out.println(path);
        return;
    }
    if (remaining >= 1) allWays(remaining - 1, path + "1");  // smaller step first → lexicographic order
    if (remaining >= 2) allWays(remaining - 2, path + "2");
}
```
Trying the 1-step before the 2-step at every call is the same lexicographic
trick as `D` before `R` in Grid Paths — smallest choice first, consistently,
gives sorted output for free without any extra sorting step afterward.

### Examples

See the dry-run traces embedded in the Subsets and Permutations walkthroughs above.

### Common Mistakes / Edge Cases

1. **Forgetting to copy before recording** (Subsets/Permutations) — storing a
   reference to the shared mutable `curr`/`path` means every recorded result
   silently becomes the same (final, usually empty) object once backtracking
   unwinds. Always `new ArrayList<>(curr)` (or `.toString()` for a
   `StringBuilder`) at the moment of recording.
2. **Skipping the un-choose step for shared mutable state** — required for a
   shared `StringBuilder`/`List`/`visited[]`; **not** required for parameters
   passed by value (`int currSum`, `String path`), since those already get a
   fresh copy per call. Know which kind of parameter you're dealing with
   before deciding whether an un-choose line is needed.
3. **Confusing the Subsets and Permutations shapes** — Subsets: fixed 2
   choices per *element* (in/out), order doesn't matter, no `visited[]`
   needed. Permutations: shrinking choices per *position*, order matters,
   needs `visited[]`.
4. **Base-case index confusion** — Subsets terminates on `idx == arr.length`
   (the element pointer); Permutations terminates on `curr.length() ==
   s.length()` (how many characters have been *placed*, not on any single
   shared index — see the `i` vs "shared pointer" explanation above).
5. **Not pruning early** — these templates as written explore the full tree;
   real interview follow-ups almost always add a constraint (target sum, no
   duplicates, subset size limit) that lets you cut a branch *before*
   recursing into it, not just check it at the leaf.

### Interview Angle

**How interviewers test this:**
- Direct: "Print/return all subsets" (LC 78), "all permutations" (LC 46),
  "subsets that sum to K" (LC 494-style), "print every path" (grid variant of LC 62).
- Follow-up: "duplicates in the input array?" → sort first, then skip over
  equal siblings at the same recursion depth — the identical duplicate-block
  idea as [Two Pointers](two-pointers-sliding-window.md#worked-problem--pairs-with-absolute-difference-b).
- Follow-up: "just the *count*, not every subset/permutation?" → often
  collapses to a DP (subset-sum count → 0/1-knapsack-style DP; see
  [Dynamic Programming](dynamic-programming.md)) instead of full enumeration.
- Follow-up: "only count paths, don't print them?" → closed-form combinatorics
  (`C(N+M-2, N-1)` for the grid), no recursion needed.

**Pattern Recognition — Keywords → Approach**

| Constraint / Keyword in Problem | Think of This Pattern |
|---|---|
| "every subset / power set" | include/exclude recursion, 2 choices per element |
| "every permutation/arrangement" | recursion + `visited[]`, choices shrink per position |
| "subsets/paths summing to target" | include/exclude + running sum, prune when sum exceeds target |
| "grid, only move Down/Right (or similar)" | 2 choices per cell, same template as Generate Parentheses |
| "print in lexicographic/sorted order" | always try the smaller/earlier choice first in the loop |
| "count only, not enumerate" | look for a closed-form or DP instead of full recursion |

---

## Divide & Conquer

_Not yet written._ (Split-solve-combine; merge sort & quicksort, binary search,
majority element, closest pair, master theorem for recurrences.)

---

## Related Notes

- [Dynamic Programming](dynamic-programming.md) — DP = recursion + memoization; every DP begins as a backtracking brute force
- [Trees & BST](trees-and-bst.md) — tree traversals are recursion in its purest form
- [Arrays, Searching & Sorting](arrays-searching-sorting.md) — merge sort & binary search are divide & conquer
- [Two Pointers & Sliding Window](two-pointers-sliding-window.md) — duplicate-block skipping in backtracking (sort, then skip equal siblings) is the same idea as duplicate-block skipping in sorted-array two pointers
- [Graphs](graphs.md) — DFS is the traversal, backtracking is DFS + pruning; grid-path backtracking is a special case of grid/graph traversal

## Cheat Sheet

→ [DSA Cheat Sheet](cheatsheet.md#recursion--backtracking)
