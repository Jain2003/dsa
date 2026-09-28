# Next Permutation & How Permutations are Formed

## Problem Description
A **permutation** of an array of integers is an arrangement of its members into a sequence or linear order.

For example, for `arr = [1, 2, 3]`, all permutations in **lexicographical (dictionary) order** are:
1. `[1, 2, 3]`
2. `[1, 3, 2]`
3. `[2, 1, 3]`
4. `[2, 3, 1]`
5. `[3, 1, 2]`
6. `[3, 2, 1]`

The **next permutation** of an array of integers is the next lexicographically greater permutation of its integer. If no such arrangement is possible (i.e., array is sorted in descending order like `[3, 2, 1]`), the array must be rearranged as the lowest possible order (i.e., sorted in ascending order `[1, 2, 3]`).

You must solve this in-place with $\mathcal{O}(1)$ extra space.

---

## 1. How Permutations Are Formed (Intuition & Decision Tree)

Think of forming permutations as filling slots from left to right: `[Slot 1, Slot 2, Slot 3]` using available numbers `{1, 2, 3}`.

```
                    Root (Choose Slot 1)
                /            |            \
            Pick 1         Pick 2        Pick 3
           /      \       /      \      /      \
        Pick 2   Pick 3 Pick 1  Pick 3 Pick 1  Pick 2
          |        |      |       |      |        |
        [1,2,3]  [1,3,2] [2,1,3] [2,3,1] [3,1,2] [3,2,1]
```

### Lexicographical (Dictionary) Order
Just like dictionary words (`cat`, `cow`, `dog`):
- `[1, 2, 3]` comes before `[1, 3, 2]` because at index 1, `2 < 3`.
- `[1, 3, 2]` comes before `[2, 1, 3]` because at index 0, `1 < 2`.

---

## 2. Algorithm to Find the "Next Permutation" in $\mathcal{O}(N)$ Time

To transform any permutation (e.g. `[2, 1, 5, 4, 3]`) into its **immediate next lexicographical successor**:

### Step 1: Find the Breakpoint / Pivot (`nums[i] < nums[i + 1]`)
Scan **from right to left** to find the first element that breaks the descending order from the right.
- In `[2, 1, 5, 4, 3]`, scanning from right (`3 -> 4 -> 5 -> 1`):
- `1` is smaller than `5`. So index $i = 1$ (`nums[1] = 1`) is our breakpoint!
- *Why?* Everything to the right of `1` (`[5, 4, 3]`) is in descending order, meaning no larger permutation can be formed using just those rightmost elements.

### Step 2: Find the Next Greater Element (`nums[j] > nums[i]`)
Scan **from right to left** to find the first element greater than `nums[i]`.
- We want to replace `1` with the smallest available number larger than `1` from the right side.
- In `[5, 4, 3]`, scanning from right, `3` is the first element $> 1$.
- So $j = 4$ (`nums[4] = 3`).

### Step 3: Swap `nums[i]` and `nums[j]`
- Swap `1` and `3`: `[2, 1, 5, 4, 3]` becomes `[2, 3, 5, 4, 1]`.

### Step 4: Reverse the Suffix (`i + 1` to end)
- The suffix after index $i$ (`[5, 4, 1]`) is still in descending order.
- Reverse it to make it ascending (smallest possible value): `[5, 4, 1]` becomes `[1, 4, 5]`.
- Final array: **`[2, 3, 1, 4, 5]`**!

---

## Edge Case: Entire Array is Descending (`[3, 2, 1]`)
- No breakpoint exists ($i = -1$).
- Simply reverse the entire array to get the smallest permutation (`[1, 2, 3]`).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N)$ — At most 3 passes over the array (finding breakpoint, finding swap target, reversing suffix).
- **Space Complexity:** $\mathcal{O}(1)$ — In-place operations only.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        int i = n - 2;

        // Step 1: Find the breakpoint (first element from right where nums[i] < nums[i+1])
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Step 2: If breakpoint exists, find the smallest element from right greater than nums[i]
        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            // Step 3: Swap nums[i] and nums[j]
            swap(nums[i], nums[j]);
        }

        // Step 4: Reverse the suffix starting at i + 1
        reverse(nums.begin() + i + 1, nums.end());
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 2, 3};

    cout << "Original: [ 1 2 3 ]\n";
    sol.nextPermutation(nums);
    cout << "Next Permutation: [ ";
    for (int x : nums) {
        cout << x << " ";
    }
    cout << "]\n";

    return 0;
}
```
