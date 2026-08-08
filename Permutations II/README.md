# 🔄 Permutations II | Unique Permutations | Backtracking

[![C++](https://img.shields.io/badge/Language-C%2B%2B-00599C?style=flat-square&logo=c%2B%2B)](https://isocpp.org/)
[![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange?style=flat-square)]()
[![Technique](https://img.shields.io/badge/Technique-Backtracking-blueviolet?style=flat-square)]()

A clean, well-explained C++ solution to the classic **Unique Permutations** problem — generating all distinct permutations of an array that may contain duplicate elements, using a **frequency-map + backtracking** approach.

---

## 📌 Problem Statement

Given an integer array `nums` that may contain **duplicate elements**, return all possible **unique permutations**.

> The answer must **not** contain duplicate permutations.

### Example

```text
Input:  nums = [1, 1, 2]

Output:
[
  [1, 1, 2],
  [1, 2, 1],
  [2, 1, 1]
]
```

Even though `1` appears twice, we must not generate duplicate permutations.

---

## 📑 Table of Contents

- [Approach](#-approach)
- [Why Use a Frequency Map?](#-why-use-a-frequency-map)
- [Recursion / Backtracking Diagram](#-recursion--backtracking-diagram)
- [Detailed Walkthrough](#-detailed-walkthrough)
- [What Does Backtracking Mean?](#-what-does-backtracking-mean)
- [Code Explanation](#-code-explanation)
- [Full Code](#-full-code)
- [Complexity Analysis](#️-complexity-analysis)
- [Key Concepts](#-key-concepts)
- [Key Takeaway](#-key-takeaway)

---

## 💡 Approach

We solve this problem using:

- **Backtracking**
- **Recursion**
- **Frequency Map** (`unordered_map`)

Instead of treating every occurrence of a duplicate number as a separate choice, we store:

```
number → frequency
```

For example, given:

```cpp
nums = [1, 1, 2, 2, 3]
```

The frequency map becomes:

| Number | Count |
|:------:|:-----:|
|   1    |   2   |
|   2    |   2   |
|   3    |   1   |

This lets us choose a number only when its remaining frequency is **greater than 0**.

---

## 🧠 Why Use a Frequency Map?

Suppose we have:

```cpp
nums = [1, 1, 2]
```

If we use naive permutation logic (treating each index as distinct), we generate duplicates:

```text
1 1 2
1 1 2   ← duplicate
1 2 1
1 2 1   ← duplicate
2 1 1
2 1 1   ← duplicate
```

This wastes work exploring branches that produce identical results.

**Instead**, we store:

```
1 → 2
2 → 1
```

At every step, we choose a **number**, not an individual copy of that number — so the two `1`s are treated as the *same* choice, eliminating duplicate branches entirely.

---

## 🔍 Frequency Map

For `nums = [1, 1, 2]`, we build:

```cpp
unordered_map<int, int> mp;
```

which contains:

| Number | Count |
|:------:|:-----:|
|   1    |   2   |
|   2    |   1   |

Meaning: there are **2 copies of `1`** and **1 copy of `2`**.

---

## 🌳 Recursion / Backtracking Diagram

Starting frequency: `1 → 2`, `2 → 1`

```text
temp = []
```

**Level 1** — we can choose `1` or `2`:

```text
        []
       /  \
      1    2
```

- Choosing `1` → `temp = [1]`, frequency becomes `1 → 1, 2 → 1`
- Choosing `2` → `temp = [2]`, frequency becomes `1 → 2, 2 → 0`

### Complete Recursion Tree

```text
                         []
                       /    \
                      1      2
                    /   \     \
                   1     2     1
                   |     |     |
                   2     1     1
```

✅ Resulting unique permutations:

```text
[1, 1, 2]
[1, 2, 1]
[2, 1, 1]
```

---

## 🔄 Detailed Walkthrough

Tracing the leftmost path for `[1, 1, 2]`:

| Step | Action | `temp` | `mp` state |
|:----:|--------|--------|------------|
| 1 | Choose `1` | `[1]` | `1→1, 2→1` |
| 2 | Choose `1` again | `[1, 1]` | `1→0, 2→1` |
| 3 | Only `2` available → choose `2` | `[1, 1, 2]` | `1→0, 2→0` |

At this point `temp.size() == n`, so we push `[1, 1, 2]` into `result`.

### ↩️ Backtracking

After storing the permutation, we **undo** the last choice:

```cpp
temp.pop_back();
mp[num]++;
```

```text
temp = [1, 1]
mp:  1 → 0
     2 → 1
```

We then try the next available number, and this process repeats until every possibility has been explored.

---

## 🔁 What Does Backtracking Mean?

Backtracking always follows the same three-step pattern:

```text
   Choose
     ↓
   Explore
     ↓
    Undo
```

In code:

```cpp
temp.push_back(num);   // Choose
mp[num]--;

solve(mp, temp);        // Explore

temp.pop_back();        // Undo
mp[num]++;
```

### Visual Flow

```text
             Choose
                ↓
        temp.push_back(num)
                ↓
             mp[num]--
                ↓
             Explore
                ↓
          solve(...)
                ↓
              Undo
                ↓
        temp.pop_back()
                ↓
             mp[num]++
```

The **undo** step is what allows us to try alternative possibilities from the same state.

---

## 🧩 Code Explanation

### 1. Global Variables

```cpp
int n;
vector<vector<int>> result;
```

- `n` — size of the input array (e.g. for `[1,1,2]`, `n = 3`)
- `result` — stores all unique permutations found

### 2. Recursive Function

```cpp
void solve(unordered_map<int, int> mp, vector<int> temp)
```

| Parameter | Meaning |
|-----------|---------|
| `mp` | current remaining frequency of each number |
| `temp` | the permutation currently being built |

### 3. Base Case

```cpp
if (temp.size() == n) {
    result.push_back(temp);
    return;
}
```

Once `temp` holds `n` elements, a complete permutation has been formed — store it and return.

### 4. Loop Through the Frequency Map

```cpp
for (auto [num, count] : mp) { ... }
```

Iterates over every distinct number and its remaining count.

### 5. Skip Exhausted Numbers

```cpp
if (count == 0) {
    continue;
}
```

If a number has no copies left, it cannot be chosen.

### 6. Choose a Number

```cpp
temp.push_back(num);
```

### 7. Decrease Frequency

```cpp
mp[num]--;
```

Marks one copy of `num` as used.

### 8. Recursive Call

```cpp
solve(mp, temp);
```

Continues building the permutation from the new state.

### 9. Undo the Choice

```cpp
temp.pop_back();
mp[num]++;
```

Restores state so the next candidate in the loop can be tried.

---

## 🧠 Complete Code Flow

```text
nums = [1, 1, 2]
       ↓
Build frequency map → 1→2, 2→1
       ↓
solve(mp, [])
       ↓
Choose 1 → solve(mp, [1])
       ↓
Choose 1 → solve(mp, [1,1])
       ↓
Choose 2 → solve(mp, [1,1,2])
       ↓
Store permutation
       ↓
Backtrack → try next choice
       ↓
Repeat until all permutations are generated
```

---

## 📝 Full Code

```cpp
class Solution {
public:
    int n;
    vector<vector<int>> result;

    void solve(unordered_map<int, int> mp, vector<int> temp) {

        // Base case
        if (temp.size() == n) {
            result.push_back(temp);
            return;
        }

        // Try every unique number
        for (auto [num, count] : mp) {

            // No copies left
            if (count == 0) {
                continue;
            }

            // Choose
            temp.push_back(num);
            mp[num]--;

            // Explore
            solve(mp, temp);

            // Undo
            temp.pop_back();
            mp[num]++;
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {

        n = nums.size();

        unordered_map<int, int> mp;

        // Build frequency map
        for (int &num : nums) {
            mp[num]++;
        }

        vector<int> temp;

        solve(mp, temp);

        return result;
    }
};
```

---

## 🧪 Example Walkthrough

**Input**

```cpp
nums = [1, 1, 2]
```

**Frequency Map**

| Number | Count |
|:------:|:-----:|
|   1    |   2   |
|   2    |   1   |

**Recursion Tree**

```text
                         []
                       /    \
                      1      2
                    /   \     \
                   1     2     1
                   |     |     |
                   2     1     1
```

**Result**

```cpp
[
  [1, 1, 2],
  [1, 2, 1],
  [2, 1, 1]
]
```

---

## ⚡ Why Are Duplicates Avoided?

The key idea:

> **We choose a VALUE, not an individual occurrence.**

For `[1, 1, 2]`, we don't distinguish between `1₁` and `1₂`. Instead:

```
1 → 2   (two copies of 1 are available)
```

Choosing `1` at a given level creates **only one branch**, regardless of which physical copy we imagine using — this is precisely what eliminates duplicate permutations.

---

## ⏱️ Complexity Analysis

Let `n = nums.size()`.

| Metric | Complexity | Notes |
|--------|:----------:|-------|
| **Time** | `O(n × n!)` | Up to `n!` permutations; copying each into `result` costs `O(n)` |
| **Auxiliary Space** | `O(n)` | Recursion depth |
| **Output Space** | `O(n × n!)` | Size of `result` in the worst case |

---

## 🔑 Key Concepts

| Concept | Purpose |
|---------|---------|
| `unordered_map` | Stores frequency of each number |
| `temp` | Stores the current in-progress permutation |
| `push_back()` | Makes a choice |
| `mp[num]--` | Uses one copy of a number |
| `solve()` | Explores remaining choices recursively |
| `pop_back()` | Undoes the last choice |
| `mp[num]++` | Restores the frequency count |
| Base case | Stores a completed permutation |

---

## 🎯 Key Takeaway

```text
Normal Permutations
        ↓
Treat every element separately
        ↓
Duplicates get generated

Unique Permutations
        ↓
Count frequency of each value
        ↓
Choose each VALUE only when available
        ↓
Decrease frequency → Recurse → Restore frequency
```

> **Frequency Map + Backtracking = Unique Permutations, without ever generating a duplicate branch.**

---

<p align="center">Made with 🧠 and a lot of backtracking</p>
