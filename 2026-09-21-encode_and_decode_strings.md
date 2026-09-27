# Encode and Decode Strings (LeetCode 271 / NeetCode)

## Problem Summary
Design an algorithm to encode a list of strings into a single string, and decode that string back to the original list of strings.

---

## 1. Why Space-Separated / `stringstream` Fails

In your code:
```cpp
string encode(vector<string>& strs) {
    string encoded_string;
    for(int i = 0; i < strs.size(); i++) {
        encoded_string += strs[i] + " ";
    }
    return encoded_string;
}

vector<string> decode(string s) {
    stringstream ss(s);
    vector<string> strs;
    string str;
    while(ss >> str) strs.push_back(str);
    return strs;
}
```

### Why it fails for `strs = ["", ""]`:
1. **In `encode`: `strs = ["", ""]`**
   - For `""`: appends `" "` (1 space).
   - For `""`: appends `" "` (1 space).
   - `encoded_string` becomes `"  "` (two spaces).
2. **In `decode`: `s = "  "`**
   - `stringstream` (`ss >> str`) automatically **skips all spaces** looking for words.
   - Since `"  "` contains only spaces, `ss >> str` finds **zero words** and terminates immediately.
   - It returns an empty vector `[]` instead of `["", ""]`!

### Other Cases Where Delimiters / `stringstream` Fail:
- `strs = ["hello world"]` $\rightarrow$ Splits into two words `["hello", "world"]`.
- `strs = ["#", "##"]` $\rightarrow$ Simple character delimiters get confused with delimiter characters inside words.

---

## 2. The Standard Solution: Length-Prefix Encoding (`length#string`)

To support **any possible character** (spaces, empty strings, `#`, symbols, etc.), prefix every string with its **`length` + `#`**.

### Pattern: `length#string`

#### Example 1: `strs = ["", ""]`
- `strs[0] = ""` $\rightarrow$ Length = `0` $\rightarrow$ `"0#"`
- `strs[1] = ""` $\rightarrow$ Length = `0` $\rightarrow$ `"0#"`
- **Encoded:** `"0#0#"`
- **Decoding `"0#0#"`:**
  1. Finds `#` at index 1 $\rightarrow$ `length = 0`. Reads 0 chars after `#` $\rightarrow$ `""`.
  2. Finds `#` at index 3 $\rightarrow$ `length = 0`. Reads 0 chars after `#` $\rightarrow$ `""`.
  3. Returns `["", ""]` (Correct!).

#### Example 2: `strs = ["hello world", "c++#magic"]`
- Encoded: `"11#hello world9#c++#magic"`
- Decoding reads exactly 11 chars for `"hello world"` regardless of spaces inside it, and reads 9 chars for `"c++#magic"` regardless of `#` inside it!

---

## 3. Complexity Analysis

- **Time Complexity:** 
  - `encode`: $O(N \cdot K)$ where $N$ is number of strings, $K$ is max string length.
  - `decode`: $O(N \cdot K)$ linear single-pass scanning.
- **Space Complexity:** $O(N \cdot K)$ auxiliary space for encoded string / decoded result.

## Code

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution {
public:
    // Encodes a list of strings to a single string.
    // Format: length + "#" + string
    string encode(vector<string>& strs) {
        // Fast I/O for LeetCode
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        string encoded = "";
        for (const string& s : strs) {
            encoded += to_string(s.length()) + "#" + s;
        }
        return encoded;
    }

    // Decodes a single string to a list of strings.
    vector<string> decode(string s) {
        vector<string> result;
        int i = 0;
        int n = s.length();

        while (i < n) {
            // Find delimiter '#'
            int j = i;
            while (s[j] != '#') {
                j++;
            }

            // Extract string length before '#'
            int length = stoi(s.substr(i, j - i));

            // Extract actual string of size 'length' after '#'
            string word = s.substr(j + 1, length);
            result.push_back(word);

            // Move index past the current word
            i = j + 1 + length;
        }

        return result;
    }
};

int main() {
    Solution sol;

    // Test Case 1: Empty strings ["", ""]
    vector<string> strs1 = {"", ""};
    string encoded1 = sol.encode(strs1);
    cout << "Encoded 1: \"" << encoded1 << "\"" << endl; // Output: "0#0#"
    vector<string> decoded1 = sol.decode(encoded1);
    cout << "Decoded 1 size: " << decoded1.size() << endl; // Output: 2

    // Test Case 2: Strings with spaces and special characters
    vector<string> strs2 = {"hello world", "c++#magic", ""};
    string encoded2 = sol.encode(strs2);
    cout << "Encoded 2: \"" << encoded2 << "\"" << endl; // Output: "11#hello world9#c++#magic0#"
    vector<string> decoded2 = sol.decode(encoded2);
    for (const string& w : decoded2) {
        cout << "[" << w << "] ";
    }
    cout << endl;

    return 0;
}
```
