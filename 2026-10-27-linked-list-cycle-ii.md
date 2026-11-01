# Linked List Cycle II - Find Starting Node of Cycle (LeetCode 142)

## Problem Description
Given the `head` of a singly linked list, return the **node where the cycle begins**. If there is no cycle, return `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to (**0-indexed**). It is `-1` if there is no cycle. **Note that `pos` is not passed as a parameter.**

**Do not modify the linked list.**

**Example 1:**
- **Input:** `head = [3, 2, 0, -4]`, `pos = 1` (Node `-4` connects to Node `2`)
- **Output:** Node with value `2`
- **Explanation:** There is a cycle in the linked list, where tail connects to the second node.

```
[3] ---> [2] ---> [0] ---> [-4]
          ^                  |
          |__________________|
```

**Example 2:**
- **Input:** `head = [1, 2]`, `pos = 0`
- **Output:** Node with value `1`

**Example 3:**
- **Input:** `head = [1]`, `pos = -1`
- **Output:** `null`

---

## 1. Mathematical Proof of Floyd's Cycle-Finding Algorithm

Floyd's Cycle-Detection (Tortoise & Hare) operates in two distinct phases:
1. **Phase 1: Detect if a cycle exists and find the meeting point.**
2. **Phase 2: Find the exact entry point of the cycle.**

```
head                                cycle entry
 [3] ------------------------------> [2] --------> [0]
                                      ^              |
                                      |              v
                                     [-4] <----------
                                  meeting point
```

### Setup the Variables
- Let $L_1$ = distance from `head` to the **cycle entry node**.
- Let $C$ = length of the cycle (number of nodes in the cycle).
- Let $d$ = distance from the **cycle entry node** to the **meeting point** along the direction of traversal.
- Let $(C - d)$ = remaining distance from the **meeting point** back to the **cycle entry node**.

### Derivation
When `slow` and `fast` collide at the meeting point:
1. Distance traveled by `slow`:
   $$S = L_1 + d$$
2. Distance traveled by `fast`:
   $$F = L_1 + n \cdot C + d \quad (\text{where } n \ge 1 \text{ is the number of full laps fast completed})$$
3. Since `fast` moves at **twice the speed** of `slow`:
   $$F = 2 \cdot S$$
   $$L_1 + n \cdot C + d = 2(L_1 + d)$$
   $$L_1 + n \cdot C + d = 2L_1 + 2d$$
   $$n \cdot C = L_1 + d$$
   $$L_1 = n \cdot C - d$$
   $$L_1 = (n - 1) \cdot C + (C - d)$$

### What does this equation tell us?
$$L_1 = (n - 1) \cdot C + (C - d)$$
- $L_1$ is the distance from `head` to `cycle entry`.
- $(C - d)$ is the distance from `meeting point` to `cycle entry`.
- $(n - 1) \cdot C$ simply means zero or more full loops around the cycle.

**The conclusion:**
If we reset `slow` back to `head` and keep `fast` at the `meeting point`, and then move **both pointers 1 step at a time**, they will travel distance $L_1$ and collide **exactly at the cycle entry node**!

---

## 2. Step-by-Step Trace on the Example: `[3] -> [2] -> [0] -> [-4] -> [2]`

Here:
- `head` = `[3]`
- Cycle entry = `[2]` ($L_1 = 1$)
- Cycle nodes = `[2], [0], [-4]` ($C = 3$)

### Phase 1: Finding the Meeting Point
Both `slow` and `fast` start at `head` (`[3]`):

| Step | `slow` position (speed = 1) | `fast` position (speed = 2) | Match? |
| :---: | :---: | :---: | :---: |
| **0** | `[3]` | `[3]` | Start |
| **1** | `[2]` | `[0]` | No |
| **2** | `[0]` | `[2]` | No |
| **3** | `[-4]` | `[-4]` | **Match! (Meeting point = `[-4]`)** |

- Distance from cycle entry `[2]` to meeting point `[-4]`: $d = 2$.
- Distance from meeting point `[-4]` to cycle entry `[2]`: $C - d = 3 - 2 = 1$.
- Distance from `head` `[3]` to cycle entry `[2]`: $L_1 = 1$.
- Notice $L_1 = C - d = 1$!

---

### Phase 2: Finding the Cycle Entry
1. Reset `slow = head = [3]`.
2. Keep `fast = meeting point = [-4]`.
3. Advance both pointers **1 step at a time**:

| Step | `slow` (starts at `head`) | `fast` (starts at meeting point `[-4]`) | Match? |
| :---: | :---: | :---: | :---: |
| **0** | `[3]` | `[-4]` | `slow != fast` |
| **1** | `[2]` | `[2]` | **`slow == fast`! Cycle Entry Found!** |

Return node `[2]`.

---

## 3. Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N)$
  - Phase 1: `slow` travels at most $L_1 + C \le N$ steps before meeting `fast`.
  - Phase 2: `slow` travels exactly $L_1 \le N$ steps to reach the cycle entry.
  - Total time is strictly linear $\mathcal{O}(N)$.
- **Space Complexity:** $\mathcal{O}(1)$
  - In-place detection using only two pointers (`slow` and `fast`). Zero hash sets or additional heap memory.

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
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    // =========================================================================
    // Optimal: Floyd's Cycle-Finding Algorithm (O(N) Time, O(1) Space)
    // =========================================================================
    ListNode *detectCycle(ListNode *head) {
        if (!head || !head->next) {
            return nullptr;
        }

        ListNode* slow = head;
        ListNode* fast = head;

        // Phase 1: Determine if a cycle exists and find the meeting point
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;

            if (slow == fast) {
                // Phase 2: Find the entry point of the cycle
                // Reset slow to head, keep fast at meeting point
                slow = head;

                // Move both pointers 1 step at a time until they collide
                while (slow != fast) {
                    slow = slow->next;
                    fast = fast->next;
                }

                return slow; // Cycle entry node
            }
        }

        return nullptr; // No cycle
    }
};

int main() {
    Solution sol;

    // Build list: 3 -> 2 -> 0 -> -4 -> (connects back to 2)
    ListNode* n1 = new ListNode(3);
    ListNode* n2 = new ListNode(2);
    ListNode* n3 = new ListNode(0);
    ListNode* n4 = new ListNode(-4);

    n1->next = n2;
    n2->next = n3;
    n3->next = n4;
    n4->next = n2; // Cycle back to node 2

    ListNode* entry = sol.detectCycle(n1);
    if (entry) {
        cout << "Cycle detected! Entry node value: " << entry->val << endl;
    } else {
        cout << "No cycle detected." << endl;
    }

    // Clean up connections to avoid infinite loop when deleting
    n4->next = nullptr;
    delete n1;
    delete n2;
    delete n3;
    delete n4;

    // Test Case with No Cycle: 1 -> 2 -> NULL
    ListNode* a = new ListNode(1);
    ListNode* b = new ListNode(2);
    a->next = b;

    ListNode* noCycleEntry = sol.detectCycle(a);
    cout << "Test no cycle result: " << (noCycleEntry == nullptr ? "nullptr (Correct)" : "Failed") << endl;

    delete a;
    delete b;

    return 0;
}
```
