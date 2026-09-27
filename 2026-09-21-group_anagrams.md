# Group Anagrams (LeetCode 49)

## Problem Summary
Given an array of strings `strs`, group the anagrams together in any order.

---

## 1. Key Insights & Intuition

1. **Anagram Signature:**
   - All strings that are anagrams of each other (e.g. `"eat"`, `"tea"`, `"ate"`) share the exact same sorted string signature (`"aet"`).
2. **Sorting Copy vs In-Place (`std::sort` returns `void`):**
   - Cannot do `mp[sort(s.begin(), s.end())]` because `sort` modifies in-place and returns `void`.
   - Must copy `string temp = strs[i]; sort(temp.begin(), temp.end()); mp[temp].push_back(i);`.

---

## 2. Essential Map Unpacking Pattern (MUST REVISE!)

```cpp
vector<vector<string>> ans;

// C++17 Structured Binding over map<string, vector<int>>
for (auto& [key, indices] : mp) {
    vector<string> grp;
    for (int idx : indices) {
        grp.push_back(strs[idx]);
    }
    ans.push_back(grp);
}
```

### Why this pattern is crucial:
- **Avoids integer map lookup (`mp[i]`):** Maps in C++ use keys, NOT integer array indices.
- **Safe 2D Vector Construction:** Creating a local `vector<string> grp;` and doing `ans.push_back(grp)` avoids out-of-bounds `ans[j]` indexing crashes.
- **Clean Structure:** `[key, indices]` unpacks the map pair directly.

---

## Complexity Analysis

- **Time Complexity:** $O(N \cdot K \log K)$, where $N$ is the number of strings and $K$ is the maximum length of a string (due to sorting each string of length $K$).
- **Space Complexity:** $O(N \cdot K)$ to store strings and indices in the map.

## Code

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <algorithm>

using namespace std;

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        // Fast I/O for LeetCode
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        // Map sorted_string -> list of original indices
        map<string, vector<int>> mp;

        for (int i = 0; i < strs.size(); i++) {
            string temp = strs[i];
            sort(temp.begin(), temp.end()); // Sort copy of string as signature
            mp[temp].push_back(i);          // Group index by sorted key
        }

        vector<vector<string>> ans;

        // C++17 Structured Binding to traverse map of vectors cleanly
        for (auto& [key, indices] : mp) {
            vector<string> grp;
            for (int idx : indices) {
                grp.push_back(strs[idx]);
            }
            ans.push_back(grp);
        }

        return ans;
    }
};

int main() {
    Solution sol;
    vector<string> strs = {"eat", "tea", "tan", "ate", "nat", "bat"};

    vector<vector<string>> result = sol.groupAnagrams(strs);

    cout << "Grouped Anagrams:\n[\n";
    for (const auto& group : result) {
        cout << "  [ ";
        for (const auto& word : group) {
            cout << "\"" << word << "\" ";
        }
        cout << "]\n";
    }
    cout << "]\n";

    return 0;
}
```
