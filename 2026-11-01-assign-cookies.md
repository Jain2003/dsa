# Assign Cookies (LeetCode 455)

## Problem Description
Assume you are an awesome parent and want to give your children some cookies. But, you should give each child at most one cookie.

Each child $i$ has a greed factor `g[i]`, which is the minimum size of a cookie that the child will be content with; and each cookie $j$ has a size `s[j]`. If `s[j] >= g[i]`, we can assign the cookie $j$ to the child $i$, and the child $i$ will be content.

Your goal is to maximize the number of content children and return the maximum number.

**Example 1:**
- **Input:** `g = [1, 2, 3]`, `s = [1, 1]`
- **Output:** `1`
- **Explanation:** You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content. You need to output 1.

**Example 2:**
- **Input:** `g = [1, 2]`, `s = [1, 2, 3]`
- **Output:** `2`
- **Explanation:** You have 2 children and 3 cookies. You can satisfy both children.

---

## 1. Intuition: The Greedy Choice Property

To maximize the number of content children:
1. **Satisfy the least greedy child first:**
   A child with smaller greed is easier to satisfy than a child with large greed.
2. **Use the smallest possible cookie that satisfies them:**
   Don't waste a cookie of size 10 on a child who only needs size 1, because that size 10 cookie could satisfy a child with greed 10 later!

### Two-Pointer Algorithm on Sorted Arrays:
1. Sort both the children's greed array `g` and the cookie sizes array `s` in ascending order.
2. Maintain two pointers:
   - `i` pointing to the current child in `g` (starts at 0).
   - `j` pointing to the current cookie in `s` (starts at 0).
3. If `s[j] >= g[i]`:
   - Cookie `j` satisfies Child `i`.
   - Child `i` is content! Move to the next child (`i++`) and next cookie (`j++`).
4. If `s[j] < g[i]`:
   - Cookie `j` is too small even for the least greedy remaining child!
   - This cookie cannot satisfy *any* remaining child. We discard it and check the next cookie (`j++`).
5. Terminate when we run out of children (`i == g.size()`) or cookies (`j == s.size()`).
6. The answer is simply `i` (number of content children).

---

## 2. Step-by-Step Trace: `g = [1, 2]`, `s = [1, 2, 3]`

- Sorted `g = [1, 2]`
- Sorted `s = [1, 2, 3]`

| Step | Child Index `i` (`g[i]`) | Cookie Index `j` (`s[j]`) | Condition `s[j] >= g[i]` | Action |
| :---: | :---: | :---: | :---: | :--- |
| **1** | `i = 0` (`g[0] = 1`) | `j = 0` (`s[0] = 1`) | $1 \ge 1$ (True) | Child 0 satisfied! `i = 1`, `j = 1` |
| **2** | `i = 1` (`g[1] = 2`) | `j = 1` (`s[1] = 2`) | $2 \ge 2$ (True) | Child 1 satisfied! `i = 2`, `j = 2` |
| **End** | `i = 2 == g.size()` | Loop ends! | — | Return `i = 2`. |

---

## 3. Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log N + M \log M)$
  - Sorting `g` of size $N$: $\mathcal{O}(N \log N)$
  - Sorting `s` of size $M$: $\mathcal{O}(M \log M)$
  - Two-pointer linear scan: $\mathcal{O}(N + M)$
  - Total Time: Dominated by sorting $\mathcal{O}(N \log N + M \log M)$.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (ignoring sorting recursion stack $\mathcal{O}(\log N + \log M)$).

---

## 4. Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    // =========================================================================
    // Optimal: Greedy Sorting + Two Pointers (O(N log N + M log M) Time, O(1) Space)
    // =========================================================================
    int findContentChildren(vector<int>& g, vector<int>& s) {
        // Step 1: Sort greed factors and cookie sizes
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());

        int i = 0; // Pointer for children (g)
        int j = 0; // Pointer for cookies (s)

        // Step 2: Try to satisfy each child with the smallest valid cookie
        while (i < g.size() && j < s.size()) {
            if (s[j] >= g[i]) {
                // Cookie satisfies child i
                i++;
            }
            // Always move to the next cookie (either used or too small)
            j++;
        }

        return i; // Number of satisfied children
    }
};

int main() {
    Solution sol;

    // Test Case 1
    vector<int> g1 = {1, 2, 3};
    vector<int> s1 = {1, 1};
    cout << "Test 1: " << sol.findContentChildren(g1, s1) << " (Expected: 1)\n";

    // Test Case 2
    vector<int> g2 = {1, 2};
    vector<int> s2 = {1, 2, 3};
    cout << "Test 2: " << sol.findContentChildren(g2, s2) << " (Expected: 2)\n";

    // Test Case 3: Cookies too small for all
    vector<int> g3 = {5, 6, 7};
    vector<int> s3 = {1, 2, 3};
    cout << "Test 3: " << sol.findContentChildren(g3, s3) << " (Expected: 0)\n";

    return 0;
}
```
