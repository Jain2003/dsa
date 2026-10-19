# Insert at Head of Linked List

## Problem Description
Given the head of a singly linked list and an integer `X`, insert a new node with value `X` at the **beginning (head)** of the linked list and return the new head.

**Example 1:**
- **Input:** `head = [1, 2, 3]`, `X = 0`
- **Output:** `[0, 1, 2, 3]`

**Example 2:**
- **Input:** `head = []`, `X = 5`
- **Output:** `[5]`

---

## 1. Key Intuition: $\mathcal{O}(1)$ Prepending

Inserting at the head of a singly linked list is an $\mathcal{O}(1)$ operation because:
1. We instantiate a new node `newHead` with value `X`.
2. We point `newHead->next = head`.
3. If `head` is passed by reference (`ListNode* &head`), we update `head = newHead`.
4. Return `head` (or `newHead`).

### Visual Representation:
```
Given list: [1] ---> [2] ---> [3]
New value: X = 0

Step 1 & 2: newHead = new ListNode(0, head)
[0] ----------+
 |            |
 v            v
[1] ---> [2] ---> [3]

Step 3: Update head = newHead
head
 v
[0] ---> [1] ---> [2] ---> [3]
```

### Important C++ Observation on Reference Parameter:
In the function signature:
```cpp
ListNode* insertAtHead(ListNode* &head, int X)
```
- Since `head` is passed **by reference** (`ListNode* &head`), updating `head = newHead;` directly mutates the caller's head variable.
- Doing `head = new ListNode(X, head); return head;` achieves the creation, linking, reference update, and return in a single line!

---

## 2. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(1)$ — Single node allocation and pointer assignment.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (allocates $\mathcal{O}(1)$ heap node).

---

## 3. Code

```cpp
#include <iostream>

using namespace std;

// Definition of singly linked list:
class ListNode {
public:
    int data;
    ListNode *next;
    ListNode() : data(0), next(nullptr) {}
    ListNode(int x) : data(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : data(x), next(next) {}
};

class Solution {
public:
    ListNode* insertAtHead(ListNode* &head, int X) {
        // Option 1: Create, link, and update reference
        head = new ListNode(X, head);
        return head;
    }
};

// Helper to print linked list
void printList(ListNode* head) {
    ListNode* curr = head;
    while (curr != nullptr) {
        cout << curr->data;
        if (curr->next != nullptr) cout << " -> ";
        curr = curr->next;
    }
    cout << "\n";
}

int main() {
    Solution sol;

    // Test Case 1: Insert into non-empty list
    ListNode* head = new ListNode(2, new ListNode(3));
    cout << "Original List: ";
    printList(head);

    head = sol.insertAtHead(head, 1);
    cout << "After inserting 1 at head: ";
    printList(head);

    // Test Case 2: Insert into empty list
    ListNode* emptyHead = nullptr;
    emptyHead = sol.insertAtHead(emptyHead, 99);
    cout << "\nEmpty List after inserting 99: ";
    printList(emptyHead);

    return 0;
}
```
