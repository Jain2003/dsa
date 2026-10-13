# Combination Sum (LeetCode 39)

## Problem Description
Given an array of **distinct** integers `candidates` and a target integer `target`, return a list of all **unique combinations** of `candidates` where the chosen numbers sum to `target`. You may return the combinations in any order.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

**Example 1:**
- **Input:** `candidates = [2, 3, 6, 7]`, `target = 7`
- **Output:** `[[2, 2, 3], [7]]`

**Example 2:**
- **Input:** `candidates = [2, 3, 5]`, `target = 8`
- **Output:** `[[2, 2, 2, 2], [2, 3, 3], [3, 5]]`

---

## 1. Bugs in Your Initial Code

### Bug 1: Missing Boundary Base Case (`i == candidates.size()`)
- If neither `sum == target` nor `sum > target` is triggered along a path where you increment `i`, `i` will exceed `candidates.size()`.
- Accessing `candidates[i]` will cause a **Segmentation Fault / Out-of-Bounds Error**.

### Bug 2: Unmatched `push_back()` and `pop_back()` Calls
Look closely at your calls:
```cpp
// 1. Pick and stay at i:
current.push_back(candidates[i]);
helper(candidates, ans, current, i, target, sum + candidates[i]);
current.pop_back(); // Matched!

// 2. Here, you DID NOT push anything, but you called pop_back():
helper(candidates, ans, current, i+1, target, sum + candidates[i]); // sum added without element in current!
current.pop_back(); // ERROR: popping element that doesn't exist!

// 3. Again popping without pushing:
helper(candidates, ans, current, i+1, target, sum);
current.pop_back(); // ERROR: pops an element from previous levels!
```
- In the 2nd call, you added `candidates[i]` to `sum`, but **never pushed** it into `current`!
- Calling `current.pop_back()` multiple extra times when nothing was pushed corrupts `current`, causing crashes on empty vectors (`pop_back()` on empty vector is undefined behavior).

### Bug 3: Duplicate Combinations
- You only need **two choices** at each index:
  1. **Pick `candidates[i]`:** stay at index `i` (since we can reuse it unlimited times).
  2. **Don't Pick `candidates[i]`:** move to index `i + 1` (never pick `candidates[i]` again).
- Your code had 3 branches, creating duplicate combinations and state confusion.

---

## 2. Key Intuition: The Unbounded Pick / Don't Pick Pattern

For any element `candidates[i]`:
1. **Choice 1 (Pick & Stay):**
   - Condition: `candidates[i] <= target` (or `sum + candidates[i] <= target`).
   - Action: Push `candidates[i]`, recurse with index **`i`** (NOT `i + 1`, allowing reuse).
   - Backtrack: Pop `candidates[i]`.
2. **Choice 2 (Don't Pick & Move On):**
   - Action: Recurse with index **`i + 1`** without modifying `current` or `sum`.

---

## 3. Decision Tree for `candidates = [2, 3]`, `target = 7`

```
                                      (i = 0, target = 7)
                                    /                     \
                      Pick 2       /                       \ Don't Pick 2
                       (Stay at i=0)                     (Move to i=1)
                                  v                         v
                       [2] (target = 5)                  [] (target = 7, candidate=3)
                     /                 \                            \
         Pick 2     /                   \ Don't Pick 2               \ Pick 3
         (Stay i=0)/                     \ (Move i=1)                 \ (Stay i=1)
                  v                       v                            v
           [2, 2] (target = 3)     [2] (target = 5)              [3] (target = 4)
          /         \                     \                            \
    Pick 2 \         \ Don't Pick 2        \ Pick 3                     \ Pick 3
   (Stay)   \         \ (Move i=1)          \ (Stay i=1)                 \ (Stay i=1)
             v         v                     v                            v
          [2,2,2]    [2, 2, 3] ✅        [2, 3] (target = 2)           [3, 3] (target = 1)
```

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(2^T \times k)$ where $T = \text{target} / \min(\text{candidates})$ is the max tree depth, and $k$ is the average length of each combination copied into `ans`.
- **Space Complexity:** $\mathcal{O}(T)$ auxiliary recursion stack space.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
private:
    void helper(const vector<int>& candidates, int target, int i, vector<int>& current, vector<vector<int>>& ans) {
        // Base case 1: Target reached!
        if (target == 0) {
            ans.push_back(current);
            return;
        }

        // Base case 2: Exhausted all candidates or target became negative
        if (i == candidates.size() || target < 0) {
            return;
        }

        // Choice 1: Pick candidates[i] (can be reused, stay at index i)
        if (candidates[i] <= target) {
            current.push_back(candidates[i]);
            helper(candidates, target - candidates[i], i, current, ans);
            current.pop_back(); // Backtrack
        }

        // Choice 2: Don't pick candidates[i] (move to next candidate i + 1)
        helper(candidates, target, i + 1, current, ans);
    }

public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> current;
        helper(candidates, target, 0, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;

    // Test Case 1: [2, 3, 6, 7], target = 7 -> Expected: [[2, 2, 3], [7]]
    vector<int> c1 = {2, 3, 6, 7};
    int t1 = 7;
    vector<vector<int>> res1 = sol.combinationSum(c1, t1);

    cout << "Test 1 (target = 7):\n";
    for (const auto& comb : res1) {
        cout << "[ ";
        for (int num : comb) cout << num << " ";
        cout << "]\n";
    }

    // Test Case 2: [2, 3, 5], target = 8 -> Expected: [[2, 2, 2, 2], [2, 3, 3], [3, 5]]
    vector<int> c2 = {2, 3, 5};
    int t2 = 8;
    vector<vector<int>> res2 = sol.combinationSum(c2, t2);

    cout << "\nTest 2 (target = 8):\n";
    for (const auto& comb : res2) {
        cout << "[ ";
        for (int num : comb) cout << num << " ";
        cout << "]\n";
    }

    return 0;
}
```
