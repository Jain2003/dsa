# Palindrome Partitioning / All Substring Partitions (LeetCode 131)

## Problem Description
Given a string `s`, partition `s` such that every substring of the partition is a **palindrome** (or in the general case, generate all possible substring partitions). Return all possible partitions.

**Example 1:**
- **Input:** `s = "aab"`
- **Output:** `[["a","a","b"],["aa","b"]]` (If filtered by palindrome)
- *(All partitions without filtering: `[["a","a","b"], ["a","ab"], ["aa","b"], ["aab"]]`)*

**Example 2:**
- **Input:** `s = "a"`
- **Output:** `[["a"]]`

---

## 1. Key Intuition: Substring Partitioning via Backtracking

At each step with index `start`:
1. Loop `end` from `start` to `s.size() - 1`:
   - Take the contiguous substring `s.substr(start, end - start + 1)`.
   - **For Palindrome Partitioning:** Only proceed if `isPalindrome(s, start, end)`.
   - Push this substring into `current`.
   - Recurse on the remaining suffix with `backtrack(s, end + 1, current, ans)`.
   - Pop from `current` (**Backtrack**).
2. **Base Case:** When `start == s.size()`, the entire string has been cleanly partitioned $\implies$ add `current` to `ans`.

---

## 2. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(2^N \times N)$ — There are $2^{N-1}$ total partitions, and checking palindrome / copying substrings takes up to $\mathcal{O}(N)$ time.
- **Space Complexity:** $\mathcal{O}(N)$ recursion stack depth and `current` storage.

---

## 3. Code (LeetCode 131 Solution)

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
private:
    // Helper to check if s[l...r] is a palindrome in O(1) extra space
    bool isPalindrome(const string &s, int l, int r) {
        while (l < r) {
            if (s[l] != s[r]) return false;
            l++;
            r--;
        }
        return true;
    }

    void backtrack(const string &s, int start, vector<string> &current, vector<vector<string>> &ans) {
        // Base case: successfully partitioned the entire string
        if (start == s.size()) {
            ans.push_back(current);
            return;
        }

        // Try ending the current substring at every possible index 'end'
        for (int end = start; end < s.size(); end++) {
            // Only branch if the current substring s[start...end] is a palindrome
            if (isPalindrome(s, start, end)) {
                // Pick: extract substring s[start ... end]
                current.push_back(s.substr(start, end - start + 1));

                // Recurse on the remaining suffix starting at end + 1
                backtrack(s, end + 1, current, ans);

                // Backtrack: undo the choice
                current.pop_back();
            }
        }
    }

public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        vector<string> current;
        backtrack(s, 0, current, ans);
        return ans;
    }
};

int main() {
    Solution sol;
    string s = "aab";
    vector<vector<string>> result = sol.partition(s);

    cout << "Palindrome Partitions for \"" << s << "\":\n";
    for (const auto &p : result) {
        cout << "[ ";
        for (const auto &sub : p) cout << "\"" << sub << "\" ";
        cout << "]\n";
    }

    return 0;
}
```
