# General DSA & C++ Learnings

This document serves as a central knowledge repository for general concepts, key insights, language quirks, and optimization tricks learned during problem-solving.

---

## 1. Data Types & Memory Limits
- **`int` Size:** In standard C++ / Java / C#, `int` is a 32-bit signed integer.
  - **Range:** \([-2,147,483,648 \text{ to } 2,147,483,647]\) (\(\approx \pm 2.14 \times 10^9\)).
- **`long long` Size:** 64-bit signed integer, range up to \(\approx \pm 9.22 \times 10^{18}\).

---

## 2. Modulo & Division Behavior with Negative Numbers
- **C++ / Java / C# / JavaScript (Truncated Remainder):**
  - Sign of `n % k` matches the sign of the dividend `n`.
  - Example: `-123 % 10 = -3`.
  - *To get a positive remainder in C++:* `(n % k + k) % k`.
- **Python (Floored Modulo):**
  - Sign of `n % k` matches the sign of the divisor `k`.
  - Example: `-123 % 10 = 7`.

---

## 3. Preventing Signed Integer Overflow in C++
- Signed integer overflow is **Undefined Behavior** in C++.
- **Wrong:** `if (rev * 10 > INT_MAX)` — `rev * 10` overflows *before* the `>` comparison occurs!
- **Right (Check Before Multiplying):**
  - `if (rev > INT_MAX / 10 || rev < INT_MIN / 10) return 0;`
- **Alternative:** Perform calculations using `long long` and check boundaries before casting back to `int`.

---

## 4. C++ Fast I/O Optimization
```cpp
ios_base::sync_with_stdio(false);
cin.tie(NULL);
```
- **What it does:** Unties `cin`/`cout` from C's `stdio` (`scanf`/`printf`) and prevents automatic flushing before reads.
- **Impact:** Drastically lowers actual wall-clock execution time (0ms ranks on LeetCode).
- **Note:** Does not change theoretical asymptotic time complexity \(O(f(n))\). Avoid mixing `scanf`/`printf` with `cin`/`cout` when enabled.

---

## 5. Exponentiation Methods
- **`std::pow(base, exp)` (`<cmath>`):** Calculates floating-point power. Watch out for precision truncation when casting to `int`.
- **Binary Exponentiation (\(O(\log n)\)):** Best for exact integer power and modular exponentiation `(base^exp) % MOD`.

---

## 6. Vector Initialization & Constructor Syntax in C++

Syntax: `std::vector<Type> name(size, fill_value);`

- **Empty Vector (Size 0):**
  - `vector<int> dp;` \(\rightarrow\) `[]`
- **Single-Element Initializer List:**
  - `vector<int> dp = {0};` \(\rightarrow\) `[0]`
- **Size `N` Default-Initialized to `0`:**
  - `vector<int> dp(n);` \(\rightarrow\) `[0, 0, 0, ..., 0]` (Size = `n`)
- **Size `N` Filled with Custom Value (e.g. `-1` or `INT_MAX`):**
  - `vector<int> dp(n + 1, -1);` \(\rightarrow\) `[-1, -1, ..., -1]` (Size = `n + 1`)
  - *Common DP Use Case:* Pre-allocates space from index `0` to `n` and fills all slots with `-1` (representing uncomputed states).
- **2D Vector (`R x C`) Filled with Custom Value (e.g. `-1`):**
  - `vector<vector<int>> dp(R, vector<int>(C, -1));` \(\rightarrow\) Creates a matrix of `R` rows and `C` columns, all filled with `-1`.


---

## 7. Time Complexity of `std::swap` in C++

| Data Type | Time Complexity | How It Works Under the Hood |
| :--- | :--- | :--- |
| **Primitives & Pointers** (`int`, `double`, `char*`) | **\(O(1)\)** | Swaps primitive scalar values directly in CPU registers. |
| **Dynamic STL Containers** (`std::vector`, `std::string`, `std::unordered_map`) | **\(O(1)\)** | **Pointer Swap:** Swaps internal heap memory pointers, size, and capacity fields. Does *not* copy elements! |
| **Fixed-Size Arrays** (`int arr[N]`, `std::array<T, N>`) | **\(O(N)\)** | **Element-by-Element Swap:** Memory is contiguous on stack/object; every element must be swapped individually. |

---

## 8. C++ STL `push_back()` Mechanism & Amortized Time Complexity

- **Purpose:** Member function used to insert a new element at the end of dynamic STL containers (`std::vector`, `std::deque`, `std::list`).
- **Syntax:** `container.push_back(value);`
- **How it Works Under the Hood:**
  - Adds a copy or moves the given element into the end position and increments size by 1.
  - **Dynamic Capacity Reallocation:** When `size == capacity`:
    1. Allocates a new, larger contiguous memory block (typically **2x** or **1.5x** the current capacity).
    2. Copies/moves existing elements to the new block.
    3. Frees the old memory block.
    4. Appends the new element.
- **Time Complexity:**
  - **Amortized Time:** **\(O(1)\)** on average (since doubling capacity makes expensive reallocations rare).
  - **Worst-Case Time:** **\(O(N)\)** when capacity reallocation occurs.
- **Optimization Tips:**
  - **`reserve(N)`:** Pre-allocates memory for \(N\) elements if size is known in advance to avoid all intermediate reallocations.
  - **`push_back()` vs `emplace_back()`:** `emplace_back(args...)` constructs the element directly in-place at the end of the container, avoiding extra copy/move calls.

---

## 9. Fibonacci Sequence: Naive Recursion vs Array Storage (Dynamic Programming)

### A. Naive Recursion (\(O(2^N)\) Time, \(O(N)\) Stack Space)
- Computes `fib(N) = fib(N-1) + fib(N-2)` by creating a binary recursion tree.
- **Drawback:** Recalculates identical subproblems repeatedly (e.g., `fib(2)` is computed multiple times for `fib(4)`).

### B. Array / Vector Storage (\(O(N)\) Time, \(O(N)\) Space)
- Storing Fibonacci numbers sequentially in an array or `std::vector` avoids repeated work and reduces complexity from exponential \(O(2^N)\) to linear **\(O(N)\)**.

```cpp
#include <iostream>
#include <vector>

using namespace std;

// Generates and stores Fibonacci numbers up to N in a vector
vector<int> getFibonacciSequence(int N) {
    if (N < 0) return {};
    if (N == 0) return {0};

    vector<int> fib(N + 1);
    fib[0] = 0;
    fib[1] = 1;

    for (int i = 2; i <= N; i++) {
        fib[i] = fib[i - 1] + fib[i - 2];
    }

    return fib; // Contains [0, 1, 1, 2, 3, ...]
}

int main() {
    int N = 4;
    vector<int> fibs = getFibonacciSequence(N);
    
    for (int val : fibs) {
        cout << val << " "; // Output: 0 1 1 2 3
    }
    cout << endl;
    return 0;
}
```

### C. Recursive Array Storage (Memoization / Top-Down DP)
- Pass a `vector<int>` **by reference** to the recursive function. As recursion unwinds, store computed results directly into `dp[n]`.

```cpp
int solveFibMemo(int n, vector<int>& dp) {
    if (n <= 1) return dp[n] = n;
    if (dp[n] != -1) return dp[n]; // Return cached result
    return dp[n] = solveFibMemo(n - 1, dp) + solveFibMemo(n - 2, dp);
}

vector<int> getFibonacciArrayRecursive(int n) {
    vector<int> dp(n + 1, -1);
    solveFibMemo(n, dp);
    return dp; // Populated vector [0, 1, 1, 2, 3, ...]
}
```

### Complexity Comparison

| Approach | Time Complexity | Auxiliary Space | Key Note |
| :--- | :--- | :--- | :--- |
| **Naive Recursion** | \(O(2^N)\) | \(O(N)\) | High redundant computations. |
| **Iterative Vector Storage** | **\(O(N)\)** | **\(O(N)\)** | Fast bottom-up linear pass. |
| **Recursive Vector Storage (DP)** | **\(O(N)\)** | **\(O(N)\)** | Fills vector recursively with memoization. |
| **Space-Optimized Iterative** | **\(O(N)\)** | **\(O(1)\)** | Keeps only last two variables (`prev1`, `prev2`). |

---

## 10. Character Arithmetic & ASCII Offsets in C++

Characters in C++ are stored internally as integer **ASCII values**:
- `'a'` = `97`, `'b'` = `98`, ..., `'z'` = `122`
- `'A'` = `65`, `'B'` = `66`, ..., `'Z'` = `90`
- `'0'` = `48`, `'1'` = `49`, ..., `'9'` = `57`

### A. Subtracting Characters (`'b' - 'a'`)
- `'b' - 'a'` computes `98 - 97 = 1` (integer `1`).
- `'z' - 'a'` computes `122 - 97 = 25` (integer `25`).
- `'a' - 'a'` computes `97 - 97 = 0` (integer `0`).

### B. Common DSA Use Cases
1. **0-Indexed Frequency Array for Alphabets:**
   - Maps lowercase letters `'a'-'z'` into 0-based array indices `0` to `25`:
     ```cpp
     int freq[26] = {0};
     freq[ch - 'a']++; // e.g., 'c' - 'a' = 2, increments freq[2]
     ```
2. **Converting Character Digit to Integer Value:**
   - Subtracting `'0'` converts a numeric character into its integer value:
     ```cpp
     char c = '7';
     int num = c - '0'; // '7' (55) - '0' (48) = 7
     ```

---

## 11. C++ STL `std::map` (Ordered Map) vs `std::unordered_map`

### A. `std::map` (Ordered Map)
- **Header:** `#include <map>`
- **Underlying Data Structure:** Red-Black Tree (Self-Balancing Binary Search Tree).
- **Key Ordering:** Stores keys in **sorted order** (ascending by default).
- **Time Complexity:**
  - Search / Insert / Delete: Guaranteed **\(O(\log N)\)**.
- **Key Types:** Works with any data type that supports the `<` operator (including `pair<int, int>`).
- **Special Features:** Supports range operations and binary search methods like `lower_bound()` and `upper_bound()`.

### B. `std::unordered_map` (Unordered Map)
- **Header:** `#include <unordered_map>`
- **Underlying Data Structure:** Hash Table (Bucket array with collision chaining).
- **Key Ordering:** **No specific order** (stored by hash key).
- **Time Complexity:**
  - Search / Insert / Delete: **Average \(O(1)\)**, **Worst-case \(O(N)\)** (when hash collisions occur for all keys).
- **Key Types:** Requires a `std::hash` specialization (works natively for `int`, `string`, `char`, but **not** `pair<int, int>` out of the box without a custom hash function).

---

### C. Comprehensive Comparison Table

| Feature | `std::map` (Ordered) | `std::unordered_map` (Unordered) |
| :--- | :--- | :--- |
| **Internal Data Structure** | Red-Black Tree (BST) | Hash Table |
| **Key Order** | Sorted (Ascending by key) | Unordered / Random |
| **Search Time** | \(O(\log N)\) (Guaranteed) | \(O(1)\) (Average), \(O(N)\) (Worst Case) |
| **Insertion Time** | \(O(\log N)\) (Guaranteed) | \(O(1)\) (Average), \(O(N)\) (Worst Case) |
| **Deletion Time** | \(O(\log N)\) (Guaranteed) | \(O(1)\) (Average), \(O(N)\) (Worst Case) |
| **Header File** | `#include <map>` | `#include <unordered_map>` |
| **Supports `pair<int,int>` as Key** | ✅ Yes (uses `<` operator) | ❌ No (requires custom hash function) |
| **Range Operations (`lower_bound`)**| ✅ Supported | ❌ Not Supported |
| **When to Use?** | Need sorted keys or range searching. | Need maximum lookup/insertion speed without caring about order. |

---

## 12. Map Traversal Methods in C++ (`std::map` & `std::unordered_map`)

Each entry in a C++ map is stored as a `std::pair<const KeyType, ValueType>`.
- `x.first` \(\rightarrow\) Accesses the **Key**
- `x.second` \(\rightarrow\) Accesses the **Value** (e.g., frequency count)

### A. Range-Based `for` Loop (`const auto&`) — Recommended
Using `const auto&` avoids copying each pair during iteration:
```cpp
for (const auto& x : mp) {
    cout << x.first << " " << x.second << "\n";
}
```

### B. C++17 Structured Binding (Cleanest Modern C++)
Unpacks the key and value directly into descriptive variable names:
```cpp
for (const auto& [key, value] : mp) {
    cout << key << " " << value << "\n";
}
```

### C. Traditional Iterator Loop
```cpp
for (auto it = mp.begin(); it != mp.end(); ++it) {
    cout << it->first << " " << it->second << "\n";
}
```

### Important Behavior Note
- **`std::map`:** Traversal visits keys in **strictly sorted ascending order**.
- **`std::unordered_map`:** Traversal visits keys in **arbitrary hash bucket order**.

---

## 13. Sorting in C++ using STL (`std::sort`)

- **Header Required:** `#include <algorithm>`
- **Underlying Algorithm:** **IntroSort** (Hybrid of QuickSort, HeapSort, and InsertionSort).
  - Guarantees worst-case **\(O(N \log N)\)** time complexity!
- **Stability:** `std::sort` is **unstable** (relative order of duplicate elements is not preserved). Use `std::stable_sort()` if stability is required.

### A. Basic Usage Syntax

1. **Vectors (`std::vector`):**
   ```cpp
   sort(vec.begin(), vec.end());                     // Ascending order (default)
   sort(vec.begin(), vec.end(), greater<int>());     // Descending order
   sort(vec.begin() + 1, vec.begin() + 4);           // Range sort [index 1 to 3]
   ```

2. **C-Style Fixed Arrays:**
   ```cpp
   int arr[] = {5, 2, 8, 1, 3};
   int n = sizeof(arr) / sizeof(arr[0]);
   sort(arr, arr + n);                               // Ascending order
   sort(arr, arr + n, greater<int>());               // Descending order
   ```

3. **Strings (`std::string`):**
   ```cpp
   string s = "dcba";
   sort(s.begin(), s.end());                         // Result: "abcd"
   ```

### B. Custom Comparators (Lambda / Functor)
When sorting custom structures (like `pair<int, int>` or custom structs), pass a custom boolean comparison function:

```cpp
// Example: Sort vector of pairs by SECOND element in ascending order
sort(vec.begin(), vec.end(), [](const pair<int,int>& a, const pair<int,int>& b) {
    return a.second < b.second; // Return true if 'a' comes strictly before 'b'
});
```
> **⚠️ Critical Rule (Strict Weak Ordering):** Comparators MUST return `true` only when `a < b`. For equal elements (`a == b`), ALWAYS return `false`! Using `<=` or `>=` violates strict weak ordering and can cause segmentation faults or runtime crashes in `std::sort`.

### C. Complexity Summary
- **Time Complexity:** **\(O(N \log N)\)** in Best, Average, and Worst cases.
- **Space Complexity:** **\(O(\log N)\)** auxiliary stack space.

---

## 14. C++ Lambda Expressions & Capture List (`[]`)

In C++, `[]` introduces a **Lambda Expression** (an unnamed inline function). `[]` is specifically called the **Capture Clause / Capture List**.

### Full Anatomy of a C++ Lambda:
```cpp
[ capture_list ] ( parameter_list ) { function_body }
```

### What `[]` (Capture List) Controls:
It specifies which variables from the **surrounding outer scope** the lambda function is allowed to access inside its body:

| Capture Syntax | Meaning | Description |
| :--- | :--- | :--- |
| **`[]`** | Empty Capture | Captures **nothing** from outer scope. Only uses input parameters (`a`, `b`). |
| **`[=]`** | Capture All by Value | Copies all outer local variables into the lambda (read-only). |
| **`[&]`** | Capture All by Reference | Passes all outer local variables by reference (can read & modify outer variables). |
| **`[x]`** | Capture `x` by Value | Copies only variable `x` into the lambda. |
| **`[&x]`** | Capture `x` by Reference | Passes only variable `x` by reference. |

### Example: Using Capture List in `std::sort()`
```cpp
int target = 10;

// [target] captures 'target' from outer scope to use inside comparison
sort(vec.begin(), vec.end(), [target](int a, int b) {
    return abs(a - target) < abs(b - target); // Sorts elements by proximity to 'target'
});
```

---

## 15. Selection Sort vs. Bubble Sort Key Mechanics

| Feature | Selection Sort | Bubble Sort |
| :--- | :--- | :--- |
| **First Sorted Element** | Finds **minimum element** and places it at **index 0** (leftmost). | "Bubbles" **maximum element** to **index N-1** (rightmost). |
| **Building Direction** | Builds sorted portion **Left-to-Right** (`0` to `N-1`). | Builds sorted portion **Right-to-Left** (`N-1` down to `0`). |
| **Core Operation** | Scans array for min index, then performs **at most 1 swap** per pass. | Compares adjacent pairs `(arr[j], arr[j+1])` and performs **multiple adjacent swaps**. |
| **Number of Swaps** | **\(O(N)\)** swaps total (at most \(N-1\) swaps). | **\(O(N^2)\)** swaps in worst case. |
| **Best-Case Time** | **\(O(N^2)\)** (always scans unsorted portion for min). | **\(O(N)\)** (when optimized with a `swapped` boolean flag). |
| **Worst-Case Time** | **\(O(N^2)\)** | **\(O(N^2)\)** |
| **Stability** | **Unstable** (default min-swap can change duplicate order). | **Stable** (adjacent equal elements are never swapped). |

---

## 16. Bubble Sort Optimizations: `didSwap` & Range Reduction

### A. The `didSwap` Early Exit Flag (**Reduces Best-Case Time to \(O(N)\)**)
- **Problem in Naive Bubble Sort:** Executes all \(N^2\) comparisons even if array is already sorted.
- **Optimization:** Track if any swap occurs during an inner pass:
  ```cpp
  if (didSwap == 0) break; // Array is already sorted!
  ```
- **Result:** For an already sorted array (e.g. `[1, 2, 3, 4, 5]`), inner loop runs **only 1 pass** (\(N-1\) comparisons), sees `didSwap == 0`, and exits immediately. Reduces **Best-Case Time Complexity from \(O(N^2)\) to \(O(N)\)**.

### B. Inner Loop Range Reduction (`j <= i - 1`)
- Outer loop shrinks `i` from `n - 1` down to `0`.
- Inner loop scans `j` from `0` up to `i - 1`.
- **Why?** Each pass locks the maximum remaining element into index `i`.
- **Result:** Skips re-checking already sorted elements at the end, cutting total comparisons from \(N^2\) to \(\frac{N(N-1)}{2} \approx \frac{N^2}{2}\) (cuts comparisons by 50%).

---

## 17. Insertion Sort Mechanics & Characteristics

- **Real-World Analogy:** Like sorting playing cards in your hand.
- **Core Mechanism:** Takes one element at a time (`key = nums[i]`) and inserts it into its correct position inside the already-sorted left subarray by shifting larger elements right.
- **Time Complexity:**
  - **Best Case:** **\(O(N)\)** (When array is already sorted; inner shift loop condition `nums[j] > key` fails instantly).
  - **Worst Case:** **\(O(N^2)\)** (When array is reverse sorted).
  - **Average Case:** **\(O(N^2)\)**
- **Space Complexity:** **\(O(1)\)** (In-place sorting).
- **Stability:** **Stable** (Duplicate elements retain their original order).

---

## 18. Floyd's Cycle Detection in Arrays (Finding Duplicate in $O(N)$ Time & $O(1)$ Space)

- **Pigeonhole Principle:** $N$ elements with values ranging from $[0, N-2]$ (or $[1, N-1]$) guarantee at least 1 duplicate.
- **Array-to-Graph Mapping:** Treating `index -> nums[index]` turns the array into a directed pointer graph. The duplicate value acts as a **cycle entry node** where two paths merge.
- **Two-Phase Algorithm:**
  1. **Phase 1 (Find Intersection inside Cycle):** `slow = nums[slow]` (1 step), `fast = nums[nums[fast]]` (2 steps). Loop until `slow == fast`.
  2. **Phase 2 (Locate Cycle Entry Node / Duplicate):** Set `finder = start`. Advance both `finder` and `slow` by 1 step at a time (`finder = nums[finder]`, `slow = nums[slow]`). The intersection node where `finder == slow` is the duplicate number!
- **Complexity:** $O(N)$ Time, $O(1)$ Space, Read-only array.

---

## 19. Two Sum Optimization: Hash Map Lookup ($O(N^2) \rightarrow O(N)$)

- **Brute Force:** Two nested loops $\rightarrow O(N^2)$ time.
- **Hash Map Strategy:** As we iterate through `nums[i]`, we calculate `moreNeeded = target - nums[i]`. We look up if `moreNeeded` exists in a `unordered_map<int, int>` (storing `number -> index`).
- **Time Complexity:** **$O(N)$** average time ($O(1)$ map lookup per element).
- **Space Complexity:** **$O(N)$** auxiliary space for the hash map.

---

## 20. Sorting + Two-Pointer Technique Time & Space Complexity

When solving search-pair problems (like Two Sum) using sorting without a Hash Map:
1. **Store Pairs `(value, original_index)`:** $O(N)$ time & space.
2. **Sort Pairs by Value:** **$O(N \log N)$** time (the primary bottleneck).
3. **Two-Pointer Scan (`left = 0`, `right = N - 1`):** $O(N)$ time.

- **Total Time Complexity:** $\mathbf{O(N \log N)}$
- **Total Space Complexity:** $\mathbf{O(N)}$ (required to preserve original indices).

---

## 21. Constructing 2D Vectors from `map<Key, vector<Value>>` (CRITICAL REVISION PATTERN)

When grouping items using a map where values are stored as vectors (e.g. `map<string, vector<int>> mp`), build the final `vector<vector<T>> ans` cleanly using C++17 Structured Binding:

```cpp
vector<vector<string>> ans;

// C++17 Structured Binding over map<string, vector<int>>
for (auto& [key, indices] : mp) {
    vector<string> grp;
    for (int idx : indices) {
        grp.push_back(strs[idx]);
    }
    ans.push_back(grp);
}
```

### Why this pattern is essential:
1. **Prevents integer map indexing errors:** Maps use keys, NOT integer array indices (`mp[i]` is WRONG).
2. **Prevents out-of-bounds 2D vector access:** Creating a local `vector<string> grp;` and calling `ans.push_back(grp)` safely constructs the 2D vector without requiring pre-allocated size.

---

## 22. `std::map` Sorting Behavior: `first` vs `second`

- **Default Sorting Rule:** `std::map` is **ALWAYS sorted by the FIRST element (`key` / `pair.first`)** in ascending order.
- It completely ignores `pair.second` (the value) for sorting.

### How to Sort a Map by `second` (Value):
Because `std::map`'s underlying Red-Black Tree is indexed by key (`first`), you cannot change its internal sorting to `second` directly. 
To sort by `second`:
1. Copy the map entries into a `vector<pair<Key, Value>>`.
2. Sort the vector using `std::sort` with a custom comparator (`a.second < b.second`).

```cpp
map<string, int> mp = {{"banana", 5}, {"apple", 100}, {"cherry", 1}};

// 1. Copy map entries into a vector of pairs
vector<pair<string, int>> vec(mp.begin(), mp.end());

// 2. Sort vector by 'second' (Value) ascending
sort(vec.begin(), vec.end(), [](const pair<string, int>& a, const pair<string, int>& b) {
    return a.second < b.second;
});
// Result vector order: [ {"cherry", 1}, {"banana", 5}, {"apple", 100} ]
```

---

## 23. Sorting Frequency Maps by Frequency (`second`) in Descending Order

To sort elements by frequency descending (e.g., `2->3, 1->2, 3->1`):

```cpp
vector<int> nums = {1, 1, 2, 2, 2, 3};

// 1. Build frequency map
unordered_map<int, int> mp;
for (int num : nums) {
    mp[num]++;
}

// 2. Copy map elements into a vector of pairs
vector<pair<int, int>> freqVec(mp.begin(), mp.end());

// 3. Sort vector by 'second' (frequency) in DESCENDING order
sort(freqVec.begin(), freqVec.end(), [](const pair<int, int>& a, const pair<int, int>& b) {
    return a.second > b.second; // '>' sorts higher frequency first
});

// Result: [ {2, 3}, {1, 2}, {3, 1} ]
```

### Alternative: Max-Heap (`std::priority_queue`)
Store `pair<frequency, number>` in a `priority_queue<pair<int, int>>`. C++ automatically orders pairs by `first` (frequency) in a max-heap!
```cpp
priority_queue<pair<int, int>> pq;
for (auto& [num, freq] : mp) {
    pq.push({freq, num}); // Priority queue sorts by 'freq' automatically!
}
```

---

## 24. Accessing Pair Elements in `vector<pair<T1, T2>>`

For `vector<pair<int, int>> vec = { {10, 20}, {30, 40} };`:

1. **Using Indexing (`vec[i].first`):**
   - `vec[i].first` \(\rightarrow\) Accesses the 1st element of the $i$-th pair.
   - `vec[i].second` \(\rightarrow\) Accesses the 2nd element of the $i$-th pair.
2. **Using Range-Based Loop:**
   - `for (const auto& p : vec) { cout << p.first; }`
3. **Using C++17 Structured Binding:**
   - `for (const auto& [firstVal, secondVal] : vec) { cout << firstVal; }`
4. **Using Iterators:**
   - `it->first` or `(*it).first`

---

## 25. Complete C++ Member Function Cheatsheet (`std::map` & `std::unordered_map`)

### A. Functions Common to BOTH `std::map` and `std::unordered_map`

| Function | What It Does | Complexity |
| :--- | :--- | :--- |
| **`mp[key]`** | Accesses or inserts `key`. Default-inserts if missing. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.at(key)`** | Accesses value at `key` with bounds checking (throws error if missing). | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.insert({k, v})`** | Inserts key-value pair. Does not overwrite if key exists. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.emplace(k, v)`** | Constructs key-value pair in-place. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.find(key)`** | Returns iterator to element, or `mp.end()` if not found. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.count(key)`** | Returns `1` if key exists, `0` if missing. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.contains(key)`** | (C++20) Returns `true` if key exists, `false` otherwise. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.erase(key)`** | Removes element by key. Returns number of removed elements. | $O(\log N)$ in `map`, $O(1)$ avg in `unordered_map` |
| **`mp.erase(it)`** | Removes element at specified iterator location. | $O(1)$ amortized |
| **`mp.clear()`** | Removes all elements (empties the map). | $O(N)$ |
| **`mp.size()`** | Returns total number of elements. | $O(1)$ |
| **`mp.empty()`** | Returns `true` if size == 0. | $O(1)$ |
| **`mp.begin()`, `mp.end()`**| Returns iterators for traversal. | $O(1)$ |

### B. Functions UNIQUE to `std::map` (Ordered Map Only)

| Function | What It Does | Complexity |
| :--- | :--- | :--- |
| **`mp.lower_bound(key)`** | Returns iterator to first element with key $\ge$ `key`. | $O(\log N)$ |
| **`mp.upper_bound(key)`** | Returns iterator to first element with key $>$ `key`. | $O(\log N)$ |
| **`mp.equal_range(key)`** | Returns `pair<lower_bound, upper_bound>`. | $O(\log N)$ |
| **`mp.rbegin()`, `mp.rend()`**| Reverse iterators for backward sorted traversal. | $O(1)$ |

### C. Functions UNIQUE to `std::unordered_map` (Hash Table Control Only)

| Function | What It Does | Complexity |
| :--- | :--- | :--- |
| **`mp.reserve(n)`** | Pre-allocates space for at least `n` elements without rehashing. | $O(N)$ |
| **`mp.rehash(n)`** | Sets number of buckets to at least `n` and rehashes. | $O(N)$ |
| **`mp.bucket_count()`** | Returns total number of hash table buckets. | $O(1)$ |
| **`mp.bucket(key)`** | Returns bucket index where `key` is located. | $O(1)$ avg |
| **`mp.load_factor()`** | Returns average elements per bucket (`size / bucket_count`). | $O(1)$ |

---

## 26. Splitting a String by Spaces in C++ (`std::stringstream`)

Header required: `#include <sstream>`

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <sstream>

using namespace std;

vector<string> splitBySpace(const string& s) {
    stringstream ss(s);
    string word;
    vector<string> words;

    // 'ss >> word' extracts words and automatically skips extra spaces!
    while (ss >> word) {
        words.push_back(word);
    }

    return words;
}

int main() {
    string str = "  hello   world   this is  c++  ";
    vector<string> words = splitBySpace(str);

    for (const string& w : words) {
        cout << "[" << w << "] ";
    }
    cout << endl; // Output: [hello] [world] [this] [is] [c++]
    return 0;
}
```

### Why `stringstream` (`ss >> word`) is best for DSA:
1. **Automatically handles multiple spaces:** Seamlessly ignores leading, trailing, and consecutive extra spaces.
2. **Clean & Fast:** $O(N)$ time complexity, requiring zero manual pointer arithmetic.

---

## 27. Length-Prefix Encoding Pattern (`length#string`)

- **Problem with Simple Delimiters / `stringstream`:** Space-separation fails for empty strings (`""`) because `stringstream` skips all spaces! Delimiter characters (like `#`) fail if strings contain that delimiter inside them.
- **Solution (Length-Prefix Encoding):** Prefix every string with `to_string(s.length()) + "#" + s`.
  - Example: `strs = ["", ""]` $\rightarrow$ `encoded = "0#0#"`.
  - Example: `strs = ["hello world", ""]` $\rightarrow$ `encoded = "11#hello world0#"`.
- **Decoding Logic:**
  ```cpp
  int i = 0, n = s.length();
  while (i < n) {
      int j = i;
      while (s[j] != '#') j++;
      int len = stoi(s.substr(i, j - i));
      string word = s.substr(j + 1, len);
      result.push_back(word);
      i = j + 1 + len;
  }
  ```

---

## 28. Product of Array Except Self ($O(N)$ Prefix & Suffix Pattern)

- **Core Concept:** $\text{ans}[i] = (\text{Product of numbers to left of } i) \times (\text{Product of numbers to right of } i)$.
- **$O(1)$ Extra Space Optimization:**
  - Pass 1 (Left-to-Right): Compute prefix products directly inside `ans` vector.
  - Pass 2 (Right-to-Left): Accumulate suffix products into a single running variable `curr_post = 1` and multiply into `ans[i]`.
```cpp
vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> ans(n, 1);
    for (int i = 1; i < n; i++) ans[i] = ans[i - 1] * nums[i - 1];

    int curr_post = 1;
    for (int i = n - 1; i >= 0; i--) {
        ans[i] *= curr_post;
        curr_post *= nums[i];
    }
    return ans;
}
```

---

## 29. 3x3 Subgrid Index Formula for Sudoku Grids

To map any cell `(row r, col c)` in a $9 \times 9$ Sudoku board to its $3 \times 3$ subgrid index ($0$ to $8$):

$$\text{boxIndex} = (r / 3) \times 3 + (c / 3)$$

- Rows $0,1,2$ & Cols $0,1,2 \rightarrow$ Box `0`
- Rows $0,1,2$ & Cols $3,4,5 \rightarrow$ Box `1`
- Rows $0,1,2$ & Cols $6,7,8 \rightarrow$ Box `2`
- Rows $3,4,5$ & Cols $0,1,2 \rightarrow$ Box `3` ... up to Box `8`.

---

## 30. C++ STL `std::set` (Ordered Set) vs `std::unordered_set`

Both `std::set` and `std::unordered_set` store **unique elements** (no duplicate values).

### A. `std::set` (Ordered Set)
- **Header:** `#include <set>`
- **Underlying Data Structure:** Red-Black Tree (Self-balancing Binary Search Tree).
- **Element Ordering:** Automatically sorted in **ascending order** by default.
- **Time Complexity:** Search / Insert / Delete are **guaranteed $O(\log N)$**.
- **Supported Types:** Works with any type supporting the `<` operator (including `pair<int, int>`).
- **Unique Features:** Supports range search operations like `lower_bound()` and `upper_bound()`.

### B. `std::unordered_set` (Unordered Set / Hash Set)
- **Header:** `#include <unordered_set>`
- **Underlying Data Structure:** Hash Table (bucket array with chaining).
- **Element Ordering:** **No guaranteed order** (stored by hash key).
- **Time Complexity:** Search / Insert / Delete are **$O(1)$ on average**, **$O(N)$ worst case** (when severe hash collisions occur).
- **Supported Types:** Requires `std::hash` specialization (works for `int`, `string`, `char`, but **not** `pair<int, int>` without a custom hash function).

---

### C. Comprehensive Comparison Table

| Feature | `std::set` (Ordered) | `std::unordered_set` (Unordered) |
| :--- | :--- | :--- |
| **Internal Structure** | Red-Black Tree (BST) | Hash Table |
| **Element Order** | Sorted (Ascending) | Arbitrary / Unordered |
| **Search (`find`)** | $O(\log N)$ (Guaranteed) | $O(1)$ Average, $O(N)$ Worst Case |
| **Insertion (`insert`)**| $O(\log N)$ (Guaranteed) | $O(1)$ Average, $O(N)$ Worst Case |
| **Deletion (`erase`)** | $O(\log N)$ (Guaranteed) | $O(1)$ Average, $O(N)$ Worst Case |
| **Header File** | `#include <set>` | `#include <unordered_set>` |
| **Supports `pair<int,int>`**| ✅ Yes (uses `<` operator) | ❌ No (requires custom hash function) |
| **Range Methods (`lower_bound`)** | ✅ Supported | ❌ Not Supported |
| **Iterators** | Bidirectional iterators | Forward iterators only |

---

### D. Complete Member Functions Cheatsheet

| Function | Description | `std::set` Complexity | `std::unordered_set` Complexity |
| :--- | :--- | :--- | :--- |
| **`s.insert(val)`** | Inserts `val`. Ignored if `val` already exists. | $O(\log N)$ | $O(1)$ avg |
| **`s.erase(val)`** | Removes element by value. Returns count removed (`0` or `1`). | $O(\log N)$ | $O(1)$ avg |
| **`s.erase(it)`** | Removes element at iterator position. | $O(1)$ amortized | $O(1)$ amortized |
| **`s.find(val)`** | Returns iterator to element, or `s.end()` if missing. | $O(\log N)$ | $O(1)$ avg |
| **`s.count(val)`** | Returns `1` if present, `0` if absent. | $O(\log N)$ | $O(1)$ avg |
| **`s.contains(val)`**| (C++20) Returns `true` if present, `false` otherwise. | $O(\log N)$ | $O(1)$ avg |
| **`s.clear()`** | Deletes all elements. | $O(N)$ | $O(N)$ |
| **`s.size()`** | Returns total number of unique elements. | $O(1)$ | $O(1)$ |
| **`s.empty()`** | Returns `true` if size == 0. | $O(1)$ | $O(1)$ |
| **`s.lower_bound(v)`**| Iterator to first element $\ge v$ (**`set` only**). | $O(\log N)$ | ❌ N/A |
| **`s.upper_bound(v)`**| Iterator to first element $> v$ (**`set` only**). | $O(\log N)$ | ❌ N/A |

---

### E. Code Example

```cpp
#include <iostream>
#include <set>
#include <unordered_set>

using namespace std;

int main() {
    // 1. std::set (Sorted)
    set<int> orderedSet = {5, 2, 8, 2, 1}; // Duplicates ignored
    cout << "Ordered Set: ";
    for (int x : orderedSet) {
        cout << x << " "; // Output: 1 2 5 8 (Always sorted!)
    }
    cout << "\n";

    // 2. std::unordered_set (Fast O(1) Lookups)
    unordered_set<int> hashSet = {5, 2, 8, 2, 1};
    cout << "Unordered Set: ";
    for (int x : hashSet) {
        cout << x << " "; // Output order is unpredictable (e.g., 1 8 2 5)
    }
    cout << "\n";

    // Fast O(1) Lookup
    if (hashSet.find(8) != hashSet.end()) {
        cout << "Found 8 in hashSet!" << endl;
    }

    return 0;
}

---

## 31. Two-Pointer Formulation & Pattern Discovery Framework

### A. How to Spot Two-Pointer Problems in Interviews
1. **Signal 1 (Pair/Boundary Search):** Problem asks to find a pair, triplet, or subarray boundary that optimizes a metric (area, sum, window length).
2. **Signal 2 (Sorted or Monotonic Input):** Input array is sorted, or array indices have a clear directional relationship.
3. **Signal 3 (Complexity Optimization):** Brute force is $O(N^2)$ (testing all index pairs `(i, j)`), but target is **$O(N)$ time & $O(1)$ space**.

---

### B. The 3 Standard Two-Pointer Layouts

| Pattern | Pointer Positions | When to Use | Key Examples |
| :--- | :--- | :--- | :--- |
| **Opposite Ends** | `left = 0`, `right = N - 1` | Maximizing container width, pair sums on sorted arrays, checking palindromes. | *Container With Most Water*, *Two Sum II*, *3Sum*, *Valid Palindrome* |
| **Same Direction** | `slow = 0`, `fast = 0` | In-place element removal, tracking sub-arrays, sliding windows. | *Remove Duplicates*, *Move Zeroes*, *Longest Substring* |
| **Two Arrays** | `p1 = 0`, `p2 = 0` | Comparing or merging two sorted datasets. | *Merge Sorted Array*, *Intersection of Arrays* |

---

### C. The Elimination Principle (How to Decide Which Pointer to Move)
To avoid needing to "look ahead" or test all future options:

1. Identify which pointer holds the **bottleneck** (e.g. in *Container With Most Water*, `min(h[l], h[r])` limits height).
2. Ask: *"If I keep the bottleneck pointer fixed and move the other pointer, can the result EVER improve?"*
3. If the answer is **NO**, the bottleneck pointer has **zero remaining potential**.
4. **Eliminate the bottleneck pointer** immediately (`left++` or `right--`).

---

## 32. Recursion & Backtracking Performance Pitfalls

### A. Pass-by-Value on Vectors / Strings (`TLE` Cause #1)
- **Symptom:** Code logic is $O(2^N)$, but gets TLE on $N = 20$.
- **Cause:** Writing `void helper(vector<int> nums, ...)` or `void helper(string s, ...)` copies the entire collection in **every single call frame**.
- **Fix:** Always pass by reference: `const vector<int> &nums` or `string &s`.

### B. Boolean Short-Circuiting in Existence Checks
- When a problem asks **"Does there exist any valid subset/path?"**:
  - Do **NOT** store all valid answers into a `vector<vector<int>> ans` and check `ans.size() > 0`.
  - Instead, declare `bool helper(...)`.
  - As soon as one branch returns `true`, immediately return `true` to cancel further recursion down other branches:
    ```cpp
    if (helper(nums, i + 1, sum + nums[i])) return true;
    if (helper(nums, i + 1, sum)) return true;
    return false;
    ```

### C. Parameterized vs. Functional Recursion (Counting Patterns)
- **Parameterized Style (`void helper(..., int &counter)`):**
  - Carries a counter reference down the recursion stack and increments at base cases.
  - Good for simple backtracking, but harder to memoize.
- **Functional Style (`int helper(...)`):**
  - Subproblems return their own counts directly:
    ```cpp
    return helper(i + 1, sum + nums[i]) + helper(i + 1, sum);
    ```
  - **Superpower:** Seamlessly transitions into Dynamic Programming (`dp[i][sum] = pick + notPick`).

---

## 33. Backtracking Paradigms: Binary Choice vs. Loop-Based Selection

### Pattern 1: Binary Decision (Pick vs. Don't Pick)
- Explicitly makes 2 calls:
  - `helper(i + 1, sum + nums[i])` (Include)
  - `helper(i + 1, sum)` (Exclude)
- Best for: Pure subset generation without duplicate elements, DP transitions.

### Pattern 2: Loop-Based Selection (Level-by-Level Candidate Placement)
- Loop iterates `for (int itr = i; itr < nums.size(); itr++)`.
- **How "Don't Pick" (skipping) is handled:**
  - Moving from iteration `itr` to `itr + 1` **IS** the "Don't Pick" of `nums[itr]`.
  - When the loop finishes, returning up the call stack represents skipping ALL remaining elements.
- **Handling Duplicates:**
  - Sort first: `sort(nums.begin(), nums.end())`.
  - Skip duplicate siblings at the same depth:
    ```
    if (itr > i && nums[itr] == nums[itr - 1]) continue;
    ```

---

## 34. The Golden Rule of Backtracking Symmetry: Match Each `push_back()` with ONE `pop_back()`

A function call must leave the shared state (`current`) in the **exact same condition** it received it.

### In Binary Choice (Include / Exclude):
```cpp
// 1. Include nums[i]
current.push_back(nums[i]);        // PUSH (+1)
helper(nums, i + 1, current, ans);

// 2. Exclude nums[i]
current.pop_back();                // POP (-1) -> Undoes Choice 1!
helper(nums, i + 1, current, ans); // Choice 2 pushes NOTHING!
// No pop_back() needed here! Net balance is 0.
```
- **Choice 1 pushed `nums[i]`:** The vector grew by 1 element.
- **`current.pop_back()` removed `nums[i]`:** The vector is restored to its original state.
- **Choice 2 (Exclude) did NOT push anything:** The vector remains at its original state.
- If you were to call `pop_back()` again at the very end, you would be removing an element that belonged to the **caller's (parent's)** frame! This causes state corruption and eventually a crash when `pop_back()` is called on an empty vector.

---

## 35. Deep Dive: The Duplicate Skipping Condition in Backtracking (`itr > i && nums[itr] == nums[itr - 1]`)

Used in **Combination Sum II (LC 40)**, **Subsets II (LC 90)**, and **Permutations II (LC 47)**.

```cpp
for (int itr = i; itr < nums.size(); itr++) {
    if (itr > i && nums[itr] == nums[itr - 1]) {
        continue;
    }
    current.push_back(nums[itr]);
    helper(..., itr + 1);
    current.pop_back();
}
```

### 1. Understanding `i` vs. `itr`
- **`i` (Depth Parameter):** The starting boundary passed from the caller. Represents: *"You are allowed to pick numbers starting from index `i`."*
- **`itr` (Loop Variable):** Iterates over remaining candidates for the **current slot**: `itr = i, i + 1, i + 2, ...`

### 2. The Two Movement Directions: Vertical vs. Horizontal

```
                      LEVEL 0: Choosing 1st element
                           /            \
                          /              \
            (itr = 0) Pick 1st '1'        (itr = 1) Pick 2nd '1'  <--- HORIZONTAL (Siblings)
                        |                            |
                     LEVEL 1                      LEVEL 1
                 Choosing 2nd element         Choosing 2nd element
                        |
                  (itr = 1) Pick 2nd '1'
                        |
                  <--- VERTICAL (Depth)
```

| Direction | Condition | `itr > i`? | What Happens? | Purpose |
| :--- | :--- | :---: | :--- | :--- |
| **Vertical (Depth)** | First candidate at new level (`itr == i`) | **FALSE** | **NOT skipped!** Allowed to pick the duplicate. | Allows valid multi-element combinations like `[1, 1, 6]`. |
| **Horizontal (Width)** | Subsequent sibling candidates at same level (`itr > i`) | **TRUE** | **SKIPPED by `continue`!** | Avoids exploring duplicate subtrees that start with the same value (e.g. redundant `[1, 7]` or `[1, 2, 5]`). |

### 3. Golden Takeaway
> **`itr == i` allows picking duplicate values across different positions (vertically down: `[1, 1]`), while `itr > i` prevents picking duplicate values for the same position (horizontally across: avoiding duplicate `[1, ...]`).**

---

## 36. C++ `std::string::size()` vs. C-Style Strings (`\0` Null Terminator)

### 1. `std::string` does NOT count `\0` in `.size()` or `.length()`
- In C++, `string s = "abc";`
  - `s.size()` $\implies \mathbf{3}$ (only actual characters `'a'`, `'b'`, `'c'`).
  - Valid indices: `s[0] = 'a'`, `s[1] = 'b'`, `s[2] = 'c'`.
- The null terminator `'\0'` exists in underlying memory to support `.c_str()`, but is **strictly excluded** from `.size()`.

### 2. When does `4` happen? (C-style char arrays)
- In C, a raw string literal or char array:
  - `char arr[] = "abc";`
  - `sizeof(arr)` $\implies \mathbf{4}$ bytes (includes the trailing `'\0'`).
  - `strlen(arr)` $\implies \mathbf{3}$ (standard C function also excludes `'\0'`).

### 3. Iteration Rule of Thumb
- Always iterate up to `itr < s.size()`, **never** `itr < s.size() - 1`.
- `itr < s.size() - 1` stops one character early and completely misses the final character!

---

## 37. Terminology Clarification: Substring vs. Subsequence vs. Substring Partition

### 1. Definitions
- **Substring:** A contiguous slice of characters from index `i` to `j`. Total non-empty substrings = $\frac{N(N+1)}{2}$. For `"aab"`: `"a"`, `"aa"`, `"aab"`, `"a"`, `"ab"`, `"b"`.
- **Subsequence:** Any sequence derived by deleting 0 or more characters without changing relative order (can be non-contiguous). Total subsequences = $2^N$.
- **Substring Partition (Palindrome Partitioning / Word Break):** Slicing the **entire string** into contiguous contiguous chunks such that their concatenation equals the original string:
  - For `"aab"`: `["a", "a", "b"]`, `["a", "ab"]`, `["aa", "b"]`, `["aab"]`.
  - Done via backtracking with a loop from `start` to `end`.

---

## 38. Deleting a Node in Singly Linked List Without `prev` Pointer ($\mathcal{O}(1)$ Trick)

### The Problem:
- Standard singly linked list deletion requires updating `prev->next = node->next`.
- If only given `ListNode* node` (without access to `head` or `prev`), we cannot traverse backwards.

### The "Identity Theft" Pattern:
1. **Copy value:** Copy successor's value into current node:
   ```cpp
   node->val = node->next->val;
   ```
2. **Bypass successor:** Point current node past the duplicate successor:
   ```cpp
   ListNode* temp = node->next;
   node->next = node->next->next;
   delete temp; // C++ good practice: free memory
   ```

### Constraint / Edge Case:
- **Cannot be applied to the tail node!** If `node` is the last node (`node->next == nullptr`), there is no successor to copy values from.

---

## 39. Creating a New Node in Linked List (C++)

### 1. Typical Node Definition
```cpp
struct ListNode {
    int val;
    ListNode* next;

    // Constructors
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode* next) : val(x), next(next) {}
};
```

### 2. Method 1: Dynamic Allocation on the Heap (Standard & Required for LeetCode)
```cpp
ListNode* newNode = new ListNode(42);
```
- **Why Heap?** Persists across function calls until explicitly destroyed with `delete`.
- Pointer syntax: uses `->` to access fields (e.g. `newNode->val`, `newNode->next`).
- Freeing memory: `delete newNode;`

### 3. Method 2: Stack Allocation (Local Only)
```cpp
ListNode node(42);
ListNode* ptr = &node;
```
- Automatically destroyed when the function exits (goes out of scope).
- **Warning:** Never return the address of a stack-allocated node from a function!

---

## 40. The Assignment vs. Comparison Trap in C++ `if` Statements (`=` vs `==`)

### The Trap:
```cpp
if (end = s.size()) { // ❌ BUG: Single '=' performs assignment!
    ...
}
```

### Why it causes silent failure:
1. **Assignment expression value:** In C and C++, the expression `(a = b)` returns the assigned value `b`.
2. **Implicit truthiness:** If `s.size()` is non-zero (e.g. `6`), `(end = 6)` evaluates to integer `6`.
3. In C++, any non-zero integer is implicitly converted to boolean **`true`**!
4. **Result:** The `if` condition **ALWAYS evaluates to `true`**, regardless of what `end` was previously!
5. Any code placed *after* this `if` block becomes completely unreachable dead code.

### The Fix:
Always use `==` for comparison:
```cpp
if (end == s.size()) { // ✅ Correct equality comparison
```
---

## 41. Generating Substrings on the Basis of Length (Sliding Window / Fixed Length)

### 1. Two Mental Models for Substring Generation
- **Model A (By Start Index):** Fix `start`, expand `end` from `start` to `N - 1`. (Good for partitioning).
- **Model B (By Length $L$):** Outer loop controls length $L = 1 \dots N$, inner loop slides a window of size $L$.

### 2. Iterative Formula (Sliding Window of Length $L$)
For a string of length $N$ and target length $L$:
- The starting index $i$ ranges from $0$ up to $N - L$.
- Substring is: `s.substr(i, L)`.
```cpp
for (int len = 1; len <= s.size(); len++) {
    for (int i = 0; i <= s.size() - len; i++) {
        string sub = s.substr(i, len);
        // Process sub (length is exactly 'len')
    }
}
```

### 3. Recursive Formulation (By Length)
- State: `(len, i)` where `len` is current substring length, and `i` is current start index.
- When `i > s.size() - len`: window can no longer slide $\implies$ increment `len` to `len + 1` and reset `i = 0`.
- Base Case: `len > s.size()`.

---

## 42. C++ `std::string::substr()` Complete Syntax & Behavior Guide

### 1. Function Signature
```cpp
string substr(size_t pos = 0, size_t count = npos) const;
```
- **`pos` (1st parameter):** The **starting 0-based index** of the first character to include.
- **`count` (2nd parameter):** The **number of characters (length)** to copy. *(NOT the ending index!)*
- If `count` is omitted, it defaults to `string::npos`, which means: *"take everything from `pos` until the end of the string."*

### 2. Common Patterns & Formulas

| What you want | Syntax | Example on `s = "abcdef"` | Result |
| :--- | :--- | :--- | :--- |
| **Slice from `start` to `end` index** | `s.substr(start, end - start + 1)` | `s.substr(1, 3 - 1 + 1)` | `"bcd"` (indices 1 to 3) |
| **Fixed length $L$ starting at `i`** | `s.substr(i, L)` | `s.substr(2, 3)` | `"cde"` (length 3 from index 2) |
| **All characters from index `i` to end** | `s.substr(i)` | `s.substr(2)` | `"cdef"` |
| **First $K$ characters (prefix)** | `s.substr(0, K)` | `s.substr(0, 3)` | `"abc"` |
| **Last $K$ characters (suffix)** | `s.substr(s.size() - K)` | `s.substr(6 - 2)` | `"ef"` |

### 3. Edge Cases & Safety Rules
- **If `pos > s.size()`:** Throws a `std::out_of_range` exception (crash)!
- **If `pos == s.size()`:** Returns an empty string `""` safely (no exception).
- **If `pos + count > s.size()`:** C++ does **NOT** throw an error; it simply stops at the end of the string and returns all characters available.
- **Time Complexity:** $\mathcal{O}(\text{count})$ — It allocates a new `std::string` and copies characters into it.

---

## 43. Backtracking Pitfall: Pass-by-Value vs Pass-by-Reference with `pop_back()`

When implementing backtracking algorithms (like grid search, subset generation, permutations), a classic mismatch happens when mixing **pass-by-value expression passing** with **backtracking cleanup (`pop_back()`)**.

### 1. The Trap: Premature `pop_back()` on Pass-by-Value
Look at this code:
```cpp
void helper(vector<vector<char>>& board, string current, int i, int j) {
    char letter = board[i][j];
    // current + letter creates a NEW temporary string passed to helper.
    // The local 'current' in this function was NEVER modified!
    helper(board, current + letter, i + 1, j);
    
    current.pop_back(); // BUG! Pops a character from the EXISTING prefix!
    helper(board, current + letter, i, j + 1); // Corrupted prefix!
    current.pop_back(); // BUG!
}
```
**Why this breaks:**
1. `current + letter` does not alter `current` in the local scope.
2. Calling `current.pop_back()` deletes the *previous* character that was already there when the function was called.
3. Multiple `pop_back()` calls will eventually call `pop_back()` on an empty string, triggering **Undefined Behavior / crash**!

### 2. The Two Correct Ways to Handle State in Backtracking

| Approach | Passing Mechanism | How to Advance | How to Backtrack | Memory / Speed |
| :--- | :--- | :--- | :--- | :--- |
| **A. Pure Functional (Pass-by-Value)** | `string current` | `helper(..., current + letter)` | **Nothing!** Do not call `pop_back()`. | High copies $\mathcal{O}(L^2)$ |
| **B. Shared State (Pass-by-Reference)** | `string& current` | `current.push_back(letter); helper(...);` | `current.pop_back();` | Fast & memory efficient $\mathcal{O}(L)$ |
| **C. Index Pointer (Optimal for Search)** | `int idx` | `helper(..., idx + 1)` | **Nothing!** Just pass `idx + 1`. | $\mathcal{O}(1)$ overhead! |

### 3. In-Place Visited Masking in Grid DFS
To prevent revisiting the same cell without creating an extra `vector<vector<bool>> visited`:
```cpp
char temp = board[r][c];
board[r][c] = '#'; // Mark as visited

// Explore 4 directions...
bool found = dfs(board, word, r + 1, c, idx + 1) ||
             dfs(board, word, r - 1, c, idx + 1) ||
             dfs(board, word, r, c + 1, idx + 1) ||
             dfs(board, word, r, c - 1, idx + 1);

board[r][c] = temp; // Backtrack / unmark
return found;
```

---

## 44. Linked List Reversal: The 3-Pointer Dance & Recursive Induction

### 1. The Iterative 3-Pointer Technique (Gold Standard: $O(N)$ Time, $O(1)$ Space)
To reverse a singly linked list in-place without losing the remainder of the chain, you need 3 pointers:
- `prev`: Tracks the head of the already reversed sublist (starts at `nullptr`).
- `curr`: The active node being reversed (starts at `head`).
- `next_node`: Temporary holder to prevent memory disconnection.

```cpp
ListNode* prev = nullptr;
ListNode* curr = head;

while (curr != nullptr) {
    ListNode* next_node = curr->next; // 1. Save rest of list
    curr->next = prev;               // 2. Reverse link to point backward
    prev = curr;                     // 3. Move prev one step forward
    curr = next_node;                // 4. Move curr one step forward
}
return prev; // prev is the new head!
```

### 2. The Recursive Approach ($O(N)$ Time, $O(N)$ Stack Space)
Key insight for recursive reversal:
```cpp
ListNode* reverseList(ListNode* head) {
    if (!head || !head->next) return head; // Base case

    ListNode* newHead = reverseList(head->next); // Reverse sublist
    
    head->next->next = head; // Make original next node point back to head
    head->next = nullptr;    // Break original forward link
    
    return newHead;
}
```
**Why `head->next->next = head` works:**
In `1 -> 2 <- 3 <- 4`, `head` is `1`, and `head->next` is `2` (the tail of the reversed sublist). Setting `head->next->next = head` makes `2->next = 1`.

---

## 45. Linked List: The Fixed-Gap Two-Pointer & Dummy Node Pattern

### 1. The Fixed-Gap Technique (Finding the $K^{\text{th}}$ Node from the End in 1 Pass)
To find or delete the $n^{\text{th}}$ node from the end without calculating the total length in a prior pass:
1. Initialize two pointers: `fast` and `slow`.
2. Move `fast` ahead by $n$ steps (or $n + 1$ steps from a dummy node).
3. Move both `fast` and `slow` together until `fast` reaches `nullptr`.
4. The distance between `fast` (at end) and `slow` remains fixed, so `slow` lands precisely at the desired location!

### 2. The Dummy Node Safeguard for Deletion
When deleting nodes from a linked list, the target node might be the `head` itself.
- Without a dummy node: You must write special-case branches (`if (x == 0) return head->next;`).
- With a stack-allocated dummy node:
  ```cpp
  ListNode dummy(0);
  dummy.next = head;
  ListNode* slow = &dummy;
  ```
  `dummy.next` always preserves the updated head, even if the original first node is deleted!

---

## 46. C++ Member Access: Dot (`.`) vs. Arrow (`->`) Operator

In C++, whether you write `dummy.next` or `dummy->next` depends entirely on **how `dummy` was declared**:

### 1. The Fundamental Difference

| Syntax | What is `dummy`? | Where is it allocated? | Meaning | Cleanup |
| :--- | :--- | :--- | :--- | :--- |
| **`dummy.next`** | An **Object / Instance** (`ListNode dummy(0);`) | **Stack** | Direct member access on the object | Automatic when function returns (RAII) |
| **`dummy->next`** | A **Pointer to an Object** (`ListNode* dummy;`) | **Heap** (if `new ListNode`) or pointing to another node | Shorthand for `(*dummy).next` (dereference + access) | Manual `delete dummy;` required if allocated with `new` |

### 2. Code Comparison in Linked List Problems

#### Pattern A: Stack-Allocated Dummy (`dummy.next`) — Preferred & Modern C++
```cpp
ListNode dummy(0);      // dummy is an OBJECT on the stack
dummy.next = head;      // Use '.' because dummy is NOT a pointer
ListNode* slow = &dummy;// Take its address using '&' to pass to pointer variables
...
return dummy.next;      // Return pointer to head; 'dummy' is cleanly popped off stack!
```
- **Pros:** Zero heap allocation overhead, impossible to leak memory, fast.

#### Pattern B: Heap-Allocated Dummy (`dummy->next`) — Traditional / Pointer style
```cpp
ListNode* dummy = new ListNode(0); // dummy is a POINTER to heap memory
dummy->next = head;                // Use '->' because dummy IS a pointer
...
ListNode* res = dummy->next;
delete dummy;                      // Crucial! Must free dummy to avoid memory leak
return res;
```
- **Cons:** Slower (`new` incurs dynamic allocator overhead), requires explicit `delete`.

### 3. Compiler Errors if Swapped
- Calling `dummy->next` on `ListNode dummy;`:
  `error: base operand of '->' has non-pointer type 'ListNode'`
- Calling `dummy.next` on `ListNode* dummy;`:
  `error: request for member 'next' in 'dummy', which is of pointer type 'ListNode*'`

---

## 47. Linked List: Value Overwriting vs. Pointer Rewiring

When solving linked list sorting or rearrangement problems (like sorting 0s, 1s, and 2s or reordering nodes):

### 1. The Value Overwriting Shortcut (Counting Approach)
Counting values and then overwriting `node->data` in a second pass:
- **Complexity:** $\mathcal{O}(N)$ time, $\mathcal{O}(1)$ space.
- **Why it is frowned upon / disallowed in interviews:**
  1. **Corrupts Object Identity:** In real systems, nodes represent entities (e.g. database rows, user sessions). External systems holding pointers to `node_A` now see the data of `node_B`.
  2. **Heavy Node Payloads:** If the node stores a large struct or heavy object rather than a primitive `int`, copying data is prohibitively slow ($\mathcal{O}(\text{payload\_size})$).
  3. **Does not test pointer manipulation:** Linked list interviews test your mastery of pointers (`next`), not basic array counting.

### 2. The 3-Dummy Partition & Stitch Pattern (Optimal Link Manipulation)
Instead of overwriting values, partition nodes into 3 sublists in a single pass:
```cpp
ListNode zeroD(0), oneD(0), twoD(0);
ListNode *z = &zeroD, *o = &oneD, *t = &twoD;

while (curr) {
    if (curr->data == 0)      { z->next = curr; z = z->next; }
    else if (curr->data == 1) { o->next = curr; o = o->next; }
    else                      { t->next = curr; t = t->next; }
    curr = curr->next;
}

// Stitching: handle edge cases where 1s or 0s might be absent!
z->next = (oneD.next) ? oneD.next : twoD.next;
o->next = twoD.next;
t->next = nullptr; // Crucial to prevent cycles!

return (zeroD.next) ? zeroD.next : ((oneD.next) ? oneD.next : twoD.next);
```

---

## 48. Linked List: Right-to-Left Operations without Reversal (Recursive Unwinding)

When an operation on a singly linked list requires working from the **least significant digit (tail)** to the **most significant digit (head)** — such as adding 1 or adding two numbers stored in normal order:

### 1. The Conventional Iterative Way: Double Reversal
- Reverse the list $\rightarrow$ Perform addition $\rightarrow$ Reverse back.
- **Pros:** $\mathcal{O}(1)$ auxiliary space.
- **Cons:** High code verbosity, 3 passes over the list.

### 2. The Recursive Carry Pattern (0 Reversals!)
Leverage the recursive call stack to reach the tail first. Carry is propagated **backwards** as the recursion unwinds:

```cpp
int addCarry(ListNode* node) {
    if (!node) return 1; // Base case: tail returns the initial +1 carry

    int carry = addCarry(node->next);
    int sum = node->val + carry;
    node->val = sum % 10; // Update current digit in-place
    return sum / 10;      // Propagate new carry back to caller
}

ListNode* addOne(ListNode* head) {
    int carry = addCarry(head);
    if (carry > 0) {
        ListNode* newHead = new ListNode(carry);
        newHead->next = head;
        return newHead;
    }
    return head;
}
```
- **Time Complexity:** $\mathcal{O}(N)$ (single pass).
- **Auxiliary Space:** $\mathcal{O}(N)$ recursion stack.








































