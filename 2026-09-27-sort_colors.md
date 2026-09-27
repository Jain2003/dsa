# Sort Colors (Dutch National Flag)

## Problem Description
Given an array `nums` with `n` objects colored red, white, or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

**Example 1:**
- **Input:** `nums = [2,0,2,1,1,0]`
- **Output:** `[0,0,1,1,2,2]`

**Follow-up:** Could you come up with a **one-pass** algorithm using only **$\mathcal{O}(1)$ constant extra space**?

---

## Analysis of Approaches

### Approach 1: Bubble Sort (User's Code)
- **Time Complexity:** $\mathcal{O}(N^2)$
- **Space Complexity:** $\mathcal{O}(1)$
- **Assessment:** Correct output, but inefficient for large arrays.

### Approach 2: Counting Sort (2-Pass Algorithm)
- Count occurrences of `0`s, `1`s, and `2`s in the first pass.
- Overwrite `nums` with `0`s, `1`s, and `2`s in the second pass.
- **Time Complexity:** $\mathcal{O}(N)$ (2 passes)
- **Space Complexity:** $\mathcal{O}(1)$

### Approach 3: Dutch National Flag Algorithm (1-Pass 3-Pointer Algorithm) 🏆 Optimal!
Invented by Edsger W. Dijkstra, this algorithm uses **3 pointers** to partition the array in **a single pass**:
- `low = 0`
- `mid = 0`
- `high = n - 1`

#### Pointers & Invariants:
- `nums[0 ... low - 1]` $\rightarrow$ All `0`s (Red)
- `nums[low ... mid - 1]` $\rightarrow$ All `1`s (White)
- `nums[mid ... high]` $\rightarrow$ Unprocessed elements
- `nums[high + 1 ... n - 1]` $\rightarrow$ All `2`s (Blue)

#### Traversal Rules (`while (mid <= high)`):
1. **If `nums[mid] == 0`:** Swap `nums[low]` and `nums[mid]`, then `low++`, `mid++`.
2. **If `nums[mid] == 1`:** `mid++` (1 is already in its correct section).
3. **If `nums[mid] == 2`:** Swap `nums[mid]` and `nums[high]`, then `high--`. *(Do NOT increment `mid` here because the element swapped from `high` hasn't been checked yet!)*

---

## Complexity Comparison

| Algorithm | Passes | Time Complexity | Auxiliary Space |
| :--- | :--- | :--- | :--- |
| **Bubble Sort** | $N$ passes | $\mathcal{O}(N^2)$ | $\mathcal{O}(1)$ |
| **Counting Sort** | 2 passes | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ |
| **Dutch National Flag** | **1 pass** 🏆 | **$\mathcal{O}(N)$** | **$\mathcal{O}(1)$** |

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void sortColors(vector<int>& nums) {
        int low = 0;
        int mid = 0;
        int high = nums.size() - 1;

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums[low], nums[mid]);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else { // nums[mid] == 2
                swap(nums[mid], nums[high]);
                high--; // Note: mid is NOT incremented here!
            }
        }
    }
};

int main() {
    Solution sol;
    vector<int> nums = {2, 0, 2, 1, 1, 0};

    sol.sortColors(nums);

    cout << "Sorted Colors: [ ";
    for (int x : nums) {
        cout << x << " ";
    }
    cout << "]\n";

    return 0;
}
```
