# Second Largest Element in Array

## Problem Summary
Given an array of integers `nums`, find and return the **second largest distinct element** in the array. If no second largest distinct element exists (e.g., all elements are identical or array size $< 2$), return `-1`.

---

## 1. Approach 1: Single Pass ($O(N)$ Time, $O(1)$ Space) — Optimal

```cpp
class Solution {
public:
    int secondLargestElement(vector<int>& nums) {
        int first = INT_MIN;
        int second = INT_MIN;

        for (auto x : nums) {
            if (x > first) {
                second = first;  // Demote old maximum to second place
                first = x;       // Update new maximum
            } else if (x > second && x != first) {
                second = x;      // Update second largest (ignoring duplicates)
            }
        }

        return (second == INT_MIN) ? -1 : second;
    }
};
```
- **Time Complexity:** $O(N)$ — Single pass through `nums`.
- **Space Complexity:** $O(1)$ — Uses only 2 integer variables (`first`, `second`).

---

## 2. Approach 2: Using `std::set` ($O(N \log N)$ Time, $O(N)$ Space)

### Intuition:
1. `std::set` automatically eliminates duplicates AND keeps elements sorted in ascending order.
2. If `s.size() < 2`, no distinct second largest exists $\rightarrow$ return `-1`.
3. Otherwise, return `n[n.size() - 2]` (or use `std::set::rbegin()`).

### Your `set` Code:
```cpp
class Solution {
public:
    int secondLargestElement(vector<int>& nums) {
        set<int> s;
        for (auto x : nums) {
            s.insert(x);
        }

        vector<int> n;
        for (auto x : s) {
            n.push_back(x);
        }

        if (n.size() == 1) {
            return -1;
        }
        return n[n.size() - 2];
    }
};
```

### Micro-Optimization (Access `set` 2nd largest without copying to `vector`):
Instead of copying `set` into a `vector` (extra $O(N)$ memory), use `std::set::rbegin()` (reverse iterator):
```cpp
if (s.size() < 2) return -1;
return *std::next(s.rbegin()); // Points directly to 2nd largest element!
```

---

## 3. Comparison Between Approaches

| Approach | Time Complexity | Space Complexity | Recommendation |
| :--- | :--- | :--- | :--- |
| **Approach 1 (Single Pass Loop)** | **$O(N)$** | **$O(1)$** | 🏆 **Best for Interviews** |
| **Approach 2 (`set` + `vector`)** | $O(N \log N)$ | $O(N)$ | Simple & short code |

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <set>
#include <climits>

using namespace std;

// Approach 1: Optimal 1-Pass
class Solution {
public:
    int secondLargestElement(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int first = INT_MIN;
        int second = INT_MIN;

        for (auto x : nums) {
            if (x > first) {
                second = first;
                first = x;
            } else if (x > second && x != first) {
                second = x;
            }
        }

        return (second == INT_MIN) ? -1 : second;
    }
};

int main() {
    Solution sol;

    vector<int> arr1 = {12, 35, 1, 10, 34, 1};
    cout << "Test 1: " << sol.secondLargestElement(arr1) << " (Expected: 34)" << endl;

    vector<int> arr2 = {10, 5, 10};
    cout << "Test 2: " << sol.secondLargestElement(arr2) << " (Expected: 5)" << endl;

    vector<int> arr3 = {10, 10, 10};
    cout << "Test 3: " << sol.secondLargestElement(arr3) << " (Expected: -1)" << endl;

    return 0;
}
```
