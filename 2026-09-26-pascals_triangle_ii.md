# Pascal's Triangle II

## Problem Description
Given an integer `rowIndex`, return the `rowIndex`-th (**0-indexed**) row of **Pascal's Triangle**.

**Example 1:**
- **Input:** `rowIndex = 3`
- **Output:** `[1, 3, 3, 1]`

**Example 2:**
- **Input:** `rowIndex = 0`
- **Output:** `[1]`

**Follow-up:** Could you optimize your algorithm to use only $\mathcal{O}(k)$ extra space? Can you also compute it in $\mathcal{O}(k)$ time?

---

## Analysis of Your Solution
Your submitted solution:
- Generates the entire 2D matrix of Pascal's Triangle (`vector<vector<int>> ans`).
- Returns the last row `ans[rowIndex]`.
- **Correctness:** Correct output!
- **Time Complexity:** $\mathcal{O}(\text{rowIndex}^2)$
- **Space Complexity:** $\mathcal{O}(\text{rowIndex}^2)$ extra space because it stores all previous rows.

---

## Optimization Strategies

### Strategy 1: 1D Dynamic Programming ($\mathcal{O}(k^2)$ Time, $\mathcal{O}(k)$ Space)
Instead of storing all rows in a 2D vector, maintain a single 1D array of size `rowIndex + 1`. Update the elements **backwards** (from right to left) to avoid overwriting values needed for the calculation:

```cpp
vector<int> row(rowIndex + 1, 1);
for (int i = 1; i <= rowIndex; i++) {
    for (int j = i - 1; j > 0; j--) {
        row[j] = row[j] + row[j - 1];
    }
}
return row;
```

### Strategy 2: Combinatorics Formula ($\mathcal{O}(k)$ Time, $\mathcal{O}(k)$ Space) 🏆 Most Optimal!
Each element at index $j$ in row $n = \text{rowIndex}$ is given by the combination formula $\binom{n}{j}$:
$$\binom{n}{j} = \frac{n!}{j! \cdot (n - j)!}$$

We can derive element $j$ directly from element $j - 1$:
$$\text{Element}_j = \text{Element}_{j-1} \times \frac{n - j + 1}{j}$$

This allows calculating the entire row in **a single pass** in $\mathcal{O}(\text{rowIndex})$ time and $\mathcal{O}(\text{rowIndex})$ space!

---

## Complexity Comparison

| Strategy | Time Complexity | Space Complexity |
| :--- | :--- | :--- |
| **2D Vector (Your Code)** | $\mathcal{O}(k^2)$ | $\mathcal{O}(k^2)$ |
| **1D DP (Backwards)** | $\mathcal{O}(k^2)$ | $\mathcal{O}(k)$ |
| **Combinatorics** | **$\mathcal{O}(k)$** | **$\mathcal{O}(k)$** 🏆 |

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    // Optimal O(k) Time and O(k) Space solution using Combinations
    vector<int> getRow(int rowIndex) {
        vector<int> row(rowIndex + 1, 1);
        long long currentVal = 1;
        
        for (int j = 1; j <= rowIndex; j++) {
            // Using combination relation: C(n, j) = C(n, j-1) * (n - j + 1) / j
            currentVal = currentVal * (rowIndex - j + 1) / j;
            row[j] = currentVal;
        }
        
        return row;
    }
};

int main() {
    Solution sol;
    int rowIndex = 3;
    vector<int> result = sol.getRow(rowIndex);

    cout << "Pascal's Triangle Row " << rowIndex << ": [ ";
    for (int val : result) {
        cout << val << " ";
    }
    cout << "]\n";

    return 0;
}
```
