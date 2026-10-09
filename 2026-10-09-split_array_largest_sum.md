# Split Array Largest Sum (Binary Search on Answer)

## Problem Description
Given an integer array `nums` and an integer `k`, split `nums` into `k` non-empty contiguous subarrays such that the **largest sum among these subarrays is minimized**.

Return the **minimized largest sum**.

**Example 1:**
- **Input:** `nums = [7,2,5,10,8]`, `k = 2`
- **Output:** `18`
- **Explanation:** 
  - Split into `[7,2,5]` and `[10,8]`.
  - Largest sum = $\max(14, 18) = 18$.

**Example 2:**
- **Input:** `nums = [1,2,3,4,5]`, `k = 2`
- **Output:** `9`
- **Explanation:** Split into `[1,2,3,4]` (sum 10) vs `[1,2,3]` (sum 6) and `[4,5]` (sum 9). Minimized largest sum = `9`.

---

## Analysis of Bugs in User Code

### 1. Flawed Tracking of Subarray Sums (`max(sum, currSum)`) ❌
- In your code:
  ```cpp
  if (currSum + x > mid) {
      subs++;
      sum = currSum; // Only keeps the last subarray sum!
      currSum = x;
  }
  ```
- If there are 3 or 4 subarrays, `sum = currSum` overwrites the recorded sum, completely losing track of earlier subarray sums!
- **Binary Search Concept:** In Binary Search on Answer Space, `mid` is the **max allowed sum limit**. If `subs <= k`, it guarantees that **no subarray sum exceeds `mid`**. Therefore, `mid` itself is the valid upper bound answer! Simply record `ans = mid`.

---

## Optimal Approach: Binary Search on Answer Space

This problem is **identical** to the *Capacity to Ship Packages Within D Days* and *Book Allocation Problem*:

- **`low = max(nums)`**: Minimum possible largest sum (a subarray with at least the largest single element).
- **`high = sum(nums)`**: Maximum possible largest sum (all elements in 1 single array when $k = 1$).

For candidate largest sum `mid`:
- Count how many contiguous subarrays `subarrays` are needed such that no subarray sum exceeds `mid`.
- If `subarrays <= k`: `mid` is a valid upper bound! Save `ans = mid` and try to find a smaller largest sum (`high = mid - 1`).
- If `subarrays > k`: `mid` is too small (too many subarrays needed)! Increase `mid` (`low = mid + 1`).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log(\sum \text{nums} - \max(\text{nums})))$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

class Solution {
public:
    int splitArray(vector<int>& nums, int k) {
        int maxVal = 0;
        long long sumVal = 0;

        for (int x : nums) {
            maxVal = max(maxVal, x);
            sumVal += x;
        }

        long long low = maxVal;
        long long high = sumVal;
        long long ans = sumVal;

        while (low <= high) {
            long long mid = low + (high - low) / 2;

            int subarrays = 1;
            long long currentSum = 0;

            for (int x : nums) {
                if (currentSum + x > mid) {
                    subarrays++;      // Start a new subarray
                    currentSum = x;   // Element x belongs to the new subarray
                } else {
                    currentSum += x;
                }
            }

            // If we can split nums into k or fewer subarrays with max sum <= mid
            if (subarrays <= k) {
                ans = mid;           // Valid max sum limit, try smaller limit
                high = mid - 1;
            } else {
                low = mid + 1;       // Limit too small, increase limit
            }
        }

        return (int)ans;
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {7, 2, 5, 10, 8};
    int k1 = 2;
    cout << "Test 1 -> Minimized Largest Sum: " << sol.splitArray(nums1, k1) << " (Expected: 18)\n";

    vector<int> nums2 = {1, 2, 3, 4, 5};
    int k2 = 2;
    cout << "Test 2 -> Minimized Largest Sum: " << sol.splitArray(nums2, k2) << " (Expected: 9)\n";

    return 0;
}
```
