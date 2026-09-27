# Rotate Array (LeetCode 189)

## Problem Summary
Given an integer array `nums`, rotate the array to the right by `k` steps, where `k` is non-negative.

Follow up: Could you do this **in-place with $O(1)$ extra space**?

**Example 1:**
- **Input:** `nums = [1,2,3,4,5,6,7], k = 3`
- **Output:** `[5,6,7,1,2,3,4]`
- **Explanation:**
  - Rotate 1 step right: `[7,1,2,3,4,5,6]`
  - Rotate 2 steps right: `[6,7,1,2,3,4,5]`
  - Rotate 3 steps right: `[5,6,7,1,2,3,4]`

---

## 1. Review & Quick Fixes for Your Solutions

### Improvement 1: Replace `while (k > n)` loop with `k = k % n`
Instead of repeatedly subtracting `nums.size()` using a `while` loop:
```cpp
// ❌ Slow while loop:
while (k > nums.size()) { k = k - nums.size(); }

// ✅ Fast O(1) Modulo:
int n = nums.size();
k = k % n;
```

---

### Improvement 2: Refactoring Your Active `push_back` + `erase` Solution

Your active solution (`push_back` + `erase`) works! Here is the clean version:

```cpp
class SolutionPushErase {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        k = k % n;
        if (k == 0) return;

        int shiftCount = n - k;
        for (int i = 0; i < shiftCount; i++) {
            nums.push_back(nums[i]);
        }
        nums.erase(nums.begin(), nums.begin() + shiftCount);
    }
};
```
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$ (temporary memory allocation when vector expands).

---

## 2. The Gold Standard: Array Reversal Algorithm ($O(N)$ Time, $O(1)$ Space)

To achieve **true $O(1)$ extra space** (no extra vectors or `push_back`), use the **3-Reversal Trick**:

1. **`k = k % n`**
2. **Reverse the ENTIRE array:** `reverse(nums.begin(), nums.end())`
3. **Reverse the FIRST `k` elements:** `reverse(nums.begin(), nums.begin() + k)`
4. **Reverse the REMAINING `n - k` elements:** `reverse(nums.begin() + k, nums.end())`

---

### 📍 Walkthrough (`nums = [1, 2, 3, 4, 5, 6, 7]`, `k = 3`):

1. **Initial Array:** `[1, 2, 3, 4, 5, 6, 7]`
2. **Reverse Entire Array:** `[7, 6, 5, 4, 3, 2, 1]`
3. **Reverse First `k=3` Elements (`7, 6, 5`):** `[5, 6, 7, | 4, 3, 2, 1]`
4. **Reverse Remaining Elements (`4, 3, 2, 1`):** `[5, 6, 7, | 1, 2, 3, 4]`

**Result:** `[5, 6, 7, 1, 2, 3, 4]` 🎯

---

## 3. Optimal C++ Code ($O(N)$ Time, $O(1)$ Space)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = nums.size();
        k = k % n; // Normalize k

        if (k == 0) return;

        // Step 1: Reverse entire array
        reverse(nums.begin(), nums.end());

        // Step 2: Reverse first k elements
        reverse(nums.begin(), nums.begin() + k);

        // Step 3: Reverse remaining n - k elements
        reverse(nums.begin() + k, nums.end());
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Reversing the array takes total $O(N)$ swaps.
- **Space Complexity:** $O(1)$ — Performs all rotations in-place without extra memory.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = nums.size();
        k = k % n;
        if (k == 0) return;

        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin() + k);
        reverse(nums.begin() + k, nums.end());
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {1, 2, 3, 4, 5, 6, 7};
    sol.rotate(nums1, 3);
    cout << "Test 1: ";
    for (int x : nums1) cout << x << " ";
    cout << "(Expected: 5 6 7 1 2 3 4)" << endl;

    vector<int> nums2 = {-1, -100, 3, 99};
    sol.rotate(nums2, 2);
    cout << "Test 2: ";
    for (int x : nums2) cout << x << " ";
    cout << "(Expected: 3 99 -1 -100)" << endl;

    return 0;
}
```
