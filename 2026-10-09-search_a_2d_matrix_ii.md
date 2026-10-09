# Search a 2D Matrix II (Staircase Search)

## Problem Description
Write an efficient algorithm that searches for a value `target` in an `m x n` integer matrix `matrix`. This matrix has the following properties:
1. Integers in each row are sorted in ascending order from left to right.
2. Integers in each column are sorted in ascending order from top to bottom.

**Example 1:**
- **Input:** 
  ```
  matrix = [
    [1,  4,  7,  11, 15],
    [2,  5,  8,  12, 19],
    [3,  6,  9,  16, 22],
    [10, 13, 14, 17, 24],
    [18, 21, 23, 26, 30]
  ]
  target = 5
  ```
- **Output:** `true`

**Example 2:**
- **Input:** `target = 20` (same matrix)
- **Output:** `false`

---

## Key Insight: Top-Right Corner Staircase Search

Why can't we perform a single standard 1D binary search like in *Search a 2D Matrix I*?
Because the matrix is NOT flattened into a single sorted 1D array. The first element of row $k$ is not necessarily greater than the last element of row $k-1$.

However, notice the decision properties of the **Top-Right Corner (`matrix[0][cols - 1]`)**:
- All elements to its **left** are **smaller** (row is sorted left-to-right).
- All elements below it are **larger** (column is sorted top-to-bottom).

This unique property gives us a clear decision path at every step:
- If `matrix[i][j] == target` $\rightarrow$ **Found!** Return `true`.
- If `matrix[i][j] > target` $\rightarrow$ Target is smaller. Eliminate column `j` and move **left (`j--`)**.
- If `matrix[i][j] < target` $\rightarrow$ Target is larger. Eliminate row `i` and move **down (`i++`)**.

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(m + n)$
  - In each step of the loop, either `i` increases or `j` decreases.
  - The maximum number of steps from top-right to bottom-left is $m + n$.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;

        int i = 0;
        int j = matrix[0].size() - 1; // Start at top-right corner

        while (j >= 0 && i < matrix.size()) {
            if (matrix[i][j] == target) {
                return true;
            } else if (matrix[i][j] > target) {
                j--; // Target is smaller, move left
            } else {
                i++; // Target is larger, move down
            }
        }

        return false;
    }
};

int main() {
    Solution sol;

    vector<vector<int>> matrix = {
        {1,  4,  7,  11, 15},
        {2,  5,  8,  12, 19},
        {3,  6,  9,  16, 22},
        {10, 13, 14, 17, 24},
        {18, 21, 23, 26, 30}
    };

    cout << "Search 5: " << (sol.searchMatrix(matrix, 5) ? "Found" : "Not Found") << " (Expected: Found)\n";
    cout << "Search 20: " << (sol.searchMatrix(matrix, 20) ? "Found" : "Not Found") << " (Expected: Not Found)\n";

    return 0;
}
```
