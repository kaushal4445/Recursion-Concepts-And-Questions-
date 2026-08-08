<div align="center">

# ♛ Power Set & Subsets
### Recursion + Backtracking — Include / Exclude Pattern

![C++](https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white)
![Recursion](https://img.shields.io/badge/Technique-Recursion%20%2B%20Backtracking-orange?style=for-the-badge)
![Complexity](https://img.shields.io/badge/Time-O(N%C2%B72%E2%81%BF)-blue?style=for-the-badge)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-yellow?style=for-the-badge)

*One algorithm, two platforms — GeeksforGeeks' **Power Set** and LeetCode's **Subsets**, solved with the same elegant recursive idea.*

</div>

---

## 📖 Table of Contents

- [📌 Problem Statement](#-problem-statement)
- [🔵 GFG vs 🟢 LeetCode](#-gfg-vs--leetcode)
- [💡 Core Idea](#-core-idea)
- [🔢 Why 2ⁿ Subsets?](#-why-are-there-2-subsets)
- [🔄 Algorithm Flow](#-algorithm)
- [🌳 Recursion Tree](#-recursion-tree)
- [📝 Dry Run](#-dry-run--leetcode)
- [↩️ Backtracking Visualization](#-backtracking-visualization)
- [🔵 GFG Solution](#-gfg--power-set)
- [🟢 LeetCode Solution](#-leetcode--subsets)
- [🔍 Key Differences](#-gfg-vs-leetcode--main-difference)
- [🚨 Common Mistakes](#-common-mistakes)
- [⏱️ Complexity Analysis](#️-time-complexity)
- [🚀 Where This Pattern Helps](#-where-this-pattern-is-useful)
- [🎯 Key Takeaways](#-key-takeaways)

---

## 📌 Problem Statement

Given a collection of elements, generate **all possible subsets**.

A subset can contain:

- ✅ All elements
- ✅ Some elements
- ✅ A single element
- ✅ No elements *(the **Empty Subset**)*

**Example** — for `[1, 2, 3]`, the possible subsets are:

```
[]
[1]
[2]
[3]
[1,2]
[1,3]
[2,3]
[1,2,3]
```

> **Total subsets = 2³ = 8**

---

## 🔵 GFG vs 🟢 LeetCode

Both problems use the **same recursion + backtracking approach**. The only real difference is the **data type**.

<table>
<tr>
<th align="center">🔵 GeeksforGeeks — Power Set</th>
<th align="center">🟢 LeetCode — Subsets</th>
</tr>
<tr>
<td>

**Input:** `string`
```cpp
"abc"
```
**Output:**
```cpp
vector<string>
```

</td>
<td>

**Input:** `vector<int>`
```cpp
[1, 2, 3]
```
**Output:**
```cpp
vector<vector<int>>
```

</td>
</tr>
</table>

---

## 💡 Core Idea

For **every** element, there are exactly **two choices**:

1. **Include** the element
2. **Exclude** the element

This is the classic **Include / Exclude** recursion pattern:

```
                    Element
                   /       \
                  /         \
             INCLUDE       EXCLUDE
                |              |
             Choose it     Don't choose it
                |              |
             RECURSE        RECURSE
```

The pattern, repeated for every element:

```
   PICK → RECURSE → UNDO → DON'T PICK → RECURSE
```

---

## 🔢 Why Are There 2ⁿ Subsets?

Each element independently has **2 possibilities** — `Include` **OR** `Exclude`.

For **N** elements:

```
2 × 2 × 2 × ... × 2   (N times)   =   2ⁿ
```

| N | Subsets (2ⁿ) |
|:-:|:-:|
| 1 | 2 |
| 2 | 4 |
| 3 | 8 |
| 4 | 16 |
| 10 | 1,024 |
| 20 | 1,048,576 |

---

## 🔄 Algorithm

```
Start
  │
  ▼
Take current element
  │
  ▼
       ┌─────────────────┐
       │                 │
       ▼                 ▼
   INCLUDE            EXCLUDE
       │                 │
       ▼                 ▼
   Add element       Don't add
       │                 │
       ▼                 ▼
    Recurse           Recurse
       │                 │
       └────────┬────────┘
                │
                ▼
             Continue
                │
                ▼
         Reached end?
           │       │
          No      Yes
           │       │
        Continue  Save
                  Subset
```

---

## 🌳 Recursion Tree

For `nums = [1, 2, 3]`:

```
                         []
                    /          \
                 [1]            []
                /   \          /  \
            [1,2]   [1]      [2]  []
             / \     / \     / \   / \
        [1,2,3] [1,2] [1,3] [1] [2,3] [2] [3] []
```

**Leaf nodes = all subsets** (2³ = 8 total):

```
[1,2,3]   [1,2]   [1,3]   [1]
[2,3]     [2]     [3]     []
```

---

## 📝 Dry Run — LeetCode

**Input:** `nums = [1, 2]`

Initially: `curr = []`, `index = 0`

| Step | Action | State of `curr` | Saved Subset |
|:--|:--|:--|:--|
| 1 | Include `1` | `[1]` | — |
| 2 | Include `2` | `[1,2]` | ✅ `[1,2]` |
| 3 | Backtrack → remove `2` | `[1]` | ✅ `[1]` |
| 4 | Backtrack → remove `1` | `[]` | — |
| 5 | Include `2` (exclude `1` branch) | `[2]` | ✅ `[2]` |
| 6 | Exclude `2` | `[]` | ✅ `[]` |

**Final result:**
```
[]  [1]  [2]  [1,2]
```

---

## ↩️ Backtracking Visualization

```
                    []
                   /  \
                INCLUDE EXCLUDE
                  /        \
                [1]         []
               /  \        /  \
             [1,2] [1]   [2]  []
                ↓
             Backtrack
                ↓
          Remove Last Element
                ↓
           Try Next Choice
```

The key operation is:

```cpp
curr.pop_back();
```

> It removes the last selected element so another possibility can be explored.

### 🧠 Why Do We Need `pop_back()`?

For `nums = [1, 2]`:

1. Select `[1]`
2. Select `[1, 2]`
3. Finish this branch → **must remove `2`** → `curr.pop_back();` → back to `[1]`
4. Now explore "don't include `2`" → `[1]`

> ⚠️ Without `pop_back()`, `2` would remain inside `curr` and produce **incorrect results**.

---

## 🔵 GFG — Power Set

### 📌 Input / Output

```cpp
Input:  s = "abc"
Output: ["", "a", "ab", "abc", "ac", "b", "bc", "c"]
```

### 💻 Code

```cpp
class Solution {
public:

    vector<string> result;

    void solve(string &s, string &curr, int index) {

        // Base Case
        if (index >= s.length()) {
            result.push_back(curr);
            return;
        }

        // Include current character
        curr.push_back(s[index]);
        solve(s, curr, index + 1);

        // Backtrack
        curr.pop_back();

        // Exclude current character
        solve(s, curr, index + 1);
    }

    vector<string> powerSet(string &s) {
        result.clear();
        string curr = "";
        solve(s, curr, 0);
        sort(result.begin(), result.end());
        return result;
    }
};
```

### 📝 Example Walkthrough

For `s = "abc"`, expanding one character at a time:

```
Step 'a':          ""
                  /    \
              Include Exclude
                 |       |
                "a"      ""

Step 'b':               ""
                     /       \
                   "a"        ""
                  /   \      /  \
               "ab"   "a"   "b"  ""

Step 'c':                       ""
                        /                \
                      "a"                 ""
                    /    \              /    \
                 "ab"    "a"          "b"     ""
                /  \    /  \         /  \    /  \
            "abc" "ab" "ac" "a"    "bc" "b" "c" ""
```

**After sorting:**
```
["", "a", "ab", "abc", "ac", "b", "bc", "c"]
```

---

## 🟢 LeetCode — Subsets

### 📌 Input / Output

```cpp
Input:  nums = [1, 2, 3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### 💻 Code

```cpp
class Solution {
public:

    vector<vector<int>> result;

    void solve(vector<int>& nums, vector<int>& curr, int index) {

        // Base Case
        if (index >= nums.size()) {
            result.push_back(curr);
            return;
        }

        // Include current element
        curr.push_back(nums[index]);
        solve(nums, curr, index + 1);

        // Backtrack
        curr.pop_back();

        // Exclude current element
        solve(nums, curr, index + 1);
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        result.clear();
        vector<int> curr;
        solve(nums, curr, 0);
        return result;
    }
};
```

---

## 🔍 GFG vs LeetCode — Main Difference

The **algorithm is identical** — only the data type changes.

| Feature | 🔵 GFG | 🟢 LeetCode |
|:--|:--|:--|
| Problem | Power Set | Subsets |
| Input | `string` | `vector<int>` |
| Example | `"abc"` | `[1,2,3]` |
| Current Subset | `string curr` | `vector<int> curr` |
| Result | `vector<string>` | `vector<vector<int>>` |
| Add Element | `curr.push_back(s[index])` | `curr.push_back(nums[index])` |
| Remove Element | `curr.pop_back()` | `curr.pop_back()` |
| Empty Subset | `""` | `[]` |
| Function | `powerSet()` | `subsets()` |
| Technique | Recursion + Backtracking | Recursion + Backtracking |
| Total Subsets | 2ⁿ | 2ⁿ |

### ⚠️ Important Initialization Difference

**🔵 GFG** (string-based):
```cpp
string curr = "";   // ✅ correct
```

**🟢 LeetCode** (vector-based):
```cpp
vector<int> curr;   // ✅ correct
```

**❌ Wrong LeetCode Code:**
```cpp
vector<int> curr = "";
// error: no viable conversion from 'const char[1]' to 'vector<int>'
```
> `""` is a **string**, while `vector<int>` is a **vector of integers** — they are different types.

---

## 🔍 How `solve()` Works

```cpp
void solve(vector<int>& nums, vector<int>& curr, int index)
```

| Parameter | Meaning | Example |
|:--|:--|:--|
| `nums` | Original input array | `[1,2,3]` |
| `curr` | Current subset being built | `[1,3]` |
| `index` | Element currently being processed | `index = 2` → `nums[2]` |

### 🧩 Base Case
```cpp
if (index >= nums.size()) {
    result.push_back(curr);
    return;
}
```
Once `index` reaches the end, a decision has been made for every element — the subset is complete and gets saved.

### ➕ Include Choice
```cpp
curr.push_back(nums[index]);
solve(nums, curr, index + 1);
```

### ↩️ Backtrack
```cpp
curr.pop_back();   // undo the include
```

### ➖ Exclude Choice
```cpp
solve(nums, curr, index + 1);
```

---

## 🚨 Common Mistakes

### 1️⃣ Removing the Empty Subset

❌ **Wrong**
```cpp
if (curr != "") {
    result.push_back(curr);
}
```
This incorrectly discards a **valid** subset (`""` or `[]`).

✅ **Correct**
```cpp
result.push_back(curr);
```

### 2️⃣ Wrong Current-Subset Type

❌ **Wrong**
```cpp
vector<int> curr = "";
```

✅ **Correct**
```cpp
vector<int> curr;
```

### 3️⃣ Forgetting `pop_back()`

❌ **Wrong**
```cpp
curr.push_back(nums[index]);
solve(nums, curr, index + 1);
solve(nums, curr, index + 1);   // element never removed!
```

✅ **Correct**
```cpp
curr.push_back(nums[index]);
solve(nums, curr, index + 1);

curr.pop_back();
solve(nums, curr, index + 1);
```

### 4️⃣ Forgetting to Clear the Result

Since `result` is a class member, always reset it before recursing:
```cpp
result.clear();
```

---

## 🔄 Complete Backtracking Flow

```
                Start
                  │
                  ▼
          Choose an element
                  │
                  ▼
              INCLUDE
                  │
                  ▼
               Recurse
                  │
                  ▼
              BACKTRACK
                  │
                  ▼
          Remove element
                  │
                  ▼
              EXCLUDE
                  │
                  ▼
               Recurse
                  │
                  ▼
                Done
```

---

## ⏱️ Time Complexity

There are **2ⁿ** possible subsets, and each subset can hold up to **N** elements.

```
Time Complexity = O(N × 2ⁿ)
```

## 💾 Space Complexity

| Type | Complexity |
|:--|:--:|
| Recursion Stack (Auxiliary) | `O(N)` |
| Output Storage | `O(N × 2ⁿ)` |
| **Total (incl. output)** | **`O(N × 2ⁿ)`** |

### 📊 Growth Example

| N | Subsets (2ⁿ) |
|:-:|:--|
| 3 | 8 |
| 10 | 1,024 |
| 20 | 1,048,576 |

> This is exactly why subset problems grow **exponentially** with input size.

---

## 🧠 Pattern to Remember

```cpp
// Include
curr.push_back(element);
solve(...);

// Backtrack
curr.pop_back();

// Exclude
solve(...);
```

```
PICK → RECURSE → UNDO → DON'T PICK → RECURSE
```

---

## 🚀 Where This Pattern Is Useful

The **Include / Exclude** recursion pattern generalizes to many classic problems:

- ✅ Power Set
- ✅ Subsets
- ✅ Subsequences
- ✅ Combination Sum
- ✅ Combinations
- ✅ 0/1 Knapsack
- ✅ Partition Problems
- ✅ Permutation-related Problems
- ✅ General Backtracking Problems

---

## 🎯 Key Takeaways

- ✅ Every element has exactly two choices: **include** or **exclude**
- ✅ Recursion explores both branches for every element
- ✅ `pop_back()` is essential for correct backtracking
- ✅ The empty subset (`""` / `[]`) is always valid — never skip it
- ✅ Total number of subsets = **2ⁿ**
- ✅ GFG works with `string`; LeetCode works with `vector<int>`
- ✅ The underlying recursion logic is **identical** across both platforms

---

## 🏆 Final Comparison

<table>
<tr>
<th align="center">🔵 GFG — Power Set</th>
<th align="center">🟢 LeetCode — Subsets</th>
</tr>
<tr>
<td valign="top">

- **Input:** `string` — e.g. `"abc"`
- **Current:** `string curr`
- **Result:** `vector<string>`
- **Empty subset:** `""`

</td>
<td valign="top">

- **Input:** `vector<int>` — e.g. `[1,2,3]`
- **Current:** `vector<int> curr`
- **Result:** `vector<vector<int>>`
- **Empty subset:** `[]`

</td>
</tr>
</table>

<div align="center">

**SAME ALGORITHM**
↓
**Recursion + Backtracking**
↓
**Include / Exclude**

</div>

---

## ⭐ Final Summary

The **GFG Power Set** and **LeetCode Subsets** problems are fundamentally the same algorithm — only the input/output types differ:

```
GFG        →  String Input        →  vector<string>
LeetCode   →  Integer Array Input →  vector<vector<int>>
```

The core algorithm always boils down to:

```
                 ELEMENT
                /       \
            INCLUDE    EXCLUDE
               ↓          ↓
            RECURSE    RECURSE
               ↓
           BACKTRACK
               ↓
          NEXT ELEMENT
```

> 🔑 **The most important line to remember:**
> ```cpp
> curr.pop_back();
> ```
> It undoes the previous choice, allowing recursion to explore the next possibility.

<div align="center">

### 🔥 Remember:
**Include → Recurse → Backtrack → Exclude → Recurse**

| Metric | Value |
|:--|:--:|
| Number of Subsets | `2ⁿ` |
| Time Complexity | `O(N × 2ⁿ)` |
| Auxiliary Space | `O(N)` |
| Output Space | `O(N × 2ⁿ)` |

</div>
