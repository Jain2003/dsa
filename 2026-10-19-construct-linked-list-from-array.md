# Construct a Linked List from an Array

## Problem Description
Given an array `arr` of integers, construct a singly linked list containing the elements of `arr` in the same order and return the `head` of the list.

**Example 1:**
- **Input:** `arr = [1, 2, 3, 4, 5]`
- **Output:** `1 -> 2 -> 3 -> 4 -> 5 -> nullptr`

**Example 2:**
- **Input:** `arr = [42]`
- **Output:** `42 -> nullptr`

**Example 3:**
- **Input:** `arr = []`
- **Output:** `nullptr`

---

## 1. Key Intuition & Patterns

There are two clean ways to build a linked list from an array:

### Method 1: The `head` + `tail` Pointer Pattern (Direct Construction)
1. **Edge Case:** If `arr.empty()`, return `nullptr`.
2. Create the first node: `head = new ListNode(arr[0])`.
3. Keep a moving pointer `tail = head`.
4. Loop through the rest of the array (`i = 1` to `n - 1`):
   - Allocate: `tail->next = new ListNode(arr[i])`.
   - Advance: `tail = tail->next`.
5. Return `head`.

### Method 2: The Dummy Node Pattern (Cleanest — Handles Empty Arrays Automatically)
- Initialize a temporary stack-allocated node: `ListNode dummy(0)`.
- Keep `tail = &dummy`.
- Loop through all elements in `arr`:
  - `tail->next = new ListNode(val)`.
  - `tail = tail->next`.
- Return `dummy.next`! (If `arr` is empty, `dummy.next` is naturally `nullptr`).

---

## 2. Visual Trace for `arr = [10, 20, 30]`

```
Step 0: Initialize head with arr[0] = 10
head, tail
    v
   [10] ---> nullptr

Step 1: Loop i = 1 (arr[1] = 20)
- tail->next = new ListNode(20)
- tail = tail->next
head         tail
  v           v
 [10] ------> [20] ---> nullptr

Step 2: Loop i = 2 (arr[2] = 30)
- tail->next = new ListNode(30)
- tail = tail->next
head                     tail
  v                       v
 [10] ------> [20] ------> [30] ---> nullptr

Return head ([10]).
```

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(N)$ — Single pass through the array of length $N$.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (allocates $\mathcal{O}(N)$ nodes for the returned list).

---

## 4. Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

class Solution {
public:
    // Method 1: Classic Head + Tail (Standard)
    ListNode* constructLL(const vector<int>& arr) {
        if (arr.empty()) return nullptr;

        ListNode* head = new ListNode(arr[0]);
        ListNode* tail = head;

        for (size_t i = 1; i < arr.size(); i++) {
            tail->next = new ListNode(arr[i]);
            tail = tail->next;
        }

        return head;
    }

    // Method 2: Dummy Node (Very clean, no special-casing arr.empty())
    ListNode* constructLLWithDummy(const vector<int>& arr) {
        ListNode dummy(0);
        ListNode* tail = &dummy;

        for (int x : arr) {
            tail->next = new ListNode(x);
            tail = tail->next;
        }

        return dummy.next;
    }
};

// Helper function to print a linked list
void printList(ListNode* head) {
    ListNode* curr = head;
    while (curr != nullptr) {
        cout << curr->val;
        if (curr->next != nullptr) cout << " -> ";
        curr = curr->next;
    }
    cout << "\n";
}

int main() {
    Solution sol;

    // Test Case 1: Multiple elements
    vector<int> arr1 = {1, 2, 3, 4, 5};
    ListNode* head1 = sol.constructLL(arr1);
    cout << "Test 1: ";
    printList(head1);

    // Test Case 2: Using Dummy Node method
    vector<int> arr2 = {10, 20, 30};
    ListNode* head2 = sol.constructLLWithDummy(arr2);
    cout << "Test 2: ";
    printList(head2);

    // Test Case 3: Empty array
    vector<int> arr3 = {};
    ListNode* head3 = sol.constructLL(arr3);
    cout << "Test 3 (Empty): ";
    printList(head3);

    return 0;
}
```
