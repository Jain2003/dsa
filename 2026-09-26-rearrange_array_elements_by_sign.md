# Rearrange Array Elements by Sign (LeetCode 2149)

## Problem Summary
You are given a 0-indexed integer array `nums` of even length consisting of an equal number of positive and negative integers.

Rearrange elements of `nums` such that:
1. Every consecutive pair of integers have opposite signs.
2. For all integers with the same sign, the order in which they were present in `nums` is preserved.
3. The rearranged array begins with a positive integer.

Return the modified array `ans`.

**Example 1:**
- **Input:** `nums = [3,1,-2,-5,2,-4]`
- **Output:** `[3,-2,1,-5,2,-4]`

---

## 1. Code Review & Optimization

Your initial approach is logically sound:
- Creates `pos` and `negs` vectors ($O(N)$ memory).
- Loops twice: once to separate, once to combine.

### How to Optimize to 1-Pass (Two Index Pointers)

Notice the index structure of the output array:
- Positive numbers ALWAYS occupy **EVEN indices**: `0, 2, 4, 6, ...`
- Negative numbers ALWAYS occupy **ODD indices**: `1, 3, 5, 7, ...`

Instead of creating separate `pos` and `negs` vectors:
1. Pre-allocate the result array: `vector<int> ans(n);`.
2. Maintain two index pointers:
   - `posIndex = 0`
   - `negIndex = 1`
3. Iterate through `nums` in a **single pass**:
   - If `num > 0`: `ans[posIndex] = num; posIndex += 2;`
   - If `num < 0`: `ans[negIndex] = num; negIndex += 2;`

---

## 2. Optimized 1-Pass C++ Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    vector<int> rearrangeArray(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = nums.size();
        vector<int> ans(n);

        int posIndex = 0; // Positives go to 0, 2, 4, 6...
        int negIndex = 1; // Negatives go to 1, 3, 5, 7...

        for (int num : nums) {
            if (num > 0) {
                ans[posIndex] = num;
                posIndex += 2;
            } else {
                ans[negIndex] = num;
                negIndex += 2;
            }
        }

        return ans;
    }
};
```

---

## 3. Comparison of Approaches

| Feature | Your Initial Solution | Optimized 1-Pass Solution |
| :--- | :--- | :--- |
| **Passes over `nums`** | 2 Passes (1 to separate, 1 to combine) | **1 Single Pass** 🏆 |
| **Temporary Vectors** | 3 Vectors (`pos`, `negs`, `ans`) | **1 Vector (`ans`)** 🏆 |
| **Time Complexity** | $O(N)$ | **$O(N)$** |
| **Auxiliary Space** | $O(N)$ extra temporary space | **$O(N)$** (only for result array) |

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    vector<int> rearrangeArray(vector<int>& nums) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = nums.size();
        vector<int> ans(n);

        int posIndex = 0;
        int negIndex = 1;

        for (int num : nums) {
            if (num > 0) {
                ans[posIndex] = num;
                posIndex += 2;
            } else {
                ans[negIndex] = num;
                negIndex += 2;
            }
        }

        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> nums1 = {3, 1, -2, -5, 2, -4};
    vector<int> res1 = sol.rearrangeArray(nums1);
    cout << "Test 1: ";
    for (int x : res1) cout << x << " ";
    cout << "(Expected: 3 -2 1 -5 2 -4)" << endl;

    vector<int> nums2 = {-1, 1};
    vector<int> res2 = sol.rearrangeArray(nums2);
    cout << "Test 2: ";
    for (int x : res2) cout << x << " ";
    cout << "(Expected: 1 -1)" << endl;

    return 0;
}
```
