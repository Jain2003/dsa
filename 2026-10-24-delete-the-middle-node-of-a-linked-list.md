# Delete the Middle Node of a Linked List (LeetCode 2095)

## Problem Description
You are given the `head` of a linked list. Delete the **middle node**, and return the `head` of the modified linked list.

The middle node of a linked list of size $n$ is the $\lfloor n / 2 \rfloor^{\text{th}}$ node from the start (0-indexed).
- For $n = 1$, node $0$ is deleted (returns `NULL`).
- For $n = 2$, node $1$ is deleted.
- For $n = 3$, node $1$ is deleted.
- For $n = 4$, node $2$ is deleted.

**Example 1:**
- **Input:** `head = [1, 3, 4, 7, 1, 2, 6]` ($n = 7$)
- **Output:** `[1, 3, 4, 1, 2, 6]`
- **Explanation:** $\lfloor 7 / 2 \rfloor = 3$. Node with value `7` (index 3) is deleted.

**Example 2:**
- **Input:** `head = [1, 2, 3, 4]` ($n = 4$)
- **Output:** `[1, 2, 4]`
- **Explanation:** $\lfloor 4 / 2 \rfloor = 2$. Node with value `3` (index 2) is deleted.

**Example 3:**
- **Input:** `head = [2, 1]` ($n = 2$)
- **Output:** `[2]`
- **Explanation:** $\lfloor 2 / 2 \rfloor = 1$. Node with value `1` (index 1) is deleted.

---

## 1. Analysis of Your Code & Why You Needed the Hardcoded Check

### Your Code
```cpp
ListNode* dummy = new ListNode(-1, head);
ListNode* tmp = dummy;
ListNode* slow = head;
ListNode* fast = head;

if (slow->next == NULL) {
    return NULL; // The hardcoded condition
}

while (fast != nullptr && fast->next != nullptr) {
    tmp = tmp->next;
    slow = slow->next;
    fast = fast->next->next;
}
tmp->next = slow->next;
return head;
```

### Why You Were Forced to Add `if (slow->next == NULL) return NULL;`
1. **Returning `head` instead of `dummy->next`:**
   In your code, you return `head`. For a single node `[1]`, deleting the node means the resulting list is empty (`nullptr`). But `head` is still pointing to `[1]`!
   If you had returned `dummy->next`, `dummy->next` would correctly reflect `nullptr` once unlinked.
2. **Three Pointers Overkill:**
   You used `tmp` (previous pointer), `slow`, and `fast`. You don't need three moving pointers!

---

## 2. Better Techniques: Eliminating Hardcoding

### Technique 1: The Dummy-Start Offset (100% Zero Hardcoding ⭐)
To delete a node, we don't want `slow` to land **on** the middle node; we want `slow` to land **strictly one node before** the middle node!

**The Trick:**
Start `slow` at `dummy`, while `fast` starts at `head`!
- Since `slow` starts **1 step behind**, when `fast` finishes, `slow` naturally stops **1 node before the middle**!
- Only **two pointers** needed (`slow` and `fast`).
- Automatically handles $n = 1$ without any `if` statements!

```
Example: [1] (n = 1)
[dummy] -> [1] -> NULL
   ^        ^
  slow     fast

fast->next is NULL, so the while loop NEVER runs!
slow is at [dummy].
slow->next = slow->next->next (dummy points to NULL!).
return dummy.next; // Returns NULL!
```

```
Example: [1, 2, 3, 4] (n = 4)
Initial:
[dummy] -> [1] -> [2] -> [3] -> [4] -> NULL
   ^        ^
  slow     fast

Step 1:
[dummy] -> [1] -> [2] -> [3] -> [4] -> NULL
            ^             ^
           slow          fast

Step 2:
[dummy] -> [1] -> [2] -> [3] -> [4] -> NULL
                   ^                    ^
                  slow                 fast (NULL!)

Loop finishes!
slow is at [2] (Node BEFORE the middle [3]!).
slow->next = slow->next->next; (Unlinks [3] and connects [2] -> [4]).
```

---

### Technique 2: Fast 2-Step Headstart (No Dummy Node)
Alternatively, start `slow = head` and give `fast` a **2-step headstart** (`fast = head->next->next`):
```cpp
if (head == nullptr || head->next == nullptr) return nullptr;

ListNode* slow = head;
ListNode* fast = head->next->next;

while (fast != nullptr && fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
}

ListNode* toDelete = slow->next;
slow->next = slow->next->next;
delete toDelete;

return head;
```
Because `fast` was moved ahead by 2 steps initially, `slow` stops exactly one step earlier!

---

## 3. Complexity Comparison

| Metric | Your Approach (3 Pointers + Heap Dummy) | Dummy-Start Offset ⭐ | Fast 2-Step Headstart |
| :--- | :--- | :--- | :--- |
| **Pointers Used** | 3 (`tmp`, `slow`, `fast`) | **2 (`slow`, `fast`)** | **2 (`slow`, `fast`)** |
| **Hardcoded `if` for $n = 1$?** | ❌ Yes (`if (slow->next == NULL)`) | ✅ **None (Zero edge case code!)** | Standard base check |
| **Memory Allocation** | Heap (`new ListNode`) | **Stack (`ListNode dummy(0)`)** | None |
| **Memory Leak Cleaned?** | ❌ No | ✅ Yes (`delete toDelete`) | ✅ Yes |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ |
| **Space Complexity** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |

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
    // =========================================================================
    // Technique 1: Dummy-Start Offset (Pure Logic, 0 Hardcoded Edge Cases)
    // =========================================================================
    ListNode* deleteMiddle(ListNode* head) {
        ListNode dummy(0);
        dummy.next = head;

        ListNode* slow = &dummy;
        ListNode* fast = head;

        // Move fast 2 steps and slow 1 step
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }

        // slow is right before the middle node!
        ListNode* toDelete = slow->next;
        slow->next = slow->next->next;
        delete toDelete; // Avoid memory leaks in C++

        return dummy.next;
    }

    // =========================================================================
    // Technique 2: Fast Pointer 2-Step Headstart (No Dummy Node)
    // =========================================================================
    ListNode* deleteMiddleFastSkip(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            delete head;
            return nullptr;
        }

        ListNode* slow = head;
        ListNode* fast = head->next->next;

        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }

        ListNode* toDelete = slow->next;
        slow->next = slow->next->next;
        delete toDelete;

        return head;
    }
};

// Helper: print list
void printList(ListNode* head) {
    ListNode* curr = head;
    while (curr != nullptr) {
        cout << curr->val;
        if (curr->next != nullptr) cout << " -> ";
        curr = curr->next;
    }
    cout << " -> NULL\n";
}

// Helper: build list from vector
ListNode* buildList(const vector<int>& nums) {
    if (nums.empty()) return nullptr;
    ListNode* head = new ListNode(nums[0]);
    ListNode* tail = head;
    for (size_t i = 1; i < nums.size(); ++i) {
        tail->next = new ListNode(nums[i]);
        tail = tail->next;
    }
    return head;
}

int main() {
    Solution sol;

    // Test Case 1: [1, 3, 4, 7, 1, 2, 6] (Odd length)
    ListNode* head1 = buildList({1, 3, 4, 7, 1, 2, 6});
    cout << "Original 1: ";
    printList(head1);
    head1 = sol.deleteMiddle(head1);
    cout << "After Delete: ";
    printList(head1);

    // Test Case 2: [1, 2, 3, 4] (Even length)
    ListNode* head2 = buildList({1, 2, 3, 4});
    cout << "\nOriginal 2: ";
    printList(head2);
    head2 = sol.deleteMiddle(head2);
    cout << "After Delete: ";
    printList(head2);

    // Test Case 3: [1] (Single node -> returns NULL without any if statements!)
    ListNode* head3 = buildList({1});
    cout << "\nOriginal 3: ";
    printList(head3);
    head3 = sol.deleteMiddle(head3);
    cout << "After Delete: ";
    printList(head3);

    return 0;
}
```
