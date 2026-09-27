# Insertion Sort

## Problem Summary
Sort an array in ascending order using **Insertion Sort**.

---

## Real-World Analogy (Playing Cards)
Insertion Sort works just like sorting a hand of playing cards:
1. You start with a sorted hand of 1 card (the first element `nums[0]`).
2. You pick the next unsorted card (`key = nums[i]`).
3. You scan backwards through your sorted hand (`while (j >= 0 && nums[j] > key)`), shifting larger cards one spot to the right to make room.
4. You drop `key` into its correct sorted slot (`nums[j + 1] = key`).

---

## Step-by-Step Trace for `{13, 46, 24, 52, 20, 9}`

- **Initially:** `[13 | 46, 24, 52, 20, 9]` *(Sorted subarray: `[13]`)*

- **Pass `i = 1` (`key = 46`):**
  - Compare `46` with `13`: `13 < 46`, no shift needed.
  - Array: `[13, 46 | 24, 52, 20, 9]`

- **Pass `i = 2` (`key = 24`):**
  - Compare `24` with `46`: `46 > 24` $\rightarrow$ shift `46` right: `[13, 46, 46, 52, 20, 9]`.
  - Compare `24` with `13`: `13 < 24` $\rightarrow$ stop.
  - Place `key` at `nums[1] = 24`.
  - Array: `[13, 24, 46 | 52, 20, 9]`

- **Pass `i = 3` (`key = 52`):**
  - Compare `52` with `46`: `46 < 52`, no shift needed.
  - Array: `[13, 24, 46, 52 | 20, 9]`

- **Pass `i = 4` (`key = 20`):**
  - Shifts `52` and `46` right, places `20` at index 1.
  - Array: `[13, 20, 24, 46, 52 | 9]`

- **Pass `i = 5` (`key = 9`):**
  - Shifts `52`, `46`, `24`, `20`, `13` right, places `9` at index 0.
  - Final Array: **`[9, 13, 20, 24, 46, 52]`**

---

## Complexity & Attributes

- **Best-Case Time Complexity:** **$O(N)$**
  - *Why?* When the array is already sorted (e.g., `[1, 2, 3, 4, 5]`), `nums[j] > key` fails immediately on every outer pass. The inner loop never shifts elements.
- **Worst-Case Time Complexity:** **$O(N^2)$**
  - *Why?* When the array is reverse sorted (e.g., `[5, 4, 3, 2, 1]`), every element must shift all the way to the start.
- **Space Complexity:** **$O(1)$** auxiliary space (In-place sorting).
- **Stability:** **Stable** (Does not alter relative order of equal elements because `nums[j] > key` stops when elements are equal).

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    // Function to sort the array using insertion sort
    vector<int> insertionSort(vector<int>& nums) {
        int n = nums.size(); // Size of the array 
        
        // For every element from index 1 to n-1
        for (int i = 1; i < n; i++) {
            int key = nums[i]; // Current element to be inserted into sorted portion
            int j = i - 1; 
            
            // Shift elements that are greater than key by one position to the right
            while (j >= 0 && nums[j] > key) {
                nums[j + 1] = nums[j];
                j--;
            }
            
            nums[j + 1] = key; // Insert key at correct position
        }
        
        return nums;
    }
};

int main() {
    Solution solution;
    
    vector<int> nums = {13, 46, 24, 52, 20, 9};
    
    cout << "Before Using Insertion Sort: " << endl;
    for (int num : nums) {
        cout << num << " ";
    }
    cout << endl;

    // Function call for insertion sort
    nums = solution.insertionSort(nums);

    cout << "After Using Insertion Sort: " << endl;
    for (int num : nums) {
        cout << num << " ";
    }
    cout << endl;

    return 0;
}
```
