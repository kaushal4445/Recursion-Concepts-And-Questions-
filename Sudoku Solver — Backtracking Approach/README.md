# Sudoku Solver — Backtracking Approach

A C++ solution to **LeetCode 37: Sudoku Solver**. It fills a 9x9 board (with `'.'`
representing empty cells) so that every row, column, and 3x3 sub-box contains
the digits `1`–`9` exactly once.

---

## 1. How It Works — The Big Picture

The algorithm is classic **backtracking (a.k.a. constraint-based DFS with undo)**:

1. Scan the board for the next empty cell (`'.'`).
2. Try placing digits `1`–`9` in that cell, one at a time.
3. For each digit, check if it's **valid** (no conflict in the row, column, or 3x3 box).
4. If valid, place it and **recursively try to solve the rest of the board**.
5. If the recursive call succeeds → done, bubble `true` all the way up.
6. If it fails → **undo** the placement (reset to `'.'`) and try the next digit.
7. If no digit works for a cell → return `false` (triggers backtracking one level up).
8. If no empty cells remain → the board is solved, return `true`.

This is the same pattern used for N-Queens, maze solving, and other
constraint-satisfaction problems: **choose → explore → un-choose**.

---

## 2. Diagram — The Backtracking Flow

```
solveSudoku(board)
        │
        ▼
backtracking(board) ──────────────────────────────────────────────┐
        │                                                         │
        ▼                                                         │
Find next empty cell (i, j)                                       │
        │                                                         │
   ┌────┴─────┐                                                   │
   │ none left │──Yes──▶ return true  (board fully solved) ───────┘
   └────┬─────┘
        │ No (found '.')
        ▼
 for digit = '1' to '9':
        │
        ▼
   isValid(board, i, j, digit)?
        │
   ┌────┴─────┐
   │    No     │───▶ try next digit
   └──────────┘
        │ Yes
        ▼
  board[i][j] = digit   (CHOOSE)
        │
        ▼
  backtracking(board)   (EXPLORE — recurse)
        │
   ┌────┴──────────┐
   │ returned true  │───▶ return true up the call stack (propagate success)
   └────────────────┘
        │ returned false
        ▼
  board[i][j] = '.'     (UN-CHOOSE / backtrack)
        │
        ▼
  try next digit (loop continues)
        │
        ▼
 if all 9 digits fail for (i, j):
        │
        ▼
   return false   (forces the PREVIOUS cell to backtrack)
```

### Visualizing one "dead end → backtrack" cycle

```
 Row 4:  5 3 . | 6 . . | . 9 8
                ▲
                └─ trying digit '7' here...
                   ...eventually every digit 1-9 fails 3 cells later
                   ↓
                backtrack: undo this '7', try '8' instead
```

Each recursive call is like a branch of a decision tree. A wrong digit grows
a branch that eventually **dead-ends** (some later empty cell has no valid
digit); the algorithm then climbs back up and prunes that branch by trying
the next candidate.

---

## 3. Function-by-Function Breakdown

### `isValid(board, row, col, digit)`
Checks three constraints in one pass:

| Check | How |
|---|---|
| **Row** | Loop `i` from 0–8, check `board[row][i] == digit` |
| **Column** | Same loop, check `board[i][col] == digit` |
| **3x3 Box** | Compute box's top-left corner via `row/3*3`, `col/3*3`, then scan the 3x3 grid |

```cpp
int start_i = row/3*3;   // rounds row down to 0, 3, or 6
int start_j = col/3*3;   // rounds col down to 0, 3, or 6
```
This trick (`(row/3)*3`) maps any row/col into the top-left coordinate of its
3x3 block, e.g. row=4 → `4/3*3 = 1*3 = 3`.

### `backtracking(board)`
Recursive engine described in the diagram above. Returns `true` once the
**entire** board has no `'.'` left.

### `solveSudoku(board)`
Public entry point — just kicks off the recursion. The board is modified
**in-place** (passed by reference), so no return value is needed.

---

## 4. Worked Example (Conceptually)

```
Before:                          After:
5 3 . | . 7 . | . . .            5 3 4 | 6 7 8 | 9 1 2
6 . . | 1 9 5 | . . .    ──▶     6 7 2 | 1 9 5 | 3 4 8
. 9 8 | . . . | . 6 .            1 9 8 | 3 4 2 | 5 6 7
------+-------+------            ------+-------+------
8 . . | . 6 . | . . 3            8 5 9 | 7 6 1 | 4 2 3
4 . . | 8 . 3 | . . 1            4 2 6 | 8 5 3 | 7 9 1
7 . . | . 2 . | . . 6            7 1 3 | 9 2 4 | 8 5 6
------+-------+------            ------+-------+------
. 6 . | . . . | 2 8 .            9 6 1 | 5 3 7 | 2 8 4
. . . | 4 1 9 | . . 5            2 8 7 | 4 1 9 | 6 3 5
. . . | . 8 . | . 7 9            3 4 5 | 2 8 6 | 1 7 9
```

The algorithm finds the first `.` (row 0, col 2), tries `1`, `2`, `3`... until
`4` passes `isValid`, places it, and moves to the *next* `.`. When it hits a
cell where **nothing** fits, it walks back and changes an earlier guess.

---

## 5. Time Complexity

**Worst case: `O(9^(n))`** where `n` = number of empty cells (up to 81).

- For each empty cell, up to 9 digits are tried.
- Each attempt triggers a recursive call that may itself branch into 9 more.
- `isValid` costs `O(9)` per check (row) + `O(9)` (col) + `O(9)` (box) = `O(27) = O(1)` (constant, since board size is fixed at 9x9).

So the theoretical bound is exponential: **`O(9^81)`** in the absolute worst
case — but this is a *loose* upper bound. In practice, constraint propagation
(the `isValid` checks) prunes the vast majority of branches immediately, so
real Sudoku puzzles solve in milliseconds because:
- Most cells have very few valid candidates (often just 1–3) due to existing clues.
- Invalid branches are cut off almost immediately rather than being explored fully.

**Practical/average case:** much closer to polynomial-ish behavior for
well-formed puzzles — this is why Sudoku solvers "feel" fast despite the
scary exponential bound.

## 6. Space Complexity

**`O(n)`** where `n` is the number of empty cells (recursion depth).

- The board itself is `O(81) = O(1)` extra space (modified in place).
- The **call stack** grows by one frame per recursive call — at most 81
  frames deep (one per empty cell being filled along the current path).
- `isValid` uses only a few `int` variables → `O(1)` per call.

So total auxiliary space ≈ **`O(81) = O(1)`** effectively, but formally
`O(n)` due to recursion depth.

---

## 7. Why This Works (Correctness Intuition)

- **Completeness**: Every possible digit (1–9) is tried at every empty cell,
  so no valid solution is missed.
- **Soundness**: `isValid` guarantees a digit is never placed if it breaks a
  Sudoku rule, so any board returned is guaranteed valid.
- **Termination**: The recursion always makes progress (fills exactly one
  more cell per level) or backtracks, and there are finitely many cells, so
  it must terminate — either with `true` (solved) or by exhausting all
  possibilities.

---

## 8. Possible Optimizations (Not in Current Code)

| Optimization | Benefit |
|---|---|
| **Bitmasking** rows/cols/boxes (instead of scanning 27 cells per check) | `isValid` becomes `O(1)` bitwise ops instead of `O(27)` scan |
| **Most-Constrained-Cell heuristic** (pick the empty cell with fewest valid candidates first) | Reduces branching factor drastically, prunes earlier |
| **Precompute candidate sets** per cell before searching | Avoids redundant `isValid` recomputation |

These aren't necessary for correctness — the given solution is a clean,
correct, textbook backtracking implementation — but they're common follow-ups
in interviews once the base solution works.

---

## 9. Summary

| Aspect | Detail |
|---|---|
| **Technique** | Backtracking (DFS + constraint checking + undo) |
| **Time Complexity** | `O(9^n)` worst case (n = empty cells), fast in practice |
| **Space Complexity** | `O(n)` recursion stack (n ≤ 81) |
| **In-place?** | Yes — board is mutated directly, no extra grid used |
| **Guaranteed correct?** | Yes — exhaustively tries all valid placements |
