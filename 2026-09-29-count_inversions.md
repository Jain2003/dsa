# Count Inversions in an Array

## Problem Description
Given an integer array `nums` of size `n`. Return the total number of **inversions** in the array.

A pair of indices `(i, j)` forms an inversion if:
- $i < j$
- $\text{nums}[i] > \text{nums}[j]$

**Example 1:**
- **Input:** `nums = [2, 3, 7, 1, 3, 5]`
- **Output:** `5`
- **Explanation:** 
  1. `(2, 1)` at indices $(0, 3)$
  2. `(3, 1)` at indices $(1, 3)$
  3. `(7, 1)` at indices $(2, 3)$
  4. `(7, 3)` at indices $(2, 4)$
  5. `(7, 5)` at indices $(2, 5)$

**Example 2:**
- **Input:** `nums = [-10, -5, 6, 11, 15, 17]`
- **Output:** `0` (Array is sorted, no inversions)

---

## 1. Why Your `lastMin` Logic Fails

In your code:
- You attempted to skip checking elements by setting `lastMin` and starting `j` from `lastMin`.
- **The Flaw:** If `lastMin` gets set to a larger index (e.g. `j = 4`) during an earlier pass, then for a subsequent index `i`, starting `j` at `lastMin` (index 4) **completely skips checking indices between `i + 1` and `lastMin - 1`**!
- For example, if `nums = [10, 2, 9, 8, 7]`:
  - For `i = 1` (`2`), `lastMin` jumps to `3`.
  - For `i = 2` (`9`), `j` starts at `3` instead of `i + 1`. If `lastMin` was higher, elements between `i + 1` and `lastMin` are skipped, resulting in **missing inversions**.

---

## 2. Optimal Approach: Modified Merge Sort ($\mathcal{O}(N \log N)$ Time)

The standard brute force checks all pairs in $\mathcal{O}(N^2)$ time. To solve this in $\mathcal{O}(N \log N)$ time, we use **Merge Sort**:

### The Key Insight during the Merge Step:
Suppose we have two sorted halves:
- `left` half: `[low ... mid]` (sorted)
- `right` half: `[mid + 1 ... high]` (sorted)

When merging `left` and `right`:
- We compare `nums[i]` (from left) and `nums[j]` (from right).
- If `nums[i] > nums[j]`:
  - Since the `left` half is already sorted, if `nums[i]` is greater than `nums[j]`, then **ALL remaining elements from `i` to `mid`** are also greater than `nums[j]`!
  - Therefore, we can count **`(mid - i + 1)`** inversions instantly in $\mathcal{O}(1)$ time!

```
Left (Sorted):  [ 3, 5, 7 ]     Right (Sorted): [ 1, 4 ]
                  ^ (i=0)                         ^ (j=0)

3 > 1! Since 3 > 1, all elements after 3 in Left ([3, 5, 7]) are also > 1!
Inversions added = (mid - i + 1) = (2 - 0 + 1) = 3 inversions!
```

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log N)$ — Standard Merge Sort recursion tree depth $\log N$, each level taking $\mathcal{O}(N)$ work.
- **Space Complexity:** $\mathcal{O}(N)$ — Auxiliary vector used during the merge step.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
private:
    long long merge(vector<int>& nums, int low, int mid, int high) {
        vector<int> temp;
        int left = low;
        int right = mid + 1;
        long long count = 0;

        while (left <= mid && right <= high) {
            if (nums[left] <= nums[right]) {
                temp.push_back(nums[left]);
                left++;
            } else {
                // nums[left] > nums[right] -> Inversion found!
                // All elements from left to mid are greater than nums[right]
                count += (mid - left + 1);
                temp.push_back(nums[right]);
                right++;
            }
        }

        // Copy remaining elements of left half
        while (left <= mid) {
            temp.push_back(nums[left]);
            left++;
        }

        // Copy remaining elements of right half
        while (right <= high) {
            temp.push_back(nums[right]);
            right++;
        }

        // Copy temp back to original array
        for (int i = low; i <= high; i++) {
            nums[i] = temp[i - low];
        }

        return count;
    }

    long long mergeSort(vector<int>& nums, int low, int high) {
        long long count = 0;
        if (low >= high) return count;

        int mid = low + (high - low) / 2;
        count += mergeSort(nums, low, mid);       // Count in left half
        count += mergeSort(nums, mid + 1, high);  // Count in right half
        count += merge(nums, low, mid, high);     // Count split inversions during merge

        return count;
    }

public:
    long long numberOfInversions(vector<int> nums) {
        return mergeSort(nums, 0, nums.size() - 1);
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {2, 3, 7, 1, 3, 5};
    cout << "Inversions for [2, 3, 7, 1, 3, 5]: " << sol.numberOfInversions(nums1) << " (Expected: 5)\n";

    vector<int> nums2 = {-10, -5, 6, 11, 15, 17};
    cout << "Inversions for [-10, -5, 6, 11, 15, 17]: " << sol.numberOfInversions(nums2) << " (Expected: 0)\n";

    return 0;
}
```
