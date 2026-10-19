# Letter Combinations of a Phone Number (LeetCode 17)

## Problem Description
Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.

A mapping of digits to letters (just like on telephone buttons) is given below. Note that 1 does not map to any letters.

```
2 -> "abc"
3 -> "def"
4 -> "ghi"
5 -> "jkl"
6 -> "mno"
7 -> "pqrs"
8 -> "tuv"
9 -> "wxyz"
```

**Example 1:**
- **Input:** `digits = "23"`
- **Output:** `["ad","ae","af","bd","be","bf","cd","ce","cf"]`

**Example 2:**
- **Input:** `digits = ""`
- **Output:** `[]`

**Example 3:**
- **Input:** `digits = "2"`
- **Output:** `["a","b","c"]`

---

## 1. Bugs & Pitfalls in Your Initial Code

### Bug 1: `stoi(digits)` Crashes on Empty String (`""`)
- If the input is empty `digits = ""`, calling `stoi("")` throws a `std::invalid_argument` runtime exception and crashes immediately.
- Furthermore, `stoi` is completely unnecessary because `digits` is already an iterable string of characters! You can directly iterate over characters (`char ch : digits`) and convert with `ch - '0'`.

### Bug 2: Off-by-One Mismatch in `combi` Mapping
- You initialized `combi` as:
  ```cpp
  {"", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"}
  ```
  Indices:
  - Index `0` $\rightarrow$ `""`
  - Index `1` $\rightarrow$ `"abc"` (Digit 2 mapped here!)
  - Index `2` $\rightarrow$ `"def"` (Digit 3 mapped here!)
- In your code, you wrote `combi[nums[i] - 1]`.
  - For digit `2`: `nums[i] - 1 = 1` $\rightarrow$ `"abc"` (matches).
  - But standard practice is to align the mapping with actual digit values by padding index `0` and index `1`:
  ```cpp
  const vector<string> pad = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
  // Then simply use: pad[digit]
  ```

### Bug 3: `current` Passed by Value (Performance Hit)
- In `helper(..., string current)`, passing by value copies the string on every recursive branch.
- Since you are properly doing `current.push_back(ch)` and `current.pop_back()` (backtracking), you can pass `string &current` by reference to save unnecessary memory allocations.

---

## 2. Decision Tree for `digits = "23"`

```
Level 0: Digit '2' -> Letters "abc"
                   /              |              \
                'a'              'b'             'c'
               / | \            / | \           / | \
Level 1:     'd''e''f'        'd''e''f'       'd''e''f'
(Digit '3'    |  |  |          |  |  |         |  |  |
 -> "def")    v  v  v          v  v  v         v  v  v
            "ad""ae""af"     "bd""be""bf"    "cd""ce""cf"
```

Total combinations = $3 \times 3 = 9$.

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(4^N \times N)$ where $N$ is the length of `digits` (at most 4 choices per digit for 7 and 9, and copying length-$N$ string takes $\mathcal{O}(N)$).
- **Space Complexity:** $\mathcal{O}(N)$ recursion stack depth.

---

## 4. Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
private:
    const vector<string> pad = {
        "",     "",     "abc",  "def", // 0, 1, 2, 3
        "ghi",  "jkl",  "mno",         // 4, 5, 6
        "pqrs", "tuv",  "wxyz"         // 7, 8, 9
    };

    void helper(const string &digits, int i, string &current, vector<string> &ans) {
        // Base case: formed a combination of full length
        if (i == digits.size()) {
            ans.push_back(current);
            return;
        }

        int digit = digits[i] - '0';
        const string &letters = pad[digit];

        for (char ch : letters) {
            current.push_back(ch);
            helper(digits, i + 1, current, ans);
            current.pop_back(); // Backtrack
        }
    }

public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) {
            return {};
        }

        vector<string> ans;
        string current = "";
        helper(digits, 0, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;

    // Test Case 1: "23"
    vector<string> res1 = sol.letterCombinations("23");
    cout << "Combinations for '23':\n";
    for (const string &s : res1) cout << s << " ";
    cout << "\n(Expected 9 combinations)\n\n";

    // Test Case 2: ""
    vector<string> res2 = sol.letterCombinations("");
    cout << "Combinations for '': " << res2.size() << " (Expected: 0)\n";

    return 0;
}
```
