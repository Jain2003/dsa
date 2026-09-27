# When & Why Time Complexity Reduces from $O(N)$ to $O(\log N)$

## Core Principle: The "Halving" Rule

The fundamental difference between $O(N)$ and $O(\log N)$ comes down to **how much work is eliminated at each step**:

- **$O(N)$ (Linear Time):** At each step, you eliminate **1 element** (or inspect 1 element). To process $N$ elements, you need $N$ steps.
- **$O(\log N)$ (Logarithmic Time):** At each step, you eliminate a **constant fraction (usually 50%)** of the remaining problem size!

### The Math Behind $O(\log_2 N)$:
Starting with $N$ items:
- **Step 1:** $\frac{N}{2}$ items remaining
- **Step 2:** $\frac{N}{4}$ items remaining
- **Step 3:** $\frac{N}{8}$ items remaining
- ...
- **Step $k$:** $\frac{N}{2^k} = 1 \implies 2^k = N \implies k = \log_2 N$ steps!

---

## 4 Logical Scenarios Where Time Complexity Reduces to $O(\log N)$

### Scenario 1: Halving the Search Space (Binary Search on Arrays)

When an array has a **monotonic property** (e.g. sorted numbers `[1, 3, 5, 8, 12]` or predicate states `[F, F, F, T, T]`), checking the middle element allows you to **discard 50% of the array instantly**.

- **$O(N)$ Naive:** Scanning left-to-right (`for (int i = 0; i < n; i++)`).
- **$O(\log N)$ Optimized:** Halving using `mid = low + (high - low) / 2`.
- **LeetCode Patterns:**
  - Search in Rotated Sorted Array (LeetCode 33)
  - Find Minimum in Rotated Sorted Array (LeetCode 154)
  - Find Peak Element (LeetCode 162)

---

### Scenario 2: Binary Search on Answer / Solution Space

Instead of searching inside a given array, you are searching for an **optimal value $X$ inside a numerical range `[min_possible, max_possible]`**.

- **$O(N)$ Naive:** Testing candidates $1, 2, 3, 4, \dots, \text{max}$ one by one.
- **$O(\log N)$ Optimized:** Binary searching the candidate answer. If speed $X$ works, test $\frac{X}{2}$ (or vice versa).
- **LeetCode Patterns:**
  - Koko Eating Bananas (LeetCode 875)
  - Capacity To Ship Packages Within D Days (LeetCode 1011)
  - Square Root (`sqrt(x)`) (LeetCode 69)

---

### Scenario 3: Tree-Based Hierarchical Division (Balanced Trees)

In a **Balanced Binary Search Tree (BST)**, Red-Black Tree, or Heap, every node splits its subtree into left and right branches.

- **$O(N)$ Naive:** Searching an unordered array/linked list requires inspecting every node.
- **$O(\log N)$ Optimized:** Traversing down 1 level in a balanced BST eliminates the entire opposite subtree.
- **C++ STL Examples:**
  - `std::set::find()` and `std::map::find()` ($O(\log N)$ guaranteed via Red-Black Tree).

---

### Scenario 4: Repeated Doubling / Halving Math Operations

When a numerical loop multiplies or divides its loop variable by a factor $k \ge 2$ instead of doing `i++` or `i--`.

- **$O(N)$ Naive:** `for (int i = 0; i < n; i++)` $\rightarrow N$ steps.
- **$O(\log N)$ Optimized:** `for (int i = 1; i < n; i *= 2)` $\rightarrow \log_2 N$ steps.

#### Classic Examples:
1. **Binary Exponentiation ($x^n$):** 
   - Naive: Multiply $x$ by itself $n$ times ($O(n)$).
   - Optimized: $x^8 = (x^4)^2 = ((x^2)^2)^2$ (3 multiplications = $O(\log n)$).
2. **Euclidean GCD Algorithm:** `gcd(a, b) = gcd(b, a % b)` (Halves values logarithmically).

---

## Comparison Table: $O(N)$ vs $O(\log N)$

| Input Size ($N$) | $O(N)$ Linear Operations | $O(\log_2 N)$ Logarithmic Operations | Speedup Factor |
| :--- | :--- | :--- | :--- |
| **100** | 100 | **7** | $\approx 14\times$ faster |
| **1,000,000 ($10^6$)** | 1,000,000 | **20** | $\mathbf{50,000\times}$ **faster** |
| **1,000,000,000 ($10^9$)** | 1,000,000,000 | **30** | $\mathbf{33,000,000\times}$ **faster** |

---

## Summary Checklist to Spot $O(\log N)$ Opportunities

Ask yourself these 3 questions:
1. *"Can I discard half of the choices at every step without looking at them?"*
2. *"Is the input sorted or monotonic (`FFF...TTT`)?"*
3. *"Am I doubling or halving my loop state at each step (`i *= 2` or `n /= 2`)?"*

If the answer to any of these is **YES**, the problem can be reduced from **$O(N)$ to $O(\log N)$**!
