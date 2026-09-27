# ⭐ MASTER PROBLEM: Container With Most Water (LeetCode 11)

## Problem Summary
You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i`-th line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the **maximum amount of water** a container can store.

---

## 🧠 MASTER PATTERN: How to Formulate & Identify Two-Pointer Strategies

Many array problems can be solved in $O(N^2)$ brute force, but can be optimized to $O(N)$ using Two Pointers. Here is the **3-Step Framework** to discover pointers on your own during an interview:

### Step 1: Identify the Pointer Signals
How do you know a problem needs Two Pointers?
- **Signal A:** You are looking for a pair/subarray boundary that maximizes or minimizes a calculation (e.g. area, sum, length).
- **Signal B:** Brute force takes $O(N^2)$ by testing all pairs `(i, j)`, but we want $O(N)$ time with $O(1)$ space.
- **Signal C:** The problem has a **Monotonic Property** (e.g., as width `right - left` decreases, we can make strategic choices to increase height).

---

### Step 2: Choose Pointer Layout & Initial Placement
Where should the pointers start?

| Layout Pattern | Placement | When to Use? |
| :--- | :--- | :--- |
| **Opposite Ends** | `left = 0`, `right = n - 1` | Maximizing width/range (Container With Most Water), or Pair Sum on sorted array (Two Sum II, 3Sum, Valid Palindrome). |
| **Same Direction** | `slow = 0`, `fast = 0` | Modifying array in-place, removing duplicates, sliding windows. |

For *Container With Most Water*, starting at `left = 0` and `right = n - 1` maximizes **width** right at the beginning!

---

### Step 3: Formulate the Decision Rule (Elimination Principle)
This is the most critical step. How do you decide **which pointer to move** without looking ahead?

1. **Write down the area formula:**
   $$\text{Area} = \min(\text{height}[left], \text{height}[right]) \times (right - left)$$

2. **Identify the Bottleneck:**
   - The height of the water is strictly limited by the **shorter line** $\min(\text{height}[left], \text{height}[right])$.

3. **Ask the Elimination Question:**
   - *"If `height[left] < height[right]`, what happens if I keep `left` fixed and try moving `right` to the left?"*
   - **Answer:**
     - Width `(right - left)` gets **smaller**.
     - Height can **never exceed `height[left]`**.
     - $\text{New Area} \le \text{height}[left] \times (\text{smaller width}) < \text{Current Area}$.
   - **Conclusion:** Holding the shorter line fixed and moving the taller line can **NEVER** yield a larger area!
   - Therefore, the shorter line has **zero remaining potential**. We eliminate `left` by doing **`left++`**!

---

## 2. Algorithm Breakdown ($O(N)$ Time, $O(1)$ Space)

1. Initialize `left = 0`, `right = height.size() - 1`, `maxArea = 0`.
2. While `left < right`:
   - Calculate `currentArea = min(height[left], height[right]) * (right - left)`.
   - `maxArea = max(maxArea, currentArea)`.
   - If `height[left] < height[right]`, increment `left++`.
   - Else, decrement `right--`.
3. Return `maxArea`.

---

## 3. Cleaned-Up C++ Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int maxArea(vector<int>& height) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int left = 0;
        int right = height.size() - 1;
        int maxArea = 0;

        while (left < right) {
            int currentArea = min(height[left], height[right]) * (right - left);
            maxArea = max(maxArea, currentArea);

            // Eliminate the shorter line (bottleneck)
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxArea;
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ — Single pass through the `height` array using two pointers.
- **Space Complexity:** $O(1)$ — Uses only 3 integer variables (`left`, `right`, `maxArea`).

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int maxArea(vector<int>& height) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int left = 0;
        int right = height.size() - 1;
        int maxArea = 0;

        while (left < right) {
            int currentArea = min(height[left], height[right]) * (right - left);
            maxArea = max(maxArea, currentArea);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxArea;
    }
};

int main() {
    Solution sol;

    vector<int> h1 = {1, 8, 6, 2, 5, 4, 3, 7};
    cout << "Test 1: " << sol.maxArea(h1) << " (Expected: 49)" << endl;

    vector<int> h2 = {1, 1};
    cout << "Test 2: " << sol.maxArea(h2) << " (Expected: 1)" << endl;

    return 0;
}
```
