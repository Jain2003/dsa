# Optimized Bubble Sort

## Problem Summary
Sort an array in ascending order using Bubble Sort with **inner loop range reduction** and **`didSwap` early exit optimization**.

---

## How This Code Reduces Time Complexity

### 1. `didSwap` Flag Optimization (**Best-Case Reduction from $O(N^2)$ to $O(N)$**)
- In naive Bubble Sort, the algorithm executes all $N^2$ iterations regardless of whether the array is already sorted.
- In this code, `didSwap` tracks if **any swap took place** during an inner pass:
  ```cpp
  if (didSwap == 0) {
      break; // Stop early! Array is already sorted.
  }
  ```
- **Impact:** If the array is already sorted (e.g., `[1, 2, 3, 4, 5]`), the inner loop runs **only 1 pass** ($N-1$ comparisons), sees `didSwap == 0`, and exits immediately.
- This improves the **Best-Case Time Complexity to $O(N)$**.

---

### 2. Inner Loop Range Reduction (`j <= i - 1`)
- Notice the inner loop limit: `j <= i - 1`, where `i` decreases from `n - 1` down to `0`.
- **Why?** After pass 1, the maximum element is locked at index $n-1$. After pass 2, the second largest is locked at $n-2$.
- The inner loop **skips checking already-sorted elements at the end**, reducing total comparisons from $N \times N = N^2$ down to:
  $$(N-1) + (N-2) + \dots + 1 = \frac{N(N-1)}{2} \approx \frac{N^2}{2}$$
- While the worst-case asymptotic notation remains $O(N^2)$, this **cuts the actual number of comparison operations in half**.

---

## Time & Space Complexity Summary

| Complexity | Naive Bubble Sort | Optimized Bubble Sort (This Code) |
| :--- | :--- | :--- |
| **Best Case Time** | $O(N^2)$ | **$O(N)$** *(Array already sorted)* |
| **Average Case Time** | $O(N^2)$ | **$O(N^2)$** |
| **Worst Case Time** | $O(N^2)$ | **$O(N^2)$** *(Array reverse sorted)* |
| **Space Complexity** | $O(1)$ | **$O(1)$** |

## Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class BubbleSort {
public:
    void bubble_sort(vector<int>& arr) {
        int n = arr.size();

        for (int i = n - 1; i >= 0; i--) {
            int didSwap = 0; // Optimization flag to detect if array is already sorted

            // Inner loop scans up to (i - 1), avoiding already sorted elements at the end
            for (int j = 0; j <= i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    swap(arr[j], arr[j + 1]);
                    didSwap = 1; // Mark that a swap occurred
                }
            }

            // If no swaps occurred in the entire inner pass, array is sorted!
            if (didSwap == 0) {
                break;
            }
        }

        cout << "After Using Bubble Sort:\n";
        for (int num : arr) {
            cout << num << " ";
        }
        cout << endl;
    }
};

int main() {
    vector<int> arr = {13, 46, 24, 52, 20, 9};

    cout << "Before Using Bubble Sort:\n";
    for (int num : arr) {
        cout << num << " ";
    }
    cout << endl;

    BubbleSort sorter;
    sorter.bubble_sort(arr);

    return 0;
}
```
