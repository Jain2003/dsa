# Koko Eating Bananas (Binary Search on Answer)

## Problem Description
Koko loves to eat bananas. There are `n` piles of bananas, the `i`-th pile has `piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her banana-eating speed `k` (bananas-per-hour). Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas during this hour.

Return the **minimum integer `k`** such that she can eat all the bananas within `h` hours.

**Example 1:**
- **Input:** `piles = [3, 6, 7, 11]`, `h = 8`
- **Output:** `4`

**Example 2:**
- **Input:** `piles = [30, 11, 23, 4, 20]`, `h = 5`
- **Output:** `30`

---

## Bugs in User's Code

1. **Division by Zero (`low = 0`):**
   - Minimum possible eating speed is `1` banana/hour (she cannot eat 0 bananas/hour).
   - When `low = 0` and `high = 1`, `mid` becomes `0`.
   - Executing `(x + mid - 1) / mid` causes a **Division by Zero (SIGFPE) Crash**!

2. **Integer Overflow (`int time`):**
   - For large inputs where `piles[i]` can be up to $10^9$ and `mid = 1`, the sum of hours across all piles can exceed `INT_MAX` ($\approx 2 \cdot 10^9$).
   - `time` (or `hours`) must be declared as **`long long`**.

---

## Optimal Approach: Binary Search on Answer Space

Since eating speed `k` is monotonic:
- If speed `k` allows Koko to finish in $\le h$ hours, any speed $> k$ will also work.
- If speed `k` takes $> h$ hours, any speed $< k$ will also fail.

Therefore, we binary search for `k` in the range `[1, max(piles)]`:

- **`low = 1`** (Minimum speed)
- **`high = max(piles)`** (Maximum speed required)
- For a candidate speed `mid`:
  - Calculate total hours required: $\text{hours} = \sum \lceil \frac{\text{pile}}{\text{mid}} \rceil = \sum \frac{\text{pile} + \text{mid} - 1}{\text{mid}}$.
  - If $\text{hours} \le h$: `mid` is a valid speed! Record `ans = mid` and try smaller speeds (`high = mid - 1`).
  - If $\text{hours} > h$: `mid` is too slow! Try faster speeds (`low = mid + 1`).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log(\max(\text{piles})))$
  - Binary search takes $\log(\max(\text{piles}))$ steps.
  - In each step, we iterate through `N` piles to calculate total hours.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

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
    int minEatingSpeed(vector<int>& piles, int h) {
        int maxPile = 0;
        for (int pile : piles) {
            maxPile = max(maxPile, pile);
        }

        int low = 1; // Speed cannot be 0!
        int high = maxPile;
        int ans = maxPile;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            long long hours = 0; // Use long long to prevent integer overflow
            for (int pile : piles) {
                hours += (long long)(pile + mid - 1) / mid; // Ceil division
            }

            if (hours <= h) {
                ans = mid;         // Valid speed, try to find a smaller speed
                high = mid - 1;
            } else {
                low = mid + 1;     // Too slow, increase speed
            }
        }

        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> piles1 = {3, 6, 7, 11};
    int h1 = 8;
    cout << "Test 1 -> Min Speed: " << sol.minEatingSpeed(piles1, h1) << " (Expected: 4)\n";

    vector<int> piles2 = {30, 11, 23, 4, 20};
    int h2 = 5;
    cout << "Test 2 -> Min Speed: " << sol.minEatingSpeed(piles2, h2) << " (Expected: 30)\n";

    return 0;
}
```
