# Delete Tail of a Linked List

## Problem Description
Given the head of a singly linked list, delete the **last node** (the tail) of the linked list and return the head of the modified list.

**Example 1:**
- **Input:** `head = [1, 2, 3, 4, 5]`
- **Output:** `[1, 2, 3, 4]`

**Example 2:**
- **Input:** `head = [7]`
- **Output:** `[]`

**Example 3:**
- **Input:** `head = []`
- **Output:** `[]`

---

## 1. Bugs & Edge Cases in Your Initial Code

### Bug 1: Null Pointer Dereference on Empty List (`head == nullptr`)
```cpp
ListNode *tmp = head;
if (!tmp->next) { ... } // CRASH if tmp == nullptr!
```
- If the list is empty (`head == nullptr`), calling `tmp->next` attempts to access memory through a null pointer.
- This results in an immediate **Segmentation Fault / Crash**!
- **Fix:** Guard with `if (head == nullptr || head->next == nullptr)` upfront.

### Bug 2: Memory Leak (C++ Best Practice)
- Disconnecting the node via `tmp->next = nullptr;` drops the pointer to the tail node, but **leaves the node allocated in memory**.
- In C++, you should free the excised node using `delete`:
  ```cpp
  delete tmp->next;
  tmp->next = nullptr;
  ```

---

## 2. Key Intuition: Finding the Second-to-Last Node

To delete the tail node, we need to stop our traversal at the **second-to-last node** (`node` whose `next->next == nullptr`).

```
Initial:
[1] ---> [2] ---> [3] ---> [4] ---> [5] ---> nullptr
                            ^
                           tmp (tmp->next->next == nullptr)

Step 1: delete tmp->next (free memory of [5])
Step 2: tmp->next = nullptr
Result:
[1] ---> [2] ---> [3] ---> [4] ---> nullptr
```

### Edge Cases Handled:
1. **`head == nullptr` (0 nodes):** Return `nullptr`.
2. **`head->next == nullptr` (1 node):** Delete `head`, set `head = nullptr`, return `nullptr`.
3. **$\ge 2$ nodes:** Traverse to second-to-last node, delete `tmp->next`, set `tmp->next = nullptr`.

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(N)$ — We traverse $N - 1$ nodes to reach the second-to-last node.
- **Space Complexity:** $\mathcal{O}(1)$ — Only a single pointer `tmp` is used.

---

## 4. Code

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
    ListNode* deleteTail(ListNode* &head) {
        // Edge Case 1 & 2: Empty list or single node
        if (head == nullptr) {
            return nullptr;
        }
        if (head->next == nullptr) {
            delete head;
            head = nullptr;
            return nullptr;
        }

        // Traverse to the second-to-last node
        ListNode *tmp = head;
        while (tmp->next->next != nullptr) {
            tmp = tmp->next;
        }

        // Free the tail node memory and sever the link
        delete tmp->next;
        tmp->next = nullptr;

        return head;
    }
};

// Helper to print list
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

    // Test Case 1: Multiple nodes [1, 2, 3, 4, 5]
    ListNode* head1 = new ListNode(1, new ListNode(2, new ListNode(3, new ListNode(4, new ListNode(5)))));
    cout << "Test 1 Original: ";
    printList(head1);
    head1 = sol.deleteTail(head1);
    cout << "Test 1 After:    ";
    printList(head1);

    // Test Case 2: Single node [7]
    ListNode* head2 = new ListNode(7);
    cout << "\nTest 2 Original: ";
    printList(head2);
    head2 = sol.deleteTail(head2);
    cout << "Test 2 After:    ";
    printList(head2);

    return 0;
}
```
