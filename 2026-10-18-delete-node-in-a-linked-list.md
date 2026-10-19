# Delete Node in a Linked List (LeetCode 237)

## Problem Description
There is a singly-linked list `head` and we want to delete a node in it.

You are given the node to be deleted `node`. You will **not** be given access to the first node of `head`.

All the values of the linked list are unique, and it is guaranteed that the given node is **not the last node** in the linked list.

Delete the given node without memory removal requirements:
- The value of the given node should not exist in the linked list.
- The number of nodes should decrease by one.
- All values before and after `node` must preserve their order.

**Example 1:**
- **Input:** `head = [4, 5, 1, 9]`, `node = 5`
- **Output:** `[4, 1, 9]`

**Example 2:**
- **Input:** `head = [4, 5, 1, 9]`, `node = 1`
- **Output:** `[4, 5, 9]`

---

## 1. Key Intuition: "Identity Theft" (Copy Value & Bypass)

Normally, deleting a node requires finding its predecessor (`prev`) so that:
$$\text{prev}\to\text{next} = \text{node}\to\text{next}$$

However, since we are only given a pointer to `node` itself, we **cannot look backwards** in a singly linked list.

### The Clever Trick:
1. **Copy Next Node's Value:**
   Copy `node->next->val` into `node->val`. Now `node` looks identical to `node->next`.
2. **Bypass the Next Node:**
   Point `node->next` to `node->next->next`.
3. The original node's value is overwritten, and the redundant successor is excised from the chain!

### Visual Diagram:
```
Target to delete: [5]

Initial:
[4] ---> [5] ---> [1] ---> [9]
          ^
         node

Step 1: node->val = node->next->val (copy 1 into 5)
[4] ---> [1] ---> [1] ---> [9]
          ^
         node

Step 2: node->next = node->next->next (bypass second 1)
[4] ---> [1] -------------> [9]
          ^
         node
```

*(Note: In production C++, we typically free `node->next` memory using `delete` to prevent memory leaks, though LeetCode does not require it).*

---

## 2. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(1)$ — Exact two pointer/value operations.
- **Space Complexity:** $\mathcal{O}(1)$ — No extra memory allocated.

---

## 3. Code

```cpp
#include <iostream>

using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    void deleteNode(ListNode* node) {
        // Copy the value of the next node into current node
        node->val = node->next->val;

        // Optionally store pointer to free memory in C++
        ListNode* temp = node->next;

        // Bypass the next node
        node->next = node->next->next;

        // Free memory of the excised node
        delete temp;
    }
};

// Helper to print linked list
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
    // Build list: 4 -> 5 -> 1 -> 9
    ListNode* head = new ListNode(4);
    head->next = new ListNode(5);
    head->next->next = new ListNode(1);
    head->next->next->next = new ListNode(9);

    cout << "Original List: ";
    printList(head);

    // Node to delete: 5
    ListNode* target = head->next;

    Solution sol;
    sol.deleteNode(target);

    cout << "After Deleting 5: ";
    printList(head);

    return 0;
}
```
