# Move Zeroes (LeetCode 283)

## Problem Summary
Given an integer array `nums`, move all `0`s to the end of it while maintaining the relative order of the non-zero elements.

Note that you must do this **in-place** without making a copy of the array.

**Example 1:**
- **Input:** `nums = [0,1,0,3,12]`
- **Output:** `[1,3,12,0,0]`

**Example 2:**
- **Input:** `nums = [0]`
- **Output:** `[0]`

---

## 1. Code Review & Simplification

Your solution logic works, but it has complex nested `if/else` conditions and peek-ahead checks like `j + 1`.

### The Standard Two-Pointer Pattern ($O(N)$ Time, $O(1)$ Space)

We can simplify the entire algorithm down to **4 lines inside a single loop**!

#### Core Idea:
- Maintain pointer `i` representing the index where the **next non-zero element** should be placed.
- Pointer `j` iterates through the array from `0` to `n - 1`.
- Whenever `nums[j] != 0`:
  - `swap(nums[i], nums[j]);`
  - Increment `i++`.

---

## 2. Walkthrough Example (`nums = [0, 1, 0, 3, 12]`)

Initial: `i = 0`

1. **`j = 0` (`nums[0] = 0`):** Is 0. Do nothing.
2. **`j = 1` (`nums[1] = 1`):** Non-zero! `swap(nums[0], nums[1])` $\rightarrow$ `nums = [1, 0, 0, 3, 12]`, `i = 1`.
3. **`j = 2` (`nums[2] = 0`):** Is 0. Do nothing.
4. **`j = 3` (`nums[3] = 3`):** Non-zero! `swap(nums[1], nums[3])` $\rightarrow$ `nums = [1, 3, 0, 0, 12]`, `i = 2`.
5. **`j = 4` (`nums[4] = 12`):** Non-zero! `swap(nums[2], nums[4])` $\rightarrow$ `nums = [1, 3, 12, 0, 0]`, `i = 3`.

**Final Array:** `[1, 3, 12, 0, 0]` 🎯

---

## 3. Clean C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int i = 0; // Pointer to place next non-zero element

        for (int j = 0; j < nums.size(); j++) {
            if (nums[j] != 0) {
                swap(nums[i], nums[j]);
                i++;
            }
        }
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single pass through `nums` array of size $N$.
- **Space Complexity:** $O(1)$ — In-place operations without extra array memory.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int i = 0;

        for (int j = 0; j < nums.size(); j++) {
            if (nums[j] != 0) {
                swap(nums[i], nums[j]);
                i++;
            }
        }
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {0, 1, 0, 3, 12};
    sol.moveZeroes(nums1);
    cout << "Test 1: ";
    for (int x : nums1) cout << x << " ";
    cout << "(Expected: 1 3 12 0 0)" << endl;

    vector<int> nums2 = {0};
    sol.moveZeroes(nums2);
    cout << "Test 2: ";
    for (int x : nums2) cout << x << " ";
    cout << "(Expected: 0)" << endl;

    return 0;
}
```
