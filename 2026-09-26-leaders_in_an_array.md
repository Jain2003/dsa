# Leaders in an Array

## Problem Summary
Given an array `arr` of integers, find all the **leaders** in the array. An element is a leader if it is **greater than or equal to all the elements to its right side**. The rightmost element is always a leader.

Return the leaders in the **same relative order** as they appear in the original array.

**Example 1:**
- **Input:** `arr = [16, 17, 4, 3, 5, 2]`
- **Output:** `[17, 5, 2]`
- **Explanation:**
  - `2` has no elements to its right $\rightarrow$ Leader.
  - `5` is $> 2$ $\rightarrow$ Leader.
  - `3` is not $> 5$ $\rightarrow$ Not leader.
  - `4` is not $> 5$ $\rightarrow$ Not leader.
  - `17` is $> 4, 3, 5, 2$ $\rightarrow$ Leader.
  - `16` is not $> 17$ $\rightarrow$ Not leader.

---

## 1. Code Review & Key Insights

Your solution is **100% correct** ($O(N)$ Time and $O(N)$ Space)!

### Why Right-to-Left Traversal Works:
- Naive Brute Force ($O(N^2)$) checks every element against all elements to its right.
- **Optimal Pattern (Right-to-Left Max Tracking):**
  - By scanning backwards from index `n - 1` down to `0`, we can track the maximum element seen so far (`maxFromRight`).
  - If `arr[j] >= maxFromRight`, then `arr[j]` is guaranteed to be a leader!
  - We push it to `ans`, update `maxFromRight = arr[j]`, and reverse `ans` at the end to restore original order.

---

## 2. Minor Safety Enhancement (`INT_MIN`)

In your code, you initialized `int rightmax = 0;`. If the array contains negative numbers (e.g. `[-3, -10, -5]`), `0` would prevent negative numbers from being recognized as leaders.

Using `INT_MIN` (from `<climits>`) makes the code universal for any input values!

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class Solution {
public:
    vector<int> leaders(vector<int>& arr) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = arr.size();
        vector<int> ans;
        int maxFromRight = INT_MIN; // Handles negative numbers safely

        for (int j = n - 1; j >= 0; j--) {
            if (arr[j] >= maxFromRight) {
                maxFromRight = arr[j];
                ans.push_back(maxFromRight);
            }
        }

        // Restore original left-to-right order
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single backward pass ($O(N)$) plus reversing the answer array ($O(K) \le O(N)$).
- **Space Complexity:** $O(N)$ — For storing the result vector `ans`.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class Solution {
public:
    vector<int> leaders(vector<int>& arr) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = arr.size();
        vector<int> ans;
        int maxFromRight = INT_MIN;

        for (int j = n - 1; j >= 0; j--) {
            if (arr[j] >= maxFromRight) {
                maxFromRight = arr[j];
                ans.push_back(maxFromRight);
            }
        }

        reverse(ans.begin(), ans.end());
        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> arr1 = {16, 17, 4, 3, 5, 2};
    vector<int> res1 = sol.leaders(arr1);
    cout << "Test 1: ";
    for (int x : res1) cout << x << " ";
    cout << "(Expected: 17 5 2)" << endl;

    vector<int> arr2 = {1, 2, 3, 4, 0};
    vector<int> res2 = sol.leaders(arr2);
    cout << "Test 2: ";
    for (int x : res2) cout << x << " ";
    cout << "(Expected: 4 0)" << endl;

    return 0;
}
```
