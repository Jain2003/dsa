# Valid Palindrome (LeetCode 125)

## Problem Summary
Given a string `s`, return `true` if it is a palindrome, considering only alphanumeric characters (letters and digits) and ignoring cases.

---

## 1. Approach 1: Filter & Reverse String ($O(N)$ Time, $O(N)$ Space)

### Intuition:
1. Iterate through `s` and extract only alphanumeric characters (`isalnum`) converted to lowercase (`tolower`) into a new string `str`.
2. Make a copy `str2 = str` and reverse `str2`.
3. Compare `str == str2`.

### Code:
```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        string str = "";
        for (int i = 0; i < s.size(); i++) {
            if (isalnum(s[i]))
                str += tolower(s[i]);
        }
        string str2 = str;
        reverse(str2.begin(), str2.end());
        return (str2 == str);
    }
};
```
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$ auxiliary space for `str` and `str2`.

---

## 2. Approach 2: Two-Pointer In-Place Scan ($O(N)$ Time, $O(1)$ Space) — Optimal

### Intuition:
Scan the string from both ends simultaneously (`left = 0`, `right = n - 1`):
1. Skip non-alphanumeric characters from the left: `while (left < right && !isalnum(s[left])) left++;`
2. Skip non-alphanumeric characters from the right: `while (left < right && !isalnum(s[right])) right--;`
3. Compare normalized characters: `if (tolower(s[left]) != tolower(s[right])) return false;`
4. Increment `left++` and decrement `right--`.

### Code:
```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int left = 0;
        int right = s.length() - 1;

        while (left < right) {
            // 1. Skip non-alphanumeric characters from left
            while (left < right && !isalnum(s[left])) left++;

            // 2. Skip non-alphanumeric characters from right
            while (left < right && !isalnum(s[right])) right--;

            // 3. Compare normalized characters
            if (tolower(s[left]) != tolower(s[right])) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }
};
```
- **Time Complexity:** $O(N)$ — Each character is visited at most once.
- **Space Complexity:** $O(1)$ — No extra string allocations required.

---

## Code

```cpp
#include <iostream>
#include <string>
#include <cctype>    // required for isalnum() and tolower()
#include <algorithm> // required for reverse()

using namespace std;

// Approach 1: Filter & Reverse
class SolutionFilter {
public:
    bool isPalindrome(string s) {
        string str = "";
        for (int i = 0; i < s.size(); i++) {
            if (isalnum(s[i]))
                str += tolower(s[i]);
        }
        string str2 = str;
        reverse(str2.begin(), str2.end());
        return (str2 == str);
    }
};

// Approach 2: Two-Pointer (Optimal O(1) Space)
class SolutionTwoPointer {
public:
    bool isPalindrome(string s) {
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        int left = 0;
        int right = s.length() - 1;

        while (left < right) {
            while (left < right && !isalnum(s[left])) left++;
            while (left < right && !isalnum(s[right])) right--;

            if (tolower(s[left]) != tolower(s[right])) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }
};

int main() {
    SolutionTwoPointer sol;
    cout << boolalpha;
    cout << "Test 1 (\"A man, a plan, a canal: Panama\"): " 
         << sol.isPalindrome("A man, a plan, a canal: Panama") << endl; // true
    cout << "Test 2 (\"race a car\"): " 
         << sol.isPalindrome("race a car") << endl;                     // false
    cout << "Test 3 (\" \"): " 
         << sol.isPalindrome(" ") << endl;                              // true
    return 0;
}
```
