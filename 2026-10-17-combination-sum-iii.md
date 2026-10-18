# Combination Sum III (LeetCode 216)

## Problem Description
Find all valid combinations of `k` numbers that sum up to `n` such that:
- Only numbers `1` through `9` are used.
- Each number is used **at most once**.
- The list must not contain duplicate combinations.

**Example 1:**
- **Input:** `k = 3`, `n = 7`
- **Output:** `[[1, 2, 4]]`
- **Explanation:** $1 + 2 + 4 = 7$. There are no other valid combinations.

**Example 2:**
- **Input:** `k = 3`, `n = 9`
- **Output:** `[[1, 2, 6], [1, 3, 5], [2, 3, 4]]`

---

## 1. Bugs in Your Initial Code

### Bug 1: Strict Inequality Misses the Exact Target Element (`<` instead of `<=`)
```cpp
if (nums[i] < sum) // BUG!
```
- If `nums[i] == sum`, this condition is **false**!
- You can never pick the last number that brings `sum` to `0`! 
- For example, if you need `4` to reach `sum = 0`, when `nums[i] = 4`, `4 < 4` is false, so `4` is **never picked**, and no combination can ever reach `sum == 0`.
- **Fix:** `if (nums[i] <= sum)`.

### Bug 2: Confusing Array Index `i` with Combination Size `k`
```cpp
void helper(..., int k, int sum, int i, ...) {
    if (i == k) { ... } // BUG!
```
- `i` is the index into `nums` ($0$ to $8$, representing numbers $1$ to $9$).
- `k` is the **number of elements you are supposed to pick** (e.g. $k = 3$).
- If `i == k` is used as the base case, the function **stops inspecting numbers as soon as index 3 is reached** (it never even considers numbers $4, 5, 6, 7, 8, 9$)!
- **Fix:** The base case should check the size of the chosen combination: `if (current.size() == k)` or keep an explicit count of elements chosen.

### Bug 3: Missing Array Bounds Check (`i == nums.size()`)
- If `i` reaches `9` (`nums.size()`), accessing `nums[i]` is an out-of-bounds memory access!
- **Fix:** `if (i == nums.size() || current.size() == k)`.

---

## 2. Approach 1: Pick / Don't Pick Recursion

```cpp
class SolutionPickDontPick {
public:
    void helper(int num, int k, int target, vector<int>& current, vector<vector<int>>& ans) {
        if (current.size() == k) {
            if (target == 0) ans.push_back(current);
            return;
        }
        if (num > 9 || target < 0) return;

        // 1. Pick 'num'
        if (num <= target) {
            current.push_back(num);
            helper(num + 1, k, target - num, current, ans);
            current.pop_back(); // Backtrack
        }

        // 2. Don't pick 'num'
        helper(num + 1, k, target, current, ans);
    }
};
```

---

## 3. Approach 2: Loop-Based Backtracking (Optimal & Standard)

Since numbers are naturally ordered $1 \dots 9$, we can simply loop from `start` to `9`:

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
private:
    void backtrack(int start, int k, int target, vector<int>& current, vector<vector<int>>& ans) {
        // Base case: formed a combination of size k
        if (current.size() == k) {
            if (target == 0) {
                ans.push_back(current);
            }
            return;
        }

        for (int num = start; num <= 9; num++) {
            // Pruning: if num exceeds remaining target, subsequent numbers will too
            if (num > target) {
                break;
            }

            current.push_back(num);
            backtrack(num + 1, k, target - num, current, ans);
            current.pop_back(); // Backtrack
        }
    }

public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> ans;
        vector<int> current;
        backtrack(1, k, n, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;

    // Test Case 1: k = 3, n = 7 -> Expected: [[1, 2, 4]]
    cout << "Test 1 (k = 3, n = 7):\n";
    vector<vector<int>> res1 = sol.combinationSum3(3, 7);
    for (const auto& comb : res1) {
        cout << "[ ";
        for (int x : comb) cout << x << " ";
        cout << "]\n";
    }

    // Test Case 2: k = 3, n = 9 -> Expected: [[1, 2, 6], [1, 3, 5], [2, 3, 4]]
    cout << "\nTest 2 (k = 3, n = 9):\n";
    vector<vector<int>> res2 = sol.combinationSum3(3, 9);
    for (const auto& comb : res2) {
        cout << "[ ";
        for (int x : comb) cout << x << " ";
        cout << "]\n";
    }

    return 0;
}
```

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}\left(\binom{9}{k} \times k\right)$ — At most $\binom{9}{k}$ combinations (maximum $\binom{9}{4} = 126$ states), which is strictly $\mathcal{O}(1)$ constant bounded time.
- **Space Complexity:** $\mathcal{O}(k)$ recursion stack depth.
