# Odd Even Linked List (LeetCode 328)

## Problem Description
Given the head of a singly linked list, group all the nodes with **odd indices** together followed by the nodes with **even indices**, and return the reordered list.

The **first** node is considered odd, and the **second** node is even, and so on.

Note that the relative order inside both the even and odd groups should remain as it was in the input.

You must solve the problem in $\mathcal{O}(1)$ extra space complexity and $\mathcal{O}(n)$ time complexity.

**Example 1:**
- **Input:** `head = [1, 2, 3, 4, 5]`
- **Output:** `[1, 3, 5, 2, 4]`

**Example 2:**
- **Input:** `head = [2, 1, 3, 5, 6, 4, 7]`
- **Output:** `[2, 3, 6, 7, 1, 5, 4]`

---

## 1. Bugs in Your Initial Code

### Bug 1: Premature Pointer Destruction
Look at these consecutive lines in your loop:
```cpp
if (odd->next) {
    odd->next = odd->next->next; // 1. Changes odd->next!
}
if (even->next) {
    even->next = even->next->next;
}
```
- Initially: `odd` is `1` (pointing to `2`), `even` is `2` (pointing to `3`).
- When you do `odd->next = odd->next->next`, `odd->next` now skips `2` and points directly to `3`!
- But `even`'s next is supposed to point to `4` (which was `odd->next->next` of the *new* odd).
- By modifying pointers independently in parallel without proper linking order, nodes get disconnected and cause **cycle loops / segmentation faults**!

### Bug 2: Null Pointer Dereference in `even = even->next`
- When `even->next` becomes `nullptr` at the end of the list, doing `even = even->next` sets `even = nullptr`.
- On the next check: `while (odd->next || even->next)`, checking `even->next` dereferences `nullptr` $\longrightarrow$ **Crash / Segfault**!

---

## 2. Key Intuition: Two-Pointer Link Stitching

We maintain two pointers:
- `odd` starting at `head` (index 1)
- `even` starting at `head->next` (index 2)
- `evenHead = even` (preserved so we can connect the end of the odd list to the start of the even list at the end!)

### The Stride Rule:
Notice that `odd` always connects to `even->next`, and `even` always connects to the *updated* `odd->next`:

```cpp
odd->next = even->next; // 1 connects to 3
odd = odd->next;         // odd advances to 3

even->next = odd->next; // 2 connects to 4
even = even->next;       // even advances to 4
```

### Clean Loop Condition:
Since `even` is always ahead of `odd`, we only need to check:
```cpp
while (even != nullptr && even->next != nullptr)
```
When this loop terminates, `even` has reached the end, and `odd` is guaranteed to be sitting at the **last odd node**!

Then we just connect:
```cpp
odd->next = evenHead;
```

---

## 3. Visual Trace (`[1, 2, 3, 4, 5]`)

```
Initial:
odd   even
 v     v
[1] -> [2] -> [3] -> [4] -> [5] -> nullptr
evenHead = [2]

Iteration 1:
- odd->next = even->next (1 points to 3)
- odd = odd->next        (odd moves to 3)
- even->next = odd->next (2 points to 4)
- even = even->next      (even moves to 4)

[1] --------> [3] -> [4] -> [5]
       [2] --------> [4]
               ^      ^
              odd    even

Iteration 2:
- odd->next = even->next (3 points to 5)
- odd = odd->next        (odd moves to 5)
- even->next = odd->next (4 points to nullptr)
- even = even->next      (even moves to nullptr)

[1] -> [3] -> [5]
[2] -> [4] -> nullptr

Loop terminates (even == nullptr).
Final connection:
odd->next = evenHead (5 points to 2)

Result:
[1] -> [3] -> [5] -> [2] -> [4] -> nullptr ✅
```

---

## 4. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(N)$ — Every node is visited exactly once.
- **Space Complexity:** $\mathcal{O}(1)$ — In-place pointer manipulation without allocating new nodes.

---

## 5. Code

```cpp
#include <iostream>

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
    ListNode* oddEvenList(ListNode* head) {
        // Edge cases: 0, 1, or 2 nodes (already ordered)
        if (head == nullptr || head->next == nullptr) {
            return head;
        }

        ListNode* odd = head;
        ListNode* even = head->next;
        ListNode* evenHead = even; // Save start of even list

        // Since 'even' is always ahead of 'odd', check even and even->next
        while (even != nullptr && even->next != nullptr) {
            odd->next = even->next;
            odd = odd->next;

            even->next = odd->next;
            even = even->next;
        }

        // Connect tail of odd list to head of even list
        odd->next = evenHead;

        return head;
    }
};

// Helper function to print linked list
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

    // Test Case 1: [1, 2, 3, 4, 5] -> Expected: [1, 3, 5, 2, 4]
    ListNode* head1 = new ListNode(1, new ListNode(2, new ListNode(3, new ListNode(4, new ListNode(5)))));
    cout << "Original 1: ";
    printList(head1);
    head1 = sol.oddEvenList(head1);
    cout << "Odd-Even 1: ";
    printList(head1);

    // Test Case 2: [2, 1, 3, 5, 6, 4, 7] -> Expected: [2, 3, 6, 7, 1, 5, 4]
    ListNode* head2 = new ListNode(2, new ListNode(1, new ListNode(3, new ListNode(5, new ListNode(6, new ListNode(4, new ListNode(7)))))));
    cout << "\nOriginal 2: ";
    printList(head2);
    head2 = sol.oddEvenList(head2);
    cout << "Odd-Even 2: ";
    printList(head2);

    return 0;
}
```
