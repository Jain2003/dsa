# Check if There Exists a Subsequence with Sum K

## Problem Description
Given an array `nums` of integers and an integer `k`, return `true` if there exists at least one **subsequence** of `nums` such that the sum of all elements in the subsequence equals `k`, otherwise return `false`.

**Example 1:**
- **Input:** `nums = [1, 2, 3, 4]`, `k = 5`
- **Output:** `true`
- **Explanation:** Subsequences `[1, 4]` or `[2, 3]` sum to 5.

**Example 2:**
- **Input:** `nums = [1, 2, 3]`, `k = 7`
- **Output:** `false`
- **Explanation:** No subsequence sums to 7.

---

## 1. Why Did Initial Code Fail?

### Bug 1: Missing Boundary Base Case (Out of Bounds)
- If the base case only checks `if (sum == k)`, and `sum` never reaches `k`, `i` increments past `nums.size()`.
- Accessing `nums[i]` triggers an immediate **Segmentation Fault / Out-of-Bounds crash**.
- **Fix:** Always guard with `if (i == nums.size())`.

### Bug 2: Passing `vector<int>` by Value (Leading to TLE)
- Passing `helper(vector<int> nums, ...)` makes an entire copy of `nums` on **every single recursive call**.
- With $2^N$ potential calls, this creates millions of vector allocations, leading to heavy **Time Limit Exceeded (TLE)**.
- **Fix:** Pass by reference: `const vector<int> &nums`.

### Bug 3: Searching All Branches Without Short-Circuiting
- Storing all subsequences in an `ans` matrix wastes time and memory when the problem only asks for an existence check (`bool`).
- Making the recursive function return a `bool` allows **immediate early exit** as soon as any valid branch returns `true`.

---

## 2. Approach 1: Recursion & Backtracking with Early Pruning

### Intuition: Pick / Don't Pick Pattern
For every index `i`:
1. **Pick `nums[i]`:** Add to current running sum, recurse on `i + 1`.
2. **Don't Pick `nums[i]`:** Leave sum unchanged, recurse on `i + 1`.

### Pruning (For Non-Negative Numbers):
- If `sum == k`, return `true` immediately.
- If `sum > k`, no further addition of non-negative elements can bring the sum back down $\rightarrow$ return `false` early.

### Complexity:
- **Time Complexity:** $\mathcal{O}(2^N)$ in the worst case (halved by short-circuiting and pruning).
- **Space Complexity:** $\mathcal{O}(N)$ recursion call stack depth.

---

## 3. Approach 2: Memoization / Dynamic Programming ($\mathcal{O}(N \times k)$)

When $N$ is larger ($N \ge 30$), pure recursion will still time out. We observe **overlapping subproblems** parameterized by `(index, current_sum)`.

We use a 2D table `dp[i][sum]`:
- `-1`: State not yet computed.
- `0`: No valid subset found from this state (`false`).
- `1`: Valid subset exists (`true`).

### Complexity:
- **Time Complexity:** $\mathcal{O}(N \times k)$ — At most $N \times (k + 1)$ unique states.
- **Space Complexity:** $\mathcal{O}(N \times k)$ for DP table + $\mathcal{O}(N)$ recursion stack.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    // Approach 1: Pure Recursion / Backtracking with Short-Circuiting
    bool helperBacktrack(const vector<int> &nums, int i, int k, int sum) {
        // Base case 1: Found target sum
        if (sum == k) return true;

        // Base case 2: Exceeded sum (prune for non-negative nums) or exhausted elements
        if (sum > k || i == nums.size()) return false;

        // Choice 1: Pick nums[i]
        if (helperBacktrack(nums, i + 1, k, sum + nums[i])) return true;

        // Choice 2: Don't pick nums[i]
        if (helperBacktrack(nums, i + 1, k, sum)) return true;

        return false;
    }

    // Approach 2: Memoized Recursion (Dynamic Programming)
    bool helperMemo(const vector<int> &nums, int i, int k, int sum, vector<vector<int>> &dp) {
        if (sum == k) return true;
        if (sum > k || i == nums.size()) return false;

        if (dp[i][sum] != -1) return dp[i][sum];

        bool pick = helperMemo(nums, i + 1, k, sum + nums[i], dp);
        if (pick) return dp[i][sum] = 1;

        bool notPick = helperMemo(nums, i + 1, k, sum, dp);
        return dp[i][sum] = notPick ? 1 : 0;
    }

    bool checkSubsequenceSum(vector<int>& nums, int k) {
        int n = nums.size();
        // Approach 2 DP table: dp[n][k + 1] initialized to -1
        vector<vector<int>> dp(n, vector<int>(k + 1, -1));
        return helperMemo(nums, 0, k, 0, dp);
    }
};

int main() {
    Solution sol;

    // Test Case 1: Valid subsequence exists [1, 4] or [2, 3] -> 5
    vector<int> nums1 = {1, 2, 3, 4};
    int k1 = 5;
    cout << "Test 1: " << (sol.checkSubsequenceSum(nums1, k1) ? "true" : "false")
         << " (Expected: true)" << endl;

    // Test Case 2: No subsequence exists
    vector<int> nums2 = {1, 2, 3};
    int k2 = 7;
    cout << "Test 2: " << (sol.checkSubsequenceSum(nums2, k2) ? "true" : "false")
         << " (Expected: false)" << endl;

    // Test Case 3: Exact single element match
    vector<int> nums3 = {4, 8, 2};
    int k3 = 8;
    cout << "Test 3: " << (sol.checkSubsequenceSum(nums3, k3) ? "true" : "false")
         << " (Expected: true)" << endl;

    return 0;
}
```
