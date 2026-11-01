# Set the Rightmost Unset Bit

## Problem Description
Given a positive integer `n`, set the **rightmost unset bit** (the first `0` bit from the right / LSB) to `1`. If all bits are already set (e.g., $n = 7 = 111_2$ or $n = 15 = 1111_2$), the number should either remain unchanged (as per GFG / LeetCode conventions: if all bits are set, return `n`).

**Example 1:**
- **Input:** `n = 6` (Binary: `110`)
- **Output:** `7` (Binary: `111`)
- **Explanation:** Bit 0 is unset (`0`). Setting it gives `111` (7).

**Example 2:**
- **Input:** `n = 15` (Binary: `1111`)
- **Output:** `15` (or `31` if allowed to expand, but standard problem specifies: if all bits are set, return `n`).

**Example 3:**
- **Input:** `n = 9` (Binary: `1001`)
- **Output:** `11` (Binary: `1011`)
- **Explanation:** Bit 1 is the rightmost unset bit (`0`). Setting it gives `1011` (11).

---

## 1. Analysis of Your Code & The 3 Critical Bugs

### Your Code
```cpp
class Solution {
public:
    int setRightmostUnsetBit(int n) {
        int i = 0;
        while(n & (1<<i) == 1 && n > i) {
            i++;
        }
        return n |= (1<<i);
    }
};
```

---

### ❌ Bug 1: Operator Precedence Trap (`==` before `&`)
```cpp
n & (1<<i) == 1
```
In C++, equality `==` has **higher precedence** than bitwise AND `&`!
This expression is evaluated by the compiler as:
```cpp
n & ((1 << i) == 1) // BUG!
```
- When $i = 0$: `(1 << 0) == 1` is `true` (1), so `n & 1`.
- When $i = 1$: `(1 << 1) == 1` is `2 == 1` $\implies$ `false` (0), so `n & 0` which is **always 0**!
- The loop terminates prematurely at $i = 1$ regardless of whether bit 1 is set or not!

**Fix:** Wrap the bitwise AND in parentheses:
```cpp
((n & (1 << i)) != 0)
```

---

### ❌ Bug 2: `(n & (1 << i)) == 1` is Only True for $i = 0$
Even if you added parentheses:
```cpp
(n & (1 << i)) == 1 // STILL A BUG for i > 0!
```
- If bit $i$ is set, `n & (1 << i)` evaluates to **`1 << i`** ($2^i$), NOT `1`!
  - If bit 1 is set ($i = 1$), `n & (1 << 1)` returns `2`, not `1`.
  - If bit 2 is set ($i = 2$), `n & (1 << 2)` returns `4`, not `1`.
- Therefore, for any $i > 0$, `(n & (1 << i)) == 1` is **always false**!

**Fix:** Compare against `!= 0`:
```cpp
(n & (1 << i)) != 0
```

---

### ❌ Bug 3: Meaningless & Dangerous Condition `n > i`
```cpp
&& n > i
```
- Comparing the numeric value `n` with the bit index `i` has no mathematical meaning.
- For example, if $n = 7 = 111_2$:
  - $i$ reaches 3. $7 > 3$ is still true.
  - If all bits are set in a large number, $i$ can advance past 31, causing **undefined behavior** (`1 << 32`).
- The condition should bound $i$ within 32 bits: `i < 32`.

---

## 2. The $\mathcal{O}(1)$ Math Trick: `n | (n + 1)` (Zero Loops!)

Instead of looping through bits, you can set the rightmost unset bit in **1 single line**:
$$\mathbf{n \ | \ (n + 1)}$$

### How it Works:
Adding `1` to `n` performs binary addition:
- All trailing `1`s turn into `0`s (propagating the carry).
- The **first (rightmost) `0` bit turns into `1`**.
- All bits higher than this rightmost `0` remain completely unchanged.

Now OR this result with `n`:
- The trailing `1`s from `n` remain `1`.
- The rightmost `0` (which is now `1` in `n + 1`) becomes `1`.
- All other bits remain untouched!

### Example 1: $n = 6$ (`110`)
- $n = 6 = 110_2$
- $n + 1 = 7 = 111_2$
- $n \ | \ (n + 1) = 110_2 \ | \ 111_2 = 111_2 = \mathbf{7}$

### Example 2: $n = 9$ (`1001`)
- $n = 9 = 1001_2$
- $n + 1 = 10 = 1010_2$
- $n \ | \ (n + 1) = 1001_2 \ | \ 1010_2 = 1011_2 = \mathbf{11}$

### Edge Case: All Bits are Set (e.g. $n = 7 = 111_2$)
- If $n = 7$:
  - $n + 1 = 8 = 1000_2$
  - $(n \ \& \ (n + 1)) == 0$ detects that all bits are set!
  - If the problem specifies to return `n` when all bits are already set:
    ```cpp
    if ((n & (n + 1)) == 0) return n;
    return n | (n + 1);
    ```

---

## 3. Complexity Comparison

| Metric | Your Code (Fixed Loop) | The `n \| (n + 1)` Formula ⭐ |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(32) = \mathcal{O}(1)$ | **$\mathcal{O}(1)$ (Single CPU Instruction)** |
| **Space Complexity** | $\mathcal{O}(1)$ | **$\mathcal{O}(1)$** |
| **Loops** | Up to 32 iterations | **0 Loops** |

---

## 4. Code

```cpp
#include <iostream>
#include <bitset>

using namespace std;

class Solution {
public:
    // =========================================================================
    // Optimal 1-Liner: O(1) Time, 0 Loops
    // =========================================================================
    int setRightmostUnsetBitOptimal(int n) {
        // If all bits are already set (e.g. 7 = 111_2, 7 & 8 == 0), return n
        if ((n & (n + 1)) == 0) {
            return n;
        }
        return n | (n + 1);
    }

    // =========================================================================
    // Fixed Loop Approach: Iterative
    // =========================================================================
    int setRightmostUnsetBitLoop(int n) {
        int i = 0;
        // While bit i is SET (1), move to next bit
        while (i < 31 && ((n & (1 << i)) != 0)) {
            i++;
        }
        // If bit i was found within 31 bits, set it
        if (i < 31) {
            n |= (1 << i);
        }
        return n;
    }
};

int main() {
    Solution sol;

    // Test Case 1: n = 6 (110_2) -> Expected 7 (111_2)
    int n1 = 6;
    cout << "n = 6 (" << bitset<8>(n1) << ")\n";
    cout << "Optimal: " << sol.setRightmostUnsetBitOptimal(n1) << " (" << bitset<8>(sol.setRightmostUnsetBitOptimal(n1)) << ")\n";
    cout << "Loop:    " << sol.setRightmostUnsetBitLoop(n1) << "\n\n";

    // Test Case 2: n = 9 (1001_2) -> Expected 11 (1011_2)
    int n2 = 9;
    cout << "n = 9 (" << bitset<8>(n2) << ")\n";
    cout << "Optimal: " << sol.setRightmostUnsetBitOptimal(n2) << " (" << bitset<8>(sol.setRightmostUnsetBitOptimal(n2)) << ")\n";
    cout << "Loop:    " << sol.setRightmostUnsetBitLoop(n2) << "\n\n";

    // Test Case 3: n = 15 (1111_2) -> All bits set
    int n3 = 15;
    cout << "n = 15 (" << bitset<8>(n3) << ")\n";
    cout << "Optimal: " << sol.setRightmostUnsetBitOptimal(n3) << " (All set, returns 15)\n";
    cout << "Loop:    " << sol.setRightmostUnsetBitLoop(n3) << "\n";

    return 0;
}
```
