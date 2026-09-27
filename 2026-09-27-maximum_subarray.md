# Maximum Subarray (Kadane's Algorithm)

## Problem Description
Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return *its sum*.

**Example 1:**
- **Input:** `nums = [-2,1,-3,4,-1,2,1,-5,4]`
- **Output:** `6`
- **Explanation:** The subarray `[4,-1,2,1]` has the largest sum = `6`.

**Example 2:**
- **Input:** `nums = [1]`
- **Output:** `1`

**Example 3:**
- **Input:** `nums = [-5,-2,-8,-1]`
- **Output:** `-1`

---

## Simple Intuition & Analogy

### The Wallet Analogy 👛
Imagine you are walking along a path collecting coins (positive numbers) and paying tolls (negative numbers):
1. **Positive Running Balance:** As long as your running balance (`currSum`) is positive, it helps future numbers. Even if you hit a negative toll (e.g., `-2`), if your total is still `+5`, carrying that `+5` into the next step is beneficial.
2. **Negative Running Balance (Debt):** If your running balance becomes negative (`currSum < 0`), it becomes a **liability**. Carrying a negative sum to future numbers will only drag down their totals!
3. **Kadane's Golden Rule:** The moment your running sum drops below `0`, discard it and reset `currSum = 0`!

---

## Step-by-Step Logic

We maintain two variables:
- `max_sum`: Stores the maximum subarray sum found so far (initialized to `INT_MIN`).
- `currSum`: Stores the sum of the subarray being currently extended (initialized to `0`).

For each element `x` in `nums`:
1. `currSum += x`
2. `max_sum = max(max_sum, currSum)`
3. `if (currSum < 0) currSum = 0`

---

## Trace for All Negative Array (`nums = [-5, -2, -8, -1]`)
Because `max_sum` is initialized to `INT_MIN` and updated **before** resetting `currSum = 0`, even if all numbers are negative, `max_sum` correctly captures the least negative single element (`-1`)!

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N)$ — Single pass through the array.
- **Space Complexity:** $\mathcal{O}(1)$ — Only 2 variables (`max_sum` and `currSum`).

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int max_sum = INT_MIN;
        int currSum = 0;

        for (auto x : nums) {
            currSum += x;
            max_sum = max(max_sum, currSum);
            if (currSum < 0) {
                currSum = 0;
            }
        }
        return max_sum;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {-2, 1, -3, 4, -1, 2, 1, -5, 4};

    int result = sol.maxSubArray(nums);
    cout << "Maximum Subarray Sum: " << result << " (Expected: 6)\n";

    return 0;
}
```
