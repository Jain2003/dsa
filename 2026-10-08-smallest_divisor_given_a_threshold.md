# Find the Smallest Divisor Given a Threshold (Binary Search on Answer)

## Problem Description
Given an array of integers `nums` and an integer `threshold`, we will choose a positive integer `divisor`, divide all the array by it, and sum the division's result. Find the **smallest `divisor`** such that the result mentioned above is less than or equal to `threshold`.

Each result of division is rounded to the nearest integer greater than or equal to that element. (For example, `7/3 = 3` and `10/2 = 5`).

The test cases are generated such that there will always be an answer.

**Example 1:**
- **Input:** `nums = [1, 2, 5, 9]`, `threshold = 6`
- **Output:** `5`
- **Explanation:** 
  - If divisor is 1: sum = 1 + 2 + 5 + 9 = 17 (> 6)
  - If divisor is 4: sum = 1 + 1 + 2 + 3 = 7 (> 6)
  - If divisor is 5: sum = 1 + 1 + 1 + 2 = 5 (<= 6)

**Example 2:**
- **Input:** `nums = [44, 22, 33, 11, 1]`, `threshold = 5`
- **Output:** `44`

---

## Analysis of User Code

Your code logic is **almost completely correct** and well-structured! 

There is just **1 potential bug**:

### 1. 32-bit Integer Overflow (`int sum`)
- `nums.length` can be up to $5 \cdot 10^4$ and `nums[i]` up to $10^6$.
- When evaluating a small divisor (like `mid = 1`), `sum` can accumulate up to $5 \cdot 10^{10}$, exceeding standard 32-bit `int` limits ($\approx 2 \cdot 10^9$).
- **Fix:** Declare `sum` as **`long long`**.

---

## Optimal Approach: Binary Search on Answer Space

The division sum is monotonic with respect to the divisor:
- Increasing the `divisor` **decreases** the resulting sum.
- Decreasing the `divisor` **increases** the resulting sum.

We binary search for `divisor` in the range `[1, max(nums)]`:
- `low = 1`
- `high = max(nums)`
- For candidate `mid`:
  - Calculate $\text{sum} = \sum \lceil \frac{\text{nums}[i]}{\text{mid}} \rceil = \sum \frac{\text{nums}[i] + \text{mid} - 1}{\text{mid}}$.
  - If $\text{sum} \le \text{threshold}$: `mid` is a valid divisor! Record `ans = mid` and search smaller divisors (`high = mid - 1`).
  - If $\text{sum} > \text{threshold}$: `mid` is too small (sum is too large)! Search larger divisors (`low = mid + 1`).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log(\max(\text{nums})))$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

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
    int smallestDivisor(vector<int>& nums, int threshold) {
        int maxVal = 0;
        for (int x : nums) {
            maxVal = max(maxVal, x);
        }

        int low = 1;
        int high = maxVal;
        int ans = maxVal;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            long long sum = 0; // Use long long to prevent integer overflow
            for (int x : nums) {
                sum += (long long)(x + mid - 1) / mid; // Ceil division
                if (sum > threshold) {
                    low = mid + 1; // Divisor too small, increase divisor
                    break;         // Early exit optimization
                }
            }

            if (sum <= threshold) {
                ans = mid;         // Valid divisor, try to find a smaller one
                high = mid - 1;
            }
        }

        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {1, 2, 5, 9};
    int threshold1 = 6;
    cout << "Test 1 -> Smallest Divisor: " << sol.smallestDivisor(nums1, threshold1) << " (Expected: 5)\n";

    vector<int> nums2 = {44, 22, 33, 11, 1};
    int threshold2 = 5;
    cout << "Test 2 -> Smallest Divisor: " << sol.smallestDivisor(nums2, threshold2) << " (Expected: 44)\n";

    return 0;
}
```
