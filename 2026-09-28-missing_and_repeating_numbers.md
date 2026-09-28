# Find Missing and Repeating Numbers

## Problem Description
Given an integer array `nums` of size `n` containing values from `[1, n]`. Every value from `1` to `n` appears exactly once, except for:
- **Repeating Number**: Appears **twice**
- **Missing Number**: Appears **zero times**

Return `[repeating, missing]` as an array of size 2.

**Example 1:**
- **Input:** `nums = [3, 1, 2, 5, 3]`
- **Output:** `[3, 4]` (3 is repeating, 4 is missing)

**Example 2:**
- **Input:** `nums = [1, 2, 3, 3]`
- **Output:** `[3, 4]`

---

## 1. Frequency Map Approach (User's Intuitive Solution)

### Intuition:
1. Count the frequency of each number using a map / hash table.
2. Iterate through the expected numbers from `1` to `n` (the full range):
   - If frequency is `2`, it is the **repeating number**.
   - If frequency is `0`, it is the **missing number**.

### Complexity:
- **Time Complexity:** $\mathcal{O}(N \log N)$ using `std::map` (or $\mathcal{O}(N)$ using `std::unordered_map` / frequency array).
- **Space Complexity:** $\mathcal{O}(N)$ auxiliary space for the map.

---

## 2. Optimal In-Place Index Marking Approach ($\mathcal{O}(N)$ Time, $\mathcal{O}(1)$ Space)

If allowed to modify the array or mark elements:
1. For each number `x = abs(nums[i])`:
   - Check `nums[x - 1]`.
   - If `nums[x - 1] < 0`, `x` was already visited $\rightarrow$ **Repeating Number**!
   - Otherwise, negate `nums[x - 1] = -nums[x - 1]`.
2. Find the index `i` that remains positive $\rightarrow$ **Missing Number = `i + 1`**.

---

## 3. Optimal Mathematical Approach ($\mathcal{O}(N)$ Time, $\mathcal{O}(1)$ Space, Read-Only)

Using sum ($S$) and sum of squares ($S_2$):
- $A - B = S - S_N$
- $A + B = \frac{S_2 - S_{2N}}{A - B}$
- $A = \frac{\text{val1} + \text{val2}}{2}, \quad B = \text{val2} - A$

---

## Complexity Comparison

| Approach | Time Complexity | Auxiliary Space | Read-Only Array? |
| :--- | :--- | :--- | :---: |
| **Frequency Map (User Code)** | $\mathcal{O}(N \log N)$ (or $\mathcal{O}(N)$) | $\mathcal{O}(N)$ | ✅ Yes |
| **Index Marking** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | ❌ No |
| **Mathematical Solution** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | ✅ Yes |

---

## Code (User's Frequency Map Solution)

```cpp
#include <iostream>
#include <vector>
#include <map>

using namespace std;

class Solution {
public:
    vector<int> findMissingRepeatingNumbers(vector<int> nums) {
        map<int, int> mp;
        
        // Step 1: Count frequency of each number
        for (auto x : nums) {
            mp[x]++;
        }
        
        int repeat = -1;
        int missing = -1;
        
        // Step 2: Check full expected range 1 to n
        for (int i = 1; i <= nums.size(); i++) {
            if (mp[i] == 2) {
                repeat = i;
            } else if (mp[i] == 0) {
                missing = i;
            }
        }
        
        return {repeat, missing};
    }
};

int main() {
    Solution sol;

    // Test 1: Standard case
    vector<int> nums1 = {3, 1, 2, 5, 3};
    vector<int> res1 = sol.findMissingRepeatingNumbers(nums1);
    cout << "Test 1 -> Repeating: " << res1[0] << ", Missing: " << res1[1] << " (Expected: [3, 4])\n";

    // Test 2: Missing last element n
    vector<int> nums2 = {1, 2, 3, 3};
    vector<int> res2 = sol.findMissingRepeatingNumbers(nums2);
    cout << "Test 2 -> Repeating: " << res2[0] << ", Missing: " << res2[1] << " (Expected: [3, 4])\n";

    return 0;
}
```
