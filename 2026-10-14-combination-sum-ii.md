# Combination Sum II (LeetCode 40)

## Problem Description
Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all **unique combinations** in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:** The solution set must **not** contain duplicate combinations.

**Example 1:**
- **Input:** `candidates = [10, 1, 2, 7, 6, 1, 5]`, `target = 8`
- **Output:**
  ```
  [
    [1, 1, 6],
    [1, 2, 5],
    [1, 7],
    [2, 6]
  ]
  ```

**Example 2:**
- **Input:** `candidates = [2, 5, 2, 1, 2]`, `target = 5`
- **Output:**
  ```
  [
    [1, 2, 2],
    [5]
  ]
  ```

---

## 1. Bugs in Your Initial Code

### Bug 1: Strict Inequality Misses Exact Matches (`<` instead of `<=`)
```cpp
if (candidates[i] < target) // BUG!
```
- If `candidates[i] == target`, this condition evaluates to **`false`**!
- Any single-element answer (e.g., `candidates[i] = 7` with `target = 7`, or `5` with `target = 5`) will be **completely ignored**.
- **Fix:** Change to `if (candidates[i] <= target)`.

### Bug 2: Generates Duplicate Combinations
- The input array `candidates` contains **duplicate numbers** (e.g., `[1, 1, 2, ...]`).
- Your code does not sort the array, nor does it skip duplicate values.
- As a result, it will pick the first `'1'` and later pick the second `'1'`, producing duplicate combinations like `[1, 7]` and `[1, 7]`.

---

## 2. The Optimal Pattern: Sort + Loop-Based Backtracking

To prevent duplicates **without** using an expensive `std::set`:

1. **Sort `candidates` first:**
   Grouping identical numbers together (e.g., `[1, 1, 2, 5, 6, 7, 10]`).
2. **Use a `for` loop from index `idx` to `n - 1`:**
   At any level of recursion, if the current element is identical to the previous element in the loop (`i > idx && candidates[i] == candidates[i - 1]`), we **skip it** (`continue`)!
3. **Early Break Pruning:**
   Since the array is sorted, if `candidates[i] > target`, all subsequent elements will also be $> \text{target}$, so we can `break` immediately.

---

## 3. Decision Tree / Level-by-Level Skipping

For `candidates = [1, 1, 2]`, `target = 3` (sorted):

```
Level 0 (target = 3, idx = 0):
  ├── i = 0: pick candidates[0] = 1 -> target becomes 2
  │     Level 1 (target = 2, idx = 1):
  │       ├── i = 1: pick candidates[1] = 1 -> target becomes 1
  │       │     Level 2 (target = 1, idx = 2):
  │       │       └── i = 2: candidates[2] = 2 > 1 -> break!
  │       └── i = 2: pick candidates[2] = 2 -> target becomes 0 => [1, 2] ✅
  │
  └── i = 1: candidates[1] == candidates[0] (i > idx) -> SKIP! (Prevents duplicate [1, 2])
  │
  └── i = 2: pick candidates[2] = 2 -> target becomes 1
        Level 1 (target = 1, idx = 3): no candidates left
```

### Deep Dive: How `[1, 1, 6]` is Formed (Why the 2nd '1' is NOT Skipped)

Given `candidates = [1, 1, 2, 5, 6, 7, 10]` (sorted), `target = 8`:

Notice the condition:
```cpp
if (itr > i && candidates[itr] == candidates[itr - 1]) continue;
```
The key is **`itr > i`**:
- **`i`** is the starting index of the current recursive call (tree level).
- When a new call starts at `i = 1`, the first iteration of its loop has **`itr = 1`**.
- Here, **`itr == i` (1 == 1)**, so **`itr > i` is FALSE!**
- The duplicate check is completely bypassed for the first candidate at that level, allowing the second `'1'` to be picked!

**Step-by-step trace:**
1. **Level 0 (`i = 0`, target = 8, `current = []`):**
   - Loop starts at `itr = 0` (`candidates[0] = 1`).
   - `itr > i` ($0 > 0$) is **False**. Not skipped!
   - `current` becomes `[1]`.
   - Recurse: `helper(..., i = itr + 1 = 1, target = 8 - 1 = 7)`.

2. **Level 1 (`i = 1`, target = 7, `current = [1]`):**
   - Loop starts at `itr = 1` (`candidates[1] = 1`).
   - Check condition: `itr > i` ($1 > 1$) is **False**!
   - Because `itr == i`, **it is NOT skipped** even though `candidates[1] == candidates[0]`!
   - `current` becomes `[1, 1]`.
   - Recurse: `helper(..., i = itr + 1 = 2, target = 7 - 1 = 6)`.

3. **Level 2 (`i = 2`, target = 6, `current = [1, 1]`):**
   - Tries `candidates[2] = 2` (`itr=2`, target becomes 4... backtracks).
   - Tries `candidates[3] = 5` (`itr=3`, target becomes 1... backtracks).
   - When loop reaches `itr = 4` (`candidates[4] = 6`):
     - `candidates[4] == target` ($6 == 6$).
     - `current` becomes `[1, 1, 6]`.
     - Recurse: `target = 6 - 6 = 0` $\implies$ **Base case! `[1, 1, 6]` added to `ans`!** 🎉


---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(2^N)$ in the worst case (significantly faster in practice due to sorting and duplicate pruning).
- **Space Complexity:** $\mathcal{O}(N)$ recursion call stack depth.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
private:
    void backtrack(const vector<int>& candidates, int target, int idx, vector<int>& current, vector<vector<int>>& ans) {
        // Base case: Valid combination found!
        if (target == 0) {
            ans.push_back(current);
            return;
        }

        for (int i = idx; i < candidates.size(); i++) {
            // Pruning: Since array is sorted, no subsequent elements can fit
            if (candidates[i] > target) {
                break;
            }

            // Skip duplicates at the same tree depth/level
            if (i > idx && candidates[i] == candidates[i - 1]) {
                continue;
            }

            // Pick candidates[i]
            current.push_back(candidates[i]);

            // Move to i + 1 because each element can be used AT MOST ONCE
            backtrack(candidates, target - candidates[i], i + 1, current, ans);

            // Backtrack
            current.pop_back();
        }
    }

public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> current;

        // Step 1: Sort candidates to handle duplicates and enable pruning
        sort(candidates.begin(), candidates.end());

        backtrack(candidates, target, 0, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;

    // Test Case 1: [10, 1, 2, 7, 6, 1, 5], target = 8
    vector<int> c1 = {10, 1, 2, 7, 6, 1, 5};
    int t1 = 8;
    vector<vector<int>> res1 = sol.combinationSum2(c1, t1);

    cout << "Test 1 (target = 8):\n";
    for (const auto& comb : res1) {
        cout << "[ ";
        for (int x : comb) cout << x << " ";
        cout << "]\n";
    }

    // Test Case 2: [2, 5, 2, 1, 2], target = 5
    vector<int> c2 = {2, 5, 2, 1, 2};
    int t2 = 5;
    vector<vector<int>> res2 = sol.combinationSum2(c2, t2);

    cout << "\nTest 2 (target = 5):\n";
    for (const auto& comb : res2) {
        cout << "[ ";
        for (int x : comb) cout << x << " ";
        cout << "]\n";
    }

    return 0;
}
```
