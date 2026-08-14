# 🌸 Beautiful Subsets — Backtracking Explained

A walkthrough of the **Include / Exclude backtracking** pattern applied to
LeetCode's *"Count the Number of Beautiful Subsets"* problem, with a full
dry run and recursion tree.

---

## 📋 Problem in one line

> Count every **non-empty** subset of `nums` such that no two elements in
> the subset differ by exactly `k`.

A subset like `{4, 6}` with `k = 2` is **not beautiful** because
`6 - 4 == 2`. A subset like `{2, 6}` **is** beautiful because `6 - 2 == 4 ≠ k`.

---

## 🧠 Core idea: Include / Exclude recursion

For every index `idx` in `nums`, we make exactly **two decisions**:

| Choice | Meaning |
|---|---|
| ❌ **Exclude** `nums[idx]` | Always valid — move on to the next index |
| ✅ **Include** `nums[idx]` | Only valid if `nums[idx] - k` and `nums[idx] + k` are **not already used** in the current subset |

We track "already used" values with a frequency map `mp`, so the check is
O(1). When `idx` reaches the end of the array, we've built one *complete*
subset (possibly empty) — increment `result`.

Because the **empty subset** is always counted once (it's always "beautiful"
by vacuous truth), the final answer subtracts it off:

```cpp
return result - 1;
```

---

## 🔍 Code, annotated

```cpp
class Solution {
public:
    int result;
    int K;

    void backtracking(int idx, vector<int>& nums, unordered_map<int, int>& mp) {
        // Base case: we've made a decision for every element
        if (idx >= nums.size()) {
            result++;                     // one more valid subset found
            return;
        }

        // --- Branch 1: EXCLUDE nums[idx] ---
        backtracking(idx + 1, nums, mp);

        // --- Branch 2: INCLUDE nums[idx], only if safe ---
        if (!mp[nums[idx] - K] && !mp[nums[idx] + K]) {
            mp[nums[idx]]++;              // mark value as "used"
            backtracking(idx + 1, nums, mp);
            mp[nums[idx]]--;              // backtrack: undo the mark
        }
    }

    int beautifulSubsets(vector<int>& nums, int k) {
        result = 0;
        K = k;
        unordered_map<int, int> mp;
        backtracking(0, nums, mp);
        return result - 1;                // remove the empty subset
    }
};
```

**Why `mp[nums[idx] - K]` / `mp[nums[idx] + K]` works as a "conflict check":**
`mp` counts how many times a value is currently sitting in the subset being
built. If `nums[idx] - K` or `nums[idx] + K` is already in the subset,
adding `nums[idx]` would create a forbidden pair — so Branch 2 is skipped.

**Why decrement after the recursive call:**
Classic backtracking "undo" — once we finish exploring "include `nums[idx]`",
we remove it from `mp` so sibling branches (which explore *not* including
it) see a clean map.

---

## 🌳 Dry run: `nums = [2, 4, 6]`, `k = 2`

Conflict rule: can't have both `x` and `x + 2` in the same subset.
So `2↔4` conflict and `4↔6` conflict — but `2` and `6` are fine together.

Each node is `idx | mp state`. Every node tries **Exclude** (always) then
**Include** (only if the map allows it). A branch that's blocked simply
never recurses — it contributes no leaf.

```
                                backtracking(idx=0, mp={})
                              /                            \
              Exclude 2                                  Include 2  (mp={2:1})
         backtracking(1, {})                        backtracking(1, {2:1})
           /              \                             /
   Exclude 4          Include 4               Exclude 4      [Include 4: BLOCKED
  bt(2,{})            mp={4:1}                bt(2,{2:1})     mp[4-2]=mp[2]=1]
   /      \          bt(2,{4:1})                /       \
Excl6   Incl6           |                    Excl6    Incl6
bt(3,{}) bt(3,{6:1})  Exclude 6            bt(3,{2:1}) mp[6-2]=mp[4]=0 ✅ OK
   |        |         bt(3,{4:1})              |       bt(3,{2:1,6:1})
 idx=3    idx=3        [Incl6: BLOCKED       idx=3          |
result++  result++      mp[6-2]=mp[4]=1]    result++      idx=3
 subset:   subset:         |                subset:      result++
   {}       {6}          idx=3                {2}        subset:
                        result++                           {2,6}
                        subset: {4}
```

### Every leaf reached

| Path (Excl/Incl at idx 0, 1, 2) | Subset built | Counted in `result`? |
|---|---|---|
| Excl 2 → Excl 4 → Excl 6 | `{}` | ✅ (yes, but this is the empty set) |
| Excl 2 → Excl 4 → Incl 6 | `{6}` | ✅ |
| Excl 2 → Incl 4 → Excl 6 | `{4}` | ✅ |
| Excl 2 → Incl 4 → Incl 6 | — | ❌ blocked (`4` conflicts with `6`) |
| Incl 2 → Excl 4 → Excl 6 | `{2}` | ✅ |
| Incl 2 → Excl 4 → Incl 6 | `{2, 6}` | ✅ |
| Incl 2 → Incl 4 → …      | — | ❌ blocked (`2` conflicts with `4`) — recursion never even reaches idx 2 on this branch |

**`result` = 5** (the 5 ✅ leaves above, including `{}`).
`beautifulSubsets` returns **`5 - 1 = 4`**.

The 4 beautiful non-empty subsets of `[2, 4, 6]` with `k = 2` are:

```
{2}   {4}   {6}   {2, 6}
```

✅ This matches LeetCode's own expected output for this input.

---

## 🖼️ The pattern, visually

```
                         backtracking(idx)
                        /                  \
                 EXCLUDE nums[idx]      INCLUDE nums[idx]
                 (always allowed)       (only if nums[idx]-K
                        |                and nums[idx]+K
                        |                are NOT in mp)
                        |                       |
              backtracking(idx+1)      mp[nums[idx]]++
                                        backtracking(idx+1)
                                        mp[nums[idx]]--   ← undo (backtrack)
```

This is the same skeleton used for **"Subsets"**, **"Combination Sum"**,
and **"Palindrome Partitioning"** — decide in/out for each element, recurse,
undo. What's special here is the O(1) *conflict check* via the frequency
map instead of scanning the whole current subset.

---

## ⏱️ Complexity

| | Complexity | Why |
|---|---|---|
| **Time** | `O(2^n)` | Each of the `n` elements branches into (up to) 2 choices |
| **Space** | `O(n)` | Recursion depth `n` + hash map of at most `n` entries |

---

## ✅ Key takeaways

- **Two recursive calls per index** = classic include/exclude subset enumeration.
- The **hash map acts as a live "conflict tracker"**, checked *before*
  including an element and cleaned up *after* (the backtracking step).
- The empty subset is always generated once → **always subtract 1** at the end.
- The `mp[key]++` / `mp[key]--` pair is the heart of backtracking: mutate,
  recurse, **undo**.
