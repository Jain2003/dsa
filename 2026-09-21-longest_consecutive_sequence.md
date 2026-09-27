# Longest Consecutive Sequence (LeetCode 128)

## Problem Summary
Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence.
You must write an algorithm that runs in $O(n)$ time.

**Example 1:**
- **Input:** `nums = [100,4,200,1,3,2]`
- **Output:** `4`
- **Explanation:** The longest consecutive elements sequence is `[1, 2, 3, 4]`. Its length is 4.

**Example 2:**
- **Input:** `nums = [0,3,7,2,5,8,4,6,0,1]`
- **Output:** `9`

---

## 1. Code Review & Logic Analysis

Your implementation is **correct and optimal**! 

### Why your approach is $O(n)$:
1. You insert all elements into an `unordered_set` $\rightarrow O(n)$ time.
2. The `if (s.find(x - 1) == s.end())` check ensures that you **only start counting from the beginning of a sequence**!
3. Even though there is a `while` loop inside the `for` loop, each number in the array is visited at most twice:
   - Once in the outer loop.
   - Once in the inner `while` loop.
   - Total time: $O(n + n) = O(n)$.

---

## 2. Cleaned-Up & Readable C++ Version

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
#include <algorithm>

using namespace std;

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> numSet(nums.begin(), nums.end());
        int maxLength = 0;

        for (int num : numSet) {
            // Only start counting if 'num' is the START of a sequence (i.e., num - 1 doesn't exist)
            if (numSet.find(num - 1) == numSet.end()) {
                int currentNum = num;
                int currentStreak = 1;

                // Expand forward as long as num + 1 exists
                while (numSet.find(currentNum + 1) != numSet.end()) {
                    currentNum++;
                    currentStreak++;
                }

                maxLength = max(maxLength, currentStreak);
            }
        }

        return maxLength;
    }
};
```

---

## Complexity Analysis

- **Time Complexity:** $O(n)$ — Lookup in `unordered_set` is $O(1)$ on average. Each element is processed at most twice.
- **Space Complexity:** $O(n)$ — To store unique numbers in the hash set.

---

## Code

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
#include <algorithm>

using namespace std;

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        // Fast I/O
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        unordered_set<int> numSet(nums.begin(), nums.end());
        int maxLength = 0;

        for (int num : numSet) {
            // Check if 'num' is the start of a sequence
            if (numSet.find(num - 1) == numSet.end()) {
                int currentNum = num;
                int currentStreak = 1;

                while (numSet.find(currentNum + 1) != numSet.end()) {
                    currentNum++;
                    currentStreak++;
                }

                maxLength = max(maxLength, currentStreak);
            }
        }

        return maxLength;
    }
};

int main() {
    Solution sol;
    vector<int> nums1 = {100, 4, 200, 1, 3, 2};
    cout << "Test 1: " << sol.longestConsecutive(nums1) << " (Expected: 4)" << endl;

    vector<int> nums2 = {0, 3, 7, 2, 5, 8, 4, 6, 0, 1};
    cout << "Test 2: " << sol.longestConsecutive(nums2) << " (Expected: 9)" << endl;

    vector<int> nums3 = {};
    cout << "Test 3: " << sol.longestConsecutive(nums3) << " (Expected: 0)" << endl;

    return 0;
}
```
