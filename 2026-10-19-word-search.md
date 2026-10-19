# Word Search (LeetCode 79)

## Problem Description
Given an `m x n` grid of characters `board` and a string `word`, return `true` if `word` exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may **not be used more than once**.

**Example 1:**
- **Input:** `board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]]`, `word = "ABCCED"`
- **Output:** `true`

**Example 2:**
- **Input:** `board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]]`, `word = "SEE"`
- **Output:** `true`

**Example 3:**
- **Input:** `board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]]`, `word = "ABCB"`
- **Output:** `false` (Cannot reuse cells)

---

## 1. Bugs in Your Initial Code

### Bug 1: Inverted Grid Dimensions (Row vs. Column Bounds Check)
```cpp
if (i < 0 || j < 0 || i >= board[0].size() || j >= board.size()) // BUG!
```
- Rows (`i`) range from `0` to `board.size() - 1`.
- Columns (`j`) range from `0` to `board[0].size() - 1`.
- Swapping them causes out-of-bounds memory crashes when the matrix is non-square ($m \neq n$).
- **Fix:** `if (i < 0 || i >= board.size() || j < 0 || j >= board[0].size())`.

### Bug 2: Starting ONLY at Cell `(0, 0)`
```cpp
helper(board, word, "", 0, 0, 0, ans);
```
- The word can start at **any cell** `(r, c)` on the board!
- If the first letter of `word` is at `(1, 2)`, your code will never even consider it.
- **Fix:** Loop through all cells `(r, c)` in `exist` and start a search from any cell matching `word[0]`.

### Bug 3: Revisiting the Same Cell (Infinite Loops & Cell Reuse)
- The problem states: *"The same letter cell may not be used more than once."*
- Without marking visited cells, your code can ping-pong between two cells: `A -> B -> A -> B`, matching a word like `"ABA"` on a grid with just `["A", "B"]`.
- **Fix (In-place Visited Masking):**
  ```cpp
  char temp = board[i][j];
  board[i][j] = '#'; // Mark as visited
  // Recurse in 4 directions...
  board[i][j] = temp; // Backtrack / unmark
  ```

### Bug 4: Base Case Check Order
- In your code, `if (current == word)` is checked **after** the boundary check.
- But if the last character was found at the edge, moving to the next cell triggers `out-of-bounds` before `current == word` is evaluated!
- Even better: simply pass an integer `idx` (index in `word`). When `idx == word.size()`, return `true` immediately!

### Bug 5: String Concatenation Overhead (`current + board[i][j] == word.substr(...)`)
- String copying and `.substr()` in every recursive step creates huge overhead.
- Instead, simply compare: `if (board[i][j] != word[idx]) return false;`.

---

## 2. Optimal Backtracking Pattern: In-Place DFS

1. In `exist(board, word)`:
   - Loop `r` from `0` to `m - 1` and `c` from `0` to `n - 1`.
   - If `dfs(board, word, r, c, 0)` returns `true`, return `true` immediately.
2. In `dfs(board, word, r, c, idx)`:
   - **Success Base Case:** If `idx == word.size()`, return `true`.
   - **Failure Base Case:** If out-of-bounds or `board[r][c] != word[idx]`, return `false`.
   - **Mark Visited:** `char temp = board[r][c]; board[r][c] = '#';`
   - **Recurse 4 Directions:** Up, Down, Left, Right with `idx + 1`.
   - **Backtrack:** `board[r][c] = temp;`
   - Return if any direction succeeded.

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(M \cdot N \cdot 3^L)$ where $M \times N$ is grid size and $L$ is word length (at each step we explore 3 directions since we don't go back to parent).
- **Space Complexity:** $\mathcal{O}(L)$ recursion stack depth.

---

## 4. Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
private:
    bool dfs(vector<vector<char>>& board, const string& word, int r, int c, int idx) {
        // Base case: all characters of word matched!
        if (idx == word.size()) {
            return true;
        }

        // Boundary check and character mismatch check
        if (r < 0 || r >= board.size() || c < 0 || c >= board[0].size() || board[r][c] != word[idx]) {
            return false;
        }

        // 1. Mark current cell as visited in-place
        char temp = board[r][c];
        board[r][c] = '#';

        // 2. Explore all 4 adjacent directions
        bool found = dfs(board, word, r + 1, c, idx + 1) ||
                     dfs(board, word, r - 1, c, idx + 1) ||
                     dfs(board, word, r, c + 1, idx + 1) ||
                     dfs(board, word, r, c - 1, idx + 1);

        // 3. Backtrack: restore cell's original character
        board[r][c] = temp;

        return found;
    }

public:
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size();
        int n = board[0].size();

        // Try starting the word from every cell in the grid
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (board[r][c] == word[0]) {
                    if (dfs(board, word, r, c, 0)) {
                        return true; // Found valid path!
                    }
                }
            }
        }

        return false;
    }
};

int main() {
    Solution sol;

    vector<vector<char>> board1 = {
        {'A','B','C','E'},
        {'S','F','C','S'},
        {'A','D','E','E'}
    };

    cout << boolalpha;
    cout << "Test 1 (\"ABCCED\"): " << sol.exist(board1, "ABCCED") << " (Expected: true)\n";
    cout << "Test 2 (\"SEE\"):    " << sol.exist(board1, "SEE") << " (Expected: true)\n";
    cout << "Test 3 (\"ABCB\"):   " << sol.exist(board1, "ABCB") << " (Expected: false)\n";

    return 0;
}
```
