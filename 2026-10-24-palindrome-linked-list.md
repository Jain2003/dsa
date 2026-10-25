# Palindrome Linked List (LeetCode 234)

## Problem Description
Given the `head` of a singly linked list, return `true` if it is a palindrome or `false` otherwise.

**Example 1:**
- **Input:** `head = [1, 2, 2, 1]`
- **Output:** `true`

**Example 2:**
- **Input:** `head = [1, 2]`
- **Output:** `false`

---

## 1. Analysis of Your Code

### What You Did Well
- You correctly identified the optimal strategy: Find the middle $\rightarrow$ reverse $\rightarrow$ compare.
- Time Complexity is $\mathcal{O}(N)$ and Space Complexity is $\mathcal{O}(1)$.

### Areas to Optimize
1. **Unnecessary Heap Dummy (`new ListNode(-1, head)`):**
   - Allocating `dummy` with `new` is slow and causes a **memory leak** because it is never `delete`d.
2. **Three Pointers Overkill (`tmp`, `slow`, `fast`):**
   - You used `tmp` to sever the list. By adjusting the fast pointer loop condition to `fast->next != nullptr && fast->next->next != nullptr`, `slow` naturally stops at the **end of the first half** without needing `tmp` or `dummy`!
3. **Mutilating the Caller's Linked List (List Restoration):**
   - `isPalindrome` is a query function. If you permanently alter the links or cut the list in half, the caller's data is corrupted!
   - In production and top-tier interviews, you should **reverse the second half back** before returning.

---

## 2. The Optimal, Clean Solution

### The First-Half Middle Trick
Instead of running `while (fast && fast->next)`:
```cpp
while (fast->next != nullptr && fast->next->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
}
```

Let's see where `slow` lands:
- **Even length (`[1, 2, 2, 1]`):**
  - `fast` starts at `1` (index 0).
  - Iteration 1: `slow` moves to first `2` (index 1), `fast` moves to `1` (index 3).
  - `fast->next` is `nullptr` $\implies$ Loop stops!
  - `slow` is at the **end of the first half** (`first 2`).
  - Second half starts at `slow->next` (`second 2`)!

- **Odd length (`[1, 2, 3, 2, 1]`):**
  - Iteration 1: `slow = 2`, `fast = 3`.
  - Iteration 2: `slow = 3`, `fast = 1` (tail).
  - `fast->next` is `nullptr` $\implies$ Loop stops!
  - `slow` is at the **exact middle** (`3`).
  - Second half starts at `slow->next` (`second 2`)!

### The 4-Step Pipeline
1. **Find End of 1st Half:** `slow` stops at the end of the first half using the condition above.
2. **Reverse 2nd Half:** `head2 = reverseLL(slow->next);`
3. **Compare:** Walk `p1 = head` and `p2 = head2` until `p2 == nullptr`.
4. **Restore (Best Practice):** `slow->next = reverseLL(head2);`

---

## 3. Complexity Comparison

| Metric | Your Approach | Optimal Clean Approach ⭐ |
| :--- | :--- | :--- |
| **Pointers Used** | 4 (`dummy`, `tmp`, `slow`, `fast`) | **2 (`slow`, `fast`)** |
| **Heap Allocations** | `new ListNode` (Memory leak) | **Zero (0)** |
| **List Restored?** | ❌ No (list broken in half) | ✅ **Yes (fully restored)** |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ |
| **Auxiliary Space** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |

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
private:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr != nullptr) {
            ListNode* nextPtr = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextPtr;
        }
        return prev;
    }

public:
    bool isPalindrome(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return true;
        }

        // 1. Find end of first half
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }

        // 2. Reverse second half
        ListNode* head2 = reverseList(slow->next);

        // 3. Compare first half and second half
        ListNode* p1 = head;
        ListNode* p2 = head2;
        bool isPali = true;

        while (p2 != nullptr) {
            if (p1->val != p2->val) {
                isPali = false;
                break;
            }
            p1 = p1->next;
            p2 = p2->next;
        }

        // 4. Restore original list structure (Good practice!)
        slow->next = reverseList(head2);

        return isPali;
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

    // Test Case 1: [1, 2, 2, 1] -> true
    ListNode* head1 = buildList({1, 2, 2, 1});
    cout << "List 1: ";
    printList(head1);
    cout << "Is Palindrome: " << (sol.isPalindrome(head1) ? "true" : "false") << "\n";
    cout << "After check (restored): ";
    printList(head1);

    // Test Case 2: [1, 2, 3, 2, 1] -> true
    ListNode* head2 = buildList({1, 2, 3, 2, 1});
    cout << "\nList 2: ";
    printList(head2);
    cout << "Is Palindrome: " << (sol.isPalindrome(head2) ? "true" : "false") << "\n";
    cout << "After check (restored): ";
    printList(head2);

    // Test Case 3: [1, 2] -> false
    ListNode* head3 = buildList({1, 2});
    cout << "\nList 3: ";
    printList(head3);
    cout << "Is Palindrome: " << (sol.isPalindrome(head3) ? "true" : "false") << "\n";
    cout << "After check (restored): ";
    printList(head3);

    return 0;
}
```
