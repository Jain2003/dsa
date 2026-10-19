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

## 1. Bugs & Pitfalls in Initial Attempts

### Bug 1: Missing Backtracking (`current.pop_back()`)
- When picking a candidate substring, you must push it to `current`, recurse, and then **immediately pop it back**:
  ```cpp
  current.push_back(s.substr(start, end - start + 1));
  helper(ans, current, s, end + 1);
  current.pop_back(); // MUST NOT BE FORGOTTEN!
  ```
- Without `pop_back()`, choices from previous branches remain inside `current`, corrupting subsequent paths.

### Bug 2: Aborting with `return` Instead of Moving to Larger Substrings
- In the loop:
  ```cpp
  if (isPali(...)) { ... }
  else return; // ❌ BUG: Kills the entire function frame!
  ```
- If a shorter substring is not a palindrome (e.g. `"ab"` in `"aba"`), returning immediately prevents the loop from checking longer palindromes (e.g. `"aba"`). Simply omit the `else` block so the loop proceeds to the next `end` index.

### Bug 3: The Double-Offset Bug (Slicing String AND Incrementing Index)
- Calling `helper(ans, current, s.substr(start + 1), start + 1)`:
  - When you pass a sliced substring `s.substr(...)`, the slice's index resets to `0`, but passing `start + 1` offsets it again, creating index corruption and out-of-bounds crashes.
  - Furthermore, the next piece must start at **`end + 1`** (since the current piece ends at `end`), NOT `start + 1`.
  - **Rule:** Keep the original string `s` intact and only advance the index pointer: `helper(ans, current, s, end + 1)`.

### Bug 4: Inefficient Palindrome Checking
- Creating copies and reversing them (`temp = str; reverse(temp); return temp == str;`) wastes $\mathcal{O}(K)$ extra memory per check. A two-pointer check `while (l < r)` is $\mathcal{O}(1)$ space and much faster.

---

## 2. Visual Recursion Tree for `s = "aab"`

```
                                          helper(start = 0, current = [])
                                           Remaining string: "aab"
                          /                          |                          \
             Cut "a" (end = 0)              Cut "aa" (end = 1)             Cut "aab" (end = 2)
            "a" is Palindrome ✅            "aa" is Palindrome ✅          "aab" is NOT Palindrome ❌
                     v                              v                                 |
         helper(start = 1, ["a"])        helper(start = 2, ["aa"])                    PRUNED ❌
         Remaining string: "ab"          Remaining string: "b"
              /              \                      |
   Cut "a" (end=1)      Cut "ab" (end=2)      Cut "b" (end=2)
  "a" is Palindrome ✅ "ab" NOT Palindrome ❌"b" is Palindrome ✅
            v                 |                     v
 helper(start=2, ["a","a"]) PRUNED ❌     helper(start=3, ["aa","b"])
 Remaining string: "b"                    Remaining string: ""
            |                                       |
     Cut "b" (end=2)                           start == 3 (BASE CASE!)
    "b" is Palindrome ✅                       ===> COLLECT ANSWER:
            v                                       ["aa", "b"] 🎉
 helper(start=3, ["a","a","b"])
 Remaining string: ""
            |
    start == 3 (BASE CASE!)
    ===> COLLECT ANSWER:
         ["a", "a", "b"] 🎉
```

---

## 3. Key Intuition: Substring Partitioning via Backtracking

At each step with index `start`:
1. Loop `end` from `start` to `s.size() - 1`:
   - Take the contiguous substring `s.substr(start, end - start + 1)`.
   - **For Palindrome Partitioning:** Only proceed if `isPalindrome(s, start, end)`.
   - Push this substring into `current`.
   - Recurse on the remaining suffix with `backtrack(s, end + 1, current, ans)`.
   - Pop from `current` (**Backtrack**).
2. **Base Case:** When `start == s.size()`, the entire string has been cleanly partitioned $\implies$ add `current` to `ans`.


---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(2^N \times N)$ — There are $2^{N-1}$ total partitions, and checking palindrome / copying substrings takes up to $\mathcal{O}(N)$ time.
- **Space Complexity:** $\mathcal{O}(N)$ recursion stack depth and `current` storage.

---

## 5. Code (LeetCode 131 Solution)

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
