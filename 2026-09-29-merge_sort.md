# Merge Sort

## Problem Description
Given an array of integers `nums`, sort the array in ascending order using the **Merge Sort** algorithm.

**Example 1:**
- **Input:** `nums = [5, 2, 3, 1]`
- **Output:** `[1, 2, 3, 5]`

**Example 2:**
- **Input:** `nums = [5, 1, 1, 2, 0, 0]`
- **Output:** `[0, 0, 1, 1, 2, 5]`

---

## Simple Intuition: "Divide and Conquer"

Sorting a large list of 1,000 numbers is hard.
Sorting 2 single numbers is trivial!

Merge Sort works by:
1. **DIVIDE:** Keep splitting the array in half recursively until every subarray has only **1 element**. *(A single element is naturally already sorted!)*
2. **CONQUER & MERGE:** Take two adjacent sorted smaller arrays and merge them back together into a larger sorted array.

---

## 🂠 Real-Life Analogy (Deck of Playing Cards)

Imagine you are handed 8 unsorted cards:
1. You split the deck into two piles of 4 cards.
2. You split those into piles of 2 cards.
3. You split those into single cards (8 piles of 1 card each).
4. **Merge Phase:**
   - Look at two 1-card piles: compare them and place them in order into a 2-card sorted pile.
   - Look at two 2-card sorted piles: compare the top cards of each pile, take the smaller one, and build a 4-card sorted pile.
   - Finally, merge the two 4-card sorted piles into one complete 8-card sorted deck!

---

## Visualizing the Tree: `[4, 2, 1, 3]`

```
                       [ 4, 2, 1, 3 ]                  <-- Original Array
                         /        \
                    [ 4, 2 ]    [ 1, 3 ]               <-- DIVIDE Phase
                    /    \        /    \
                  [4]    [2]    [1]    [3]             <-- Single elements (Sorted!)
                    \    /        \    /
                    [ 2, 4 ]    [ 1, 3 ]               <-- MERGE Phase
                         \        /
                       [ 1, 2, 3, 4 ]                  <-- Fully Sorted Array!
```

---

## How the `merge()` Step Works

How do you merge two already sorted arrays `Left = [2, 4]` and `Right = [1, 3]`?

We use two pointers `i` (for Left) and `j` (for Right):
1. **Compare `Left[0] (2)` and `Right[0] (1)`:** `1` is smaller $\rightarrow$ Place `1`. Move `j`.
2. **Compare `Left[0] (2)` and `Right[1] (3)`:** `2` is smaller $\rightarrow$ Place `2`. Move `i`.
3. **Compare `Left[1] (4)` and `Right[1] (3)`:** `3` is smaller $\rightarrow$ Place `3`. Move `j`.
4. **Copy remaining elements (`4`):** Place `4`.
5. **Result:** `[1, 2, 3, 4]` in $\mathcal{O}(N)$ time!

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log N)$
  - Splitting the array takes $\log N$ levels of division.
  - Merging at each level takes $\mathcal{O}(N)$ total work across all subarrays.
  - Overall time is $\mathcal{O}(N \log N)$ in Best, Average, and Worst cases!
- **Space Complexity:** $\mathcal{O}(N)$ auxiliary memory for the temporary array during merging.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
private:
    // Helper function to merge two sorted halves: [low...mid] and [mid+1...high]
    void merge(vector<int>& nums, int low, int mid, int high) {
        vector<int> temp;
        int left = low;
        int right = mid + 1;

        // Compare elements from both halves and insert the smaller one into temp
        while (left <= mid && right <= high) {
            if (nums[left] <= nums[right]) {
                temp.push_back(nums[left]);
                left++;
            } else {
                temp.push_back(nums[right]);
                right++;
            }
        }

        // Copy remaining elements of left half (if any)
        while (left <= mid) {
            temp.push_back(nums[left]);
            left++;
        }

        // Copy remaining elements of right half (if any)
        while (right <= high) {
            temp.push_back(nums[right]);
            right++;
        }

        // Copy merged elements back into the original nums array
        for (int i = low; i <= high; i++) {
            nums[i] = temp[i - low];
        }
    }

    void mergeSortHelper(vector<int>& nums, int low, int high) {
        // Base Case: 1 element left -> already sorted
        if (low >= high) return;

        int mid = low + (high - low) / 2;

        // 1. DIVIDE: Recursively sort left and right halves
        mergeSortHelper(nums, low, mid);
        mergeSortHelper(nums, mid + 1, high);

        // 2. CONQUER: Merge the two sorted halves
        merge(nums, low, mid, high);
    }

public:
    vector<int> sortArray(vector<int>& nums) {
        mergeSortHelper(nums, 0, nums.size() - 1);
        return nums;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {4, 2, 1, 3};

    sol.sortArray(nums);

    cout << "Sorted Array: [ ";
    for (int x : nums) {
        cout << x << " ";
    }
    cout << "]\n";

    return 0;
}
```
