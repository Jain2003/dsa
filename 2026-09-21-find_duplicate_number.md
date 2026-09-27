# Find the Duplicate Number (Floyd's Tortoise and Hare Cycle Detection)

## Problem Summary
Given an array of $N$ integers where elements range from $0$ to $N-2$ (or $1$ to $N-1$), find the duplicate element under strict constraints:
1. **Time Complexity:** $O(N)$
2. **Space Complexity:** $O(1)$ auxiliary memory
3. **Read-Only:** Must NOT modify the input array.

---

## 1. Why a Duplicate Must Exist (Pigeonhole Principle)
- An array of length $N$ has $N$ slots.
- If values range from $0$ to $N-2$, there are only $N-1$ unique possible values.
- **Pigeonhole Principle:** Placing $N$ items into $N-1$ slots guarantees at least **one repeated value (duplicate)**.

---

## 2. Why a Loop (Cycle) is 100% Guaranteed to Form

1. **You cannot fall off the array:** Every value in `nums` is a valid array index ($0$ to $N-1$). Jumping `index -> nums[index]` can continue infinitely without reaching `NULL`.
2. **Finite slots:** There are only $N$ total indices.
3. **Repeated State:** Taking $N$ jumps guarantees landing on an index you have already visited.
4. Landing on a previously visited index repeats the exact same sequence of jumps $\rightarrow$ **A closed loop (cycle) MUST form.**

---

## 3. Why the Duplicate Element is the Entry Node to the Cycle

A duplicate value $X$ means at least two different indices $A$ and $B$ store the same value $X$:
$$\text{nums}[A] = X \quad \text{and} \quad \text{nums}[B] = X$$

In pointer terms, Node $A$ points to $X$ and Node $B$ points to $X$.
Node $X$ acts as a **Funnel / Merge Point** where two separate paths collide:
- **Path 1:** From the start (tail / driveway).
- **Path 2:** From inside the cycle (looping back around).

```
Tail (from Start) ---------> [ X ] (Duplicate / Cycle Entry)
                               |
                               v
Loop Path -----------------> [ Next Node ] ----> [ Next Node ] --+
                               ^                                 |
                               |                                 |
                               +---------------------------------+
```
Because $X$ has two incoming arrows, **$X$ is the exact entrance (Gate) to the cycle**.

---

## 4. Why Index $N-1$ (or 0) is Guaranteed to be a Driveway (Tail)

In a 0-based array of size $N$ with values in $[0, N-2]$:
- No element can ever hold the value $N-1$.
- No node has an arrow pointing TO index $N-1$.
- **Index $N-1$ can NEVER be inside a cycle!** It is guaranteed to be a tail node that leads into the cycle.

---

## 5. The Lollipop Track Analogy ðŸ­ (Intuition)

Imagine a track shaped like a Lollipop:
- **Driveway (Tail):** Length $C$ meters from `START` to `GATE`.
- **Circular Track:** Perimeter $L$ meters.
- **GATE:** The duplicate element $X$.

```
    Driveway (Length C)                  Circular Track (Perimeter L)
[ START ] ============================> [ GATE (Duplicate) ]
                                              ||
                                              \/
                                          (  Circle  )
                                              ||
                                              \/
                                       [ Meeting Point M ]
```

### Phase 1: Fast & Slow Pointer Collision
- **Slow** moves 1 step per iteration (`slow = nums[slow]`).
- **Fast** moves 2 steps per iteration (`fast = nums[nums[fast]]`).
- Fast enters the circle, loops around, and catches Slow from behind at **Meeting Point $M$**.

### Phase 2: Finding the Gate (Duplicate)
- Place **`finder`** at `START` (index 0). Keep **`slow`** at `Meeting Point M`.
- Both move at the **same speed (1 step at a time)**.
- **The Magic Math:** 
  - `finder` needs $C$ steps to reach the `GATE`.
  - `slow` needs $C$ steps around the circle to reach the `GATE`.
  - Both take $C$ steps and **collide on the GATE simultaneously**!

---

## 6. Mathematical Proof of Phase 2

Let:
- $C$ = length of driveway from `START` to `GATE`.
- $L$ = length of the cycle loop.
- $d$ = distance from `GATE` to collision point $M$.

When `slow` and `fast` collide in Phase 1:
$$\text{Distance of Slow} = C + d$$
$$\text{Distance of Fast} = C + d + k \cdot L \quad (\text{where } k \text{ is number of loops})$$

Since Fast moves twice as fast as Slow:
$$2(C + d) = C + d + k \cdot L$$
$$C + d = k \cdot L \implies \mathbf{C = k \cdot L - d}$$

**Interpretation:**
- $k \cdot L - d$ is the remaining distance from Meeting Point $M$ around the loop back to the `GATE`.
- Since $C = k \cdot L - d$, moving $C$ steps from `START` and $C$ steps from $M$ lands both pointers on the **GATE (Duplicate Element)** at the exact same second!

---

## 7. Step-by-Step Trace for `nums = [1, 3, 4, 2, 2]`

### Pointer Graph:
`0 -> 1 -> 3 -> 2 <-> 4` (Cycle entry = Node 2)

### Phase 1:
- Start: `slow = 0`, `fast = 0`
- Iter 1: `slow = 1`, `fast = 3`
- Iter 2: `slow = 3`, `fast = 4`
- Iter 3: `slow = 2`, `fast = 4`
- Iter 4: `slow = 4`, `fast = 4` $\rightarrow$ **Collision at index 4!**

### Phase 2:
- Setup: `finder = 0`, `slow = 4`
- Iter 1: `slow = 2`, `finder = 1`
- Iter 2: `slow = 4`, `finder = 3`
- Iter 3: `slow = 2`, `finder = 2` $\rightarrow$ **Collision at index 2!**

**Result:** Duplicate = **`2`**.

---

## 8. Common Implementation Bug & Fix

**Wrong Code (Phase 2):**
```cpp
int finder = 0;
while (true) {
    slow = nums[slow];
    fast = nums[fast]; // BUG! 'fast' was already equal to 'slow' from Phase 1!
    if (slow == fast) break; // Breaks immediately on iteration 1!
}
```

**Correct Code (Phase 2):**
```cpp
int finder = 0;
while (slow != finder) {
    slow = nums[slow];
    finder = nums[finder]; // Correctly advances finder from index 0!
}
return slow;
```

---

## Complexity Analysis

| Metric | Complexity | Explanation |
| :--- | :--- | :--- |
| **Time Complexity** | **$O(N)$** | Phase 1 takes $\le 2N$ steps; Phase 2 takes $\le N$ steps. |
| **Space Complexity** | **$O(1)$** | Uses only 3 integer variables (`slow`, `fast`, `finder`). |
| **Array Mutability** | **Read-Only** | Does not modify or swap any elements in the input array. |

## Code

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        // Fast I/O for LeetCode
        ios_base::sync_with_stdio(false);
        cin.tie(NULL);

        // Phase 1: Fast & Slow pointers to find collision point inside the cycle
        int slow = 0;
        int fast = 0;

        while (true) {
            slow = nums[slow];
            fast = nums[nums[fast]];

            if (slow == fast)
                break;
        }

        // Phase 2: Start finder from index 0 and move slow & finder at equal speed
        int finder = 0;

        while (slow != finder) {
            slow = nums[slow];
            finder = nums[finder]; // MUST use finder here, NOT fast!
        }

        return slow; // Cycle entry point is the duplicate element
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 3, 4, 2, 2};
    cout << "Duplicate number: " << sol.findDuplicate(nums) << endl; // Output: 2
    return 0;
}
```
