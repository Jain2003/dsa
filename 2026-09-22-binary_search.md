# Binary Search (Concept & Master Template)

## Problem Summary
Binary Search is a divide-and-conquer search algorithm that finds the position of a `target` value within a **sorted array** (or a monotonic search space).

---

## 1. Real-World Analogy

Imagine guessing a secret number between `1` and `100`:
- **Linear Search:** Guessing `1`, `2`, `3`, `4`... (Takes up to 100 guesses).
- **Binary Search:** You guess `50`. The host says "Too High!". Now you know the number is between `1` and `49` (eliminating 50 numbers in ONE guess!). Next guess: `25`.

---

## 2. Core Mechanism & Step-by-Step Algorithm

Maintain two boundary pointers: `low = 0` and `high = n - 1`.

1. While `low <= high`:
   - Compute midpoint: `mid = low + (high - low) / 2` (Prevents integer overflow!).
   - If `arr[mid] == target` $\rightarrow$ **Found!** Return `mid`.
   - If `arr[mid] < target` $\rightarrow$ Target is in the right half $\rightarrow$ **`low = mid + 1`**.
   - If `arr[mid] > target` $\rightarrow$ Target is in the left half $\rightarrow$ **`high = mid - 1`**.
2. If `low > high`, target is not present in the array $\rightarrow$ Return `-1`.

---

## 3. Why `mid = low + (high - low) / 2` Prevents Integer Overflow

In C++, a standard signed 32-bit `int` can store values up to `INT_MAX = 2,147,483,647` ($\approx 2.14 \times 10^9$).

### The Overflow Problem with `(low + high) / 2`:
Suppose you have a huge array where:
- `low = 1,500,000,000`
- `high = 1,800,000,000`

Both `low` and `high` fit inside `int` on their own. But when you evaluate `(low + high)`:
$$\text{low} + \text{high} = 1,500,000,000 + 1,800,000,000 = 3,300,000,000$$

Since $3.3 \times 10^9 > 2.14 \times 10^9$, **it overflows 32-bit `int` into a negative number** (wraps around to `-994,967,296`).
Dividing by `2` gives a negative index `-497,483,648`, causing an **Out of Bounds Array Crash**!

### The Mathematical Fix: `low + (high - low) / 2`
Algebraically:
$$\text{low} + \frac{\text{high} - \text{low}}{2} = \frac{2\text{low} + \text{high} - \text{low}}{2} = \frac{\text{low} + \text{high}}{2}$$

The key difference is the **order of operations**:
1. `(high - low)` calculates the **distance** between pointers ($1.8 \times 10^9 - 1.5 \times 10^9 = 300,000,000$), which is a small positive number!
2. `(high - low) / 2` halves that distance ($150,000,000$).
3. `low + 150,000,000` gives `1,650,000,000`, which is $\le \text{high} \le \text{INT\_MAX}$.

**At no point in the calculation does any intermediate value exceed `INT_MAX`!**

---

## Complexity Analysis

| Algorithm | Best Case | Average Case | Worst Case | Auxiliary Space |
| :--- | :--- | :--- | :--- | :--- |
| **Linear Search** | $O(1)$ | $O(N)$ | $O(N)$ | $O(1)$ |
| **Binary Search** | $O(1)$ | $O(\log_2 N)$ | $O(\log_2 N)$ | $O(1)$ |

### Power of $O(\log N)$:
- For $N = 1,000,000$ ($10^6$) elements: Binary Search takes at most **20 comparisons**!
- For $N = 1,000,000,000$ ($10^9$) elements: Binary Search takes at most **30 comparisons**!

---

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class BinarySearch {
public:
    int search(vector<int>& nums, int target) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int low = 0;
        int high = nums.size() - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            if (nums[mid] == target) {
                return mid; // Target found at index 'mid'
            } else if (nums[mid] < target) {
                low = mid + 1; // Search right half
            } else {
                high = mid - 1; // Search left half
            }
        }

        return -1; // Target not found
    }
};

int main() {
    BinarySearch bs;
    vector<int> nums = {-1, 0, 3, 5, 9, 12}; // MUST BE SORTED

    cout << "Index of 9: " << bs.search(nums, 9) << " (Expected: 4)" << endl;
    cout << "Index of 2: " << bs.search(nums, 2) << " (Expected: -1)" << endl;

    return 0;
}
```
