# Find a Peak Element II (Binary Search on 2D Matrix)

## Problem Description
A **peak element** in a 2D grid is an element that is **strictly greater** than all of its adjacent neighbors to the left, right, top, and bottom.

Given a 0-indexed `m x n` matrix `mat` where no two adjacent cells are equal, find any peak element `mat[i][j]` and return the length 2 array `[i, j]`.

You may assume that the entire matrix is surrounded by an outer perimeter with the value `-1` in each cell.

You must write an algorithm that runs in **$\mathcal{O}(m \log n)$** or **$\mathcal{O}(n \log m)$** time.

**Example 1:**
- **Input:** `mat = [[1,4],[3,2]]`
- **Output:** `[0,1]` or `[1,0]` (4 is > 1 and 2; 3 is > 1 and 2)

**Example 2:**
- **Input:** `mat = [[10,20,15],[21,30,14],[7,16,32]]`
- **Output:** `[1,1]` or `[2,2]` (30 is > 20, 21, 14, 16; 32 is > 16, 7)

---

## Key Intuition: Binary Search on Columns + Column Max

To achieve $\mathcal{O}(m \log n)$ time, we perform Binary Search on columns from `lowCol = 0` to `highCol = n - 1`:

### Step-by-Step Strategy:

1. **Pick Middle Column:**
   `midCol = lowCol + (highCol - lowCol) / 2`

2. **Find Maximum in Column `midCol` ($\mathcal{O}(m)$ time):**
   Find the row index `maxRow` that contains the largest element in column `midCol`.
   - *Why?* Because `mat[maxRow][midCol]` is the largest in its column, it is **already guaranteed to be strictly greater than its top and bottom neighbors**!

3. **Check Left and Right Neighbors:**
   Now we only need to compare `mat[maxRow][midCol]` with its left (`midCol - 1`) and right (`midCol + 1`) neighbors:
   - **Case 1 (Peak Found!):** If `mat[maxRow][midCol]` is greater than BOTH left and right neighbors:
     Return `[maxRow, midCol]`!
   - **Case 2 (Move Left):** If `mat[maxRow][midCol - 1] > mat[maxRow][midCol]`:
     A peak is guaranteed to exist in the left columns $\rightarrow$ `highCol = midCol - 1`.
   - **Case 3 (Move Right):** If `mat[maxRow][midCol + 1] > mat[maxRow][midCol]`:
     A peak is guaranteed to exist in the right columns $\rightarrow$ `lowCol = midCol + 1`.

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(m \log n)$
  - Binary Search on $n$ columns takes $\mathcal{O}(\log n)$ steps.
  - In each step, finding the maximum element in column `midCol` takes $\mathcal{O}(m)$ time.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
private:
    // Helper function to find the row index with maximum element in column col
    int findMaxRow(vector<vector<int>>& mat, int m, int col) {
        int maxRow = 0;
        for (int r = 1; r < m; r++) {
            if (mat[r][col] > mat[maxRow][col]) {
                maxRow = r;
            }
        }
        return maxRow;
    }

public:
    vector<int> findPeakGrid(vector<vector<int>>& mat) {
        int m = mat.size();
        int n = mat[0].size();

        int lowCol = 0;
        int highCol = n - 1;

        while (lowCol <= highCol) {
            int midCol = lowCol + (highCol - lowCol) / 2;

            // Step 1: Find the max element in column midCol
            int maxRow = findMaxRow(mat, m, midCol);

            // Step 2: Get values of left and right neighbors (-1 if out of bounds)
            int leftVal = (midCol > 0) ? mat[maxRow][midCol - 1] : -1;
            int rightVal = (midCol < n - 1) ? mat[maxRow][midCol + 1] : -1;

            // Step 3: Compare with neighbors
            if (mat[maxRow][midCol] > leftVal && mat[maxRow][midCol] > rightVal) {
                return {maxRow, midCol}; // Peak Found!
            } else if (leftVal > mat[maxRow][midCol]) {
                highCol = midCol - 1;   // Peak must exist in left half
            } else {
                lowCol = midCol + 1;    // Peak must exist in right half
            }
        }

        return {-1, -1};
    }
};

int main() {
    Solution sol;

    vector<vector<int>> mat1 = {
        {1, 4},
        {3, 2}
    };
    vector<int> res1 = sol.findPeakGrid(mat1);
    cout << "Test 1 Peak: [" << res1[0] << ", " << res1[1] << "] (Val: " << mat1[res1[0]][res1[1]] << ")\n";

    vector<vector<int>> mat2 = {
        {10, 20, 15},
        {21, 30, 14},
        {7,  16, 32}
    };
    vector<int> res2 = sol.findPeakGrid(mat2);
    cout << "Test 2 Peak: [" << res2[0] << ", " << res2[1] << "] (Val: " << mat2[res2[0]][res2[1]] << ")\n";

    return 0;
}
```
