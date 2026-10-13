# Count Good Numbers (LeetCode 1922)

## Problem Description
A digit string is **good** if:
- The digits (0-indexed) at **even indices** are **even** (`0, 2, 4, 6, 8` $\rightarrow$ 5 choices).
- The digits at **odd indices** are **prime** (`2, 3, 5, 7` $\rightarrow$ 4 choices).

For example, `"2582"` is good because the digits at even positions (`2` and `8`) are even, and the digits at odd positions (`5` and `2`) are prime.

Given an integer `n` ($1 \le n \le 10^{15}$), return the total number of good digit strings of length `n`. Since the answer may be large, return it **modulo $10^9 + 7$**.

---

## 1. Why Recursion / Backtracking Fails
- For a string of length $n$, the total number of combinations is:
  $$5^{\lceil n/2 \rceil} \times 4^{\lfloor n/2 \rfloor}$$
- Because $n \le 10^{15}$, exploring the recursion tree would require $\approx 20^{5 \times 10^{14}}$ steps, resulting in **Time Limit Exceeded** and **Stack Overflow**.
- We only need the **count**, not the actual strings.

---

## 2. Mathematical Intuition
For an $n$-length string (0-indexed):
1. **Count of even positions ($0, 2, 4, \dots$):**
   $$\text{even\_count} = \left\lceil \frac{n}{2} \right\rceil = \frac{n + 1}{2}$$
   Each even position has **5** valid choices: $\{0, 2, 4, 6, 8\}$.

2. **Count of odd positions ($1, 3, 5, \dots$):**
   $$\text{odd\_count} = \left\lfloor \frac{n}{2} \right\rfloor = \frac{n}{2}$$
   Each odd position has **4** valid choices: $\{2, 3, 5, 7\}$.

By the Fundamental Principle of Counting:
$$\text{Total Good Strings} = \left( 5^{\text{even\_count}} \times 4^{\text{odd\_count}} \right) \pmod{10^9 + 7}$$

---

## 3. Pitfalls & Debugging Log

### Pitfall 1: 32-bit `int` Overflow for Exponent (`power`)
- **Buggy Code:**
  ```cpp
  long long pow_helper(long long num, int power) // BUG: int overflows when n = 806166225460393!
  ```
- **Why it failed:** $n$ is up to $10^{15}$. A standard signed `int` caps at $\approx 2 \times 10^9$. Passing $\approx 4 \times 10^{14}$ into `int` causes silent integer truncation / negative values.
- **Fix:** `power` must be `long long`:
  ```cpp
  long long pow_helper(long long num, long long power)
  ```

### Pitfall 2: Modulo at Every Intermediate Multiplication
- Taking `% MOD` only at the end causes numbers to overflow `long long` within a few loop steps.
- Must apply `% MOD` at each `(num * num) % MOD` and `(ans * num) % MOD`.

### Pitfall 3: Final Multiplication Overflow
- Even when `even_pow` and `odd_pow` are $\le 10^9 + 7$, multiplying them can yield $\approx 10^{18}$.
- If stored in `int`, this overflows 32 bits before modulo. Both variables must be `long long`:
  ```cpp
  long long ans = (even_pow * odd_pow) % MOD;
  ```

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(\log n)$ — Binary exponentiation takes $\mathcal{O}(\log n)$ operations.
- **Space Complexity:** $\mathcal{O}(1)$ — Constant auxiliary space.

---

## Code

```cpp
#include <iostream>

using namespace std;

class Solution {
private:
    // Helper function to compute (num^power) % mod in O(log power) time
    long long pow_helper(long long num, long long power, long long mod) {
        long long ans = 1;
        num %= mod;
        while (power > 0) {
            if (power % 2 == 0) {
                num = (num * num) % mod;
                power /= 2;
            } else {
                ans = (ans * num) % mod;
                power--;
            }
        }
        return ans;
    }

public:
    int countGoodNumbers(long long n) {
        const long long MOD = 1e9 + 7;

        long long even = (n + 1) / 2; // Positions 0, 2, 4, ...
        long long odd = n / 2;        // Positions 1, 3, 5, ...

        long long even_pow = pow_helper(5, even, MOD);
        long long odd_pow = pow_helper(4, odd, MOD);

        return (even_pow * odd_pow) % MOD;
    }
};

int main() {
    Solution sol;

    // Test Case 1: n = 1
    // Even index 0: 5 choices -> 5
    cout << "n = 1: " << sol.countGoodNumbers(1) << " (Expected: 5)" << endl;

    // Test Case 2: n = 4
    // 5^2 * 4^2 = 25 * 16 = 400
    cout << "n = 4: " << sol.countGoodNumbers(4) << " (Expected: 400)" << endl;

    // Test Case 3: n = 806166225460393 (Large test case testing 64-bit bounds)
    cout << "n = 806166225460393: " << sol.countGoodNumbers(806166225460393LL)
         << " (Expected: 643535977)" << endl;

    return 0;
}
```

---

## 5. Follow-Up Variation: Generating / Printing All Good Numbers

If the interview question asks to **print or return all valid good digit strings** instead of just counting them (where $n$ is typically small, e.g. $n \le 6$):

### Backtracking Strategy:
1. Maintain `string current` to represent the string built so far.
2. At each position `i`:
   - If `i == n`: base case reached, save `current`.
   - If `i % 2 == 0`: loop through `{'0', '2', '4', '6', '8'}`.
   - If `i % 2 != 0`: loop through `{'2', '3', '5', '7'}`.
3. Push character, recurse to `i + 1`, and pop character (`backtrack`).

### Complexity for Generating:
- **Time Complexity:** $\mathcal{O}(n \cdot 5^{\lceil n/2 \rceil} \cdot 4^{\lfloor n/2 \rfloor})$ — There are $5^{\lceil n/2 \rceil} \times 4^{\lfloor n/2 \rfloor}$ strings, each taking $\mathcal{O}(n)$ to construct and copy.
- **Space Complexity:** $\mathcal{O}(n)$ auxiliary stack space (excluding output storage).

### C++ Code for Generating All Good Numbers:

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class SolutionGenerate {
private:
    const string evens = "02468";
    const string primes = "2357";

    void backtrack(int i, int n, string &current, vector<string> &result) {
        if (i == n) {
            result.push_back(current);
            return;
        }

        // Even index (0, 2, 4, ...): pick from {0, 2, 4, 6, 8}
        if (i % 2 == 0) {
            for (char ch : evens) {
                current.push_back(ch);
                backtrack(i + 1, n, current, result);
                current.pop_back(); // Backtrack
            }
        } 
        // Odd index (1, 3, 5, ...): pick from {2, 3, 5, 7}
        else {
            for (char ch : primes) {
                current.push_back(ch);
                backtrack(i + 1, n, current, result);
                current.pop_back(); // Backtrack
            }
        }
    }

public:
    vector<string> generateGoodNumbers(int n) {
        vector<string> result;
        string current = "";
        backtrack(0, n, current, result);
        return result;
    }
};
```
