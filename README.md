# 🧠 Data Structures & Algorithms (DSA) Practice & Notes

A comprehensive collection of Data Structures and Algorithms problem solutions, detailed intuitions, complexity analyses, and core C++ / DSA learnings.

---

## 📌 Repository Overview

This repository documents my daily problem-solving journey, covering fundamental algorithms, LeetCode / GFG patterns, and interview preparations. Each problem note includes:
- **Problem Statement & Summary**
- **Brute Force $\rightarrow$ Optimized Approaches**
- **Step-by-step Dry Runs & Traces**
- **Time & Space Complexity Proofs**
- **Clean C++ Implementations with Edge Case Handling**

---

## 📚 Central Knowledge Base

- **[General DSA & C++ Learnings](file:///mnt/d/sahaj/dsa/general_learning.md)**: Deep dive into data types, memory limits, integer overflow prevention, hash map pitfalls, bit manipulation tricks, and standard library behaviors.

---

## 🗂️ Problem Directory by Topic

### 🔢 Arrays & Hashing
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Two Sum** | [2026-09-21-two_sum.md](file:///mnt/d/sahaj/dsa/2026-09-21-two_sum.md) | Hash Map (`unordered_map`) 1-Pass |
| **Group Anagrams** | [2026-09-21-group_anagrams.md](file:///mnt/d/sahaj/dsa/2026-09-21-group_anagrams.md) | Frequency Count / Sorted String Key |
| **Product of Array Except Self** | [2026-09-21-product_of_array_except_self.md](file:///mnt/d/sahaj/dsa/2026-09-21-product_of_array_except_self.md) | Prefix & Suffix Products |
| **Longest Consecutive Sequence** | [2026-09-21-longest_consecutive_sequence.md](file:///mnt/d/sahaj/dsa/2026-09-21-longest_consecutive_sequence.md) | Hash Set sequence starter checks |
| **Valid Sudoku** | [2026-09-21-valid_sudoku.md](file:///mnt/d/sahaj/dsa/2026-09-21-valid_sudoku.md) | Subgrid hashing & bitsets |
| **Encode and Decode Strings** | [2026-09-21-encode_and_decode_strings.md](file:///mnt/d/sahaj/dsa/2026-09-21-encode_and_decode_strings.md) | Length-prefix encoding delimiter |
| **Second Largest Element** | [2026-09-25-second_largest_element.md](file:///mnt/d/sahaj/dsa/2026-09-25-second_largest_element.md) | Single-pass dual tracker |
| **Remove Duplicates from Sorted Array** | [2026-09-26-remove_duplicates_from_sorted_array.md](file:///mnt/d/sahaj/dsa/2026-09-26-remove_duplicates_from_sorted_array.md) | In-place two pointers |
| **Rotate Array** | [2026-09-25-rotate_array.md](file:///mnt/d/sahaj/dsa/2026-09-25-rotate_array.md) | Reversal algorithm ($k \pmod n$) |
| **Move Zeroes** | [2026-09-26-move_zeroes.md](file:///mnt/d/sahaj/dsa/2026-09-26-move_zeroes.md) | In-place swapping pointer |
| **Max Consecutive Ones** | [2026-09-25-max_consecutive_ones.md](file:///mnt/d/sahaj/dsa/2026-09-25-max_consecutive_ones.md) | State counting |
| **Leaders in an Array** | [2026-09-26-leaders_in_an_array.md](file:///mnt/d/sahaj/dsa/2026-09-26-leaders_in_an_array.md) | Right-to-left suffix max |
| **Rearrange Array Elements by Sign** | [2026-09-26-rearrange_array_elements_by_sign.md](file:///mnt/d/sahaj/dsa/2026-09-26-rearrange_array_elements_by_sign.md) | Even/Odd index pointer placement |
| **Pascal's Triangle** | [2026-09-26-pascals_triangle.md](file:///mnt/d/sahaj/dsa/2026-09-26-pascals_triangle.md) | Dynamic row building |
| **Pascal's Triangle II** | [2026-09-26-pascals_triangle_ii.md](file:///mnt/d/sahaj/dsa/2026-09-26-pascals_triangle_ii.md) | Combinatorics & $O(K)$ space optimization |
| **Maximum Subarray (Kadane's Algorithm)** | [2026-09-27-maximum_subarray.md](file:///mnt/d/sahaj/dsa/2026-09-27-maximum_subarray.md) | Reset negative running sum (1 pass, $O(N)$) |

### 👉 Two Pointers
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Valid Palindrome** | [2026-09-21-valid_palindrome.md](file:///mnt/d/sahaj/dsa/2026-09-21-valid_palindrome.md) | Shrinking two pointers with sanitization |
| **Two Sum II (Sorted Array)** | [2026-09-21-two_sum_ii.md](file:///mnt/d/sahaj/dsa/2026-09-21-two_sum_ii.md) | Left/Right pointers |
| **3Sum** | [2026-09-21-three_sum.md](file:///mnt/d/sahaj/dsa/2026-09-21-three_sum.md) | Sorting + Two Pointers + Duplicate skip |
| **4Sum** | [2026-09-27-four_sum.md](file:///mnt/d/sahaj/dsa/2026-09-27-four_sum.md) | Nested Loops + Two Pointers + Duplicate skip |
| **Container With Most Water** | [2026-09-21-container_with_most_water.md](file:///mnt/d/sahaj/dsa/2026-09-21-container_with_most_water.md) | Greedy boundary reduction |
| **Sort Colors (Dutch National Flag)** | [2026-09-27-sort_colors.md](file:///mnt/d/sahaj/dsa/2026-09-27-sort_colors.md) | 3-pointer partition (1 pass, $O(N)$) |

### 🔍 Binary Search
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Binary Search** | [2026-09-22-binary_search.md](file:///mnt/d/sahaj/dsa/2026-09-22-binary_search.md) | Search space halving & overflow prevention |
| **Search a 2D Matrix** | [2026-09-22-search_a_2d_matrix.md](file:///mnt/d/sahaj/dsa/2026-09-22-search_a_2d_matrix.md) | Virtual 1D flattening (`row = mid / cols`, `col = mid % cols`) |
| **Single Element in Sorted Array** | [2026-09-25-single_element_in_sorted_array.md](file:///mnt/d/sahaj/dsa/2026-09-25-single_element_in_sorted_array.md) | Even/Odd index parity observation |
| **Logarithmic Time Complexity** | [2026-09-25-logarithmic_time_complexity.md](file:///mnt/d/sahaj/dsa/2026-09-25-logarithmic_time_complexity.md) | Mathematical intuition of $O(\log N)$ |

### 🥞 Stack
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Valid Parentheses** | [2026-09-21-valid_parentheses.md](file:///mnt/d/sahaj/dsa/2026-09-21-valid_parentheses.md) | LIFO bracket matching |
| **Evaluate Reverse Polish Notation** | [2026-09-21-eval_rpn.md](file:///mnt/d/sahaj/dsa/2026-09-21-eval_rpn.md) | Operand stack evaluation |

### 🔄 Sorting Algorithms
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Bubble Sort** | [2026-09-21-bubble_sort.md](file:///mnt/d/sahaj/dsa/2026-09-21-bubble_sort.md) | Adjacent swap, early exit flag |
| **Insertion Sort** | [2026-09-21-insertion_sort.md](file:///mnt/d/sahaj/dsa/2026-09-21-insertion_sort.md) | Incremental insertion in sorted prefix |

### 🐢 Fast & Slow Pointers / Cycle Detection
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Find the Duplicate Number** | [2026-09-21-find_duplicate_number.md](file:///mnt/d/sahaj/dsa/2026-09-21-find_duplicate_number.md) | Floyd's Tortoise and Hare cycle detection |

### 🔢 Math & Bit Manipulation
| Problem / Topic | File | Key Pattern / Concept |
| :--- | :--- | :--- |
| **Palindrome Number** | [2026-09-21-palindrome_number.md](file:///mnt/d/sahaj/dsa/2026-09-21-palindrome_number.md) | Reversing half of the number |
| **Reverse Digits** | [2026-09-21-reverse_digits.md](file:///mnt/d/sahaj/dsa/2026-09-21-reverse_digits.md) | Modulo and division digit extraction |
| **Reverse Integer** | [2026-09-21-reverse_integer.md](file:///mnt/d/sahaj/dsa/2026-09-21-reverse_integer.md) | 32-bit signed overflow bounds check |
| **Fibonacci Number** | [2026-09-21-fibonacci_number.md](file:///mnt/d/sahaj/dsa/2026-09-21-fibonacci_number.md) | Iterative space-optimized DP ($O(1)$ space) |

---

## 🛠️ Languages & Tools

- **Language:** C++ (C++17 / C++20)
- **Formatting:** Markdown with LaTeX math notation
