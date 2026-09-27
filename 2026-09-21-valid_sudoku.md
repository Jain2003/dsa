# Valid Sudoku (LeetCode 36)

## Problem Summary
Determine if a $9 \times 9$ Sudoku board is valid. Only the filled cells need to be validated according to 3 rules:
1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the nine $3 \times 3$ sub-boxes must contain the digits `1-9` without repetition.

---

## 1. Syntax & Logic Bug in Your Initial Code

### Your Code:
```cpp
for (int i{0}; i < 9; i++) {
    for (int j{0}; i < 9; j++) { // âŒ CRITICAL BUG: 'i < 9' instead of 'j < 9'!
        if (board[i][j] != '.') {
            mp[board[i][j]].push_back({i, j});
        }
    }
}
```

### Why it failed:
In the inner loop condition, you wrote `i < 9` instead of `j < 9`.
Since `i` remains `0` during the first outer iteration, `i < 9` is **always true for any value of `j`**.
`j` increments infinitely (`j = 0, 1, 2, ..., 100, 1000...`), causing an **Infinite Loop & Out-of-Bounds Memory Crash (`board[i][j]`)**!

---

## 2. Intuitive Breakdown of the $O(1)$ Array Solution

Think of the Sudoku board validation as maintaining **3 Checklists**:

1. **`rows[9][9]`**: 9 row checklists. Each checklist has 9 slots (`0` to `8`) corresponding to digits `1` to `9`.
   - `rows[i][num] = true` means: *"Row `i` already contains digit `num + 1`."*
2. **`cols[9][9]`**: 9 column checklists.
   - `cols[j][num] = true` means: *"Column `j` already contains digit `num + 1`."*
3. **`boxes[9][9]`**: 9 sub-box checklists ($3 \times 3$ subgrids).
   - `boxes[boxIndex][num] = true` means: *"Sub-box `boxIndex` already contains digit `num + 1`."*

---

### Step 1: Mapping Characters `'1'`-`'9'` to Index `0`-`8`

`board[i][j]` contains characters like `'1'`, `'2'`, ..., `'9'`.
Subtracting `'1'` (ASCII character subtraction) converts characters to array indices:
- `'1' - '1' = 0`
- `'5' - '1' = 4`
- `'9' - '1' = 8`

---

### Step 2: The $3 \times 3$ Box Index Formula Explained

A $9 \times 9$ grid is divided into 9 sub-boxes ($3 \times 3$ each):

```
Column ->  0 1 2   3 4 5   6 7 8
Row 0-2:  [ Box 0 ] [ Box 1 ] [ Box 2 ]
Row 3-5:  [ Box 3 ] [ Box 4 ] [ Box 5 ]
Row 6-8:  [ Box 6 ] [ Box 7 ] [ Box 8 ]
```

How do we convert cell position `(i, j)` to `boxIndex`?

1. **Row Block (`i / 3`)**:
   - Rows `0, 1, 2` $\rightarrow 0 / 3 = 0, 1 / 3 = 0, 2 / 3 = 0$ (Top row block)
   - Rows `3, 4, 5` $\rightarrow 3 / 3 = 1, 4 / 3 = 1, 5 / 3 = 1$ (Middle row block)
   - Rows `6, 7, 8` $\rightarrow 6 / 3 = 2, 7 / 3 = 2, 8 / 3 = 2$ (Bottom row block)

2. **Column Block (`j / 3`)**:
   - Columns `0, 1, 2` $\rightarrow 0$ (Left col block)
   - Columns `3, 4, 5` $\rightarrow 1$ (Middle col block)
   - Columns `6, 7, 8` $\rightarrow 2$ (Right col block)

3. **Formula**:
   $$\text{boxIndex} = (\text{row block}) \times 3 + (\text{col block}) = (i / 3) \times 3 + (j / 3)$$

#### Example: Cell at Row 4, Column 7 `(i = 4, j = 7)`
- Row block: `4 / 3 = 1`
- Col block: `7 / 3 = 2`
- `boxIndex = (1) * 3 + 2 = 5` (Box 5!)

---

## 3. Walkthrough of the Algorithm

For every cell `(i, j)`:
1. If `board[i][j] == '.'`, ignore it and move on.
2. Otherwise, get digit `num = board[i][j] - '1'`.
3. Compute `boxIndex = (i / 3) * 3 + (j / 3)`.
4. Check if `rows[i][num]`, `cols[j][num]`, or `boxes[boxIndex][num]` is `true`.
   - If any is `true`, it means we saw this digit before in the same row/col/box $\rightarrow$ **Return `false` (Invalid Sudoku)**!
5. Mark `rows[i][num] = true`, `cols[j][num] = true`, `boxes[boxIndex][num] = true`.
6. If loop completes with no duplicates $\rightarrow$ **Return `true` (Valid Sudoku)**.

---

## Complexity Analysis

- **Time Complexity:** $O(81) = O(1)$ since the board size is fixed at $9 \times 9$.
- **Space Complexity:** $O(81) = O(1)$ auxiliary space for tracking arrays.

## Code

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
#include <string>

using namespace std;

class Solution {
public:
    // Approach 1: Boolean tracking matrices for Row, Column, and 3x3 Box
    // Time Complexity: O(81) = O(1), Space Complexity: O(81) = O(1)
    bool isValidSudoku(vector<vector<char>>& board) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        bool rows[9][9] = {false};
        bool cols[9][9] = {false};
        bool boxes[9][9] = {false};

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) { // Fixed: j < 9 condition (was i < 9)
                if (board[i][j] != '.') {
                    int num = board[i][j] - '1'; // Map char '1'-'9' to 0-8 index
                    int boxIndex = (i / 3) * 3 + (j / 3); // Calculate 3x3 subgrid index

                    // Check if digit already exists in row, col, or 3x3 box
                    if (rows[i][num] || cols[j][num] || boxes[boxIndex][num]) {
                        return false;
                    }

                    // Mark digit as seen
                    rows[i][num] = true;
                    cols[j][num] = true;
                    boxes[boxIndex][num] = true;
                }
            }
        }

        return true;
    }
};

int main() {
    Solution sol;
    vector<vector<char>> board = {
        {'5','3','.','.','7','.','.','.','.'},
        {'6','.','.','1','9','5','.','.','.'},
        {'.','9','8','.','.','.','.','6','.'},
        {'8','.','.','.','6','.','.','.','3'},
        {'4','.','.','8','.','3','.','.','1'},
        {'7','.','.','.','2','.','.','.','6'},
        {'.','6','.','.','.','.','2','8','.'},
        {'.','.','.','4','1','9','.','.','5'},
        {'.','.','.','.','8','.','.','7','9'}
    };

    cout << boolalpha;
    cout << "Is Valid Sudoku? " << sol.isValidSudoku(board) << endl; // Output: true

    return 0;
}
```
