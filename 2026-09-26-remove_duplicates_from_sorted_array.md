# Remove Duplicates from Sorted Array (LeetCode 26)

## Problem Summary
Given an integer array `nums` sorted in non-decreasing order, remove the duplicates **in-place** such that each unique element appears only once. The relative order of the elements should be kept the same.

Return `k` (the number of unique elements in `nums`).

---

## 1. Bugs in Your Initial Code

### Bug 1: Wrong Return Value
```cpp
return nums.size() - s.size(); // ❌ CRITICAL BUG!
```
- **Why it fails:** `nums.size() - s.size()` calculates the number of **deleted duplicate items**, not the count `k` of **unique elements remaining**!
- **Example:** For `nums = [1, 1, 2]`, `s.size()` is `2`. You returned `3 - 2 = 1`. But LeetCode expects `2` (the number of unique elements)!
- **Fix:** Return `s.size()` (or `i + 1` in two-pointer approach).

---

### Bug 2: Fails the $O(1)$ Space Constraint
- Using `set<int> s` allocates $O(N)$ extra memory and takes $O(N \log N)$ time.
- The problem explicitly constraints: **"Must be solved in-place with $O(1)$ extra memory."**

---

## 2. Optimal Two-Pointer Solution ($O(N)$ Time, $O(1)$ Space)

Since `nums` is **already sorted**, all duplicate elements are adjacent to each other!

### Two-Pointer Strategy:
- `i` tracks the index of the **last unique element found**.
- `j` scans through the array from index `1` to `n - 1`.
- When `nums[j] != nums[i]`:
  - Advance `i++`.
  - Copy `nums[i] = nums[j]`.
- Return `i + 1` (the total count of unique elements `k`).

---

## 3. Clean C++ Solution

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        if (nums.empty()) return 0;

        int i = 0; // Index of last unique element

        for (int j = 1; j < nums.size(); j++) {
            if (nums[j] != nums[i]) {
                i++;
                nums[i] = nums[j]; // Move new unique element into position
            }
        }

        return i + 1; // Return number of unique elements (k)
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single pass through `nums` using two pointers.
- **Space Complexity:** $O(1)$ — Performs all replacements in-place without extra memory.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        if (nums.empty()) return 0;

        int i = 0;

        for (int j = 1; j < nums.size(); j++) {
            if (nums[j] != nums[i]) {
                i++;
                nums[i] = nums[j];
            }
        }

        return i + 1;
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {1, 1, 2};
    int k1 = sol.removeDuplicates(nums1);
    cout << "Test 1 Unique Count: " << k1 << " (Expected: 2)\nElements: ";
    for (int idx = 0; idx < k1; idx++) cout << nums1[idx] << " ";
    cout << endl;

    vector<int> nums2 = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4};
    int k2 = sol.removeDuplicates(nums2);
    cout << "Test 2 Unique Count: " << k2 << " (Expected: 5)\nElements: ";
    for (int idx = 0; idx < k2; idx++) cout << nums2[idx] << " ";
    cout << endl;

    return 0;
}
```
