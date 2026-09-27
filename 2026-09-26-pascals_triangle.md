# Pascal's Triangle

## Problem Description
Given an integer `numRows`, return the first `numRows` of **Pascal's Triangle**.

In Pascal's Triangle, each number is the sum of the two numbers directly above it.

**Example 1:**
- **Input:** `numRows = 5`
- **Output:** `[[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]`

---

## Bugs in User's Initial Code

1. **Vector Out-of-Bounds Error (Segmentation Fault):**
   - `vector<vector<int>> ans;` creates an empty 2D vector with size `0`.
   - Accessing `ans[rows][cols]` directly without allocating memory (using `.push_back()` or `.resize()`) causes a runtime segmentation fault.

2. **Infinite Loop Bug:**
   - In the `if (cols == 0)` block:
     ```cpp
     if (cols == 0) {
         ans[rows][cols] = 1;
         // missing cols++!
     }
     ```
     Because `cols` is never incremented when `cols == 0`, `cols` remains `0` forever, resulting in an infinite loop for `rows > 0`.

3. **Incorrect Row Length / Boundary Logic:**
   - A row `i` (0-indexed) has `i + 1` elements (indices `0` to `i`).
   - Checking `cols == rows - 1` ends the row prematurely at index `rows - 1` instead of `rows`.

---

## Optimal Approach & Intuition

1. **Row Structure:**
   - Row `i` (0-indexed) contains `i + 1` elements.
   - The first element (`j = 0`) and the last element (`j = i`) of every row are always `1`.

2. **Inner Elements Formula:**
   - For all elements between the boundaries ($1 \le j < i$):
     $$\text{row}[j] = \text{ans}[i-1][j-1] + \text{ans}[i-1][j]$$

3. **Clean Implementation:**
   - Initialize each row vector with size `i + 1` and default value `1`.
   - Fill inner values using a nested loop from `j = 1` to `j = i - 1`.
   - Append the row to `ans`.

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(\text{numRows}^2)$
  - Total numbers generated is $1 + 2 + 3 + \dots + \text{numRows} = \frac{\text{numRows} \cdot (\text{numRows} + 1)}{2}$.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (excluding the output `ans` 2D vector).

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;
        
        for (int i = 0; i < numRows; i++) {
            // Create a row of size (i + 1) initialized with 1s
            vector<int> row(i + 1, 1);
            
            // Fill in the inner elements (between index 1 and i - 1)
            for (int j = 1; j < i; j++) {
                row[j] = ans[i - 1][j - 1] + ans[i - 1][j];
            }
            
            ans.push_back(row);
        }
        
        return ans;
    }
};

int main() {
    Solution sol;
    int numRows = 5;
    vector<vector<int>> result = sol.generate(numRows);

    cout << "Pascal's Triangle for numRows = " << numRows << ":\n";
    for (const auto& row : result) {
        cout << "[ ";
        for (int val : row) {
            cout << val << " ";
        }
        cout << "]\n";
    }

    return 0;
}
```
