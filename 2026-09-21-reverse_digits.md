# Reverse Digits of a Number

## Problem Summary
Given an integer `n`, extract its digits one by one from right to left and build its reverse.

---

## Key Understandings & Intuition

1. **Extracting Last Digit (`n % 10`):**
   - The modulo operation `% 10` gives the rightmost (last) digit of a number.
   - Example: `12345 % 10 = 5`.

2. **Building the Reversed Number (`rev = rev * 10 + lastDigit`):**
   - Multiplying the existing `rev` by `10` shifts its digits one position to the left (making space for the next digit).
   - Adding `lastDigit` places the extracted digit into the units position.

3. **Removing Last Digit (`n = n / 10`):**
   - Integer division `/ 10` discards the last digit.
   - Example: `12345 / 10 = 1234`.

---

## Complexity Analysis

- **Time Complexity:** \(O(\log_{10}(n))\) or \(O(d)\), where \(d\) is the number of digits in \(n\).
  - *Why?* In each step, `n` is divided by 10, so the loop executes once per digit.
- **Space Complexity:** \(O(1)\) auxiliary space.
  - *Why?* Uses only a fixed number of integer variables (`revNum`, `lastDigit`).

## Code

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    // Function to reverse digits of a positive number
    int reverseNumber(int n) {
        int revNum = 0;

        // Loop until all digits are processed
        while (n > 0) {
            int lastDigit = n % 10;          // Extract the last digit
            revNum = revNum * 10 + lastDigit; // Append to reversed number
            n = n / 10;                      // Remove last digit from n
        }

        return revNum;
    }
};

// Driver code
int main() {
    Solution obj;
    int num = 12345;
    cout << "Original: " << num << endl;
    cout << "Reversed: " << obj.reverseNumber(num) << endl; // Output: 54321
    return 0;
}
```
