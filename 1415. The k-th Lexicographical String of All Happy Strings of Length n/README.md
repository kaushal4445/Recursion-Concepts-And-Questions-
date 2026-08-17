# K-th Lexicographically Smallest Happy String — Two Backtracking Approaches

LeetCode 1415: A **happy string** of length `n` uses only `'a'`, `'b'`, `'c'` and never has two adjacent equal characters. Generate all happy strings of length `n` in lexicographic order and return the `k`-th one (or `""` if fewer than `k` exist).

Both solutions use backtracking over the same recursion tree — the difference is **when they stop exploring it**.

---

## Solution 1 — Generate All, Then Index

```cpp
class Solution {
public:
    void solve(int n, string &curr, vector<string> &result) {
        if (curr.length() == n) {
            result.push_back(curr);
            return;
        }
        for (char ch = 'a'; ch <= 'c'; ch++) {
            if (!curr.empty() && curr.back() == ch)
                continue;

            curr.push_back(ch);   // Do
            solve(n, curr, result); // Explore
            curr.pop_back();      // Undo
        }
    }

    string getHappyString(int n, int k) {
        string curr = "";
        vector<string> result;
        solve(n, curr, result);

        if (result.size() < k) return "";
        return result[k - 1];
    }
};
```

### How it works

It exhaustively builds **every** happy string of length `n` (skipping a character only if it repeats the previous one), collects them all into `result`, and then simply reads off index `k - 1` — relying on the fact that DFS over `'a' → 'b' → 'c'` naturally visits strings in lexicographic order.

### Complexity

| | Complexity | Why |
|---|---|---|
| **Time** | `O(n · 2ⁿ)` | First character has 3 choices, every character after has only 2 (can't repeat the previous one). Leaves = `3 · 2ⁿ⁻¹` ≈ `O(2ⁿ)`, and each leaf costs `O(n)` to build/copy the string. |
| **Space** | `O(n · 2ⁿ)` | Every happy string of length `n` is stored in `result`. Recursion stack itself is only `O(n)`, but it's dominated by the stored output. |

---

## Solution 2 — Stop as Soon as the k-th String Is Found

```cpp
class Solution {
public:
    void solve(int n, string &curr, int &count, int k, string &result) {
        if (curr.length() == n) {
            count++;
            if (count == k) result = curr; // Store only the k-th string
            return;
        }
        for (char ch = 'a'; ch <= 'c'; ch++) {
            if (!curr.empty() && curr.back() == ch)
                continue;

            curr.push_back(ch);
            solve(n, curr, count, k, result);

            if (!result.empty()) return;  // early exit: k-th string already found
            curr.pop_back();
        }
    }

    string getHappyString(int n, int k) {
        string curr = "", result = "";
        int count = 0;
        solve(n, curr, count, k, result);
        return result;
    }
};
```

### How it works

Same tree, same DFS order — but the moment the `k`-th leaf is reached, `result` becomes non-empty, and the `if (!result.empty()) return;` check immediately unwinds **every** stack frame above it without exploring any remaining branches. (Note: it deliberately skips `curr.pop_back()` on the way out — harmless, since the whole search is being abandoned and `curr`'s state no longer matters.)

### Complexity

| | Complexity | Why |
|---|---|---|
| **Time** | `O(n · min(k, 2ⁿ))` | Only the first `k` leaves (and the branches leading to them) are ever visited. If `k` is small relative to `2ⁿ`, this is dramatically faster than Solution 1. Worst case (`k` invalid/too large) still degrades to `O(n · 2ⁿ)`. |
| **Space** | `O(n)` | No result vector — only the recursion stack and the `curr` string, both bounded by `n`. |

---

## Worked Example: `n = 2`, `k = 3`

Happy strings of length 2, DFS order: `ab, ac, ba, bc, ca, cb` → the 3rd one is **`"ba"`**.

### Solution 1's tree — explores everything

```
                          ""
              /            |            \
            "a"           "b"           "c"
           /    \        /    \        /    \
        "ab"   "ac"   "ba"   "bc"   "ca"   "cb"
         (1)    (2)    (3)    (4)    (5)    (6)
```
All 6 leaves generated and stored → then `result[2]` ("ba") is returned. **10 nodes visited** (3 internal + 6 leaves + root), plus 6 strings kept in memory.

### Solution 2's tree — stops the instant count == k

```
                          ""
              /            |
            "a"           "b"          ✗ (never visited)
           /    \        /
        "ab"   "ac"   "ba"
         (1)    (2)    (3) ← count==k, STOP, unwind immediately
```
Only **6 nodes visited** (root, `a`, `ab`, `ac`, `b`, `ba`) — the `"c"` branch and the `"bc"` leaf are never explored at all, and nothing is stored beyond the single answer string.

---

## Side-by-Side Comparison

| Aspect | Solution 1 (generate all) | Solution 2 (early exit) |
|---|---|---|
| Stops early once k-th string found? | No — always builds the full tree | Yes — unwinds the instant `count == k` |
| Nodes visited (`n=2, k=3` example) | 10 | 6 |
| Extra data structure | `vector<string> result` holding *every* happy string | Single `string result` holding just the answer |
| Time complexity | `O(n · 2ⁿ)` always | `O(n · min(k, 2ⁿ))` — scales with `k`, not the full state space |
| Space complexity | `O(n · 2ⁿ)` | `O(n)` |
| Core idea | Compute the whole ordered list, then index into it | Recognize DFS visits leaves in lex order, so the `k`-th visit *is* the answer — no need to go further |

**Takeaway:** Solution 2 exploits the fact that plain DFS over `'a' → 'b' → 'c'` already produces happy strings in lexicographic order, so there's no need to materialize the whole list. It answers the same question using far less time and memory whenever `k` is much smaller than the total number of happy strings (`3 · 2ⁿ⁻¹`).
