# Letter Tile Possibilities — Two Backtracking Approaches

LeetCode 1079: given a string `tiles`, count how many **non-empty** sequences of letters can be formed using the tiles (order matters, and you can use each physical tile at most once).

Both solutions below solve it with backtracking, but they take very different paths to get there.

---

## Solution 1 — Frequency-Count Backtracking (no duplicates generated)

```cpp
class Solution {
public:
    int n;
    int count;
    void backtracking(vector<int> &vec) {
        count++;                       // count every state reached (including empty string)

        for (int i = 0; i < 26; i++) {
            if (vec[i] == 0) continue;

            vec[i]--;                  // choose letter i
            backtracking(vec);         // explore
            vec[i]++;                  // undo (backtrack)
        }
    }

    int numTilePossibilities(string tiles) {
        count = 0;
        vector<int> vec(26, 0);
        for (char &ch : tiles) vec[ch - 'A']++;

        backtracking(vec);
        return count - 1;              // subtract the empty sequence
    }
};
```

### How it works

Instead of tracking *which* physical tile was used, it tracks *how many of each letter* remain (a 26-length frequency array). Because identical letters are indistinguishable in this array, **the same sequence can never be generated twice** — there's no need for a `set` to dedupe.

### Worked example: `tiles = "AAB"` → `vec = [A:2, B:1]`

```
                         ""                      (root, count=1)
                 /                \
              A(vec:A1,B1)        B(vec:A2,B0)
             /        \                 \
        A(A0,B1)      B(A1,B0)          A(A1,B0)
           |             |                 |
        B(A0,B0)      A(A0,B0)          A(A0,B0)
         "AAB"          "ABA"             "BAA"
```

Sequences visited: `"" , A, AA, AAB, AB, ABA, B, BA, BAA` → **9 nodes total**
`count - 1 = 8` ✅ (matches LeetCode's expected answer for `"AAB"`)

Every node in this tree is a **distinct** sequence — nothing is ever revisited or duplicated.

### Complexity

| | Complexity | Why |
|---|---|---|
| **Time** | `O(26 · n!)` (bounded, since `n ≤ 7` in constraints) | The recursion tree has at most `Σ P(26, k)` nodes for `k = 0..n`, but since letters repeat, it's really `Σ P(n, k)` distinct arrangements ≈ `O(n!)`. Each node does `O(26)` work scanning the frequency array. |
| **Space** | `O(n)` | Recursion depth is at most `n` (the length of `tiles`); the frequency array is a fixed `O(26)` = `O(1)`. No extra storage for results — just a counter. |

---

## Solution 2 — Permutation Backtracking + Set Deduplication

```cpp
class Solution {
public:
    int n;
    void backtracking(string &tiles, vector<bool> &used,
                       unordered_set<string> &result, string &curr) {
        result.insert(curr);           // record current sequence (dedup happens here)

        for (int i = 0; i < n; i++) {
            if (used[i]) continue;

            used[i] = true;
            curr.push_back(tiles[i]);

            backtracking(tiles, used, result, curr);

            used[i] = false;
            curr.pop_back();
        }
    }

    int numTilePossibilities(string tiles) {
        n = tiles.size();
        vector<bool> used(n, false);
        unordered_set<string> result;
        string curr = "";
        backtracking(tiles, used, result, curr);
        return result.size() - 1;
    }
};
```

### How it works

This treats every character **by index**, not by value — so two `'A'`s at different positions are treated as different tiles during the search. This means the same *string* gets generated multiple times through different index orders, and an `unordered_set` is used afterward to collapse duplicates.

### Worked example: `tiles = "AAB"` (indices `A0, A1, B`)

```
                              ""
             /                 |                 \
          A0                  A1                  B
         /    \              /    \              /    \
      A0A1     A0B        A1A0     A1B         BA0      BA1
        |        |           |       |           |        |
     A0A1B     A0BA1      A1A0B    A1BA0        BA0A1    BA1A0
     "AAB"     "ABA"      "AAB"    "ABA"        "BAA"    "BAA"
     (dup)               (dup✗)   (dup✗)                (dup✗)
```

Total nodes visited: `1 + 3 + 6 + 6 = 16`
After inserting into the set: `{"", A, B, AA, AB, BA, AAB, ABA, BAA}` → 9 unique strings
`result.size() - 1 = 8` ✅ — same final answer, but **16 nodes explored vs. 9**, plus hashing/dedup overhead.

### Complexity

| | Complexity | Why |
|---|---|---|
| **Time** | `O(n · n!)` | The recursion generates *every* permutation of *every* prefix length, `Σ P(n, k)` for `k = 0..n`, which is `≈ e·n! - 1` nodes — always more (or equal) than Solution 1. Each node also does `O(k)` work to hash/copy the current string of length `k` into the `unordered_set`. |
| **Space** | `O(n · n!)` | Dominated by storing every *unique* permutation (up to length `n`) inside the `unordered_set`. Recursion stack + `curr` string is only `O(n)` on top of that. |

---

## Side-by-Side Comparison

| Aspect | Solution 1 (frequency array) | Solution 2 (index + set) |
|---|---|---|
| Duplicate sequences generated? | Never | Yes, then removed via `unordered_set` |
| Recursion tree size (for `"AAB"`) | 9 nodes | 16 nodes |
| Extra data structure | None (just an `int count`) | `unordered_set<string>` storing all results |
| Time complexity | `O(26 · n!)` ≈ `O(n!)` | `O(n · n!)` (extra factor from string hashing) |
| Space complexity | `O(n)` | `O(n · n!)` |
| Core idea | Avoid duplicates **structurally** (counts merge identical letters) | Avoid duplicates **after the fact** (generate everything, then dedupe) |

**Takeaway:** Solution 1 is strictly better — it reaches the exact same answer while doing less work and using less memory, because it prevents duplicate sequences from ever being created instead of generating and then filtering them out.
