# Remove Nth Node From End of List (LeetCode 19)

## Problem Description
Given the `head` of a linked list, remove the $n^{\text{th}}$ node from the end of the list and return its head.

**Example 1:**
- **Input:** `head = [1, 2, 3, 4, 5]`, `n = 2`
- **Output:** `[1, 2, 3, 5]`
- **Explanation:** The $2^{\text{nd}}$ node from the end is `4`. Removing it gives `[1, 2, 3, 5]`.

**Example 2:**
- **Input:** `head = [1]`, `n = 1`
- **Output:** `[]`

**Example 3:**
- **Input:** `head = [1, 2]`, `n = 1`
- **Output:** `[1]`

---

## 1. Analysis of Your 2-Pass Solution

### Your Current Approach
1. **Pass 1:** Traverse the list to calculate its total length $L$.
2. **Handle Head Removal:** If $L - n == 0$, return `head->next`.
3. **Pass 2:** Traverse to the $(L - n - 1)^{\text{th}}$ node and rewire: `tmp2->next = tmp2->next->next`.

```cpp
// 2-Pass: O(2N) time, O(1) space
int len = 0;
ListNode* tmp = head;
while (tmp) { len++; tmp = tmp->next; }

int x = len - n;
if (x == 0) return head->next;

ListNode* tmp2 = head;
while (--x) { tmp2 = tmp2->next; }
tmp2->next = tmp2->next->next;
return head;
```

### Limitations of 2-Pass
1. **Two traversals:** Total steps $= L + (L - n) \approx 2N$ operations.
2. **Edge case branching:** Requires special handling when the node to delete is the `head` (`x == 0`).
3. **Memory leak in C++:** The unlinked node is never freed using `delete`.

---

## 2. The Optimization: One-Pass Two-Pointer (Runner / Window Gap) with Dummy Node

### Key Insight
Instead of counting the length first, maintain a **fixed gap of $n$ nodes** between two pointers: `fast` and `slow`.

1. **Dummy Node Trick:**
   Create a `dummy` node pointing to `head`:
   ```
   [dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
   ```
   This unifies all edge cases (especially deleting the `head` node) without needing any `if` statements!

2. **Establish the Gap:**
   Advance `fast` pointer $n + 1$ steps forward starting from `dummy`.
   Now, the distance between `fast` and `slow` is exactly $n + 1$ nodes.

3. **Slide the Window:**
   Move both `fast` and `slow` forward together one node at a time until `fast == nullptr`.

4. **Target Reached:**
   Because `fast` is at `nullptr` (end of list) and the gap is $n + 1$, `slow` is guaranteed to be at the **node right before the node to be removed**!

5. **Delete & Rewire:**
   ```cpp
   ListNode* toDelete = slow->next;
   slow->next = slow->next->next;
   delete toDelete; // Clean up memory
   ```

### Step-by-Step Visual Walkthrough (`head = [1, 2, 3, 4, 5]`, `n = 2`)

```
Step 0: Create dummy and attach to head
[dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
  ^
slow, fast

Step 1: Move fast n + 1 (3 steps) forward
[dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
  ^                       ^
 slow                    fast

Step 2: Move slow and fast together until fast == NULL
Iteration 1:
[dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
             ^                       ^
            slow                    fast

Iteration 2:
[dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
                    ^                       ^
                   slow                    fast

Iteration 3:
[dummy] -> [1] -> [2] -> [3] -> [4] -> [5] -> NULL
                           ^                   ^
                          slow                fast (NULL!)

Now slow points to [3].
slow->next is [4] (the 2nd from the end!).
Rewire: slow->next = slow->next->next (points to [5]).
```

---

## 3. Complexity Comparison

| Metric | 2-Pass Approach (Your Code) | 1-Pass Two-Pointer (Optimized) ⭐ |
| :--- | :--- | :--- |
| **Passes** | 2 passes ($L + L - n$) | **1 pass ($L$ steps)** |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ (half the pointer hops) |
| **Auxiliary Space** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |
| **Edge Cases** | Needs explicit `if (x == 0)` | Cleanly handled by `dummy` |
| **Memory Cleanup** | Memory leak without `delete` | Explicit `delete toDelete` |

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
    // Optimal 1-Pass Two-Pointer (Runner Technique with Dummy Node)
    // =========================================================================
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode dummy(0);
        dummy.next = head;

        ListNode* fast = &dummy;
        ListNode* slow = &dummy;

        // Move fast ahead by n + 1 steps to create the (n + 1) node gap
        for (int i = 0; i <= n; i++) {
            fast = fast->next;
        }

        // Move both pointers until fast hits the end
        while (fast != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }

        // slow is now right before the target node to delete
        ListNode* toDelete = slow->next;
        slow->next = slow->next->next;
        delete toDelete; // Free heap memory in C++

        return dummy.next;
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

    // Test Case 1: [1, 2, 3, 4, 5], n = 2
    ListNode* head1 = buildList({1, 2, 3, 4, 5});
    cout << "Original 1: ";
    printList(head1);
    head1 = sol.removeNthFromEnd(head1, 2);
    cout << "After removing 2nd from end: ";
    printList(head1);

    // Test Case 2: [1], n = 1 (Deleting the head / single node)
    ListNode* head2 = buildList({1});
    cout << "\nOriginal 2: ";
    printList(head2);
    head2 = sol.removeNthFromEnd(head2, 1);
    cout << "After removing 1st from end: ";
    printList(head2);

    // Test Case 3: [1, 2], n = 2 (Deleting the head of multi-node list)
    ListNode* head3 = buildList({1, 2});
    cout << "\nOriginal 3: ";
    printList(head3);
    head3 = sol.removeNthFromEnd(head3, 2);
    cout << "After removing 2nd from end: ";
    printList(head3);

    return 0;
}
```
