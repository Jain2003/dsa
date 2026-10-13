# Count Subsequences with Target Sum K

## Problem Description
Given an array `nums` of integers and an integer `k`, return the total **number of subsequences** whose sum of elements equals `k`.

**Example 1:**
- **Input:** `nums = [1, 2, 3, 4]`, `k = 5`
- **Output:** `2`
- **Explanation:** Subsequences `[1, 4]` and `[2, 3]` both sum to 5.

**Example 2:**
- **Input:** `nums = [1, 2, 1]`, `k = 2`
- **Output:** `2`
- **Explanation:** Subsequences `[1, 1]` (indices 0 and 2) and `[2]` sum to 2.

---

## 1. Approaches Overview

### Approach 1: Parameterized Recursion (Counter by Reference)
- Pass a `counter` variable by reference into the helper function.
- At the base case (`i == nums.size()`), if `sum == k`, increment `counter++`.
- Explore both choices:
  - `helper(nums, k, i + 1, sum + nums[i], counter)`
  - `helper(nums, k, i + 1, sum, counter)`

### Approach 2: Functional Recursion (Return Count Directly)
- Instead of carrying a mutable reference counter, each call returns the number of valid subsequences found in its subtree.
- **Base Case:** 
  $$\text{if } i == n \implies \text{return } (\text{sum} == k \ ? \ 1 : 0)$$
- **Transition:**
  $$\text{return } \text{pick} + \text{notPick}$$
- **Advantage:** Directly maps to dynamic programming / memoization.

### Approach 3: Memoization / Dynamic Programming ($\mathcal{O}(N \times k)$)
- When $N > 20$, the $2^N$ tree causes Time Limit Exceeded (TLE).
- Overlapping subproblems exist for identical pairs of `(index, current_sum)`.
- Use a 2D table `dp[i][sum]` to cache counts.

---

## 2. Complexity Analysis

| Approach | Time Complexity | Space Complexity | Best Used When |
| :--- | :--- | :--- | :--- |
| **Backtracking (Pure Recursion)** | $\mathcal{O}(2^N)$ | $\mathcal{O}(N)$ recursion stack | $N \le 20$ |
| **Memoization (DP)** | $\mathcal{O}(N \times k)$ | $\mathcal{O}(N \times k) + \mathcal{O}(N)$ stack | $N \le 100, k \le 10^3$ |

---

## 3. Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    // Approach 1: Parameterized Backtracking (Counter by Reference)
    void helperParameterized(const vector<int>& nums, int k, int i, int sum, int &counter) {
        if (i == nums.size()) {
            if (sum == k) {
                counter++;
            }
            return;
        }

        // Pick nums[i]
        helperParameterized(nums, k, i + 1, sum + nums[i], counter);
        // Don't pick nums[i]
        helperParameterized(nums, k, i + 1, sum, counter);
    }

    // Approach 2: Functional Recursion (Returns count directly)
    int helperFunctional(const vector<int>& nums, int k, int i, int sum) {
        if (i == nums.size()) {
            return (sum == k) ? 1 : 0;
        }

        int pick = helperFunctional(nums, k, i + 1, sum + nums[i]);
        int notPick = helperFunctional(nums, k, i + 1, sum);

        return pick + notPick;
    }

    // Approach 3: Memoized Recursion (DP)
    int helperMemo(const vector<int>& nums, int k, int i, int sum, vector<vector<int>>& dp) {
        if (i == nums.size()) {
            return (sum == k) ? 1 : 0;
        }
        if (sum > k) return 0; // Pruning for non-negative numbers

        if (dp[i][sum] != -1) return dp[i][sum];

        int pick = helperMemo(nums, k, i + 1, sum + nums[i], dp);
        int notPick = helperMemo(nums, k, i + 1, sum, dp);

        return dp[i][sum] = pick + notPick;
    }

    int countSubsequences(vector<int>& nums, int k) {
        // Example using Functional Recursion
        return helperFunctional(nums, k, 0, 0);
    }
};

int main() {
    Solution sol;

    // Test Case 1: nums = [1, 2, 3, 4], k = 5 -> Expected: 2 ([1, 4] and [2, 3])
    vector<int> nums1 = {1, 2, 3, 4};
    int k1 = 5;
    cout << "Test 1: " << sol.countSubsequences(nums1, k1) << " (Expected: 2)" << endl;

    // Test Case 2: nums = [1, 2, 1], k = 2 -> Expected: 2 ([1, 1] and [2])
    vector<int> nums2 = {1, 2, 1};
    int k2 = 2;
    cout << "Test 2: " << sol.countSubsequences(nums2, k2) << " (Expected: 2)" << endl;

    // Test Case 3: nums = [1, 2, 3], k = 10 -> Expected: 0
    vector<int> nums3 = {1, 2, 3};
    int k3 = 10;
    cout << "Test 3: " << sol.countSubsequences(nums3, k3) << " (Expected: 0)" << endl;

    return 0;
}
```
