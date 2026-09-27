# Palindrome Number (Half-Reversal Optimization)

## Problem Summary
Given an integer `x`, return `true` if `x` is a palindrome, and `false` otherwise.

---

## Key Understandings & Optimizations

1. **Negative Numbers are NEVER Palindromes:**
   - Reading `-121` backward gives `121-`, which is not equal.
   - Any number ending in `0` (except `0` itself) cannot be a palindrome because no positive number starts with `0`.
   - Condition: `if (x < 0 || (x % 10 == 0 && x != 0)) return false;`

2. **The 50% Speedup (Reversing Only HALF the Digits):**
   - Reversing the whole number takes extra loop cycles and requires integer overflow handling.
   - Reversing only half until `x <= rev` cuts iterations by 50% and **completely eliminates overflow risks** (half of a 32-bit int can never overflow `INT_MAX`).

3. **Handling Even vs. Odd Digit Lengths:**
   - **Even length (e.g., `1221`):** Loop stops when `x = 12` and `rev = 12`. Result: `x == rev`.
   - **Odd length (e.g., `12321`):** Loop stops when `x = 12` and `rev = 123`. The middle digit `3` doesn't affect palindrome symmetry, so we compare `x == rev / 10` (`12 == 123 / 10`).

4. **No Debug Output in Production Code:**
   - Removing `cout` statements / debugging prints drastically improves runtime on online judges.

---

## Complexity Analysis

- **Time Complexity:** \(O(\log_{10}(n))\) â€” Takes half the digit count iterations (at most 5 steps for 32-bit int).
- **Space Complexity:** \(O(1)\) auxiliary space.

## Code

```cpp
#include <iostream>

using namespace std;

class Solution {
public:
    bool isPalindrome(int x) {
        // Fast I/O for LeetCode
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        // Negative numbers are not palindromes (e.g. -121 -> 121-).
        // Also, if the last digit is 0, the first digit must be 0 (only '0' itself satisfies this).
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }

        int rev = 0;
        // Reverse only half of the number
        while (x > rev) {
            rev = rev * 10 + x % 10;
            x /= 10;
        }

        // For even-length numbers: x == rev (e.g., 1221 -> x = 12, rev = 12)
        // For odd-length numbers: x == rev / 10 (e.g., 12321 -> x = 12, rev = 123 -> discard middle digit '3')
        return x == rev || x == rev / 10;
    }
};

int main() {
    Solution obj;
    cout << boolalpha;
    cout << "121 is palindrome? " << obj.isPalindrome(121) << endl;     // true
    cout << "-121 is palindrome? " << obj.isPalindrome(-121) << endl;   // false
    cout << "10 is palindrome? " << obj.isPalindrome(10) << endl;       // false
    cout << "0 is palindrome? " << obj.isPalindrome(0) << endl;         // true
    return 0;
}
```
