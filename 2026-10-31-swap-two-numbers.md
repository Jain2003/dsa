# Swap Two Numbers Without a Temporary Variable

## Problem Description
Given two integers `a` and `b`, swap their values in-place using only the two variables (without allocating any temporary variable).

Solve it using:
1. **Bitwise XOR (`^`)** (Recommended & safe from overflow)
2. **Arithmetic Operations (`+` and `-`)**

**Example 1:**
- **Input:** `a = 5, b = 10`
- **Output:** `a = 10, b = 5`

**Example 2:**
- **Input:** `a = -100, b = -200`
- **Output:** `a = -200, b = -100`

---

## 1. Method 1: Bitwise XOR (`^`) (Optimal & Overflow-Safe ⭐)

### Core Properties of XOR
1. $X \oplus X = 0$ (Self-canceling)
2. $X \oplus 0 = X$ (Identity)
3. $A \oplus B = B \oplus A$ (Commutative)
4. $(A \oplus B) \oplus C = A \oplus (B \oplus C)$ (Associative)

### 3-Step XOR Swap Algorithm
```cpp
a = a ^ b; // Step 1: a stores XOR sum of both (a_orig ^ b_orig)
b = a ^ b; // Step 2: b = (a_orig ^ b_orig) ^ b_orig = a_orig ^ 0 = a_orig
a = a ^ b; // Step 3: a = (a_orig ^ b_orig) ^ a_orig = b_orig ^ 0 = b_orig
```

### Trace: `a = 5 (0101_2)`, `b = 10 (1010_2)`
- **Step 1:** `a = 5 ^ 10 = 15 (1111_2)`
- **Step 2:** `b = 15 ^ 10 = 5` (Now `b` has original `a`!)
- **Step 3:** `a = 15 ^ 5 = 10` (Now `a` has original `b`!)
- **Final Result:** `a = 10, b = 5`.

### Why XOR is the Best:
- **Zero Arithmetic Overflow Risk:** XOR operates on individual bit positions independently. There are no carries, so it can never exceed the bounds of `int`, even for `INT_MAX` or negative numbers.

---

## 2. Method 2: Arithmetic Operations (`+` and `-`)

### Algorithm
```cpp
a = a + b; // Step 1: a stores total sum
b = a - b; // Step 2: b = (a + b) - b = original a
a = a - b; // Step 3: a = (a + b) - original a = original b
```

### Trace: `a = 5, b = 10`
- **Step 1:** `a = 5 + 10 = 15`
- **Step 2:** `b = 15 - 10 = 5`
- **Step 3:** `a = 15 - 5 = 10`
- **Final Result:** `a = 10, b = 5`.

### ⚠️ Critical Warning with Arithmetic Swap:
If `a` and `b` are both large (e.g. `a = INT_MAX, b = 10`), `a + b` causes **Signed Integer Overflow**, which is **Undefined Behavior (UB)** in C and C++!
- XOR does not have this issue.

---

## 3. The Classic Edge Case: Same Memory Address (`&a == &b`)
If you use XOR swap on elements in an array where the indices are equal (`swap(arr[i], arr[i])`):
```cpp
a = a ^ a; // a becomes 0!
```
XORing a variable with itself wipes it out to `0`. Always ensure `&a != &b` before swapping via pointer/reference XOR.

---

## 4. Complexity Comparison

| Method | Time Complexity | Auxiliary Space | Overflow Risk? |
| :--- | :---: | :---: | :---: |
| **Bitwise XOR (`^`)** ⭐ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | **None (Safe across all integer ranges)** |
| **Arithmetic (`+`, `-`)** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | ⚠️ **Risk of signed integer overflow (UB)** |
| **Built-in `std::swap`** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | None (Modern compiler optimizes via registers) |

---

## 5. Code

```cpp
#include <iostream>
#include <utility>

using namespace std;

class Solution {
public:
    // =========================================================================
    // Method 1: Bitwise XOR Swap (Optimal, Safe from Overflow)
    // =========================================================================
    static void swapXOR(int &a, int &b) {
        if (&a == &b) return; // Prevent wiping out if both refer to same memory location
        a = a ^ b;
        b = a ^ b;
        a = a ^ b;
    }

    // =========================================================================
    // Method 2: Arithmetic Swap (+ and -)
    // =========================================================================
    static void swapArithmetic(int &a, int &b) {
        a = a + b;
        b = a - b;
        a = a - b;
    }
};

int main() {
    // Test 1: Positive numbers with XOR
    int a1 = 5, b1 = 10;
    cout << "Original 1: a = " << a1 << ", b = " << b1 << endl;
    Solution::swapXOR(a1, b1);
    cout << "After XOR Swap: a = " << a1 << ", b = " << b1 << "\n\n";

    // Test 2: Negative numbers with XOR
    int a2 = -100, b2 = -200;
    cout << "Original 2: a = " << a2 << ", b = " << b2 << endl;
    Solution::swapXOR(a2, b2);
    cout << "After XOR Swap: a = " << a2 << ", b = " << b2 << "\n\n";

    // Test 3: Arithmetic Swap
    int a3 = 42, b3 = 99;
    cout << "Original 3: a = " << a3 << ", b = " << b3 << endl;
    Solution::swapArithmetic(a3, b3);
    cout << "After Arithmetic Swap: a = " << a3 << ", b = " << b3 << "\n\n";

    // Test 4: Same memory location safeguard
    int arr[] = {7, 8, 9};
    Solution::swapXOR(arr[0], arr[0]);
    cout << "Self-swap test: arr[0] = " << arr[0] << " (Expected: 7)\n";

    return 0;
}
```
