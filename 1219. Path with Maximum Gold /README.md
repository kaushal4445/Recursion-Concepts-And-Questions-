# 🪙 Path with Maximum Gold — Backtracking Explained

A visual, example-driven walkthrough of the classic **"Path with Maximum Gold"**
(LeetCode 1219) backtracking solution in C++.

---

## 📜 Problem

You are given an `m x n` grid of integers where each cell is either `0`
(empty) or holds some amount of gold.

From any cell you may walk to an **adjacent** cell (up / down / left / right),
but:

- You can't walk into a cell containing `0`.
- You can't visit the **same cell twice** in one path.
- You want to find the path that collects the **maximum total gold**.

---

## 💡 Core Idea — Backtracking (DFS + Undo)

Try every possible starting cell that has gold, and from each one explore
every direction with DFS. Because a cell can't be revisited, we **mark it as
visited** by temporarily setting it to `0`, explore all four neighbors, then
**restore** the original value before returning — this is the "backtrack"
step.

```
1. Pick a start cell (i, j) with gold > 0
2. Remember its gold, then set grid[i][j] = 0   ← mark visited
3. Recurse into all 4 neighbors, take the best result
4. Restore grid[i][j] = original gold           ← undo (backtrack)
5. Return gold_here + best neighbor result
```

This "mark → explore → unmark" pattern is the heart of backtracking: it lets
the same cell be reused freely by *other* paths that don't currently pass
through it.

---

## 🗺️ Example Grid

```
        col0  col1  col2
row0  [   0     6     0  ]
row1  [   5     8     7  ]
row2  [   0     9     0  ]
```

**Expected answer: `24`** → the path **9 → 8 → 7**
(start bottom-middle, go up, go right).

---

## 🔎 Step-by-Step Visualization

Starting DFS from `grid[2][1] = 9`:

```mermaid
flowchart TD
    A["Start: (2,1) = 9\ngrid becomes 0 here"] --> B{Explore 4 directions}
    B -->|up (1,1)=8| C["(1,1) = 8\ngrid becomes 0 here"]
    B -->|down: out of bounds| X1["return 0"]
    B -->|right (2,2)=0| X2["return 0 (empty cell)"]
    B -->|left (2,0)=0| X3["return 0 (empty cell)"]

    C --> D{Explore 4 directions from 8}
    D -->|up (0,1)=6| E["(0,1) = 6\ngrid becomes 0 here"]
    D -->|down: back to (2,1), now 0| X4["return 0"]
    D -->|right (1,2)=7| F["(1,2) = 7\ngrid becomes 0 here"]
    D -->|left (1,0)=5| G["(1,0) = 5\ngrid becomes 0 here"]

    E --> E1["all neighbors 0/oob\nreturn 6"]
    F --> F1["all neighbors 0/oob\nreturn 7"]
    G --> G1["all neighbors 0/oob\nreturn 5"]

    D --> D1["max(6,0,7,5) = 7\nreturn 8 + 7 = 15"]
    B --> B1["max(15,0,0,0) = 15\nreturn 9 + 15 = 24 ✅"]
```

Notice how each node **restores its value** (backtracks) right after its
children return — that's what allows sibling branches like `(0,1)=6` and
`(1,0)=5` to each see the grid as if the *other* hadn't been visited.

---

## 🌳 Recursion Tree (call stack view)

```
backtracking(2,1)=9                                   grid[2][1] → 0
├── backtracking(1,1)=8                                grid[1][1] → 0
│   ├── backtracking(0,1)=6                             grid[0][1] → 0
│   │   ├── backtracking(-1,1)  → 0  (out of bounds)
│   │   ├── backtracking(1,1)   → 0  (currently marked 0)
│   │   ├── backtracking(0,2)   → 0  (empty cell)
│   │   └── backtracking(0,0)   → 0  (empty cell)
│   │   returns 6 + max(0,0,0,0) = 6      grid[0][1] restored → 6
│   │
│   ├── backtracking(2,1)   → 0  (currently marked 0)
│   │
│   ├── backtracking(1,2)=7                              grid[1][2] → 0
│   │   ├── backtracking(0,2) → 0 (empty)
│   │   ├── backtracking(2,2) → 0 (empty)
│   │   ├── backtracking(1,3) → 0 (out of bounds)
│   │   └── backtracking(1,1) → 0 (currently marked 0)
│   │   returns 7 + max(...) = 7          grid[1][2] restored → 7
│   │
│   └── backtracking(1,0)=5                               grid[1][0] → 0
│       ├── backtracking(0,0) → 0 (empty)
│       ├── backtracking(2,0) → 0 (empty)
│       ├── backtracking(1,1) → 0 (currently marked 0)
│       └── backtracking(1,-1)→ 0 (out of bounds)
│       returns 5 + max(...) = 5          grid[1][0] restored → 5
│
│   best child = max(6, 0, 7, 5) = 7
│   returns 8 + 7 = 15                    grid[1][1] restored → 8
│
├── backtracking(3,1)  → 0 (out of bounds)
├── backtracking(2,2)  → 0 (empty cell)
└── backtracking(2,0)  → 0 (empty cell)

best child = max(15, 0, 0, 0) = 15
returns 9 + 15 = 24  ✅                    grid[2][1] restored → 9
```

Every branch that hits a `0`, an out-of-bounds index, or an already-visited
(temporarily zeroed) cell **prunes immediately** and contributes `0` — this
keeps the tree from exploding despite exploring in all 4 directions.

---

## 🧠 Why "set to 0, then restore" Works

| Step | What happens | Why |
|------|---------------|-----|
| `grid[i][j] = 0` | Temporarily erase the gold | Prevents revisiting this cell *during this path* |
| Recurse into neighbors | Explore all 4 directions | Try every possible continuation |
| `grid[i][j] = origvalgold` | Put the gold back | So a **different path** (from another branch/start cell) can use this cell again |

This is the textbook backtracking pattern:

```
choose → explore → un-choose
```

---

## ⏱️ Complexity

- **Time:** In the worst case (grid full of gold, no zeros), each cell can
  branch into up to 4 neighbors, giving roughly `O(3^(m*n))` in the worst
  theoretical case (since you never immediately backtrack into the parent
  cell you came from — it's now `0`). In practice, LeetCode constraints keep
  the grid small (≤ 15 non-zero cells) specifically to keep this tractable.
- **Space:** `O(m*n)` for the recursion call stack in the worst case (one
  long snaking path).

---

## 🧩 Full Code

```cpp
class Solution {
public:
    int m, n;
    vector<vector<int>> directions{{-1, 0}, {1, 0}, {0, 1}, {0, -1}};

    int backtracking(vector<vector<int>>& grid, int i, int j) {
        if (i >= m || i < 0 || j >= n || j < 0 || grid[i][j] == 0) {
            return 0;
        }

        int origValGold = grid[i][j];
        grid[i][j] = 0;          // mark visited

        int maxGold = 0;
        for (vector<int>& dir : directions) {
            int new_i = i + dir[0];
            int new_j = j + dir[1];
            maxGold = max(maxGold, backtracking(grid, new_i, new_j));
        }

        grid[i][j] = origValGold; // backtrack: unmark

        return origValGold + maxGold;
    }

    int getMaximumGold(vector<vector<int>>& grid) {
        m = grid.size();
        n = grid[0].size();

        int maxGold = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] != 0) {
                    maxGold = max(maxGold, backtracking(grid, i, j));
                }
            }
        }
        return maxGold;
    }
};
```

---

## ✅ Key Takeaways

- Try **every** non-zero cell as a possible starting point — the best path
  doesn't necessarily start at `grid[0][0]`.
- Use the grid itself as the "visited" tracker (set to `0`, then restore) —
  no extra visited matrix needed.
- Base case handles **three pruning conditions at once**: out of bounds,
  and empty/visited cell.
- The result of a cell = `its own gold + best result among its 4 neighbors`.
