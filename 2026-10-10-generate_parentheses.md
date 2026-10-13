# Generate Parentheses (Backtracking / Recursion)

## Problem Description
Given `n` pairs of parentheses, write a function to generate all combinations of **well-formed parentheses**.

**Example 1:**
- **Input:** `n = 3`
- **Output:** `["((()))","(()())","(())()","()(())","()()()"]`

**Example 2:**
- **Input:** `n = 1`
- **Output:** `["()"]`

---

## Key Intuition: Backtracking with 2 Golden Rules

A string of parentheses is well-formed if and only if at **any point** as we build the string from left to right:
1. The total number of open brackets `'('` placed never exceeds `n`.
2. The number of closed brackets `')'` placed **never exceeds the number of open brackets**!

### The 2 Decisions:
Track two counters: `open` and `close`.
- **Decision 1 (Add `'('`):** Only if `open < n`.
- **Decision 2 (Add `')'`):** Only if `close < open`.
- **Base Case:** When `current.size() == 2 * n`, save `current` to our results list.

---

## Decision Tree ($n = 2$)

```
                             "" (open=0, close=0)
                              |
                             "(" (open=1, close=0)
                            /   \
          "((" (open=2, close=0)  "()" (open=1, close=1)
                |                       |
          "(()" (open=2, close=1)      "()(" (open=2, close=1)
                |                       |
          "(())" (open=2, close=2)     "()()" (open=2, close=2)
              [Valid!]                     [Valid!]
```

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}\left(\frac{4^n}{\sqrt{n}}\right)$ — The number of valid parentheses sequences of length $2n$ is given by the $n$-th Catalan number $C_n = \frac{1}{n+1}\binom{2n}{n} \approx \frac{4^n}{n\sqrt{\pi n}}$.
- **Space Complexity:** $\mathcal{O}(n)$ — Maximum depth of the recursion tree is $2n$.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
public:
    void helper(int n, int open, int close, string current, vector<string>& ans) {
        if (current.size() == 2 * n) {
            ans.push_back(current);
            return;
        }

        if (open < n) {
            helper(n, open + 1, close, current + "(", ans);
        }

        if (close < open) {
            helper(n, open, close + 1, current + ")", ans);
        }
    }

    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        helper(n, 0, 0, "", ans);
        return ans;
    }
};

int main() {
    Solution sol;

    int n = 3;
    vector<string> combinations = sol.generateParenthesis(n);

    cout << "Valid Parentheses combinations for n = " << n << ":\n";
    for (const string& s : combinations) {
        cout << s << "\n";
    }

    return 0;
}
```
