# Product of Array Except Self (LeetCode 238)

## Problem Summary
Given an integer array `nums`, return an array `ans` such that `ans[i]` is equal to the product of all elements of `nums` except `nums[i]` without using division and in $O(N)$ time.

---

## 1. Analysis of Your Code & The Bug

### Your Code:
```cpp
vector<int> ans; // Empty vector (size = 0)

for (int i = 0; i < nums.size(); i++) {
    ans[i] = pre[i] * post[i]; // âŒ CRASH! Out-of-bounds access!
}
```

### Why it crashed:
`vector<int> ans;` creates a vector of **size 0**. Accessing `ans[i]` on an empty vector tries to write to memory outside the vector's bounds, causing a **Segmentation Fault (Runtime Error)**.

### Fix:
Initialize `ans` with size `nums.size()`:
```cpp
vector<int> ans(nums.size()); // âœ… Pre-allocated size n
```

---

## 2. Approach 1: Prefix & Suffix Arrays ($O(N)$ Time, $O(N)$ Space)

Calculate prefix (left) products and suffix (right) products into two separate vectors:
1. `pre[i]` = product of all elements to the left of index `i`.
2. `post[i]` = product of all elements to the right of index `i`.
3. `ans[i] = pre[i] * post[i]`.

---

## 3. Approach 2: Optimal $O(1)$ Auxiliary Space Solution

Instead of allocating two separate vectors `pre` and `post`, use the output `ans` vector to hold prefix products, and a single running `int curr_post = 1` variable for suffix products:

```cpp
vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> ans(n, 1);

    // Pass 1: Store prefix products directly inside ans
    for (int i = 1; i < n; i++) {
        ans[i] = ans[i - 1] * nums[i - 1];
    }

    // Pass 2: Multiply suffix products using running variable
    int curr_post = 1;
    for (int i = n - 1; i >= 0; i--) {
        ans[i] *= curr_post;
        curr_post *= nums[i];
    }

    return ans;
}
```

---

## Complexity Summary

| Approach | Time Complexity | Auxiliary Space | Notes |
| :--- | :--- | :--- | :--- |
| **Brute Force** | $O(N^2)$ | $O(1)$ | Nested loops recalculating product for each $i$. |
| **3-Pass Prefix/Suffix Arrays** | **$O(N)$** | $O(N)$ | Uses extra `pre` and `post` vectors. |
| **Optimal Two-Pass** | **$O(N)$** | **$O(1)$** | (Output array doesn't count as extra space per problem statement). |

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    // Approach 1: 3-Pass using Prefix and Suffix Arrays (O(N) Time, O(N) Space)
    vector<int> productExceptSelfArrays(vector<int>& nums) {
        int n = nums.size();
        vector<int> pre(n);
        vector<int> post(n);

        pre[0] = 1;
        post[n - 1] = 1;

        // Pass 1: Build Prefix Array
        for (int i = 1; i < n; i++) {
            pre[i] = pre[i - 1] * nums[i - 1];
        }

        // Pass 2: Build Suffix Array
        for (int i = n - 2; i >= 0; i--) {
            post[i] = post[i + 1] * nums[i + 1];
        }

        // Pass 3: Combine Prefix and Suffix
        vector<int> ans(n); // Allocated size n to avoid out-of-bounds crash
        for (int i = 0; i < n; i++) {
            ans[i] = pre[i] * post[i];
        }

        return ans;
    }

    // Approach 2: Optimal Two-Pass (O(N) Time, O(1) Auxiliary Extra Space)
    vector<int> productExceptSelf(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int n = nums.size();
        vector<int> ans(n, 1);

        // Pass 1: Store prefix products directly in ans array
        for (int i = 1; i < n; i++) {
            ans[i] = ans[i - 1] * nums[i - 1];
        }

        // Pass 2: Multiply suffix products using a single running variable
        int curr_post = 1;
        for (int i = n - 1; i >= 0; i--) {
            ans[i] *= curr_post;
            curr_post *= nums[i];
        }

        return ans;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 2, 3, 4};

    vector<int> result = sol.productExceptSelf(nums);

    cout << "Result: [ ";
    for (int x : result) {
        cout << x << " ";
    }
    cout << "]" << endl; // Output: [ 24 12 8 6 ]

    return 0;
}
```
