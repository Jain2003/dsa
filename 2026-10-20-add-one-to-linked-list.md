# Add 1 to a Number Represented as Linked List

## Problem Description
A number is represented as a singly linked list where each node contains a single digit ($0 - 9$). The most significant digit is at the head of the list.
Add 1 to the number and return the head of the updated linked list.

**Example 1:**
- **Input:** `head = [4, 5, 6]`
- **Output:** `[4, 5, 7]` ($456 + 1 = 457$)

**Example 2:**
- **Input:** `head = [9, 9, 9]`
- **Output:** `[1, 0, 0, 0]` ($999 + 1 = 1000$)

---

## 1. Bugs in Your Code

Your code has **6 distinct bugs** (both compilation errors and logical bugs):

### ❌ Compilation Bug 1: Function Name Mismatch / Typo
- Definition: `ListNode *reverveLL(ListNode *node)` (spelled with a `'v'`: `reverveLL`).
- Calls: `ListNode *l1 = reverseLL(head);` and `reverseLL(dummy->next);` (spelled with `'s'`).
- **Error:** `'reverseLL' was not declared in this scope`.

### ❌ Critical Logic Bug 2: `reverveLL` returns `curr` instead of `prev`
```cpp
while (curr) {
    ListNode *nextPtr = curr->next;
    curr->next = prev;
    prev = curr;
    curr = nextPtr;
}
return curr; // BUG! curr is NULL when the loop terminates!
```
- When the while loop finishes, `curr` is always `nullptr`.
- Returning `curr` returns `nullptr`, so `l1` immediately becomes `nullptr` and your entire logic collapses!
- **Fix:** `return prev;`

### ❌ Compilation Bug 3: Type Mismatch with `dummy`
```cpp
ListNode dummy = new ListNode(0); // BUG!
ListNode *tail = dummy;           // BUG!
```
- `new ListNode(0)` returns a pointer `ListNode*`, but `dummy` is declared as an object `ListNode`.
- **Fix:** `ListNode dummy(0); ListNode *tail = &dummy;`

### ❌ Compilation Bug 4: Member Name `val` vs `data`
- Your struct defines `int val;`, but in the loop you access `l1->data` and `l2->data`.
- **Error:** `'struct ListNode' has no member named 'data'`.

### ❌ Infinite Stagnation Bug 5: `tail` is never advanced
```cpp
tail->next = new ListNode(sum);
// tail = tail->next; is MISSING!
```
- Because `tail` is never moved forward, every iteration overwrites `tail->next`.
- The output list will end up with only 1 single node!

### ❌ Math Bug 6: `sum /= 10` instead of `sum % 10`
```cpp
int sum = val1 + val2 + carry;
carry = sum / 10;
sum /= 10; // BUG!
tail->next = new ListNode(sum);
```
- If `val1 = 9, carry = 1` $\rightarrow$ `sum = 10`.
- `carry = 10 / 10 = 1`.
- `sum /= 10` sets `sum` to `1` instead of the digit `0`!
- The node digit must be `sum % 10`.

---

## 2. Optimized Approaches

### Approach 1: Reverse $\rightarrow$ Add 1 In-Place $\rightarrow$ Reverse Back (Iterative)
You do **not** need to create new nodes for every digit or create a separate `l2` list!
1. Reverse the list.
2. Traverse in-place with `carry = 1`. For each node:
   - `sum = curr->val + carry;`
   - `curr->val = sum % 10;`
   - `carry = sum / 10;`
   - If `carry == 0`, you can **break early**! (e.g. for `456`, modifying `6 -> 7` leaves `carry = 0`, no need to touch `5` or `4`).
3. If after the loop `carry == 1` (e.g., `999 -> 000`), append a new node `new ListNode(1)`.
4. Reverse the list back and return.

### Approach 2: Recursive Backtracking (No Reversal Needed!)
Since we need to add from right to left, recursion naturally processes from tail to head on the way back (unwinding the call stack):
1. Recurse to the end of the list.
2. Base case: `if (!curr) return 1;` (this initial 1 is the +1 we want to add!).
3. At each node:
   - `sum = curr->val + carry;`
   - `curr->val = sum % 10;`
   - `return sum / 10;` (propagate carry back to previous digit).
4. In main function:
   - If the return carry from the head is `1` (e.g. `999 -> 000` with carry `1`), prepend `new ListNode(1)` before `head`.

#### Step-by-Step Call Stack Trace on `[1] -> [2] -> [9] -> NULL` ($129 + 1 = 130$)

**Phase 1: Winding the Call Stack (Diving to the Tail)**
```
addOneHelper([1])
 └──> addOneHelper([2])
       └──> addOneHelper([9])
             └──> addOneHelper(NULL)  --> Hits Base Case! Returns carry = 1
```

**Phase 2: Unwinding the Call Stack (Right-to-Left Arithmetic)**

1. **At Node `[9]`:**
   - Receives: `carry = 1` (from `NULL`)
   - Computes: `sum = 9 + 1 = 10`
   - Updates Node: `node->val = 10 % 10 = 0`
   - Returns to caller: `carry = 10 / 10 = 1`
   - *List state so far:* `[1] -> [2] -> [0]`

2. **At Node `[2]`:**
   - Receives: `carry = 1` (from Node `[9]`)
   - Computes: `sum = 2 + 1 = 3`
   - Updates Node: `node->val = 3 % 10 = 3`
   - Returns to caller: `carry = 3 / 10 = 0`
   - *List state so far:* `[1] -> [3] -> [0]`

3. **At Node `[1]`:**
   - Receives: `carry = 0` (from Node `[2]`)
   - Computes: `sum = 1 + 0 = 1`
   - Updates Node: `node->val = 1 % 10 = 1`
   - Returns to `addOne`: `carry = 1 / 10 = 0`
   - *List state so far:* `[1] -> [3] -> [0]`

**Phase 3: Final Check in `addOne()`**
- Return carry from head is `0`.
- No new node needed at the front.
- Returns `head` pointing to `[1] -> [3] -> [0] -> NULL`.


---

## 3. Complexity Comparison

| Metric | Your Approach (Fixed) | Optimal Iterative | Optimal Recursive ⭐ |
| :--- | :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ (with early exit) | $\mathcal{O}(N)$ |
| **Auxiliary Space** | $\mathcal{O}(N)$ (new nodes) | $\mathcal{O}(1)$ in-place | $\mathcal{O}(N)$ call stack |
| **Reversals Needed** | 2 | 2 | **0 (No list reversal!)** |

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
    // Helper for Approach 2: Recursive carry propagation
    int addOneHelper(ListNode* node) {
        if (node == nullptr) {
            return 1; // Base case: return the +1 to add
        }

        int carry = addOneHelper(node->next);
        int sum = node->val + carry;
        node->val = sum % 10;
        return sum / 10; // Return carry to caller
    }

public:
    // =========================================================================
    // Technique 1: Clean Recursive (Most Elegant, No Reversing)
    // =========================================================================
    ListNode* addOneRecursive(ListNode* head) {
        int carry = addOneHelper(head);

        // If carry propagated all the way past the head (e.g., 999 -> 1000)
        if (carry > 0) {
            ListNode* newHead = new ListNode(carry);
            newHead->next = head;
            return newHead;
        }

        return head;
    }

    // =========================================================================
    // Technique 2: In-Place Reverse -> Add 1 -> Reverse Back (O(1) Auxiliary Space)
    // =========================================================================
    ListNode* reverseLL(ListNode* head) {
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

    ListNode* addOneIterative(ListNode* head) {
        // 1. Reverse to access least significant digit first
        head = reverseLL(head);

        // 2. Add 1 in-place
        ListNode* curr = head;
        ListNode* prev = nullptr;
        int carry = 1;

        while (curr != nullptr && carry > 0) {
            int sum = curr->val + carry;
            curr->val = sum % 10;
            carry = sum / 10;
            prev = curr;
            curr = curr->next;
        }

        // If carry remains after the last node (e.g. 999)
        if (carry > 0) {
            prev->next = new ListNode(carry);
        }

        // 3. Reverse back to restore original order
        return reverseLL(head);
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

// Helper: build list
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

    // Test Case 1: 4 -> 5 -> 6 (Result: 4 -> 5 -> 7)
    ListNode* head1 = buildList({4, 5, 6});
    cout << "Original 1: ";
    printList(head1);
    head1 = sol.addOneRecursive(head1);
    cout << "Added 1 (Recursive): ";
    printList(head1);

    // Test Case 2: 9 -> 9 -> 9 (Result: 1 -> 0 -> 0 -> 0)
    ListNode* head2 = buildList({9, 9, 9});
    cout << "\nOriginal 2: ";
    printList(head2);
    head2 = sol.addOneIterative(head2);
    cout << "Added 1 (Iterative): ";
    printList(head2);

    return 0;
}
```
