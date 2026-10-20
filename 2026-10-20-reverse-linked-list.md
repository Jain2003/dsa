# Reverse Linked List (LeetCode 206)

## Problem Description
Given the `head` of a singly linked list, reverse the list, and return the reversed list.

**Example 1:**
- **Input:** `head = [1, 2, 3, 4, 5]`
- **Output:** `[5, 4, 3, 2, 1]`

**Example 2:**
- **Input:** `head = [1, 2]`
- **Output:** `[2, 1]`

**Example 3:**
- **Input:** `head = []`
- **Output:** `[]`

---

## 1. The Best Technique: Iterative 3-Pointer Approach

### Why is it the "Best"?
- **Time Complexity:** $\mathcal{O}(N)$ — visits each node exactly once.
- **Space Complexity:** $\mathcal{O}(1)$ — strictly in-place, zero auxiliary memory or heap allocations.
- **Robustness:** Unlike recursion, it **never suffers from Call Stack Overflow** on large lists ($N \ge 10^5$).

### The Intuition & The 4-Step Loop
At any point in time during the traversal:
1. `prev`: points to the head of the already-reversed prefix (initially `nullptr`).
2. `curr`: points to the node we are currently reversing (initially `head`).
3. `next_node`: temporarily stores the remainder of the original list so we don't lose access when reversing `curr->next`.

```
Initial State:
prev = NULL
curr = [1] -> [2] -> [3] -> NULL

Step 1: Save next
next_node = curr->next   (which is [2])

Step 2: Reverse link
curr->next = prev        ([1] -> NULL)

Step 3 & 4: Advance pointers
prev = curr              (prev is now [1])
curr = next_node         (curr is now [2])
```

When `curr` reaches `nullptr`, `prev` will be pointing to the new head of the reversed list!

---

## 2. Alternative Technique: Recursive Approach

### Intuition
Think recursively:
1. **Base Case:** If the list is empty (`!head`) or has only one node (`!head->next`), it is already reversed. Return `head`.
2. **Recursive Leap of Faith:** Assume `reverseList(head->next)` successfully reverses the entire sublist from node 2 onwards and returns the `newHead` (the original tail).
3. **Stitching the Link Backwards:**
   - In the reversed sublist, the node that was immediately after `head` (`head->next`) is now the **tail** of that reversed sublist.
   - To link `head` behind it: `head->next->next = head;`
   - To make `head` the new tail (pointing to nothing): `head->next = nullptr;`
4. Return `newHead`.

```
Original: [1] -> [2] <- [3] <- [4] (sublist already reversed!)
                  ^
              head->next

Step: head->next->next = head  ==>  [2] -> [1]
Step: head->next = nullptr     ==>  [1] -> NULL
```

---

## 3. Comparison of Approaches

| Metric | Iterative (3-Pointer) ⭐ | Recursive | Brute Force (Stack / Value Array) |
| :--- | :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ |
| **Space Complexity** | $\mathcal{O}(1)$ (Optimal) | $\mathcal{O}(N)$ (Call stack) | $\mathcal{O}(N)$ (Extra container) |
| **Stack Overflow Risk** | None | Yes (fails if $N > 10^5$) | None |
| **Modifies Pointers?** | Yes (in-place) | Yes (in-place) | No (overwrites data) |
| **Recommendation** | **Industry & Interview Standard** | Great for demonstrating recursion | Avoid in interviews |

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
    // Technique 1: Iterative 3-Pointer (BEST: O(N) Time, O(1) Space)
    // =========================================================================
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;

        while (curr != nullptr) {
            ListNode* next_node = curr->next; // 1. Save next node
            curr->next = prev;               // 2. Reverse link
            prev = curr;                     // 3. Move prev forward
            curr = next_node;                // 4. Move curr forward
        }

        return prev; // prev is the new head
    }

    // =========================================================================
    // Technique 2: Recursive (O(N) Time, O(N) Stack Space)
    // =========================================================================
    ListNode* reverseListRecursive(ListNode* head) {
        // Base case: empty list or single node
        if (head == nullptr || head->next == nullptr) {
            return head;
        }

        // Recursively reverse the rest of the list
        ListNode* newHead = reverseListRecursive(head->next);

        // Reverse current node's pointer
        head->next->next = head;
        head->next = nullptr;

        return newHead;
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
    cout << " -> NULL\n";
}

// Helper function to construct linked list from vector
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

    // Test Case 1: [1, 2, 3, 4, 5]
    vector<int> nums1 = {1, 2, 3, 4, 5};
    ListNode* head1 = buildList(nums1);
    cout << "Original List 1: ";
    printList(head1);
    ListNode* rev1 = sol.reverseList(head1);
    cout << "Reversed List 1 (Iterative): ";
    printList(rev1);

    // Test Case 2: [1, 2] using Recursive
    vector<int> nums2 = {1, 2};
    ListNode* head2 = buildList(nums2);
    cout << "\nOriginal List 2: ";
    printList(head2);
    ListNode* rev2 = sol.reverseListRecursive(head2);
    cout << "Reversed List 2 (Recursive): ";
    printList(rev2);

    // Test Case 3: Empty List
    ListNode* head3 = nullptr;
    ListNode* rev3 = sol.reverseList(head3);
    cout << "\nReversed Empty List: ";
    printList(rev3);

    return 0;
}
```
