# README: `findPermutation` Solution Explanation

This document explains the provided C++ solution for finding a permutation of indices that minimizes a custom “score”. The algorithm uses backtracking with branch‑and‑bound pruning.

---

## Problem Statement (Inferred from Code)

Given an array `nums` of length `n`, we want to find a permutation of the indices `0, 1, …, n-1` that starts with `0` and minimises the total score defined as:

- For each consecutive pair `(prev, curr)` in the permutation, add the cost  
  `abs(prev - nums[curr])`.
- For the final wrap‑around pair `(last, first)`, add the cost  
  `abs(last - nums[first])`.

The result is the permutation (vector of indices) that yields the minimum score.

---

## Algorithm Overview

The solution performs a **backtracking search** over all permutations of indices, but it prunes branches that cannot improve the current best solution.

### Key Variables
- `n` : size of `nums`.
- `minScore` : the smallest total score found so far (initialised to `INT_MAX`).
- `result` : the permutation corresponding to `minScore`.
- `visited` : boolean array to mark used indices.
- `temp` : current partial permutation (always starts with `[0]`).

### Pruning Condition
At any node, if the accumulated score `score` is **not strictly less** than `minScore`, the branch is discarded. Because all future costs are non‑negative, this guarantees that we never miss a better solution.

---

## Detailed Code Walkthrough

```cpp
class Solution {
public:
    int n;
    int minScore = INT_MAX;
    vector<int> result;

    void backtracking(vector<int>& nums, vector<bool>& visited,
                      vector<int>& temp, int score) {
        // Prune: current partial score already >= best found
        if (minScore <= score)
            return;

        // Complete permutation: add wrap‑around cost and update best
        if (temp.size() == nums.size()) {
            score += abs(temp.back() - nums[temp[0]]);
            if (score < minScore) {
                minScore = score;
                result = temp;
            }
            return;
        }

        // Try every unused index as the next element
        for (int num = 0; num < n; ++num) {
            if (!visited[num]) {
                visited[num] = true;
                temp.push_back(num);

                // Add cost from previous index to this new index
                int prev = temp[temp.size() - 2];   // index before the new one
                int curr = temp[temp.size() - 1];   // new index
                int add = abs(prev - nums[curr]);
                backtracking(nums, visited, temp, score + add);

                // Backtrack
                temp.pop_back();
                visited[num] = false;
            }
        }
    }

    vector<int> findPermutation(vector<int>& nums) {
        n = nums.size();
        vector<bool> visited(n, false);
        vector<int> temp = {0};          // lexicographically smallest starts with 0
        visited[0] = true;

        backtracking(nums, visited, temp, 0);
        return result;
    }
};


Example with Recursion Tree
Let nums = [1, 2, 3] (n = 3).
We want to find the best permutation starting with 0.

The cost function uses:
cost(prev, curr) = abs(prev - nums[curr])
Final wrap‑around: abs(last - nums[first]).

All possible permutations (starting with 0)
Permutation (indices)	Score Calculation
[0, 1, 2]	abs(0 - nums[1]) + abs(1 - nums[2]) + abs(2 - nums[0])
= |0-2| + |1-3| + |2-1| = 2 + 2 + 1 = 5
[0, 2, 1]	abs(0 - nums[2]) + abs(2 - nums[1]) + abs(1 - nums[0])
= |0-3| + |2-2| + |1-1| = 3 + 0 + 0 = 3
So the minimum score is 3, achieved by [0, 2, 1].

Recursion Tree (showing pruning)
text
                       [0]  score=0
                     /         \
              [0,1]  score=2    [0,2]  score=3
              /                   \
       [0,1,2]  score=4          [0,2,1]  score=3
       + wrap = 4+1=5           + wrap = 3+0=3  → update best = 3
At [0,1] (score=2), both children explored.
At [0,2] (score=3), the complete permutation gives score=3. Later, any branch with score ≥ 3 would be pruned because minScore is already 3.

Complexity
Time: In the worst case (no pruning) it explores all (n-1)! permutations, so O(n!).
With pruning, performance improves significantly for many inputs.

Space: O(n) for the recursion stack + visited and temp arrays.

Important Note on the Cost Function
The cost function as implemented uses abs(prev - nums[curr]) – i.e., the difference between the previous index and the value at the current index. This is unusual and may be a typo.
If the intended problem was to minimise the sum of absolute differences of values (i.e., abs(nums[prev] - nums[curr])), the code would need adjustment. Nevertheless, the explanation above follows the code exactly.

