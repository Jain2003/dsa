# Subsets II (LeetCode 90)

## Problem Description
Given an integer array `nums` that may contain **duplicates**, return **all possible subsets** (the power set).

The solution set must **not** contain duplicate subsets. Return the solution in any order.

**Example 1:**
- **Input:** `nums = [1, 2, 2]`
- **Output:** `[[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]`

**Example 2:**
- **Input:** `nums = [0]`
- **Output:** `[[], [0]]`

---

## 1. Bugs in Your Initial Code

### Bug 1: Pushing `nums[i]` Instead of `nums[itr]`
```cpp
for (int itr = i; itr < nums.size(); itr++) {
    ...
    current.push_back(nums[i]); // BUG: itr is the loop variable, not i!
```
- In the loop, `itr` advances through the candidates, but you pushed `nums[i]`.
- That means on iteration `itr = 1`, it was still pushing `nums[0]`!
- **Fix:** `current.push_back(nums[itr])`.

### Bug 2: `break` Instead of `continue`
```cpp
if (itr > i && nums[itr] == nums[itr - 1]) 
    break; // BUG: kills loop prematurely!
```
- `break` exits the entire loop, skipping all remaining elements in the array (even completely different numbers like `3, 4, 5`)!
- We only want to skip the current duplicate number and move to the next candidate.
- **Fix:** Use `continue`.

### Bug 3: Missing `sort(nums.begin(), nums.end())`
- For duplicate detection (`nums[itr] == nums[itr - 1]`) to work, duplicate values **must be adjacent**.
- If `nums = [4, 1, 4]`, without sorting, the two `4`s are not adjacent, so duplicates will still be generated!

### Bug 4: Adding `current` to `ans` ONLY at `i == nums.size()`
- In Subsets, **every state** (including `[]`, `[1]`, `[1, 2]`) is a valid subset!
- If you only push when `i == nums.size()`, you miss intermediate subsets of lengths $0, 1, 2 \dots$
- **Fix:** Add `ans.push_back(current)` at the very beginning of `helper`, for every recursive call!

---

## 2. Key Intuition: Level-by-Level Subset Generation

At every call to `helper`:
1. **First action:** `ans.push_back(current)` — Add whatever subset has been constructed so far.
2. **Loop from `i` to `n - 1`:**
   - If `itr > i && nums[itr] == nums[itr - 1]`, `continue` (skip duplicates at this depth).
   - `current.push_back(nums[itr])`.
   - Recurse with `itr + 1`.
   - `current.pop_back()` (backtrack).

---

## 3. Decision Tree for `nums = [1, 2, 2]`

```
Level 0: [] (add to ans)
  ├── itr = 0: Pick nums[0] = 1 -> [1] (add to ans)
  │     Level 1:
  │       ├── itr = 1: Pick nums[1] = 2 -> [1, 2] (add to ans)
  │       │     Level 2:
  │       │       └── itr = 2: Pick nums[2] = 2 -> [1, 2, 2] (add to ans)
  │       │
  │       └── itr = 2: nums[2] == nums[1] (itr > 1) -> SKIP! (prevents duplicate [1, 2])
  │
  ├── itr = 1: Pick nums[1] = 2 -> [2] (add to ans)
  │     Level 1:
  │       └── itr = 2: Pick nums[2] = 2 -> [2, 2] (add to ans)
  │
  └── itr = 2: nums[2] == nums[1] (itr > 0) -> SKIP! (prevents duplicate [2])
```

**Final Answer:** `[[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]` (6 unique subsets).

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(N \log N + 2^N \times N)$ — Sorting takes $\mathcal{O}(N \log N)$, and there are at most $2^N$ subsets, each taking up to $\mathcal{O}(N)$ to copy into `ans`.
- **Space Complexity:** $\mathcal{O}(N)$ auxiliary recursion stack space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
private:
    void helper(const vector<int>& nums, int i, vector<int>& current, vector<vector<int>>& ans) {
        // Every state in the recursion tree represents a valid subset!
        ans.push_back(current);

        for (int itr = i; itr < nums.size(); itr++) {
            // Skip duplicates at the same tree depth
            if (itr > i && nums[itr] == nums[itr - 1]) {
                continue;
            }

            // Pick nums[itr]
            current.push_back(nums[itr]);

            // Recurse to next index
            helper(nums, itr + 1, current, ans);

            // Backtrack
            current.pop_back();
        }
    }

public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> current;

        // Step 1: Sort to group duplicate elements together
        sort(nums.begin(), nums.end());

        helper(nums, 0, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;

    // Test Case 1: nums = [1, 2, 2]
    vector<int> nums1 = {1, 2, 2};
    vector<vector<int>> res1 = sol.subsetsWithDup(nums1);

    cout << "Subsets for [1, 2, 2]:\n";
    for (const auto& subset : res1) {
        cout << "[ ";
        for (int x : subset) cout << x << " ";
        cout << "]\n";
    }

    // Test Case 2: nums = [0]
    vector<int> nums2 = {0};
    vector<vector<int>> res2 = sol.subsetsWithDup(nums2);

    cout << "\nSubsets for [0]:\n";
    for (const auto& subset : res2) {
        cout << "[ ";
        for (int x : subset) cout << x << " ";
        cout << "]\n";
    }

    return 0;
}
```
