# Find Length of Loop in Linked List

## Problem Description
Given the `head` of a linked list, determine whether the list contains a loop (cycle). If a loop is present, return the **number of nodes in the loop**; otherwise, return `0`.

**Example 1:**
- **Input:** `head = [3, 2, 0, -4]`, loop connects `-4` back to `2`
- **Output:** `3`
- **Explanation:** The nodes in the loop are `2 -> 0 -> -4`, so the loop length is `3`.

```
[3] ---> [2] ---> [0] ---> [-4]
          ^                  |
          |__________________|
```

**Example 2:**
- **Input:** `head = [1, 2, 3, 4, 5]`, no loop
- **Output:** `0`
- **Explanation:** There is no loop in the linked list.

**Example 3:**
- **Input:** `head = [1]`, connects to itself
- **Output:** `1`

---

## 1. Intuition & Approach

### Floyd's Cycle Detection + Loop Traversal
1. **Detect Loop:**
   - Use two pointers, `slow` (advances 1 step) and `fast` (advances 2 steps).
   - If `fast == nullptr` or `fast->next == nullptr`, the list terminates $\implies$ return `0`.
   - If `slow == fast`, a loop is detected.

2. **Count Loop Length:**
   - When `slow == fast`, both pointers are at some **meeting point inside the loop**.
   - To find the length of the loop, freeze `slow` at the meeting point.
   - Start a pointer `curr = slow->next` and initialize `size = 1`.
   - Traverse `curr` around the cycle until it returns back to `slow`, incrementing `size` at each step.
   - Return `size`.

```
Meeting Point inside cycle: [M]
Start: curr = [M]->next, size = 1
Step 1: curr advances, size = 2
Step 2: curr advances, size = 3
...
When curr == [M], loop has been traversed completely!
```

---

## 2. Dry Run on `[3] -> [2] -> [0] -> [-4] -> [2]`

- `head = [3]`, cycle connects `[-4]` to `[2]`.
- **Phase 1: Detection:**
  - Step 0: `slow = 3`, `fast = 3`
  - Step 1: `slow = 2`, `fast = 0`
  - Step 2: `slow = 0`, `fast = 2`
  - Step 3: `slow = -4`, `fast = -4` $\implies$ Match! Meeting point is `[-4]`.
- **Phase 2: Count Cycle:**
  - `slow = [-4]`
  - `size = 1`, `curr = slow->next = [2]`
  - `curr != slow` (`2 != -4`): `size = 2`, `curr = [0]`
  - `curr != slow` (`0 != -4`): `size = 3`, `curr = [-4]`
  - `curr == slow` (`-4 == -4`): Loop terminates!
  - Return `size = 3`.

---

## 3. Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N)$
  - Phase 1 (Detection): Fast & slow meet within $\le N$ steps.
  - Phase 2 (Counting): `curr` walks the loop of length $C$ ($C \le N$) exactly once.
  - Total Time: $\mathcal{O}(N)$ operations.
- **Space Complexity:** $\mathcal{O}(1)$
  - Only uses pointer variables (`slow`, `fast`, `curr`) and an integer counter `size`. Zero auxiliary space.

---

## 4. Code

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
    // =========================================================================
    // Optimal: Floyd's Cycle Detection + Loop Walk (O(N) Time, O(1) Space)
    // =========================================================================
    int findLengthOfLoop(ListNode *head) {
        ListNode* slow = head;
        ListNode* fast = head;

        // Phase 1: Detect cycle
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;

            // Cycle detected!
            if (slow == fast) {
                // Phase 2: Count the length of the cycle
                int size = 1;
                ListNode* curr = slow->next;

                while (curr != slow) {
                    size++;
                    curr = curr->next;
                }

                return size;
            }
        }

        // No cycle exists
        return 0;
    }
};

int main() {
    Solution sol;

    // Test Case 1: [3, 2, 0, -4] with loop from -4 to 2 (Length 3)
    ListNode* n1 = new ListNode(3);
    ListNode* n2 = new ListNode(2);
    ListNode* n3 = new ListNode(0);
    ListNode* n4 = new ListNode(-4);

    n1->next = n2;
    n2->next = n3;
    n3->next = n4;
    n4->next = n2; // Loop: 2 -> 0 -> -4 -> 2

    cout << "Test 1 Loop Length: " << sol.findLengthOfLoop(n1) << " (Expected: 3)\n";

    // Disconnect loop for clean deletion
    n4->next = nullptr;
    delete n1; delete n2; delete n3; delete n4;

    // Test Case 2: [1, 2, 3] with No loop (Length 0)
    ListNode* a = new ListNode(1, new ListNode(2, new ListNode(3)));
    cout << "Test 2 Loop Length: " << sol.findLengthOfLoop(a) << " (Expected: 0)\n";

    // Clean up
    delete a->next->next;
    delete a->next;
    delete a;

    // Test Case 3: Single node with self loop (Length 1)
    ListNode* selfNode = new ListNode(42);
    selfNode->next = selfNode;
    cout << "Test 3 Loop Length: " << sol.findLengthOfLoop(selfNode) << " (Expected: 1)\n";
    selfNode->next = nullptr;
    delete selfNode;

    return 0;
}
```
