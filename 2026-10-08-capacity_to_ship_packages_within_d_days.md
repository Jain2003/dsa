# Capacity To Ship Packages Within D Days (Binary Search on Answer)

## Problem Description
A conveyor belt has packages that must be shipped from one port to another within `days` days.

The `i`-th package on the conveyor belt has a weight of `weights[i]`. Each day, we load the ship with packages on the conveyor belt (in the order given by `weights`). We may not load more weight than the maximum weight capacity of the ship.

Return the **least weight capacity of the ship** that will result in all the packages on the conveyor belt being shipped within `days` days.

**Example 1:**
- **Input:** `weights = [1,2,3,4,5,6,7,8,9,10]`, `days = 5`
- **Output:** `15`
- **Explanation:** 
  - 1st day: 1, 2, 3, 4, 5 (sum = 15)
  - 2nd day: 6, 7 (sum = 13)
  - 3rd day: 8 (sum = 8)
  - 4th day: 9 (sum = 9)
  - 5th day: 10 (sum = 10)

**Example 2:**
- **Input:** `weights = [3,2,2,4,14]`, `days = 3`
- **Output:** `15`

---

## Bugs in User's Code

### 1. `low` Must Start at `max(weights)`! (Minimum Capacity Bug) 💥
- The ship capacity `mid` **must be at least as large as the heaviest package** (`max(weights)`).
- If `low = 1` and `mid < max(weights)`, a single package heavier than `mid` can never fit on the ship!
- **Fix:** Set `low = max(weights)`.

### 2. Incorrect Decision Condition (`days_taken >= days`) ❌
- If `days_taken <= days` (e.g. takes 5 days when `days = 5`), the capacity `mid` is **valid**! We should save `ans = mid` and try smaller capacities (`high = mid - 1`).
- In your code, `if (days_taken >= days) low = mid + 1;` rejects valid capacities when `days_taken == days`!

### 3. Flawed Day-Counting Simulation ❌
- The standard way to simulate ship loading:
  ```cpp
  int daysTaken = 1;
  int currentLoad = 0;
  for (int w : weights) {
      if (currentLoad + w > capacity) {
          daysTaken++;       // Start a new day
          currentLoad = w;   // Load package w on the new day
      } else {
          currentLoad += w;  // Add to current day
      }
  }
  ```

---

## Optimal Approach: Binary Search on Answer Space

Search range for ship capacity:
- **`low = max(weights)`** (Smallest possible valid capacity)
- **`high = sum(weights)`** (Capacity to ship everything in 1 day)

For candidate capacity `mid`:
- Calculate `daysTaken` needed to ship all packages.
- If `daysTaken <= days`: `mid` is a valid capacity! Save `ans = mid` and search smaller capacities (`high = mid - 1`).
- If `daysTaken > days`: `mid` is too small! Search larger capacities (`low = mid + 1`).

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N \log(\sum \text{weights} - \max(\text{weights})))$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

class Solution {
public:
    int shipWithinDays(vector<int>& weights, int days) {
        int maxWeight = 0;
        int sumWeight = 0;

        for (int w : weights) {
            maxWeight = max(maxWeight, w);
            sumWeight += w;
        }

        // low must be at least maxWeight!
        int low = maxWeight;
        int high = sumWeight;
        int ans = sumWeight;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            int daysTaken = 1;
            int currentLoad = 0;

            for (int w : weights) {
                if (currentLoad + w > mid) {
                    daysTaken++;     // Ship current load, start a new day
                    currentLoad = w; // Load package w on new day
                } else {
                    currentLoad += w;
                }
            }

            // If we can ship within 'days' days
            if (daysTaken <= days) {
                ans = mid;         // Valid capacity, try smaller capacity
                high = mid - 1;
            } else {
                low = mid + 1;     // Capacity too small, increase capacity
            }
        }

        return ans;
    }
};

int main() {
    Solution sol;

    vector<int> w1 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    cout << "Test 1 -> Min Capacity: " << sol.shipWithinDays(w1, 5) << " (Expected: 15)\n";

    vector<int> w2 = {3, 2, 2, 4, 14};
    cout << "Test 2 -> Min Capacity: " << sol.shipWithinDays(w2, 3) << " (Expected: 15)\n";

    return 0;
}
```
