# Sort a Linked List of 0s, 1s, and 2s

## Problem Description
Given a linked list consisting of `0`s, `1`s, and `2`s, sort the linked list such that all `0`s appear first, followed by all `1`s, and finally all `2`s.

**Example 1:**
- **Input:** `head = [1, 2, 2, 1, 2, 0, 2, 2]`
- **Output:** `[0, 1, 1, 2, 2, 2, 2, 2]`

**Example 2:**
- **Input:** `head = [2, 2, 0, 1]`
- **Output:** `[0, 1, 2, 2]`

---

## 1. Analysis of Your Solution: The Counting Approach

### How Your Code Works
```cpp
// Pass 1: Count frequencies of 0, 1, and 2
int zeros = 0, ones = 0, twos = 0;
// Pass 2: Overwrite the node values directly in place
for (int i = 0; i < zeros; i++) { tmp2->data = 0; tmp2 = tmp2->next; }
for (int i = 0; i < ones; i++)  { tmp2->data = 1; tmp2 = tmp2->next; }
for (int i = 0; i < twos; i++)  { tmp2->data = 2; tmp2 = tmp2->next; }
```

### Is It a "Bad" Solution?
- **Complexity-wise:** No. It is $\mathcal{O}(2N)$ time and $\mathcal{O}(1)$ space, which is asymptotically linear and efficient.
- **Interview & Real-World-wise:** **Yes, it is frowned upon / often disallowed.**

### Why Interviewers Dislike Data Overwriting
1. **Violates Pointer Manipulation Goal:** Linked list problems test your ability to rewire pointers (`next`), not count numbers.
2. **Heavy Node Payloads:** In real-world software, a `ListNode` usually holds complex objects (user profiles, database records, large structs), not just an `int`. Overwriting values requires expensive deep copies.
3. **Object Identity & Pointer Stability:** External services or hash maps may hold pointers to specific nodes. If you overwrite values, the node that used to represent User A now has the data of User B, corrupting external references.
4. **Explicit Restriction:** Many interviewers will explicitly say: *"Sort the list by changing links, not by swapping or overwriting data."*

---

## 2. The Optimal Solution: 3 Dummy Lists & Pointer Stitching

### Intuition
Partition the nodes into **three separate linked lists** in a single pass without copying or overwriting data:
1. `zeroHead` $\rightarrow$ chain of all `0` nodes.
2. `oneHead` $\rightarrow$ chain of all `1` nodes.
3. `twoHead` $\rightarrow$ chain of all `2` nodes.

Use **three stack-allocated dummy nodes** (`zeroDummy`, `oneDummy`, `twoDummy`) with tracking tails (`zeroTail`, `oneTail`, `twoTail`).

### Visual Walkthrough

```
Original: [1] -> [2] -> [0] -> [1] -> NULL

Distribute:
zeroDummy -> [0]
oneDummy  -> [1] -> [1]
twoDummy  -> [2]

Stitch Together:
zeroTail->next = (oneDummy.next) ? oneDummy.next : twoDummy.next;
oneTail->next  = twoDummy.next;
twoTail->next  = nullptr; // CRITICAL: terminate the list to prevent cycles!

Result: [0] -> [1] -> [1] -> [2] -> NULL
```

### Key Edge Case: Missing 0s, 1s, or 2s
What if there are no `1`s in the list?
- If `oneDummy.next == nullptr`, `zeroTail->next` must directly connect to `twoDummy.next`!
- The conditional stitching handles any missing digit gracefully:
  ```cpp
  zeroTail->next = (oneDummy.next != nullptr) ? oneDummy.next : twoDummy.next;
  oneTail->next = twoDummy.next;
  twoTail->next = nullptr;
  ```

---

## 3. Complexity Comparison

| Metric | Counting & Value Overwrite (Your Code) | 3-Dummy Pointer Stitching (Optimal) ⭐ |
| :--- | :--- | :--- |
| **Passes** | 2 passes ($2N$) | **1 pass ($N$)** |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ |
| **Auxiliary Space** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ (only pointer variables) |
| **Preserves Node Identity** | ❌ No (data rewritten) | ✅ **Yes (pointers rewired)** |
| **Supports Heavy Payloads** | ❌ No (expensive copies) | ✅ **Yes ($\mathcal{O}(1)$ pointer assigns)** |
| **Interview Acceptability** | Often rejected / asked to redo | **100% Expected standard** |

---

## 4. Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int data;
    ListNode *next;
    ListNode() : data(0), next(nullptr) {}
    ListNode(int x) : data(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : data(x), next(next) {}
};

class Solution {
public:
    // =========================================================================
    // Optimal: 1-Pass Pointer Stitching using 3 Dummy Nodes (O(N) Time, O(1) Space)
    // =========================================================================
    ListNode* sortList(ListNode* head) {
        if (!head || !head->next) {
            return head;
        }

        // Three dummy nodes for the three buckets
        ListNode zeroDummy(0);
        ListNode oneDummy(0);
        ListNode twoDummy(0);

        // Pointers to the tails of the three sublists
        ListNode* zeroTail = &zeroDummy;
        ListNode* oneTail  = &oneDummy;
        ListNode* twoTail  = &twoDummy;

        // Traverse the original list and distribute nodes
        ListNode* curr = head;
        while (curr != nullptr) {
            if (curr->data == 0) {
                zeroTail->next = curr;
                zeroTail = zeroTail->next;
            } else if (curr->data == 1) {
                oneTail->next = curr;
                oneTail = oneTail->next;
            } else {
                twoTail->next = curr;
                twoTail = twoTail->next;
            }
            curr = curr->next;
        }

        // Stitch the three sublists together
        // 1. Connect zero list to one list (or two list if one list is empty)
        zeroTail->next = (oneDummy.next != nullptr) ? oneDummy.next : twoDummy.next;

        // 2. Connect one list to two list
        oneTail->next = twoDummy.next;

        // 3. Crucial: Terminate two list to prevent infinite cycles!
        twoTail->next = nullptr;

        // Resulting head is the first non-dummy node
        ListNode* newHead = zeroDummy.next != nullptr ? zeroDummy.next : 
                           (oneDummy.next != nullptr ? oneDummy.next : twoDummy.next);

        return newHead;
    }
};

// Helper: print list
void printList(ListNode* head) {
    ListNode* curr = head;
    while (curr != nullptr) {
        cout << curr->data;
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

    // Test Case 1: All 0s, 1s, and 2s present
    ListNode* head1 = buildList({1, 2, 2, 1, 2, 0, 2, 2});
    cout << "Original 1: ";
    printList(head1);
    head1 = sol.sortList(head1);
    cout << "Sorted 1:   ";
    printList(head1);

    // Test Case 2: Missing 1s (edge case for stitching)
    ListNode* head2 = buildList({2, 0, 2, 0});
    cout << "\nOriginal 2 (no 1s): ";
    printList(head2);
    head2 = sol.sortList(head2);
    cout << "Sorted 2:           ";
    printList(head2);

    // Test Case 3: Missing 0s (edge case for return head)
    ListNode* head3 = buildList({2, 1, 1, 2});
    cout << "\nOriginal 3 (no 0s): ";
    printList(head3);
    head3 = sol.sortList(head3);
    cout << "Sorted 3:           ";
    printList(head3);

    return 0;
}
```
