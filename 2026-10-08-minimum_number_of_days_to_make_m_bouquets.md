# Minimum Number of Days to Make m Bouquets (Binary Search on Answer)

## Problem Description
You are given an integer array `bloomDay`, an integer `m` and an integer `k`.

You want to make `m` bouquets. To make a bouquet, you need to use `k` **adjacent flowers** from the garden.

The garden consists of `n` flowers, where the `i`-th flower will bloom in the `bloomDay[i]` and then can be used in **exactly one** bouquet.

Return the **minimum number of days** you need to wait to be able to make `m` bouquets from the garden. If it is impossible to make `m` bouquets return `-1`.

**Example 1:**
- **Input:** `bloomDay = [1,10,3,10,2]`, `m = 3`, `k = 1`
- **Output:** `3`
- **Explanation:** 
  - After day 1: `[1, _, _, _, _]` -> 1 bouquet
  - After day 2: `[1, _, _, _, 2]` -> 2 bouquets
  - After day 3: `[1, _, 3, _, 2]` -> 3 bouquets! (Return 3)

**Example 2:**
- **Input:** `bloomDay = [1,10,3,10,2]`, `m = 3`, `k = 2`
- **Output:** `-1`
- **Explanation:** We need $3 \times 2 = 6$ flowers, but array size is 5. Output `-1`.

---

## Analysis of Bugs in User Code

### 1. 32-bit Integer Overflow (`int n = m * k`) 💥
- `m` and `k` can be up to $10^9$.
- `m * k` can be up to $10^{18}$, which **overflows standard 32-bit `int`**, causing a negative or truncated value.
- **Fix:** Use `long long required = (long long)m * k;`.

### 2. Strict Equality Check (`if (nums == m)`) ❌
- If on day `mid`, we can make **`nums >= m` bouquets** (e.g. 5 bouquets when we only need `m = 3`), that day is **100% valid**!
- Using `if (nums == m)` fails whenever a day produces more than `m` bouquets.
- **Fix:** Change condition to **`if (bouquets >= m)`**.

---

## Optimal Approach: Binary Search on Answer Space

The number of bouquets we can make on day `mid` is monotonically increasing with `mid`:
- If we can make $\ge m$ bouquets on day `mid`, any day $> mid$ will also work.
- Search range: `low = 1`, `high = max(bloomDay)`.

### Counting Consecutive Bloomed Flowers:
Iterate through `bloomDay`:
- If `bloomDay[i] <= mid`: `count++`.
- If `count == k`: `bouquets++`, reset `count = 0`.
- If `bloomDay[i] > mid`: reset `count = 0` (adjacent sequence broken).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log(\max(\text{bloomDay})))$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int minDays(vector<int>& bloomDay, int m, int k) {
        // Total flowers needed = m * k
        long long requiredFlowers = (long long)m * k;
        if (bloomDay.size() < requiredFlowers) {
            return -1; // Not enough total flowers
        }

        int minDay = 1;
        int maxDay = 0;
        for (int day : bloomDay) {
            maxDay = max(maxDay, day);
        }

        int low = minDay;
        int high = maxDay;
        int ans = maxDay;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            int bouquets = 0;
            int count = 0; // Tracks consecutive bloomed flowers

            for (int day : bloomDay) {
                if (day <= mid) {
                    count++;
                    if (count == k) {
                        bouquets++;
                        count = 0;
                    }
                } else {
                    count = 0; // Sequence broken
                }
            }

            // If we can make AT LEAST m bouquets on day mid
            if (bouquets >= m) {
                ans = mid;         // Valid day, try to find an earlier day
                high = mid - 1;
            } else {
                low = mid + 1;     // Not enough bouquets, wait more days
            }
        }

        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> b1 = {1, 10, 3, 10, 2};
    cout << "Test 1 -> Min Days: " << sol.minDays(b1, 3, 1) << " (Expected: 3)\n";

    vector<int> b2 = {1, 10, 3, 10, 2};
    cout << "Test 2 -> Min Days: " << sol.minDays(b2, 3, 2) << " (Expected: -1)\n";

    return 0;
}
```
