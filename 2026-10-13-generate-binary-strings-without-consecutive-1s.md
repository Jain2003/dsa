# Generate Binary Strings Without Consecutive 1s (LeetCode / GFG)

## Problem Description
Given an integer `n`, generate all binary strings of length `n` such that there are **no consecutive 1's**. 
(Alternatively phrased as: strings where every `'1'` must not be immediately followed by another `'1'`).

**Example 1:**
- **Input:** `n = 3`
- **Output:** `["000", "001", "010", "100", "101"]`

**Example 2:**
- **Input:** `n = 2`
- **Output:** `["00", "01", "10"]`

---

## 1. Bugs in Your Initial Code

### Bug 1: Undefined Behavior & Out-of-Bounds on Empty String
```cpp
if (current[current.size() - 1] == '0')
```
- At the initial call `i = 0`, `current = ""`.
- `current.size()` is `0`, so `current[0 - 1] = current[-1]`.
- Accessing index `-1` of an empty `std::string` causes **Undefined Behavior / Segmentation Fault**.

### Bug 2: Unintentional Mutating `pop_back()`
- Because `current + '1'` was passed as a copy into the first call, `current` inside the caller was **never modified**.
- Then calling `current.pop_back()` on the caller's string erases a character from the prefix.
- At `i = 0` when `current` is empty, calling `current.pop_back()` crashes immediately.

### Bug 3: Missing the Choice to Add `'0'`
- You can **always** safely append `'0'` to any valid binary string, whether the last character was `'0'`, `'1'`, or if it's the very first character.
- In your code, `'0'` was only appended if the last character was `'1'`. That missed valid sequences like `"000"`.

---

## 2. Key Intuition & Branching Rules

At any position:
1. **Choice 1: Append `'0'`** $\longrightarrow$ **Always Allowed!**
2. **Choice 2: Append `'1'`** $\longrightarrow$ **Allowed ONLY IF** the string is empty OR the previous character is `'0'` (`current.empty() || current.back() == '0'`).

### Passing `current` by Reference vs. Value:
- **Your updated logic is 100% correct!**
- **One final optimization:** in `void helper(vector<string> &ans, string current, ...)`, `current` is passed by value (making a copy on every call). Changing it to `string &current` avoids all string copy allocations during recursion.

---

## 3. Decision Tree ($n = 3$)

```
                              "" (Length 0)
                            /    \
                 Add '0'   /      \  Add '1'
                         "0"       "1"
                        /   \        \
               Add '0' /     \ '1'    \ '0' (Cannot add '1')
                     "00"    "01"     "10"
                    /   \      \      /   \
                  "000" "001" "010" "100" "101"
```

Total valid binary strings for length $n$: Follows Fibonacci numbers $F(n + 2)$.

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(2^n)$ in the worst case (more precisely $\mathcal{O}(F_{n+2}) \approx \mathcal{O}(1.618^n)$).
- **Space Complexity:** $\mathcal{O}(n)$ auxiliary recursion stack space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
private:
    void helper(vector<string> &ans, string &current, int n) {
        // Base case: formed a string of length n
        if (current.size() == n) {
            ans.push_back(current);
            return;
        }

        // Choice 1: '0' can ALWAYS be added
        current.push_back('0');
        helper(ans, current, n);
        current.pop_back(); // Backtrack

        // Choice 2: '1' can ONLY be added if string is empty or last char was '0'
        if (current.empty() || current.back() == '0') {
            current.push_back('1');
            helper(ans, current, n);
            current.pop_back(); // Backtrack
        }
    }

public:
    vector<string> generateBinaryStrings(int n) {
        vector<string> ans;
        string current = "";
        helper(ans, current, n);
        return ans;
    }
};

int main() {
    Solution sol;

    int n1 = 3;
    vector<string> res1 = sol.generateBinaryStrings(n1);
    cout << "Binary strings of length " << n1 << " without consecutive 1s:" << endl;
    for (const string &s : res1) cout << s << " ";
    cout << "\n(Expected: 000 001 010 100 101)\n\n";

    int n2 = 2;
    vector<string> res2 = sol.generateBinaryStrings(n2);
    cout << "Binary strings of length " << n2 << " without consecutive 1s:" << endl;
    for (const string &s : res2) cout << s << " ";
    cout << "\n(Expected: 00 01 10)\n";

    return 0;
}
```
