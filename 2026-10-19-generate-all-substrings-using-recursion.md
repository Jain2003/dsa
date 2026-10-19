# Generate All Substrings of a String Using Recursion

## Problem Description
Given a string `s`, generate and return all possible **contiguous substrings** of `s` using **recursion**.

*(Recall: A substring must consist of contiguous/consecutive characters. For a string of length $N$, there are exactly $\frac{N(N+1)}{2}$ non-empty substrings).*

**Example 1:**
- **Input:** `s = "abc"`
- **Output:** `["a", "ab", "abc", "b", "bc", "c"]`

**Example 2:**
- **Input:** `s = "aab"`
- **Output:** `["a", "aa", "aab", "a", "ab", "b"]`

---

## 1. Key Intuition: Recursive 2-Index Exploration

In iterative code, you generate all substrings with two nested loops:
- Outer pointer `start` ($0 \le \text{start} < N$)
- Inner pointer `end` ($\text{start} \le \text{end} < N$)

To translate this cleanly into **recursion**:
1. **Helper 1 (Outer Recursion):** Fixes the starting character index `start` from $0$ up to $N - 1$.
2. **Helper 2 (Inner Recursion):** Expands the ending character index `end` from `start` up to $N - 1$, extracting and printing `s.substr(start, end - start + 1)`.

Alternatively, a single recursive function parameterized by `(start, end)` can simulate the inner and outer loop!

---

## 2. Common Pitfall: The Single Equals Assignment Trap (`if (end = s.size())`)
- **Buggy Code:**
  ```cpp
  if (end = s.size()) { // ❌ BUG: '=' assigns instead of compares!
      helper(s, start + 1, start + 1, result);
      return;
  }
  ```
- **Why it breaks:** The expression `end = s.size()` sets `end` to `s.size()` (e.g. `6`). Since `6 != 0`, the `if` condition evaluates to `true` on **every single call frame**.
- **Consequence:** The function immediately branches to `start + 1` without ever reaching `result.push_back(...)`. The output list remains completely **empty**!
- **Fix:** Always use `==`: `if (end == s.size())`.


---

## 3. Decision Trace for `s = "abc"`

```
helper(start = 0, end = 0) -> "a"
  └── helper(start = 0, end = 1) -> "ab"
        └── helper(start = 0, end = 2) -> "abc"
              └── end reached N -> advance start to 1!

helper(start = 1, end = 1) -> "b"
  └── helper(start = 1, end = 2) -> "bc"
        └── end reached N -> advance start to 2!

helper(start = 2, end = 2) -> "c"
  └── end reached N -> advance start to 3 (start == N -> Base Case, Terminate!)
```

Total substrings collected: $\frac{3 \times 4}{2} = \mathbf{6}$.

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(N^2 \times N)$ — There are $\frac{N(N+1)}{2} = \mathcal{O}(N^2)$ substrings, and extracting/copying each takes $\mathcal{O}(N)$ time.
- **Space Complexity:** $\mathcal{O}(N)$ recursion call stack depth.

---

## 4. Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
private:
    // Recursive function that acts as both outer (start) and inner (end) loops
    void getSubstrings(const string &s, int start, int end, vector<string> &result) {
        // Base case: start reached the end of the string
        if (start == s.size()) {
            return;
        }

        // When end reaches the end, reset end to the next start position
        if (end == s.size()) {
            getSubstrings(s, start + 1, start + 1, result);
            return;
        }

        // 1. Process current substring s[start ... end]
        result.push_back(s.substr(start, end - start + 1));

        // 2. Expand current substring by advancing 'end'
        getSubstrings(s, start, end + 1, result);
    }

public:
    vector<string> generateAllSubstrings(string s) {
        vector<string> result;
        if (s.empty()) return result;

        getSubstrings(s, 0, 0, result);
        return result;
    }
};

int main() {
    Solution sol;

    // Test Case 1: "abc"
    string s1 = "abc";
    vector<string> res1 = sol.generateAllSubstrings(s1);
    cout << "Substrings of \"" << s1 << "\":\n";
    for (const string &sub : res1) {
        cout << "\"" << sub << "\" ";
    }
    cout << "\nTotal: " << res1.size() << " (Expected: 6)\n\n";

    // Test Case 2: "aab"
    string s2 = "aab";
    vector<string> res2 = sol.generateAllSubstrings(s2);
    cout << "Substrings of \"" << s2 << "\":\n";
    for (const string &sub : res2) {
        cout << "\"" << sub << "\" ";
    }
    cout << "\nTotal: " << res2.size() << " (Expected: 6)\n";

    return 0;
}
```
