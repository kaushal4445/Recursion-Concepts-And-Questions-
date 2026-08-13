# Combination Sum IV — Full Explanation (with DP basics)

This README explains your C++ solution line-by-line, what `t[idx][target]` actually means,
and — since you said you don't know DP yet — a full beginner walkthrough of what
**Dynamic Programming** is, using this exact problem as the running example.

---

## 1. What problem is this solving?

This is **LeetCode 377 – Combination Sum IV**.

> Given an array of distinct positive integers `nums` and a target integer `target`,
> return the number of possible **ordered** combinations that add up to `target`.

Key word: **ordered**. `(1,2,1)` and `(1,1,2)` and `(2,1,1)` are counted as **3 different**
answers even though they use the same numbers. That's why this is closer to counting
"sequences" than "sets" — more like permutations than combinations, despite the
misleading LeetCode name.

Example:
```
nums = [1, 2, 3], target = 4

Valid sequences that sum to 4:
(1,1,1,1)
(1,1,2)
(1,2,1)
(2,1,1)
(2,2)
(1,3)
(3,1)

Total = 7  →  combinationSum4([1,2,3], 4) = 7
```

---

## 2. The two functions

### `int combinationSum4(vector<int>& nums, int target)`

This is the **entry point** — the function LeetCode actually calls.

```cpp
int combinationSum4(vector<int>& nums, int target) {
    n = nums.size();              // store size globally so backtracking() can use it
    memset(t, -1, sizeof(t));     // reset the memo table: -1 means "not computed yet"
    return backtracking(0, nums, target);  // kick off the recursion
}
```

What it does, step by step:
1. Saves `nums.size()` into the member variable `n` (so we don't need to pass it around).
2. Clears the memo table `t` by filling every cell with `-1`. `-1` is a **sentinel value**
   meaning "we have not solved this subproblem yet" (a valid answer is always ≥ 0, so `-1`
   can never be confused with a real answer).
3. Calls `backtracking(0, nums, target)` — "starting fresh, with the full target left to make."

### `int backtracking(int idx, vector<int>& nums, int target)`

This is the recursive worker function that actually counts combinations.

```cpp
int backtracking(int idx, vector<int>& nums, int target){
    if(target == 0)
        return 1;                     // base case: we built the target exactly. 1 valid way.

    if(idx >= n || target < 0 )
        return 0;                     // base case: ran out of numbers, or overshot the target.

    if(t[idx][target] != -1)
        return t[idx][target];        // already solved this subproblem before, reuse it

    int result = 0;
    for(int i = idx; i < n; i++){
        int take_index = backtracking(0, nums, target - nums[i]); // try using nums[i]
        result += take_index;
    }

    return t[idx][target] = result;   // save the answer before returning it
}
```

In plain English: *"How many ordered ways can I build up exactly `target` using numbers
from `nums`?"* For every number `nums[i]`, subtract it from `target` and ask the same
question again for the smaller target. Add up all the ways.

---

## 3. What exactly is `t[idx][target]`?

```cpp
int t[201][1001];
```

This is the **memoization (cache) table**.

- `t[idx][target]` is meant to store: *"the number of ways to reach `target`, computed
  starting the search from index `idx`."*
- Size `[201][1001]` just means it can hold indices `0..200` and targets `0..1000`
  (LeetCode's constraints for this problem).
- Before solving, every cell is `-1` (meaning "unknown / not solved yet").
- The line `if(t[idx][target] != -1) return t[idx][target];` is the **memo check**:
  before doing any work, look in the table — if we've already solved this exact
  `(idx, target)` pair before, just return the stored answer instantly instead of
  recomputing it.
- The line `return t[idx][target] = result;` **stores** the freshly computed answer
  into the table so future calls with the same `(idx, target)` are instant.

### Important inefficiency worth knowing about

Look closely at the recursive call inside the loop:

```cpp
int take_index = backtracking(0, nums, target - nums[i]);   // always passes 0, not i or idx+1
```

It **always passes `0`** as the index, never `i`. That means every single recursive call
in this program is really only ever working with `idx == 0` (the outer loop bound
`for(int i = idx; ...)` barely matters since `idx` is always `0` once you're inside the
recursion).

Effect: `t[idx][target]` in practice **only ever uses row `t[0][...]`**. Rows `t[1]`
through `t[200]` are allocated but never meaningfully filled. This still gives the
**correct final answer** (because "ordered ways to reach target using any of the
numbers" doesn't actually need an index dimension — the standard solution to this
problem only needs a 1-D `dp[target]` array), but the 2-D table here wastes memory
(~200x more than needed) and the `idx` parameter is dead weight.

A cleaner, equivalent version only needs:
```cpp
int t[1001];              // 1-D table is enough
int backtracking(vector<int>& nums, int target){
    if(target == 0) return 1;
    if(target < 0) return 0;
    if(t[target] != -1) return t[target];
    int result = 0;
    for(int i = 0; i < n; i++)
        result += backtracking(nums, target - nums[i]);
    return t[target] = result;
}
```
Same output, a fraction of the memory, and no confusing unused `idx`.

---

## 4. Visual trace: `nums = [1,2,3], target = 4`

Every call tries subtracting `1`, `2`, or `3` from the current target.

```
                         solve(4)
              /             |             \
        solve(3)        solve(2)        solve(1)
        (used 1)        (used 2)        (used 3)
       /   |   \         /    \            |
  solve(2) solve(1) solve(0) solve(0)  solve(-1)... solve(0)
   /  |  \    |  \    =1       =1         =0           =1
  ...  ...  ...
```

Reading the tree:
- `solve(0)` → **base case, return 1** (target hit exactly = one valid combination found).
- `solve(negative)` → **base case, return 0** (overshot, dead end).
- Every internal node's answer = **sum of its children's answers**.
- Once `solve(2)` is computed the *first* time (from the `solve(3)` branch), it gets
  **cached** in `t[0][2]`. The *next* time anything asks for `solve(2)` again (which
  happens a lot in the full tree — this repetition is called "overlapping subproblems"),
  it's read straight from the table instead of re-expanding the whole subtree again.

Without memoization this tree branches out exponentially. With memoization, each
distinct target value (0 to 1000) is only ever *really* solved once.

---

## 5. Dynamic Programming (DP) — the beginner explanation

You said you don't know DP yet — here's the core idea from scratch, using this problem.

### 5.1 The two ingredients DP needs

A problem is a good fit for DP when it has **both**:

1. **Optimal substructure** — the answer to the big problem can be built from answers to
   smaller versions of the *same* problem.
   *Here:* "ways to make `target`" = sum of "ways to make `target - nums[i]`" for every `i`.

2. **Overlapping subproblems** — solving it the naive recursive way asks the *exact same
   question* many times.
   *Here:* `solve(2)` gets asked for over and over from many different branches of the
   recursion tree (see the diagram above) — it's not a one-off, it repeats.

If a problem has overlapping subproblems but you *don't* cache results, you end up
redoing the same work exponentially many times. That's what plain recursion (no `t[][]`)
would do here. DP is simply: **"cache the answer to each subproblem the first time you
solve it, so you never solve it twice."**

### 5.2 Two flavors of DP

| | Top-down (Memoization) | Bottom-up (Tabulation) |
|---|---|---|
| Direction | Start from the big problem, recurse down to small ones | Start from the smallest subproblems, build up to the big one |
| Style | Recursion + a cache (`t[][]`) | Loop + an array, no recursion |
| This code | This is what your code does | Would look like: `dp[0]=1; for t in 1..target: dp[t] = sum(dp[t-num] for num in nums if t-num>=0)` |
| Pros | Easy to write from a brute-force recursion — just add a cache | No recursion overhead / no stack-overflow risk |
| Cons | Function-call overhead, risk of stack overflow for huge inputs | Slightly less intuitive to derive at first |

Your code is **top-down / memoized DP**: it's a plain recursive brute-force solution
(try every number, recurse on the remainder) with one addition — the `t[idx][target]`
cache — that turns exponential time into polynomial time.

### 5.3 The recipe you can reuse for any DP problem

1. Write the brute-force recursive solution first (ignore performance).
   - Define what a "state" is (here: `target` — how much is left to make).
   - Define the base case(s) (`target == 0` succeeds; `target < 0` fails).
   - Define the recurrence (try every choice, recurse, combine results).
2. Ask: *"Am I solving the same state more than once?"* If yes → add memoization.
3. Add a cache keyed by the state (`t[target]`, or `t[idx][target]` if the state truly
   has two varying parts).
4. Before doing work, check the cache. After computing the answer, store it in the cache.

That's exactly the transformation applied here: plain recursion + `int t[201][1001]`
memo table + the two guard lines (`if(t[idx][target] != -1) return ...` and
`return t[idx][target] = result;`).

---

## 6. Complexity

- **Time:** without memoization, exponential (`O(k^target)` where `k = nums.size()`,
  since at each of `target` levels you can branch into up to `k` choices).
  With memoization (as written, effectively `t[0][target]`), each distinct target value
  from `0` to `target` is computed once, and each computation does `O(n)` work (the
  for-loop over `nums`) → **`O(n × target)`** overall.
- **Space:** `O(target)` recursion-stack depth in the worst case, plus the table itself
  (`201 × 1001` ints ≈ 800 KB — bigger than necessary given section 3, but fine within
  typical limits).

---

## 7. Quick glossary

- **Recursion**: a function calling itself on a smaller version of the same problem.
- **Base case**: the simplest version of the problem, solved directly without recursing
  (here: `target == 0` and `target < 0` / `idx >= n`).
- **Memoization**: caching the result of a function call so repeated calls with the same
  arguments are instant.
- **Sentinel value**: a special marker value (`-1` here) used to mean "not yet computed,"
  chosen because it can never be a legitimate answer.
- **State**: the set of variables that uniquely describe a subproblem — here the *true*
  state is just `target` (since `idx` is effectively unused, see section 3).
