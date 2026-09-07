<!--
Recurring-mistakes tracker, read by the /dsa-coach skill at the start of every
session and appended to when a mistake repeats. This is the user's cross-session
weak-spots log — review it before interviews.

Each entry:
### <short mistake name>   (count: N)
- **Pattern:** what the user tends to do wrong
- **Root cause:** the underlying thinking error
- **Rule:** tiny reusable fix
Bump the count each time it recurs; add the date of the latest occurrence.
-->

# DSA — Recurring Mistakes (cross-session weak spots)

### Creating a helper function too early   (count: 1)
- **Pattern:** reaching for `helper()` as soon as recursion/loops appear, before the logic is clear.
- **Root cause:** designing functions before identifying the algorithm + state + flow.
- **Rule:** First write the main skeleton inline. Extract a function only when logic repeats (e.g. `isValidCell(r,c)`) or a block has one clear responsibility. For DFS a function is natural (it *is* the state); for BFS the `while(queue)` loop usually needs no helper.
- Latest: 2026-09-06

### Forgetting Java pass-by-value   (count: 1)
- **Pattern:** expecting a primitive changed inside a method to change the caller's variable.
- **Root cause:** treating `int`/primitives like shared references.
- **Rule:** primitives are copied (return the value instead); objects (arrays, lists) share the reference, so mutations *are* visible.
- Latest: 2026-09-06

### Choosing algorithm from data structure, not requirement   (count: 1)
- **Pattern:** "it's a grid → DFS/backtracking" when the requirement was shortest path.
- **Root cause:** matching on the data structure instead of what's being asked.
- **Rule:** read the *requirement* first — "shortest path + equal-cost moves → BFS", not "grid → DFS".
- Latest: 2026-09-06
