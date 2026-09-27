# Reverse Integer (Handling 32-Bit Signed Integer Overflow)

## Problem Summary
Given a signed 32-bit integer `x`, return `x` with its digits reversed. If reversing `x` causes the value to go outside the 32-bit signed integer range \([-2^{31}, 2^{31} - 1]\), return `0`.

---

## Key Understandings & Pitfalls

1. **Handling Negative Numbers (`x != 0` vs `x > 0`):**
   - In C++, `-123 % 10` is `-3`.
   - Using `while (x != 0)` ensures the loop processes negative numbers correctly down to `0`.

2. **The Overflow Trap (`rev * 10 > INT_MAX` is WRONG):**
   - You **cannot** check overflow using `if (rev * 10 > INT_MAX)`.
   - Computing `rev * 10` when `rev` is large (e.g., `964632435`) performs the multiplication **before** comparing, causing **Signed Integer Overflow (Undefined Behavior)**.

3. **Preventing Overflow BEFORE Multiplication:**
   - Instead, divide the boundary by 10:
     - `if (rev > INT_MAX / 10 || rev < INT_MIN / 10) return 0;`
   - Alternatively, store `rev` in a `long long` (64-bit integer) during calculation and check `if (rev > INT_MAX || rev < INT_MIN)` before casting back to `int`.

4. **Fast I/O Optimization (`ios_base::sync_with_stdio(false); cin.tie(NULL);`):**
   - Unties C++ streams from C stdio to speed up I/O execution on platforms like LeetCode. Does not change theoretical time complexity.

---

## Complexity Analysis

- **Time Complexity:** \(O(\log_{10}|x|)\) â€” Maximum of **10 iterations** (since 32-bit integers have at most 10 digits).
- **Space Complexity:** \(O(1)\) auxiliary space.

## Code

```cpp
#include <iostream>
#include <climits>

using namespace std;

class Solution {
public:
    int reverse(int x) {
        // Fast I/O optimization for online judges
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int rev = 0;
        
        while (x != 0) {
            int ld = x % 10;
            
            // Check for potential 32-bit overflow BEFORE multiplying by 10
            if (rev > INT_MAX / 10 || rev < INT_MIN / 10) {
                return 0; // Return 0 if reversing causes signed 32-bit int overflow
            }
            
            rev = rev * 10 + ld;
            x /= 10;
        }
        
        return rev;
    }
};

int main() {
    Solution obj;
    cout << "Reverse of 123: " << obj.reverse(123) << endl;     // 321
    cout << "Reverse of -123: " << obj.reverse(-123) << endl;   // -321
    cout << "Reverse of 120: " << obj.reverse(120) << endl;     // 21
    cout << "Reverse of 1534236469: " << obj.reverse(1534236469) << endl; // 0 (Overflows!)
    return 0;
}
```
