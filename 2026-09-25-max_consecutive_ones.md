# Max Consecutive Ones (LeetCode 485)

## Problem Summary
Given a binary array `nums`, return the maximum number of consecutive `1`s in the array.

**Example 1:**
- **Input:** `nums = [1,1,0,1,1,1]`
- **Output:** `3`
- **Explanation:** The first two digits or the last three digits are consecutive 1s. The maximum number of consecutive 1s is 3.

**Example 2:**
- **Input:** `nums = [1,0,1,1,0,1]`
- **Output:** `2`

---

## 1. Code Review & Simplification

Your solution is **100% correct** ($O(N)$ Time and $O(1)$ Space)!

### Clean-Up Tip: Eliminating Redundant Pointer `i`
In your code, pointer `i` tracks the start of the current consecutive sequence of 1s. However, you are already keeping track of `count`. 

Because `count` automatically tracks how many consecutive `1`s you have seen so far, **pointer `i` is redundant**!

---

## 2. Simplified & Optimal Solution

Instead of two pointers `i` and `j`, use a simple single loop:
- If `num == 1`: Increment `currentOnes++` and update `maxOnes = max(maxOnes, currentOnes)`.
- If `num == 0`: Reset `currentOnes = 0`.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int maxOnes = 0;
        int currentOnes = 0;

        for (int num : nums) {
            if (num == 1) {
                currentOnes++;
                maxOnes = max(maxOnes, currentOnes);
            } else {
                currentOnes = 0; // Reset count when 0 is encountered
            }
        }

        return maxOnes;
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single pass through the `nums` array.
- **Space Complexity:** $O(1)$ — Uses only 2 integer variables (`maxOnes`, `currentOnes`).

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int maxOnes = 0;
        int currentOnes = 0;

        for (int num : nums) {
            if (num == 1) {
                currentOnes++;
                maxOnes = max(maxOnes, currentOnes);
            } else {
                currentOnes = 0;
            }
        }

        return maxOnes;
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {1, 1, 0, 1, 1, 1};
    cout << "Test 1: " << sol.findMaxConsecutiveOnes(nums1) << " (Expected: 3)" << endl;

    vector<int> nums2 = {1, 0, 1, 1, 0, 1};
    cout << "Test 2: " << sol.findMaxConsecutiveOnes(nums2) << " (Expected: 2)" << endl;

    return 0;
}
```
