# Storing Fibonacci Numbers into an Array Using Recursion

## Problem Summary
Populate and return an array/vector of the first $N + 1$ Fibonacci numbers using **recursion**.

---

## Technical Approach: Recursive Memoization (Top-Down Dynamic Programming)

### 1. The Strategy
Pass a `vector<int>` (or array) **by reference** (`vector<int>& dp`) to the recursive helper function. As recursion solves each subproblem, it stores the calculated Fibonacci number directly into `dp[n]`.

### 2. Code Breakdown
```cpp
int solveFibMemo(int n, vector<int>& dp) {
    // Base Cases: fib(0) = 0, fib(1) = 1
    if (n <= 1) {
        return dp[n] = n;
    }
    
    // Memoization Check: Avoid redundant recursive calls
    if (dp[n] != -1) {
        return dp[n];
    }

    // Compute recursively & store in array
    return dp[n] = solveFibMemo(n - 1, dp) + solveFibMemo(n - 2, dp);
}
```

### 3. How the Recursion Fills the Array
For $N = 4$:
1. `dp` starts initialized as `[-1, -1, -1, -1, -1]`.
2. `solveFibMemo(4)` calls `solveFibMemo(3)` $\rightarrow$ `solveFibMemo(2)` $\rightarrow$ `solveFibMemo(1)`.
3. Base cases store `dp[0] = 0` and `dp[1] = 1`.
4. `dp[2] = dp[1] + dp[0] = 1`.
5. `dp[3] = dp[2] + dp[1] = 2`.
6. `dp[4] = dp[3] + dp[2] = 3`.
7. Final array `dp` is `[0, 1, 1, 2, 3]`.

---

## Complexity Analysis

- **Time Complexity:** $O(N)$
  - *Why?* Each state from $0$ to $N$ is computed **only once** because calculated values are saved in `dp`.
- **Space Complexity:** $O(N)$
  - *Why?* Requires $O(N)$ memory for the output array `dp` and $O(N)$ call stack depth for recursion.

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
private:
    // Helper recursive function with Memoization (DP)
    int solveFibMemo(int n, vector<int>& dp) {
        if (n <= 1) {
            return dp[n] = n;
        }
        
        // If already calculated, return stored value
        if (dp[n] != -1) {
            return dp[n];
        }

        // Store result of recursive calls into the array/vector
        return dp[n] = solveFibMemo(n - 1, dp) + solveFibMemo(n - 2, dp);
    }

public:
    // 1. Recursive method to populate and return an array of Fibonacci numbers
    vector<int> getFibonacciArrayRecursive(int n) {
        if (n < 0) return {};
        
        // Create an array/vector of size (n + 1) initialized to -1
        vector<int> dp(n + 1, -1);
        
        // Trigger recursive calculation from top down
        solveFibMemo(n, dp);
        
        return dp; // Returns array containing [0, 1, 1, 2, 3, ...]
    }
};

int main() {
    Solution obj;
    int n = 4;

    vector<int> fibs = obj.getFibonacciArrayRecursive(n);

    cout << "Fibonacci array up to N = " << n << " using recursion: ";
    for (int val : fibs) {
        cout << val << " "; // Output: 0 1 1 2 3
    }
    cout << endl;

    return 0;
}
```
