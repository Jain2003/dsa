# Intersection of Two Linked Lists (LeetCode 160)

## Problem Description
Given the heads of two singly linked-lists `headA` and `headB`, return the node at which the two lists intersect. If the two linked lists have no intersection at all, return `null`.

The test cases are generated such that there are no cycles anywhere in the entire linked structure.

**Note:** The linked lists must retain their original structure after the function returns.

**Example 1:**
- **Input:** `intersectVal = 8`, `listA = [4, 1, 8, 4, 5]`, `listB = [5, 6, 1, 8, 4, 5]`, `skipA = 2`, `skipB = 3`
- **Output:** Intersected at node with value `8`.

**Example 2:**
- **Input:** `intersectVal = 2`, `listA = [1, 9, 1, 2, 4]`, `listB = [3, 2, 4]`, `skipA = 3`, `skipB = 1`
- **Output:** Intersected at node with value `2`.

**Example 3:**
- **Input:** `intersectVal = 0`, `listA = [2, 6, 4]`, `listB = [1, 5]`, `skipA = 3`, `skipB = 2`
- **Output:** `null` (No intersection).

---

## 1. Approaches & Progression

### Approach 1: Hash Set (Brute Force / Memory Overhead)
- Traverse List A and store every node pointer in an `unordered_set<ListNode*>`.
- Traverse List B and check if the current pointer exists in the set.
- The first common pointer found is the intersection node.
- **Time Complexity:** $\mathcal{O}(M + N)$
- **Space Complexity:** $\mathcal{O}(M)$ auxiliary space (violates the $\mathcal{O}(1)$ space requirement).

---

### Approach 2: Length Difference Alignment
1. Traverse both lists to find lengths $L_A$ and $L_B$.
2. The difference $d = |L_A - L_B|$ tells us how many extra nodes the longer list has before the potential intersection.
3. Advance the pointer of the longer list by $d$ steps.
4. Now both pointers are equidistant from the end. Move both pointers one step at a time until `ptrA == ptrB`.
- **Time Complexity:** $\mathcal{O}(2M + 2N) = \mathcal{O}(M + N)$
- **Space Complexity:** $\mathcal{O}(1)$

---

### Approach 3: Two-Pointer Switching ("The Great Equalizer") ⭐
Instead of counting lengths explicitly, we can achieve the exact same alignment implicitly:

#### The Core Mathematical Insight
Let:
- $a = \text{length of unique nodes in List A}$
- $b = \text{length of unique nodes in List B}$
- $c = \text{length of shared nodes (intersection to end)}$

```
List A: [a nodes] --------\
                           [c nodes] -> NULL
List B: [b nodes] --------/
```

- If pointer `pA` starts at `headA`, traverses all of List A, and when it hits `nullptr`, redirects to `headB`:
  $$\text{Total distance traveled by } pA = (a + c) + b$$
- If pointer `pB` starts at `headB`, traverses all of List B, and when it hits `nullptr`, redirects to `headA`:
  $$\text{Total distance traveled by } pB = (b + c) + a$$

Notice:
$$(a + c) + b = (b + c) + a = a + b + c$$

Both pointers travel the **exact same total distance**! 
Therefore, on their second pass, they will hit the intersection node at the **exact same step**.

#### What if there is NO intersection ($c = 0$)?
- `pA` travels $a$ nodes $\to$ hits `nullptr` $\to$ switches to `headB` $\to$ travels $b$ nodes $\to$ hits `nullptr`.
- `pB` travels $b$ nodes $\to$ hits `nullptr` $\to$ switches to `headA` $\to$ travels $a$ nodes $\to$ hits `nullptr`.
- Both pointers travel exactly $a + b$ steps and hit `nullptr` at the **exact same iteration**: `pA == pB == nullptr`.
- The loop condition `pA != pB` evaluates to `nullptr != nullptr` (which is `false`), so the loop terminates and cleanly returns `nullptr`!

---

## 2. Visual Walkthrough

### Case 1: With Intersection
```
List A: [4] -> [1] \
                    -> [8] -> [4] -> [5] -> NULL
List B: [5] -> [6] -> [1] /

pA path: 4 -> 1 -> 8 -> 4 -> 5 -> NULL -> (switches to B) -> 5 -> 6 -> 1 -> [8]
pB path: 5 -> 6 -> 1 -> 8 -> 4 -> 5 -> NULL -> (switches to A) -> 4 -> 1 -> [8]
                                                                             ^
                                                             Both meet here!
```

### Case 2: Completely Disjoint Lists (No Intersection)
Let `List A = [1, 2, 3]` ($m = 3$) and `List B = [4, 5]` ($n = 2$).

| Iteration | `pA` Points To | `pB` Points To | Note |
| :--- | :--- | :--- | :--- |
| **Start** | `Node(1)` | `Node(4)` | `pA != pB` |
| **Step 1** | `Node(2)` | `Node(5)` | `pA != pB` |
| **Step 2** | `Node(3)` | `nullptr` | `pB` hits end of List B |
| **Step 3** | `nullptr` | `Node(1)` (switches to A) | `pA` hits end of List A; `pB` restarts at A |
| **Step 4** | `Node(4)` (switches to B) | `Node(2)` | `pA` restarts at B |
| **Step 5** | `Node(5)` | `Node(3)` | |
| **Step 6** | `nullptr` | `nullptr` | **Both hit `nullptr` at the exact same step!** |

At Step 6: `pA == nullptr` and `pB == nullptr`.
Loop condition `while (pA != pB)` sees `nullptr != nullptr` $\implies$ **`false`**!
Loop terminates and returns `pA` (`nullptr`).


---

## 3. Complexity Comparison

| Metric | Hash Set | Length Difference | Two-Pointer Switching ⭐ |
| :--- | :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(M + N)$ | $\mathcal{O}(M + N)$ | $\mathcal{O}(M + N)$ |
| **Space Complexity** | $\mathcal{O}(M)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |
| **Passes** | 2 passes | 2-3 passes | **1-2 passes (Extremely concise)** |
| **Code Length** | Moderate | Long & verbose | **5 lines of core logic** |

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
    // Optimal: Two-Pointer Switching (O(M + N) Time, O(1) Space)
    // =========================================================================
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }

        ListNode* pA = headA;
        ListNode* pB = headB;

        // Either they meet at the intersection node,
        // or both reach nullptr simultaneously (no intersection)
        while (pA != pB) {
            pA = (pA == nullptr) ? headB : pA->next;
            pB = (pB == nullptr) ? headA : pB->next;
        }

        return pA;
    }
};

int main() {
    Solution sol;

    // Build common tail: [8, 4, 5]
    ListNode* common = new ListNode(8, new ListNode(4, new ListNode(5)));

    // List A: [4, 1] -> [8, 4, 5]
    ListNode* headA = new ListNode(4, new ListNode(1, common));

    // List B: [5, 6, 1] -> [8, 4, 5]
    ListNode* headB = new ListNode(5, new ListNode(6, new ListNode(1, common)));

    ListNode* intersection = sol.getIntersectionNode(headA, headB);
    if (intersection) {
        cout << "Intersected at node with value: " << intersection->val << endl;
    } else {
        cout << "No intersection." << endl;
    }

    // Test with no intersection
    ListNode* noIntersectA = new ListNode(1, new ListNode(2));
    ListNode* noIntersectB = new ListNode(3, new ListNode(4));
    ListNode* res = sol.getIntersectionNode(noIntersectA, noIntersectB);
    cout << "No intersection test result: " << (res == nullptr ? "NULL (Passed)" : "Failed") << endl;

    return 0;
}
```
