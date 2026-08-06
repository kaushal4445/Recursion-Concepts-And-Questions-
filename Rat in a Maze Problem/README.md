# 🐭 Rat in a Maze (Backtracking) | GeeksforGeeks

## 📌 Problem Statement

Given an `N × N` maze where:

- `1` represents an open cell.
- `0` represents a blocked cell.

A rat starts from the **top-left corner `(0,0)`** and has to reach the **bottom-right corner `(N-1,N-1)`**.

The rat can move in four directions:

- **D** → Down
- **L** → Left
- **R** → Right
- **U** → Up

Find **all possible paths** from source to destination.

---

# Example

### Input

```text
1 0 0 0
1 1 0 1
1 1 0 0
0 1 1 1
```

### Output

```text
DRDDRR
```

---

# Maze Representation

```
        0   1   2   3

0      [1] [0] [0] [0]

1      [1] [1] [0] [1]

2      [1] [1] [0] [0]

3      [0] [1] [1] [1]
```

```
Start = (0,0)
Destination = (3,3)
```

---

# Approach

This problem is solved using **Backtracking (DFS).**

At every cell:

1. Check whether the current position is valid.
2. Mark the current cell as visited.
3. Explore all four possible directions.
4. After returning from recursion, restore the cell so it can be used by other paths.

---

# Recursion Tree

```
                    (0,0)
                       |
          --------------------------
          |      |       |        |
          D      L       R        U
          |
        (1,0)
      /   |   |   \
     D    L   R    U
```

Every recursive call explores one possible path.

---

# Dry Run

Maze

```
1 1 0
1 1 1
0 1 1
```

Start

```
(0,0)
```

Initial Path

```
""
```

### Step 1

Move Down

```
Path = "D"

0 1 0
R 1 1
0 1 E
```

---

### Step 2

Move Right

```
Path = "DR"

0 1 0
0 R 1
0 1 E
```

---

### Step 3

Move Down

```
Path = "DRD"

0 1 0
0 0 1
0 R E
```

---

### Step 4

Move Right

```
Path = "DRDR"

Destination Reached
```

Store

```
DRDR
```

---

# Why Mark a Cell as Visited?

Suppose the maze is

```
1 1
1 1
```

Without marking visited:

```
(0,0)
   ↓
(0,1)
   ↓
(0,0)
   ↓
(0,1)
   ↓
Infinite Loop
```

To avoid revisiting the same cell,

```cpp
maze[i][j] = 0;
```

---

# Why Restore the Cell?

After exploring one path,

```cpp
maze[i][j] = 1;
```

This allows another valid path to reuse the same cell.

Example

```
        A
       / \
      B   C
```

If B permanently marks A as visited,

C can never use A.

This process is called **Backtracking**.

---

# Algorithm

1. Start from `(0,0)`.
2. If the cell is outside the maze or blocked, return.
3. If the destination is reached, store the current path.
4. Mark the current cell as visited.
5. Explore all four directions.
6. Remove the last move after returning from recursion.
7. Restore the current cell.
8. Return all stored paths.

---

# Code Explanation

## 1. Result Vector

```cpp
vector<string> result;
```

Stores all valid paths.

Example

```
[
"DDRR",
"DRDR",
"RDDR"
]
```

---

## 2. isSafe()

```cpp
bool isSafe(int i,int j,int n)
{
    return i>=0 && i<n &&
           j>=0 && j<n;
}
```

Checks whether the current cell lies inside the maze.

Valid

```
(2,3)
```

Invalid

```
(-1,2)

(5,1)
```

---

## 3. Base Case

```cpp
if(i==n-1 && j==n-1)
{
    result.push_back(path);
    return;
}
```

When the destination is reached,

store the current path.

Example

```
Path = DDRR
```

Store

```
result

DDRR
```

---

## 4. Mark Current Cell

```cpp
maze[i][j]=0;
```

Before

```
1
```

After

```
0
```

The current path cannot revisit this cell.

---

## 5. Explore Four Directions

### Down

```cpp
path.push_back('D');
solve(i+1,j,...);
path.pop_back();
```

```
↓
```

---

### Left

```cpp
path.push_back('L');
solve(i,j-1,...);
path.pop_back();
```

```
←
```

---

### Right

```cpp
path.push_back('R');
solve(i,j+1,...);
path.pop_back();
```

```
→
```

---

### Up

```cpp
path.push_back('U');
solve(i-1,j,...);
path.pop_back();
```

```
↑
```

---

# Why push_back() and pop_back()?

Current Path

```
DR
```

Try Down

```
push_back('D')

DRD
```

Return

```
pop_back()

DR
```

Try Right

```
push_back('R')

DRR
```

Without `pop_back()`,

```
DRD

↓

DRDR
```

The path becomes incorrect.

Backtracking restores the previous state before exploring another direction.

---

# Restore Cell

```cpp
maze[i][j]=1;
```

Now another recursive path can use this cell.

---

# Complete Backtracking Flow

```
Visit Cell

↓

Mark Visited

↓

Move Down

↓

Return

↓

Undo Path

↓

Move Left

↓

Return

↓

Undo Path

↓

Move Right

↓

Return

↓

Undo Path

↓

Move Up

↓

Return

↓

Undo Path

↓

Restore Cell

↓

Return
```

---

# Common Mistakes

### ❌ Forgetting to Restore the Cell

```cpp
maze[i][j]=1;
```

Without restoring, other valid paths cannot use this cell.

---

### ❌ Forgetting pop_back()

```cpp
path.pop_back();
```

Without it, the path keeps growing incorrectly.

---

### ❌ Wrong Traversal Order

Many platforms expect paths in **lexicographical order**.

Correct order:

```
Down
↓

Left
↓

Right
↓

Up
```

or simply

```cpp
sort(result.begin(), result.end());
```

before returning.

---

### ❌ Multiple Test Cases

Always clear the answer vector.

```cpp
result.clear();
```

Otherwise previous test case results remain.

---

# Complexity Analysis

### Time Complexity

```
O(4^(N²))
```

Worst-case upper bound due to exploring all possible paths.

---

### Space Complexity

```
O(N²)
```

Used by recursion stack and visited cells.

---

# Concepts Used

- Backtracking
- Depth First Search (DFS)
- Recursion
- Matrix Traversal
- Visited Array Technique
- Lexicographical Ordering
- Path Construction
- Backtracking (Undo Operations)

---

# Final Code Logic

```
Start

↓

Check Valid Cell

↓

Destination?

↓

Yes → Store Path

↓

No

↓

Mark Visited

↓

Move Down

↓

Move Left

↓

Move Right

↓

Move Up

↓

Undo Moves

↓

Restore Cell

↓

Return
```

---

## ⭐ Key Learning

Backtracking follows the principle:

> **Choose → Explore → Undo**

- **Choose** a direction.
- **Explore** recursively.
- **Undo** the choice so other paths can be explored.

This technique is widely used in problems like:
- Rat in a Maze
- N-Queens
- Sudoku Solver
- Word Search
- Permutations
- Combination Sum
