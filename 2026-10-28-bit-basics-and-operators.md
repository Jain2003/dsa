# Bit Basics and Operators

## Problem Description / Topic Overview
Computers represent data in binary (bits: `0` or `1`). A bit is the fundamental unit of digital storage. Bit manipulation involves inspecting, setting, clearing, toggling, or shifting individual bits using low-level bitwise operations.

Bit manipulation is widely used in:
- **Bitmasking & Flag Management** (representing multiple booleans in a single integer)
- **Subset Generation** (Power Set using bit representations $0 \dots 2^N - 1$)
- **Permission Systems** (read, write, execute permissions)
- **Compression & Compact State Storage** (e.g. DP state compression)
- **Mathematical & Structural Tricks** (powers of two, finding single/missing numbers, parity checks)

---

## 1. Binary Number Representation & Indexing

### Place Value & Conversion
A binary number uses base 2:
$$\text{Value} = \sum_{i=0}^{n-1} b_i \cdot 2^i$$

For example, $1101_2$:
- Bit 3 ($2^3 = 8$): $1 \times 8 = 8$
- Bit 2 ($2^2 = 4$): $1 \times 4 = 4$
- Bit 1 ($2^1 = 2$): $0 \times 2 = 0$
- Bit 0 ($2^0 = 1$): $1 \times 1 = 1$
- **Total:** $8 + 4 + 0 + 1 = 13_{10}$

### Indexing Convention
Bits are indexed from **right to left**, starting at index **0**:
- **LSB (Least Significant Bit):** Rightmost bit (index 0, represents $2^0$).
- **MSB (Most Significant Bit):** Leftmost bit (highest power of 2, sign bit in signed types).

---

## 2. Core Bitwise Operators

| Operator | Symbol | Operation | Example (`A = 5 (0101_2)`, `B = 3 (0011_2)`) | Result |
| :--- | :---: | :--- | :--- | :--- |
| **AND** | `&` | `1` only if **both** bits are `1` | `0101 & 0011` | `0001` (1) |
| **OR** | `\|` | `1` if **at least one** bit is `1` | `0101 \| 0011` | `0111` (7) |
| **XOR** | `^` | `1` if bits are **different** (exclusive OR) | `0101 ^ 0011` | `0110` (6) |
| **NOT** | `~` | Inverts all bits (`0 -> 1`, `1 -> 0`) | `~5` (32-bit two's complement) | `-6` |
| **Left Shift** | `<<` | Shifts bits left, fills right with `0` ($N \times 2^k$) | `3 << 2` (`0011 << 2`) | `1100` (12) |
| **Right Shift** | `>>` | Shifts bits right, drops discarded bits ($\lfloor N / 2^k \rfloor$) | `13 >> 1` (`1101 >> 1`) | `0110` (6) |

---

## 3. Two's Complement & Negative Numbers

Most modern architectures represent signed integers using **Two's Complement**:
$$\mathbf{-N = \sim N + 1} \quad \iff \quad \mathbf{\sim N = -(N + 1)}$$

### Why `~5 == -6`?
In 8-bit signed integer representation:
- $+5 = 00000101_2$
- $\sim 5 = 11111010_2$
- Leading bit is `1`, meaning it represents a negative number.
- Invert and add 1 to find its magnitude:
  $\sim(11111010_2) + 1 = 00000101_2 + 1 = 00000110_2 = 6_{10}$
- Hence, $\sim 5 = -6$.

---

## 4. Fundamental Bitmasking Recipes

| Operation | Formula | Explanation | Example ($N = 10 = 1010_2$, $i = 1$) |
| :--- | :--- | :--- | :--- |
| **Check $i^{\text{th}}$ Bit** | `(N & (1 << i)) != 0` | Isolates bit $i$; non-zero if set | `1010 & 0010 = 0010 != 0` (True) |
| **Set $i^{\text{th}}$ Bit** | `N \| (1 << i)` | Forces bit $i$ to `1` without altering others | `1000 \| 0010 = 1010` (10) |
| **Clear $i^{\text{th}}$ Bit** | `N & ~(1 << i)` | Mask has `0` at bit $i$ and `1` elsewhere | `1010 & ~0010 = 1010 & 1101 = 1000` (8) |
| **Toggle $i^{\text{th}}$ Bit** | `N ^ (1 << i)` | Flips bit $i$ (`0 -> 1`, `1 -> 0`) | `1010 ^ 0100 = 1110` (14) |
| **Check Odd / Even** | `(N & 1) != 0` | Checks LSB ($2^0$) | `(5 & 1) == 1` (Odd), `(4 & 1) == 0` (Even) |
| **Remove Lowest Set Bit** | `N & (N - 1)` | Clears the rightmost set bit | `1100 & 1011 = 1000` |
| **Extract Lowest Set Bit** | `N & (-N)` | Isolates only the lowest set bit | `1100 & (-1100) = 0100` |
| **Check Power of 2** | `N > 0 && (N & (N - 1)) == 0` | Power of 2 has exactly 1 set bit | `8 = 1000`, `8 & 7 = 1000 & 0111 = 0` |

---

## 5. Important Bitwise Identities & Properties

### XOR Properties (The Self-Canceling Magic)
1. $N \oplus 0 = N$
2. $N \oplus N = 0$
3. $A \oplus B = B \oplus A$ (Commutative)
4. $(A \oplus B) \oplus C = A \oplus (B \oplus C)$ (Associative)
5. **Key Use Case:** Finding single non-duplicate numbers, in-place variable swaps without temporary variables.

### Lowest Set Bit: Why `N & (N - 1)` Works
Subtracting 1 from a binary number turns the lowest set bit from `1` to `0`, and flips all trailing zeros to `1`:
- $N = 12 = 1100_2$
- $N - 1 = 11 = 1011_2$
- $N \ \& \ (N - 1) = 1100_2 \ \& \ 1011_2 = 1000_2$ (lowest set bit at index 2 is removed).

---

## 6. Common Pitfalls & C++ Specifics

1. **Operator Precedence (The `#1` Bug Source):**
   Bitwise operators have **lower precedence** than comparison and arithmetic operators!
   - `if (N & 1 == 0)` parses as `if (N & (1 == 0))` $\implies$ **Wrong!**
   - **Fix:** Always wrap bitwise operations in parentheses: `if ((N & 1) == 0)`.
2. **64-bit Overflow when Shifting `1`:**
   `1 << 35` causes undefined behavior in C++ because `1` is a 32-bit signed integer literal!
   - **Fix:** Use `1ULL << 35` or `1LL << 35`.
3. **Shift Out of Bounds:**
   Shifting by $\ge 32$ bits on a 32-bit integer, or shifting by negative numbers, is **Undefined Behavior**.
4. **Bitwise vs. Logical Operators:**
   `&`, `|`, `~` evaluate all bits on integers. `&&`, `||`, `!` treat inputs as boolean conditions and use short-circuiting.

---

## 7. Complexity Analysis
- All basic bitwise operations (`&`, `|`, `^`, `~`, `<<`, `>>`) execute in **$\mathcal{O}(1)$ time** and **$\mathcal{O}(1)$ auxiliary space**.
- Hardware executes them directly in a single CPU clock cycle (ALU instruction).

---

## 8. Code

```cpp
#include <iostream>
#include <string>
#include <bitset>

using namespace std;

class BitOperations {
public:
    // Check if ith bit is set (0-indexed from right)
    static bool checkBit(int n, int i) {
        return (n & (1 << i)) != 0;
    }

    // Set the ith bit
    static int setBit(int n, int i) {
        return n | (1 << i);
    }

    // Clear the ith bit
    static int clearBit(int n, int i) {
        return n & ~(1 << i);
    }

    // Toggle the ith bit
    static int toggleBit(int n, int i) {
        return n ^ (1 << i);
    }

    // Check if number is power of two
    static bool isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }

    // Remove the lowest set bit
    static int clearLowestSetBit(int n) {
        return n & (n - 1);
    }

    // Isolate the lowest set bit
    static int getLowestSetBit(int n) {
        return n & (-n);
    }

    // Count number of set bits (Brian Kernighan's Algorithm)
    static int countSetBits(int n) {
        int count = 0;
        while (n > 0) {
            n = n & (n - 1); // Clears rightmost set bit
            count++;
        }
        return count;
    }
};

int main() {
    int n = 10; // Binary: 1010
    cout << "Original N = " << n << " (" << bitset<8>(n) << ")\n";

    // 1. Check Bit
    cout << "Is bit 1 set? " << (BitOperations::checkBit(n, 1) ? "Yes" : "No") << "\n";
    cout << "Is bit 2 set? " << (BitOperations::checkBit(n, 2) ? "Yes" : "No") << "\n";

    // 2. Set Bit
    int setRes = BitOperations::setBit(n, 2); // 1010 | 0100 = 1110 (14)
    cout << "After setting bit 2: " << setRes << " (" << bitset<8>(setRes) << ")\n";

    // 3. Clear Bit
    int clearRes = BitOperations::clearBit(n, 1); // 1010 & ~0010 = 1000 (8)
    cout << "After clearing bit 1: " << clearRes << " (" << bitset<8>(clearRes) << ")\n";

    // 4. Toggle Bit
    int toggleRes = BitOperations::toggleBit(n, 2); // 1010 ^ 0100 = 1110 (14)
    cout << "After toggling bit 2: " << toggleRes << " (" << bitset<8>(toggleRes) << ")\n";

    // 5. Lowest Set Bit & Kernighan's Count
    int val = 12; // 1100
    cout << "\nFor val = 12 (" << bitset<8>(val) << "):\n";
    cout << "Lowest set bit isolated: " << BitOperations::getLowestSetBit(val) << "\n";
    cout << "After removing lowest set bit: " << BitOperations::clearLowestSetBit(val) << "\n";
    cout << "Total set bits count: " << BitOperations::countSetBits(val) << "\n";

    // 6. Power of Two Check
    cout << "\nIs 16 power of two? " << (BitOperations::isPowerOfTwo(16) ? "Yes" : "No") << "\n";
    cout << "Is 18 power of two? " << (BitOperations::isPowerOfTwo(18) ? "Yes" : "No") << "\n";

    return 0;
}
```
