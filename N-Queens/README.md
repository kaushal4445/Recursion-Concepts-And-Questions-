# ♛ N-Queens Problem | Backtracking

## 📌 Problem Statement

Given an integer **N**, place **N Queens** on an **N × N** chessboard such that **no two queens attack each other**.

A queen can attack:
- Same **Column**
- Same **Row**
- Both **Diagonals**

Return **all possible valid board configurations**.

---

# 💡 Approach

We solve this problem using **Backtracking**.

The idea is to place **one queen in each row**.

For every row:
1. Try every column.
2. Check whether placing the queen is safe.
3. If safe:
   - Place the queen.
   - Move to the next row.
4. If no valid position exists, remove the previously placed queen (**Backtrack**) and try another column.

Since we place queens **row by row**, there will never be more than one queen in the same row.

Therefore, we only need to check:
- ↑ Same Column
- ↖ Upper Left Diagonal
- ↗ Upper Right Diagonal

---

# 🔄 Algorithm

```
Start
  │
  ▼
Try every column in current row
  │
  ▼
Is position safe?
  │
 ┌───────┴────────┐
 │                │
No              Yes
 │                │
Skip         Place Queen
                  │
                  ▼
          Solve Next Row
                  │
                  ▼
         All rows completed?
          │             │
         Yes            No
          │             │
 Save Current Board     Continue
                        │
                        ▼
                No valid position?
                        │
                        ▼
                  Backtrack
           (Remove Last Queen)
                        │
                        ▼
               Try Next Column
```

---

# 📝 Dry Run (N = 4)

### Initial Board

```
. . . .
. . . .
. . . .
. . . .
```

---

### Step 1

Place Queen at **(0,1)**

```
. Q . .
. . . .
. . . .
. . . .
```

---

### Step 2

Move to Row 1

Try every column.

Column 0 ❌

```
. Q . .
Q . . .
```

Queen attacks diagonally.

---

Column 1 ❌

```
. Q . .
. Q . .
```

Same column.

---

Column 2 ❌

```
. Q . .
. . Q .
```

Diagonal attack.

---

Column 3 ✅

```
. Q . .
. . . Q
. . . .
. . . .
```

Move to next row.

---

One Valid Solution

```
. Q . .
. . . Q
Q . . .
. . Q .
```

---

# 🔍 How isValid() Works

Before placing a queen at **(row, col)**, we check only **three directions**.

## 1️⃣ Same Column

```
      Q
      │
      │
      ?
```

```cpp
for(int i = row - 1; i >= 0; i--)
{
    if(board[i][col] == 'Q')
        return false;
}
```

---

## 2️⃣ Upper Left Diagonal

```
Q
 \
  \
   ?
```

```cpp
for(int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--)
{
    if(board[i][j] == 'Q')
        return false;
}
```

---

## 3️⃣ Upper Right Diagonal

```
      Q
     /
    /
   ?
```

```cpp
for(int i = row - 1, j = col + 1; i >= 0 && j < N; i--, j++)
{
    if(board[i][j] == 'Q')
        return false;
}
```

---

# ❓ Why Don't We Check Downward?

Suppose we are placing a queen in **Row 2**.

```
Row 0  ✅
Row 1  ✅
Row 2  ← Current Row
Row 3  Empty
```

Rows below are still empty because they haven't been processed yet.

Therefore, checking downward is unnecessary.

---

# 🔄 Backtracking Visualization

```
                    Row 0
               /   |   |   \
              0    1    2    3
                   |
                Place Q
                   |
                 Row 1
         /     /    |     \
        X     X     X      ✓
                            |
                         Place Q
                            |
                          Row 2
                     /    |    |    \
                    ✓     X    X     X
                    |
                 Place Q
                    |
                  Row 3
                    |
            No valid position
                    |
               Backtrack ↑
                    |
          Remove Last Queen
                    |
          Try Next Column
```

Whenever no valid position is found:

```
Remove Queen
      ↓
Try Next Column
      ↓
Continue Searching
```

This process is called **Backtracking**.

---

# ⚠️ Common Mistake

❌ Wrong

```cpp
if(board[row][col] == 'Q')
```

This checks the **current empty cell**, which always contains `'.'` before placing a queen.

---

✅ Correct

```cpp
board[i][col]
```

or

```cpp
board[i][j]
```

Always check the cells you are traversing, **not** the current position.

---

# ⏱️ Time Complexity

At every row, we try placing a queen in multiple columns.

Possible choices:

```
Row 1 → N choices

Row 2 → N−1 choices

Row 3 → N−2 choices

...

Last Row → 1 choice
```

Total possible arrangements:

```
N × (N−1) × (N−2) × ... × 1
```

```
= O(N!)
```

For every attempted placement, `isValid()` checks:

- Same Column → **O(N)**
- Left Diagonal → **O(N)**
- Right Diagonal → **O(N)**

So each safety check costs:

```
O(N)
```

### Overall Time Complexity

```
O(N! × N)
```

Although the worst-case complexity is large, **Backtracking prunes invalid branches early**, making it much faster than checking every possible board arrangement.

---

# 💾 Space Complexity

### 1. Chessboard

The board stores **N × N** cells.

```
O(N²)
```

### 2. Recursive Call Stack

The recursion depth is at most **N**.

```
O(N)
```

### Total Auxiliary Space

```
O(N² + N)
```

Since **N²** dominates **N**,

```
Overall Space Complexity = O(N²)
```

> **Note:** If we also count the memory used to store all valid solutions in the `result` vector, the total memory usage will be larger because every valid board configuration is saved.

---

# ✅ Complete C++ Solution

```cpp
class Solution {
public:
    vector<vector<string>> result;
    int N;

    bool isValid(vector<string>& board, int row, int col) {

        // Check same column
        for(int i = row - 1; i >= 0; i--) {
            if(board[i][col] == 'Q')
                return false;
        }

        // Check upper-left diagonal
        for(int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if(board[i][j] == 'Q')
                return false;
        }

        // Check upper-right diagonal
        for(int i = row - 1, j = col + 1; i >= 0 && j < N; i--, j++) {
            if(board[i][j] == 'Q')
                return false;
        }

        return true;
    }

    void solve(vector<string>& board, int row) {

        if(row == N) {
            result.push_back(board);
            return;
        }

        for(int col = 0; col < N; col++) {

            if(isValid(board, row, col)) {

                board[row][col] = 'Q';

                solve(board, row + 1);

                board[row][col] = '.';
            }
        }
    }

    vector<vector<string>> solveNQueens(int n) {

        N = n;

        vector<string> board(n, string(n, '.'));

        solve(board, 0);

        return result;
    }
};
```

---

# 🎯 Key Takeaways

- ✅ Place **one queen per row**.
- ✅ Check only **three upward directions**.
- ✅ Use **Backtracking** to explore all possible solutions.
- ✅ Remove the queen after recursion to explore other possibilities.
- ✅ Invalid branches are discarded immediately, making the algorithm efficient.

### Backtracking Pattern

```
Choose
   ↓
Check
   ↓
Place
   ↓
Recurse
   ↓
Backtrack
   ↓
Try Next Choice
```

This is one of the most important backtracking patterns and is widely used in problems like:

- Sudoku Solver
- Rat in a Maze
- Word Search
- Palindrome Partitioning
- Combination Sum
- Permutations
- Generate Parentheses

Mastering this pattern makes many recursion problems much easier.
