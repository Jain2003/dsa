# Search a 2D Matrix (LeetCode 74)

## Problem Summary
You are given an `m x n` integer matrix `matrix` with the following two properties:
1. Each row is sorted in non-decreasing order.
2. The first integer of each row is greater than the last integer of the previous row.

Given an integer `target`, return `true` if `target` is in `matrix` or `false` otherwise.

You must write a solution in **$O(\log(m \times n))$ time**.

---

## 1. Bug Breakdown in Your Initial Code

### The Typo in Your `mid` Formula:
```cpp
int mid = (j - i) / 2 + i / 2; // ❌ CRITICAL BUG!
```

### Why it failed:
Mathematically, `(j - i) / 2 + i / 2` evaluates to:
$$\frac{j - i}{2} + \frac{i}{2} = \frac{j}{2}$$

#### Numerical Example:
Suppose `i = 4` and `j = 8`:
- **Correct Midpoint:** `mid = 4 + (8 - 4) / 2 = 6`
- **Your Formula:** `(8 - 4) / 2 + 4 / 2 = 2 + 2 = 4`!

Because `mid` evaluated to `4` (which equals `i`), `mid` failed to reach the center, causing an **Infinite Loop or Incorrect Searches**.

### The Fix:
```cpp
int mid = i + (j - i) / 2; // ✅ Correct midpoint formula!
```

---

## 2. Correct & Optimal C++ Solution ($O(\log(m \times n))$ Time, $O(1)$ Space)

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int rows = matrix.size();
        int cols = matrix[0].size();

        int low = 0;
        int high = rows * cols - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2; // Fixed midpoint formula

            int row = mid / cols;
            int col = mid % cols;

            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return false;
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(\log(m \times n))$ — Performs binary search over $m \times n$ total elements.
- **Space Complexity:** $O(1)$ — Uses only integer variables (`low`, `high`, `mid`, `row`, `col`).

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int rows = matrix.size();
        int cols = matrix[0].size();

        int low = 0;
        int high = rows * cols - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            int row = mid / cols;
            int col = mid % cols;

            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return false;
    }
};

int main() {
    Solution sol;
    vector<vector<int>> matrix = {
        {1,  3,  5,  7},
        {10, 11, 16, 20},
        {23, 30, 34, 60}
    };

    cout << boolalpha;
    cout << "Search 3: " << sol.searchMatrix(matrix, 3) << " (Expected: true)" << endl;
    cout << "Search 13: " << sol.searchMatrix(matrix, 13) << " (Expected: false)" << endl;

    return 0;
}
```
