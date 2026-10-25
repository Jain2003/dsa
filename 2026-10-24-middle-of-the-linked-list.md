# Middle of the Linked List (LeetCode 876)

## Problem Description
Given the `head` of a singly linked list, return the **middle node** of the linked list.

If there are two middle nodes (i.e. even number of nodes), return the **second middle node**.

**Example 1:**
- **Input:** `head = [1, 2, 3, 4, 5]`
- **Output:** `[3, 4, 5]`
- **Explanation:** The middle node of the list is node `3`.

**Example 2:**
- **Input:** `head = [1, 2, 3, 4, 5, 6]`
- **Output:** `[4, 5, 6]`
- **Explanation:** Since the list has two middle nodes with values `3` and `4`, we return the second one, which is `4`.

---

## 1. Analysis of Your Approach: 2-Pass Traversal

### How Your Code Works
```cpp
int size = 0;
ListNode *tmp = head;
while (tmp) {
    tmp = tmp->next;
    size++;
}

size /= 2;
while (size) {
    head = head->next;
    size--;
}
return head;
```

### Pros and Cons
- **Pros:** Correctness is $100\%$, simple and easy to understand.
- **Time Complexity:** $\mathcal{O}(N + \frac{N}{2}) = \mathcal{O}(N)$ operations.
- **Space Complexity:** $\mathcal{O}(1)$.
- **Limitation:** Traverses the first half of the linked list twice (once during counting, once during retrieval). In streaming scenarios or single-pass constraints, you cannot restart traversal from `head`.

---

## 2. The Better Way: Tortoise and Hare (Fast & Slow Pointers)

### Key Intuition
Instead of counting, use two pointers that start at `head` and move at different speeds:
- `slow` moves **1 step** at a time ($v = 1$).
- `fast` moves **2 steps** at a time ($v = 2$).

Because `fast` travels at **twice the speed** of `slow`:
$$\text{distance}(\text{slow}) = \frac{1}{2} \times \text{distance}(\text{fast})$$

When `fast` reaches the end of the list, `slow` will naturally be sitting **exactly at the middle**!

### Visualizing Odd vs. Even Length Lists

#### Case 1: Odd Length (`n = 5`, `[1, 2, 3, 4, 5]`)
```
Start:
[1] -> [2] -> [3] -> [4] -> [5] -> NULL
 ^
slow, fast

Step 1:
[1] -> [2] -> [3] -> [4] -> [5] -> NULL
        ^             ^
       slow          fast

Step 2:
[1] -> [2] -> [3] -> [4] -> [5] -> NULL
               ^             ^
              slow          fast

Loop terminates because fast->next == NULL!
slow is pointing at [3] (Exact middle).
```

#### Case 2: Even Length (`n = 6`, `[1, 2, 3, 4, 5, 6]`)
```
Start:
[1] -> [2] -> [3] -> [4] -> [5] -> [6] -> NULL
 ^
slow, fast

Step 1:
[1] -> [2] -> [3] -> [4] -> [5] -> [6] -> NULL
        ^             ^
       slow          fast

Step 2:
[1] -> [2] -> [3] -> [4] -> [5] -> [6] -> NULL
               ^                           ^
              slow                        fast

Step 3:
[1] -> [2] -> [3] -> [4] -> [5] -> [6] -> NULL
                      ^                     ^
                     slow                  fast (NULL!)

Loop terminates because fast == NULL!
slow is pointing at [4] (Second middle node, as required).
```

### The Termination Condition
```cpp
while (fast != nullptr && fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
}
```
- `fast != nullptr` prevents null pointer dereference on even length lists.
- `fast->next != nullptr` prevents null pointer dereference on odd length lists when accessing `fast->next->next`.

---

## 3. Complexity Comparison

| Metric | 2-Pass Counting (Your Code) | Fast & Slow Pointer (Optimal) ⭐ |
| :--- | :--- | :--- |
| **Passes** | 2 passes ($1.5N$ node visits) | **1 pass ($N$ node visits)** |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ |
| **Auxiliary Space** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |
| **Stream Friendly?** | ❌ No (requires second pass) | ✅ **Yes (finds middle in real-time)** |
| **Interview Expectation** | Considered naive | **Gold standard technique** |

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
    // Optimal: Tortoise & Hare (Fast and Slow Pointers) - 1 Pass
    // =========================================================================
    ListNode* middleNode(ListNode* head) {
        ListNode* slow = head;
        ListNode* fast = head;

        // While fast can take two steps forward
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;         // 1 step
            fast = fast->next->next;   // 2 steps
        }

        return slow; // slow is guaranteed to be at the middle
    }
};

// Helper: print list from a given node
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

    // Test Case 1: Odd number of elements [1, 2, 3, 4, 5]
    ListNode* head1 = buildList({1, 2, 3, 4, 5});
    cout << "List 1: ";
    printList(head1);
    ListNode* mid1 = sol.middleNode(head1);
    cout << "Middle Node 1: ";
    printList(mid1);

    // Test Case 2: Even number of elements [1, 2, 3, 4, 5, 6]
    ListNode* head2 = buildList({1, 2, 3, 4, 5, 6});
    cout << "\nList 2: ";
    printList(head2);
    ListNode* mid2 = sol.middleNode(head2);
    cout << "Middle Node 2: ";
    printList(mid2);

    // Test Case 3: Single element [1]
    ListNode* head3 = buildList({1});
    cout << "\nList 3: ";
    printList(head3);
    ListNode* mid3 = sol.middleNode(head3);
    cout << "Middle Node 3: ";
    printList(mid3);

    return 0;
}
```
