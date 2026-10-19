# Add Two Numbers (LeetCode 2)

## Problem Description
You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example 1:**
- **Input:** `l1 = [2, 4, 3]`, `l2 = [5, 6, 4]` (representing $342 + 465 = 807$)
- **Output:** `[7, 0, 8]`
- **Explanation:** $342 + 465 = 807$.

**Example 2:**
- **Input:** `l1 = [0]`, `l2 = [0]`
- **Output:** `[0]`

**Example 3:**
- **Input:** `l1 = [9, 9, 9, 9, 9, 9, 9]`, `l2 = [9, 9, 9, 9]`
- **Output:** `[8, 9, 9, 9, 0, 0, 0, 1]`

---

## 1. Key Intuition: Primary School Addition with Reverse Order

Because digits are given in **reverse order**, the heads of both lists are already at the **units place**. This allows us to simulate standard elementary school column addition:

$$\text{totalSum} = \text{val1} + \text{val2} + \text{carry}$$
$$\text{digit} = \text{totalSum} \pmod{10}$$
$$\text{carry} = \lfloor \text{totalSum} / 10 \rfloor$$

### Loop Termination Condition:
The loop must continue running as long as:
$$\text{l1} \neq \text{nullptr} \quad \mathbf{OR} \quad \text{l2} \neq \text{nullptr} \quad \mathbf{OR} \quad \text{carry} > 0$$

Checking `carry > 0` directly inside the loop condition elegantly handles edge cases like $5 + 5 = 10$, where a final extra node `[1]` must be appended even after both lists are exhausted!

---

## 2. Visual Walkthrough (`l1 = [2, 4, 3]`, `l2 = [5, 6, 4]`)

```
Step 1:
- l1 val = 2, l2 val = 5, carry = 0
- sum = 2 + 5 + 0 = 7 -> digit = 7, carry = 0
- Result: [7]

Step 2:
- l1 val = 4, l2 val = 6, carry = 0
- sum = 4 + 6 + 0 = 10 -> digit = 0, carry = 1
- Result: [7] -> [0]

Step 3:
- l1 val = 3, l2 val = 4, carry = 1
- sum = 3 + 4 + 1 = 8 -> digit = 8, carry = 0
- Result: [7] -> [0] -> [8]

Loop terminates (both null, carry == 0).
Output: [7, 0, 8] (representing 807).
```

---

## 3. Complexity Analysis
- **Time Complexity:** $\mathcal{O}(\max(M, N))$ — Where $M$ and $N$ are the lengths of `l1` and `l2`. We iterate at most $\max(M, N) + 1$ times.
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (excluding the output linked list of length $\max(M, N) + 1$).

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dummy(0);
        ListNode* tail = &dummy;
        int carry = 0;

        // Continue if there are digits left in l1 OR l2 OR an active carry
        while (l1 != nullptr || l2 != nullptr || carry != 0) {
            int val1 = (l1 != nullptr) ? l1->val : 0;
            int val2 = (l2 != nullptr) ? l2->val : 0;

            int sum = val1 + val2 + carry;
            carry = sum / 10;

            tail->next = new ListNode(sum % 10);
            tail = tail->next;

            if (l1 != nullptr) l1 = l1->next;
            if (l2 != nullptr) l2 = l2->next;
        }

        return dummy.next;
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

    // Test Case 1: 342 + 465 = 807
    // l1 = [2, 4, 3], l2 = [5, 6, 4]
    ListNode* l1 = new ListNode(2, new ListNode(4, new ListNode(3)));
    ListNode* l2 = new ListNode(5, new ListNode(6, new ListNode(4)));

    cout << "List 1: ";
    printList(l1);
    cout << "List 2: ";
    printList(l2);

    ListNode* sumList = sol.addTwoNumbers(l1, l2);
    cout << "Sum:    ";
    printList(sumList); // Expected: 7 -> 0 -> 8

    // Test Case 2: Extra carry at end: 5 + 5 = 10
    // l3 = [5], l4 = [5] -> sum = [0, 1]
    ListNode* l3 = new ListNode(5);
    ListNode* l4 = new ListNode(5);
    ListNode* sumList2 = sol.addTwoNumbers(l3, l4);
    cout << "\n5 + 5 Sum: ";
    printList(sumList2); // Expected: 0 -> 1

    return 0;
}
```
