# 🧩 Generate Parentheses | Backtracking

## 📌 Problem Statement

Given `n` pairs of parentheses, generate **all combinations of well-formed (valid) parentheses**.

### Example

**Input**

```text
n = 3
```

**Output**

```text
[
 "((()))",
 "(()())",
 "(())()",
 "()(())",
 "()()()"
]
```

---

# 💡 Solution 1 : Brute Force + Validation

## Intuition

Generate **every possible string** of length `2 × n`.

Each position has only **two choices**:

- `'('`
- `')'`

After generating a string, check whether it is a valid parenthesis string using a helper function.

---

## Algorithm

```
Generate every possible string
            │
            ▼
Length == 2*n ?
            │
            ▼
      Check isSafe()
            │
      ┌─────┴─────┐
      │           │
   Invalid      Valid
      │           │
   Ignore      Store Answer
```

---

## Example (n = 2)

Generate all strings of length 4.

```
((((
((()
(()(
(())

()((
()()
())(
()))

)(((
)(()
)()(
)())

))((

....

))))
```

Most of them are invalid.

Only

```
(())
()()
```

are valid.

---

## How isSafe() Works

Maintain a counter.

```
'('  → +1

')'  → -1
```

If the counter becomes negative,

```
Invalid
```

because a closing bracket appeared before an opening bracket.

Finally,

```
Counter == 0
```

means the string is balanced.

---

### Example

```
(()())
```

| Character | Count |
|-----------|------:|
| ( | 1 |
| ( | 2 |
| ) | 1 |
| ( | 2 |
| ) | 1 |
| ) | 0 ✅ |

Valid.

---

### Invalid Example

```
())(
```

| Character | Count |
|-----------|------:|
| ( | 1 |
| ) | 0 |
| ) | -1 ❌ |

Immediately invalid.

---

## Complexity

### Number of generated strings

Each position has 2 choices.

```
Length = 2n
```

Total strings

```
2^(2n)
```

Each string takes

```
O(2n)
```

to validate.

### Time Complexity

```
O(2^(2n) × 2n)

≈ O(n × 4^n)
```

### Space Complexity

Recursive stack

```
O(2n)

≈ O(n)
```

---

# 🚀 Solution 2 : Optimized Backtracking

Instead of generating **every string**, generate **only valid prefixes**.

We keep track of:

```
open
close
```

where

```
open  = number of '(' used

close = number of ')' used
```

---

## Rules

### Add '('

Only if

```
open < n
```

---

### Add ')'

Only if

```
close < open
```

This guarantees that we never create an invalid string.

---

# 🌳 Recursion Tree (n = 3)

```
                         ""
                          │
                     add '('
                          │
                         (
                  /                \
             add '('            add ')'
                │                   ✗
               ((
          /            \
     add '('       add ')'
        │               │
      (((             (()
      │               │
    add ')'        add '('
      │               │
    ((()           (()(
      │               │
    add ')'        add ')'
      │               │
   ((())          (()()
      │               │
    add ')'        add ')'
      │               │
   ((()))        (()())
```

Invalid branches are never explored.

---

# 📖 Dry Run (n = 2)

Initially

```
curr = ""

open = 0

close = 0
```

---

## Step 1

```
(
```

```
open = 1

close = 0
```

---

## Step 2

Two choices

```
((
```

or

```
()
```

---

Choose

```
((
```

```
open = 2

close = 0
```

Cannot add another '(' because

```
open == n
```

Only add ')'

```
(()
```

Then

```
(())
```

Store answer.

---

Backtrack.

Now explore

```
()
```

↓

```
()(
```

↓

```
()()
```

Store answer.

Final Answer

```
(())
()()
```

---

# 🔄 Backtracking Visualization

```
Choose '('
      │
      ▼
Update open
      │
      ▼
Recursive Call
      │
      ▼
Remove '('
      │
      ▼
Try ')'
```

Every recursive call restores the previous state.

---

# ✅ Why This Works

Suppose

```
curr = ())
```

Already

```
close > open
```

No future character can make this valid.

Therefore,

instead of generating it,

we simply never explore this branch.

This is called **Pruning**.

---

# 📊 Complexity

The algorithm generates **only valid parenthesis strings**.

The number of valid strings is the **Catalan Number**.

```
Cn = (2n)! / ((n+1)! × n!)
```

For large `n`

```
Cn ≈ 4^n / (n^(3/2))
```

Each valid string has length

```
2n
```

### Time Complexity

```
O(Cn × n)
```

or

```
O((4^n / √n))
```

which is much better than brute force.

---

### Space Complexity

Recursive stack

```
O(n)
```

Current string

```
O(n)
```

Total

```
O(n)
```

(excluding output).

---

# 💻 Optimized C++ Solution

```cpp
class Solution {
public:
    vector<string> result;

    void solve(int n, string curr, int open, int close) {

        if(curr.length() == 2 * n) {
            result.push_back(curr);
            return;
        }

        if(open < n) {
            curr.push_back('(');
            solve(n, curr, open + 1, close);
            curr.pop_back();
        }

        if(close < open) {
            curr.push_back(')');
            solve(n, curr, open, close + 1);
            curr.pop_back();
        }
    }

    vector<string> generateParenthesis(int n) {

        solve(n, "", 0, 0);

        return result;
    }
};
```

---

# 🎯 Key Differences

| Brute Force | Optimized Backtracking |
|-------------|------------------------|
| Generates every possible string | Generates only valid strings |
| Uses `isSafe()` | No validation function needed |
| Many invalid strings explored | Invalid branches are pruned immediately |
| Time: **O(n × 4ⁿ)** | Time: **O(Cn × n)** |
| Slower | Much Faster |

---

# ✅ Key Takeaways

- Every position has two choices: `'('` or `')'`.
- Brute force generates all combinations and filters valid ones.
- Backtracking avoids generating invalid combinations by enforcing:
  - `open < n`
  - `close < open`
- This pruning makes the optimized solution significantly faster.

### Backtracking Pattern

```
Choose
   ↓
Check Constraint
   ↓
Recurse
   ↓
Backtrack
   ↓
Try Next Choice
```

This same pattern is used in:

- N-Queens
- Sudoku Solver
- Rat in a Maze
- Combination Sum
- Palindrome Partitioning
- Word Search
- Permutations
