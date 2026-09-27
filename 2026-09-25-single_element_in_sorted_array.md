# Single Element in a Sorted Array (LeetCode 540)

## Problem Summary
You are given a sorted array consisting of only integers where every element appears exactly twice, except for one element which appears exactly once.

Return the single element that appears only once.

Your solution must run in **$O(\log n)$ time** and **$O(1)$ space**.

**Example 1:**
- **Input:** `nums = [1,1,2,3,3,4,4,8,8]`
- **Output:** `2`

---

## 1. Bug Analysis in Your `mid % 2` Code

### Your Code:
```cpp
while (l <= h) {
    int mid = (h - l) / 2 + l;

    if ((mid % 2 == 0) && (nums[mid] == nums[mid + 1])) {
        l = mid + 1;
    } else if ((mid % 2 == 1) && (nums[mid] == nums[mid + 1])) { // ❌ BUG HERE!
        h = mid - 1;
    }
}
```

### Why It Fails (Infinite Loop / Time Limit Exceeded):

1. **Incorrect Pair Partner when `mid` is ODD:**
   - In a normal pair `(even, odd)`:
     - The **EVEN** index matches its **NEXT** neighbor (`mid + 1`).
     - The **ODD** index matches its **PREVIOUS** neighbor (`mid - 1`).
   - In your code, when `mid` is ODD (`mid % 2 == 1`), you checked `nums[mid] == nums[mid + 1]` instead of `nums[mid] == nums[mid - 1]`!

2. **Unhandled Infinite Loop Case:**
   - What if `mid` is ODD and `nums[mid] == nums[mid - 1]`?
   - `mid % 2 == 0` is `false`.
   - `mid % 2 == 1 && nums[mid] == nums[mid + 1]` is `false` (since `nums[mid]` matches `mid - 1`, not `mid + 1`).
   - **Neither branch executes!** Neither `l` nor `h` changes, causing an **Infinite Loop (Time Limit Exceeded)**.

---

## 2. Fixing Your Code (Using Explicit `mid % 2`)

If you prefer writing out `mid % 2` without bitwise operators:

### Fix Strategy: Make `mid` Always Even!
If `mid` is odd, subtract 1 (`mid--`) so it points to the start of the pair. Then compare `nums[mid] == nums[mid + 1]`:

```cpp
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int l = 0;
        int h = nums.size() - 2;

        while (l <= h) {
            int mid = l + (h - l) / 2;

            // Make mid even so it always points to the first element of a pair
            if (mid % 2 == 1) {
                mid--;
            }

            if (nums[mid] == nums[mid + 1]) {
                l = mid + 2; // Pair is valid, single element is on the RIGHT
            } else {
                h = mid - 1; // Pair is broken, single element is on the LEFT
            }
        }

        return nums[l];
    }
};
```

---

## 3. Alternative Clean Solution using Bitwise `mid ^ 1`

```cpp
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int low = 0;
        int high = nums.size() - 2;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            // mid ^ 1 automatically checks next index if mid is even, or previous index if mid is odd!
            if (nums[mid] == nums[mid ^ 1]) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return nums[low];
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(\log n)$ — Halves search space on each iteration using Binary Search.
- **Space Complexity:** $O(1)$ — Uses only integer pointers (`l`, `h`, `mid`).

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int low = 0;
        int high = nums.size() - 2;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            if (nums[mid] == nums[mid ^ 1]) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return nums[low];
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {1, 1, 2, 3, 3, 4, 4, 8, 8};
    cout << "Test 1: " << sol.singleNonDuplicate(nums1) << " (Expected: 2)" << endl;

    vector<int> nums2 = {3, 3, 7, 7, 10, 11, 11};
    cout << "Test 2: " << sol.singleNonDuplicate(nums2) << " (Expected: 10)" << endl;

    vector<int> nums3 = {1};
    cout << "Test 3: " << sol.singleNonDuplicate(nums3) << " (Expected: 1)" << endl;

    return 0;
}
```
