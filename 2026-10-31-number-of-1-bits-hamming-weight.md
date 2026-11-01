# Number of 1 Bits - Hamming Weight (LeetCode 191)

## Problem Description
Given a positive integer `n`, write a function that returns the number of set bits (1s) it has (also known as the **Hamming weight**).

**Example 1:**
- **Input:** `n = 11` (Binary: `00000000000000000000000000001011`)
- **Output:** `3`
- **Explanation:** The input binary string has three set bits.

**Example 2:**
- **Input:** `n = 128` (Binary: `00000000000000000000000010000000`)
- **Output:** `1`
- **Explanation:** The input binary string has one set bit.

**Example 3:**
- **Input:** `n = 2147483645` (Binary: `01111111111111111111111111111101`)
- **Output:** `30`

---

## 1. Analysis of Your Approach: Division & Modulo

### How Your Code Works
```cpp
int num = 0;
while (n) {
    num += n % 2;
    n /= 2;
}
return num;
```

### Limitations of this Approach
1. **Division & Modulo are Arithmetic Operations:**
   Arithmetic division `/ 2` and modulo `% 2` involve CPU arithmetic pipeline cycles, whereas bitwise operations (`>> 1` and `& 1`) run directly on the bit registers in 1 clock cycle.
2. **Loops Over All Significant Bits:**
   If `n = 1000000000_2` (e.g. $2^{30}$), it runs about 31 iterations even though there is **only one single set bit**!

---

## 2. The Better Methods

### Method 1: Brian Kernighan’s Algorithm (Optimal Algorithmic Approach ⭐)
Instead of shifting bit-by-bit through all 32 positions, jump **directly from one set bit to the next** using the identity:
$$\mathbf{n \ \& \ (n - 1)}$$

Every execution of `n = n & (n - 1)` **clears the rightmost (lowest) set bit** of `n`.

#### Why does this work?
- Subtracting 1 flips the lowest set bit from `1` to `0`, and turns all lower zeros into ones.
- ANDing with the original number cancels that lowest set bit completely:
  - For $n = 12 = 1100_2$:
    - Iteration 1: $1100_2 \ \& \ 1011_2 = 1000_2$ (`count = 1`)
    - Iteration 2: $1000_2 \ \& \ 0111_2 = 0000_2$ (`count = 2`)
    - `n` is now 0 $\implies$ Loop terminates in **just 2 iterations**!

#### Complexity:
- **Time Complexity:** $\mathcal{O}(K)$ where $K$ is the **number of set bits** ($K \le 32$).
  - For $n = 2^{30}$, your code runs 31 times; Kernighan’s algorithm runs **only 1 time**!
- **Space Complexity:** $\mathcal{O}(1)$.

---

### Method 2: C++ Builtin Hardware Instruction (`__builtin_popcount`)
Modern CPU instruction sets (x86 SSE4.2 / AVX, ARM NEON) provide a dedicated hardware instruction:
- x86: `POPCNT` (computes bit population count in **1 CPU clock cycle**).
- GCC / Clang intrinsic: `__builtin_popcount(n)` or in C++20 `<bit>`: `std::popcount(n)`.

```cpp
return __builtin_popcount(n);
```

---

## 3. Complexity Comparison

| Metric | Your Approach (`% 2, / 2`) | Bit Shift & Mask (`& 1, >> 1`) | Brian Kernighan's Algorithm ⭐ | `__builtin_popcount` (Hardware) |
| :--- | :--- | :--- | :--- | :--- |
| **Loop Iterations** | $\approx \log_2(N)$ | 32 iterations | **$K$ (Number of set bits only)** | **0 loops** |
| **Time Complexity** | $\mathcal{O}(\log N)$ | $\mathcal{O}(32) = \mathcal{O}(1)$ | $\mathcal{O}(K) = \mathcal{O}(1)$ (Fastest loop) | $\mathcal{O}(1)$ (1 CPU instruction) |
| **Space Complexity** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |
| **Best For** | Naive baseline | Simple bit shift | **Interview explanation gold standard** | **Production & Competitive Programming** |

---

## 4. Code

```cpp
#include <iostream>
#include <bitset>

using namespace std;

class Solution {
public:
    // =========================================================================
    // Method 1: Brian Kernighan's Algorithm (Best Algorithmic Method)
    // Runs in O(number of set bits), skips all zeros directly!
    // =========================================================================
    int hammingWeightKernighan(int n) {
        // Treat as unsigned to safely handle 32-bit values without sign extension
        unsigned int val = n;
        int count = 0;

        while (val > 0) {
            val = val & (val - 1); // Clears the lowest set bit
            count++;
        }

        return count;
    }

    // =========================================================================
    // Method 2: Hardware Intrinsic (1 CPU Instruction)
    // =========================================================================
    int hammingWeightBuiltin(int n) {
        return __builtin_popcount(n);
    }

    // =========================================================================
    // Method 3: Bitwise Shift (Faster than / 2 and % 2)
    // =========================================================================
    int hammingWeightShift(int n) {
        unsigned int val = n;
        int count = 0;
        while (val > 0) {
            count += (val & 1); // Check LSB
            val >>= 1;          // Shift right by 1
        }
        return count;
    }
};

int main() {
    Solution sol;

    // Test Case 1: 11 (1011 in binary -> 3 set bits)
    int n1 = 11;
    cout << "Test 1: n = 11 (" << bitset<8>(n1) << ")\n";
    cout << "Kernighan: " << sol.hammingWeightKernighan(n1) << "\n";
    cout << "Builtin:   " << sol.hammingWeightBuiltin(n1) << "\n";

    // Test Case 2: 128 (10000000 in binary -> 1 set bit, runs in 1 loop!)
    int n2 = 128;
    cout << "\nTest 2: n = 128 (" << bitset<8>(n2) << ")\n";
    cout << "Kernighan: " << sol.hammingWeightKernighan(n2) << " (took only 1 iteration!)\n";
    cout << "Builtin:   " << sol.hammingWeightBuiltin(n2) << "\n";

    // Test Case 3: 2147483645 (30 set bits)
    int n3 = 2147483645;
    cout << "\nTest 3: n = 2147483645\n";
    cout << "Kernighan: " << sol.hammingWeightKernighan(n3) << "\n";
    cout << "Builtin:   " << sol.hammingWeightBuiltin(n3) << "\n";

    return 0;
}
```
