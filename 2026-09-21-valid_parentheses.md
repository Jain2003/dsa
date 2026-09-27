# Valid Parentheses (LeetCode 20)

## Problem Summary
Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

---

## 1. Key Insights & Stack Data Structure

- **LIFO (Last-In, First-Out) Matching:** The most recently opened bracket MUST be closed first. A `std::stack` perfectly models this requirement!
- **Opening Brackets:** Push onto stack (`stx.push(x)`).
- **Closing Brackets:** 
  1. Check if stack is empty (`if (stx.empty()) return false;`).
  2. Inspect top (`char top = stx.top();`).
  3. If matching pair, pop from stack (`stx.pop()`).
  4. Otherwise, return `false` (mismatch).
- **Final Validation:** Return `stx.empty()` (true only if all open brackets were closed).

---

## 2. Final Correct C++ Solution

```cpp
#include <iostream>
#include <string>
#include <stack>

using namespace std;

class Solution {
public:
    bool isValid(string s) {
        stack<char> stx;

        for (auto x : s) {
            if (x == '(' || x == '[' || x == '{') {
                stx.push(x);
            } else {
                if (stx.empty()) {
                    return false;
                }
                char top = stx.top();
                if ((x == ')' && top == '(') || (x == ']' && top == '[') || (x == '}' && top == '{')) {
                    stx.pop();
                } else {
                    return false;
                }
            }
        }
        return stx.empty();
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single pass through string `s` of length $N$.
- **Space Complexity:** $O(N)$ worst-case auxiliary space for the stack (e.g. `s = "((((("`).

---

## Code

```cpp
#include <iostream>
#include <string>
#include <stack>

using namespace std;

class Solution {
public:
    bool isValid(string s) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        stack<char> stx;

        for (auto x : s) {
            if (x == '(' || x == '[' || x == '{') {
                stx.push(x);
            } else {
                if (stx.empty()) {
                    return false;
                }
                char top = stx.top();
                if ((x == ')' && top == '(') || (x == ']' && top == '[') || (x == '}' && top == '{')) {
                    stx.pop();
                } else {
                    return false;
                }
            }
        }
        return stx.empty();
    }
};

int main() {
    Solution sol;
    cout << boolalpha;

    cout << "Test 1 (\"()\"): " << sol.isValid("()") << " (Expected: true)" << endl;
    cout << "Test 2 (\"()[]{}\"): " << sol.isValid("()[]{}") << " (Expected: true)" << endl;
    cout << "Test 3 (\"(]\"): " << sol.isValid("(]") << " (Expected: false)" << endl;
    cout << "Test 4 (\"]\"): " << sol.isValid("]") << " (Expected: false)" << endl;

    return 0;
}
```
