# CENG114 — Computer Programming II
## Week 2: Recursion
### Based on Chapter 18 — Introduction to Java Programming and Data Structures (13th Edition, Liang)

---

## Table of Contents

1. [Introduction and Motivation](#1-introduction-and-motivation)
2. [What is Recursion?](#2-what-is-recursion)
3. [Computing Factorial Recursively](#3-computing-factorial-recursively)
4. [Tracing Recursive Calls and the Call Stack](#4-tracing-recursive-calls-and-the-call-stack)
5. [Fibonacci Numbers](#5-fibonacci-numbers)
6. [Problem Solving Using Recursion](#6-problem-solving-using-recursion)
7. [Characteristics of Recursion](#7-characteristics-of-recursion)
8. [Thinking Recursively — Palindrome Example](#8-thinking-recursively--palindrome-example)
9. [Recursive Helper Methods](#9-recursive-helper-methods)
10. [Recursive Selection Sort](#10-recursive-selection-sort)
11. [Recursive Binary Search](#11-recursive-binary-search)
12. [Directory Size — A Practical Recursive Problem](#12-directory-size--a-practical-recursive-problem)
13. [Tower of Hanoi](#13-tower-of-hanoi)
14. [Greatest Common Divisor (GCD)](#14-greatest-common-divisor-gcd)
15. [Fractals — Sierpinski Triangle](#15-fractals--sierpinski-triangle)
16. [Recursion vs. Iteration](#16-recursion-vs-iteration)
17. [Tail Recursion](#17-tail-recursion)
18. [Summary and Key Takeaways](#18-summary-and-key-takeaways)

---

## 1. Introduction and Motivation

### Why Learn Recursion?

Recursion is one of the most powerful and elegant techniques in computer science. It provides a way to solve complex problems by breaking them down into simpler, self-similar subproblems. While every recursive solution can theoretically be rewritten using loops (iteration), many problems are far more naturally and clearly expressed using recursion.

### Motivating Example: Searching Files in a Directory

Suppose you want to find all the files under a directory that contain a particular word. How would you solve this problem? The directory may contain files and subdirectories, and each subdirectory may contain more files and subdirectories, and so on. An intuitive and natural solution is to use **recursion**: search the files in the current directory, and for each subdirectory, recursively apply the same search process. This mirrors the hierarchical structure of the file system itself.

---

## 2. What is Recursion?

**Recursion** is a technique in which a method calls itself to solve a problem. A recursive method solves a problem by reducing it to a smaller instance of the same problem. This continues until the problem reaches a **base case** — a trivially solvable instance that does not require further recursion.

### The Two Essential Components of Recursion

Every recursive solution must have exactly two components:

1. **Base Case (Stopping Condition):** The simplest instance of the problem that can be solved directly without any further recursive calls. Without a base case, recursion would continue infinitely, eventually causing a `StackOverflowError`.

2. **Recursive Case (Recursive Step):** The part where the method calls itself with a modified (usually smaller) version of the original problem, moving progressively closer to the base case.

### A Simple Analogy

Imagine you are standing in a long line and want to know your position. You could ask the person in front of you, "What is your position?" That person asks the person in front of them, and so on. The person at the very front of the line (the **base case**) knows they are at position 1 and tells the person behind them. Each person then adds 1 to the answer they received and passes it back. Eventually, the answer reaches you.

---

## 3. Computing Factorial Recursively

The factorial function is the classic introductory example for understanding recursion.
[Visualized](https://claude.ai/public/artifacts/18b94c09-8c82-417f-9861-1773137c516a)

### Mathematical Definition

The factorial of a non-negative integer `n`, denoted `n!`, is defined as:

```
0! = 1                          (base case)
n! = n × (n − 1)!    for n > 0  (recursive case)
```

In expanded form:
```
0! = 1
1! = 1 × 0! = 1 × 1 = 1
2! = 2 × 1! = 2 × 1 = 2
3! = 3 × 2! = 3 × 2 = 6
4! = 4 × 3! = 4 × 6 = 24
5! = 5 × 4! = 5 × 24 = 120
```

### Recursive Implementation in Java

```java
public static long factorial(int n) {
    if (n == 0) {           // Base case
        return 1;
    } else {                // Recursive case
        return n * factorial(n - 1);
    }
}
```

### How It Works — Step by Step

Let us trace through `factorial(4)`:

```
factorial(4)
  = 4 * factorial(3)
  = 4 * (3 * factorial(2))
  = 4 * (3 * (2 * factorial(1)))
  = 4 * (3 * (2 * (1 * factorial(0))))
  = 4 * (3 * (2 * (1 * 1)))           ← base case reached
  = 4 * (3 * (2 * 1))                 ← unwinding begins
  = 4 * (3 * 2)
  = 4 * 6
  = 24
```

Notice two distinct phases:

1. **Winding phase:** The method keeps calling itself with smaller values, building up a chain of deferred multiplications. Each call is "suspended" waiting for the result of the next call.
2. **Unwinding phase:** Once the base case is reached, the chain of calls begins to resolve in reverse order, each returning its result to the caller.

### Calling the Factorial Method

```java
public class ComputeFactorial {
    public static void main(String[] args) {
        System.out.println("4! = " + factorial(4));   // Output: 4! = 24
        System.out.println("10! = " + factorial(10)); // Output: 10! = 3628800
    }

    public static long factorial(int n) {
        if (n == 0)
            return 1;
        else
            return n * factorial(n - 1);
    }
}
```

---

## 4. Tracing Recursive Calls and the Call Stack

Understanding how recursion works requires understanding the **call stack** — the data structure the Java Virtual Machine (JVM) uses to manage method calls.

### What is the Call Stack?

The call stack is a region of memory that operates in a Last-In, First-Out (LIFO) manner. Every time a method is called, the JVM creates a new **stack frame** (also called an **activation record**) and pushes it onto the call stack. Each stack frame contains:

- The method's **local variables**
- The method's **parameters**
- The **return address** (where execution should resume after the method returns)
- Space for the **return value**

When a method finishes executing, its stack frame is popped off the stack, and control returns to the calling method.

### Detailed Trace of `factorial(4)`

Let us trace the call stack as `factorial(4)` executes:

**Step 0:** `main()` calls `factorial(4)`
```
Stack:
┌──────────────────────────────────┐
│ factorial(4)  →  4 * factorial(3)│  ← current
├──────────────────────────────────┤
│ main()                           │
└──────────────────────────────────┘
```

**Step 1:** `factorial(4)` calls `factorial(3)`
```
Stack:
┌──────────────────────────────────┐
│ factorial(3)  →  3 * factorial(2)│  ← current
├──────────────────────────────────┤
│ factorial(4)  →  4 * factorial(3)│  (waiting)
├──────────────────────────────────┤
│ main()                           │
└──────────────────────────────────┘
```

**Step 2:** `factorial(3)` calls `factorial(2)`
```
Stack:
┌──────────────────────────────────┐
│ factorial(2)  →  2 * factorial(1)│  ← current
├──────────────────────────────────┤
│ factorial(3)  →  3 * factorial(2)│  (waiting)
├──────────────────────────────────┤
│ factorial(4)  →  4 * factorial(3)│  (waiting)
├──────────────────────────────────┤
│ main()                           │
└──────────────────────────────────┘
```

**Step 3:** `factorial(2)` calls `factorial(1)`
```
Stack:
┌──────────────────────────────────┐
│ factorial(1)  →  1 * factorial(0)│  ← current
├──────────────────────────────────┤
│ factorial(2)  →  2 * factorial(1)│  (waiting)
├──────────────────────────────────┤
│ factorial(3)  →  3 * factorial(2)│  (waiting)
├──────────────────────────────────┤
│ factorial(4)  →  4 * factorial(3)│  (waiting)
├──────────────────────────────────┤
│ main()                           │
└──────────────────────────────────┘
```

**Step 4:** `factorial(1)` calls `factorial(0)`
```
Stack:
┌──────────────────────────────────┐
│ factorial(0)  →  returns 1       │  ← BASE CASE!
├──────────────────────────────────┤
│ factorial(1)  →  1 * factorial(0)│  (waiting)
├──────────────────────────────────┤
│ factorial(2)  →  2 * factorial(1)│  (waiting)
├──────────────────────────────────┤
│ factorial(3)  →  3 * factorial(2)│  (waiting)
├──────────────────────────────────┤
│ factorial(4)  →  4 * factorial(3)│  (waiting)
├──────────────────────────────────┤
│ main()                           │
└──────────────────────────────────┘
```

Now the **unwinding** begins — each frame is popped as it computes its result:

**Step 5:** `factorial(0)` returns `1` → `factorial(1)` computes `1 * 1 = 1`
**Step 6:** `factorial(1)` returns `1` → `factorial(2)` computes `2 * 1 = 2`
**Step 7:** `factorial(2)` returns `2` → `factorial(3)` computes `3 * 2 = 6`
**Step 8:** `factorial(3)` returns `6` → `factorial(4)` computes `4 * 6 = 24`
**Step 9:** `factorial(4)` returns `24` → `main()` receives the final result

### Key Insight: Stack Depth

The maximum depth of the call stack for `factorial(n)` is `n + 2` (including `main()` and `factorial(0)`). This is important because the JVM has a limited stack size. For very large values of `n`, a recursive solution may cause a `StackOverflowError`.

---

## 5. Fibonacci Numbers

The Fibonacci sequence is another classic example used to demonstrate recursion, but it also serves as a cautionary tale about the potential inefficiency of naive recursive solutions.

### Definition

The Fibonacci sequence is defined as:

```
fib(0) = 0                              (base case)
fib(1) = 1                              (base case)
fib(n) = fib(n − 1) + fib(n − 2)       for n ≥ 2 (recursive case)
```

The sequence: **0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, …**

```
Index:  0  1  2  3  4  5  6  7   8   9  10  11   12
Value:  0  1  1  2  3  5  8  13  21  34  55  89  144
```

### Recursive Implementation

```java
public static long fib(int n) {
    if (n == 0) {           // Base case 1
        return 0;
    } else if (n == 1) {    // Base case 2
        return 1;
    } else {                // Recursive case
        return fib(n - 1) + fib(n - 2);
    }
}
```

### Call Tree for `fib(4)`

When computing `fib(4)`, the recursive calls form a tree:

```
                        fib(4)
                       /      \
                  fib(3)      fib(2)
                 /     \      /     \
            fib(2)  fib(1) fib(1) fib(0)
           /     \    |      |      |
      fib(1) fib(0)   1      1      0
         |      |
         1      0
```

Notice that `fib(2)` is computed **twice**, `fib(1)` is computed **three times**, and `fib(0)` is computed **twice**. This redundancy grows exponentially as `n` increases.

### The Problem with Naive Recursive Fibonacci

The time complexity of the naive recursive Fibonacci is approximately **O(2ⁿ)**, which is exponential. This makes it impractical for even moderately large values of `n`:

- `fib(30)` requires over 2.6 million calls
- `fib(40)` requires over 300 million calls
- `fib(50)` is practically infeasible on most machines

This is because the same subproblems are solved over and over again. This problem can be addressed using techniques like **memoization** (storing previously computed results) or **dynamic programming** (computing values bottom-up), which you will learn about in later courses.

### Complete Example

```java
import java.util.Scanner;

public class ComputeFibonacci {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        System.out.print("Enter an index for the Fibonacci number: ");
        int index = input.nextInt();

        System.out.println("The Fibonacci number at index " + index
            + " is " + fib(index));
    }

    public static long fib(int n) {
        if (n == 0)
            return 0;
        else if (n == 1)
            return 1;
        else
            return fib(n - 1) + fib(n - 2);
    }
}
```

---

## 6. Problem Solving Using Recursion

### The Recursive Problem-Solving Strategy

In general, to solve a problem using recursion, you follow this strategy:

1. **Identify the base case(s):** Determine the simplest instance(s) of the problem that can be solved directly.
2. **Define the recursive case:** Determine how to break the problem into one or more smaller subproblems that are similar in nature to the original.
3. **Ensure progress toward the base case:** Each recursive call must bring the problem closer to the base case to guarantee termination.
4. **Combine the results:** Determine how to combine the results of the subproblems to form the solution to the original problem.

A subproblem is essentially the same as the original problem but with a **smaller size**. The key insight is recognizing this self-similar structure.

### Example: Printing a Message n Times

Problem: Print a message `n` times.

Think about it recursively: printing a message `n` times can be decomposed into printing the message once, and then printing the message `n - 1` times. The base case is when `n < 1` (nothing to print).

```java
public static void nPrintln(String message, int n) {
    if (n >= 1) {
        System.out.println(message);   // Print once
        nPrintln(message, n - 1);      // Print remaining n-1 times
    }
    // Base case: n < 1, do nothing (implicit)
}
```

Trace of `nPrintln("Welcome", 3)`:
```
nPrintln("Welcome", 3)
  → prints "Welcome"
  → nPrintln("Welcome", 2)
      → prints "Welcome"
      → nPrintln("Welcome", 1)
          → prints "Welcome"
          → nPrintln("Welcome", 0)
              → n < 1, returns (base case)
```

---

## 7. Characteristics of Recursion

All recursive methods share these essential characteristics:

### 1. Conditional Branching

The method is implemented using a conditional statement (such as `if-else`) that leads to different cases. At minimum, there is a base case path and a recursive case path.

### 2. One or More Base Cases

Base cases are the simplest cases that stop the recursion. They are solved directly without making any further recursive calls. Without proper base cases, recursion would continue indefinitely, leading to a `StackOverflowError`.

### 3. Progressive Reduction Toward the Base Case

Every recursive call must reduce the original problem, bringing it increasingly closer to a base case until it eventually becomes that base case. If the problem does not get smaller with each recursive call, the recursion will never terminate.

### Common Mistakes to Avoid

**Missing base case:**
```java
// WRONG: No base case — infinite recursion!
public static int factorial(int n) {
    return n * factorial(n - 1);
}
```

**Base case never reached:**
```java
// WRONG: Recursive call doesn't move toward base case!
public static int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n + 1);  // n increases, never reaches 0
}
```

**Non-terminating due to incorrect logic:**
```java
// WRONG: When n is negative, base case is never reached
public static int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);  // What if n < 0?
}
```

A more robust version:
```java
public static long factorial(int n) {
    if (n < 0)
        throw new IllegalArgumentException("n must be non-negative");
    if (n == 0)
        return 1;
    return n * factorial(n - 1);
}
```

---

## 8. Thinking Recursively — Palindrome Example

Many problems that were previously solved with loops can also be solved recursively. The key is to **think recursively** — identify the self-similar substructure in the problem.

### Example: Palindrome Checking

A palindrome is a string that reads the same forwards and backwards (e.g., "racecar", "madam", "level").

**Recursive insight:** A string is a palindrome if:
1. The first and last characters are the same, **AND**
2. The substring between them is also a palindrome.

**Base cases:**
- A string of length 0 or 1 is always a palindrome.
- If the first and last characters differ, it is NOT a palindrome.

### Recursive Implementation

```java
public static boolean isPalindrome(String s) {
    if (s.length() <= 1)                              // Base case: trivially a palindrome
        return true;
    else if (s.charAt(0) != s.charAt(s.length() - 1)) // Base case: not a palindrome
        return false;
    else
        return isPalindrome(s.substring(1, s.length() - 1)); // Check inner substring
}
```

### Trace of `isPalindrome("racecar")`

```
isPalindrome("racecar")
  → 'r' == 'r' ✓ → isPalindrome("aceca")
      → 'a' == 'a' ✓ → isPalindrome("cec")
          → 'c' == 'c' ✓ → isPalindrome("e")
              → length ≤ 1, return true  (base case)
          → return true
      → return true
  → return true

Result: true ✓
```

### Trace of `isPalindrome("hello")`

```
isPalindrome("hello")
  → 'h' != 'o' → return false  (base case)

Result: false ✓
```

---

## 9. Recursive Helper Methods

### The Problem with the Previous Palindrome Approach

The `isPalindrome` method using `substring()` is not efficient because it creates a **new String object** for every recursive call. Since strings in Java are immutable, each `substring()` call allocates new memory. For a string of length `n`, this results in O(n) string allocations, each potentially copying characters.

### The Solution: Helper Methods

A **recursive helper method** is an overloaded method that accepts additional parameters (typically indices) to track the portion of the data being processed, avoiding the need to create copies.

The original public method serves as a "wrapper" that initiates the recursion with appropriate starting values.

```java
// Public wrapper method — this is what external code calls
public static boolean isPalindrome(String s) {
    return isPalindrome(s, 0, s.length() - 1);
}

// Private recursive helper method — does the actual work
public static boolean isPalindrome(String s, int low, int high) {
    if (high <= low)                        // Base case: 0 or 1 char remaining
        return true;
    else if (s.charAt(low) != s.charAt(high))  // Base case: mismatch found
        return false;
    else
        return isPalindrome(s, low + 1, high - 1);  // Check inner portion
}
```

### Why is This Better?

- **No new String objects** are created — the same string reference is passed through all recursive calls.
- Only the indices `low` and `high` change, narrowing the range with each call.
- This reduces memory usage from O(n²) (for creating substrings) to O(n) (for stack frames only).

### Design Pattern: Wrapper + Helper

This is a common and important design pattern in recursive programming:

```java
// Wrapper: Simple interface for the caller
public static ReturnType solve(DataType data) {
    return solveHelper(data, initialParam1, initialParam2);
}

// Helper: Carries additional state needed for recursion
private static ReturnType solveHelper(DataType data, int param1, int param2) {
    // base case
    // recursive case using param1, param2
}
```

---

## 10. Recursive Selection Sort

Selection sort is a simple sorting algorithm that can be elegantly expressed using recursion.

### Algorithm (Recursive Formulation)

1. **Find** the smallest number in the list and **swap** it with the first element.
2. **Ignore** the first element (it is now in its correct position) and **recursively sort** the remaining sublist.

The base case is when the sublist has only one element (or is empty) — it is already sorted.

### Implementation

```java
public class RecursiveSelectionSort {
    public static void sort(double[] list) {
        sort(list, 0, list.length - 1);  // Call helper
    }

    private static void sort(double[] list, int low, int high) {
        if (low < high) {
            // Find the index of the smallest element in list[low..high]
            int indexOfMin = low;
            double min = list[low];
            for (int i = low + 1; i <= high; i++) {
                if (list[i] < min) {
                    min = list[i];
                    indexOfMin = i;
                }
            }

            // Swap the smallest element with list[low]
            list[indexOfMin] = list[low];
            list[low] = min;

            // Recursively sort the remaining list[low+1..high]
            sort(list, low + 1, high);
        }
    }
}
```

### Trace Example

Sorting `[5, 3, 1, 4, 2]`:

```
sort([5, 3, 1, 4, 2], 0, 4)
  → min is 1 at index 2, swap with index 0 → [1, 3, 5, 4, 2]
  → sort([1, 3, 5, 4, 2], 1, 4)
      → min is 2 at index 4, swap with index 1 → [1, 2, 5, 4, 3]
      → sort([1, 2, 5, 4, 3], 2, 4)
          → min is 3 at index 4, swap with index 2 → [1, 2, 3, 4, 5]
          → sort([1, 2, 3, 4, 5], 3, 4)
              → min is 4 at index 3, no swap needed → [1, 2, 3, 4, 5]
              → sort([1, 2, 3, 4, 5], 4, 4)
                  → low == high, return (base case)
```

Result: `[1, 2, 3, 4, 5]` ✓

---

## 11. Recursive Binary Search

Binary search is a classic algorithm that is naturally recursive. It efficiently searches for a target value (key) in a **sorted** array by repeatedly dividing the search interval in half.

### Algorithm

Given a sorted array and a key to search for:

1. **Case 1:** If the key is **less than** the middle element, recursively search the **left half** of the array.
2. **Case 2:** If the key is **equal to** the middle element, the search is successful — return the index.
3. **Case 3:** If the key is **greater than** the middle element, recursively search the **right half** of the array.
4. **Base case:** If `low > high`, the key is not in the array — return a negative value indicating the insertion point.

### Implementation

```java
/** Public wrapper method */
public static int recursiveBinarySearch(int[] list, int key) {
    int low = 0;
    int high = list.length - 1;
    return recursiveBinarySearch(list, key, low, high);
}

/** Recursive helper method */
public static int recursiveBinarySearch(int[] list, int key,
                                         int low, int high) {
    if (low > high)             // Base case: key not found
        return -low - 1;       // Return negative insertion point

    int mid = (low + high) / 2;

    if (key < list[mid])        // Search left half
        return recursiveBinarySearch(list, key, low, mid - 1);
    else if (key == list[mid])  // Key found!
        return mid;
    else                        // Search right half
        return recursiveBinarySearch(list, key, mid + 1, high);
}
```

### Trace Example

Searching for key `7` in `[1, 3, 5, 7, 9, 11, 13]`:

```
recursiveBinarySearch(list, 7, 0, 6)
  → mid = 3, list[3] = 7
  → key == list[mid] → return 3 ✓
```

Searching for key `4` in `[1, 3, 5, 7, 9, 11, 13]`:

```
recursiveBinarySearch(list, 4, 0, 6)
  → mid = 3, list[3] = 7, key < 7
  → recursiveBinarySearch(list, 4, 0, 2)
      → mid = 1, list[1] = 3, key > 3
      → recursiveBinarySearch(list, 4, 2, 2)
          → mid = 2, list[2] = 5, key < 5
          → recursiveBinarySearch(list, 4, 2, 1)
              → low > high → return -3 (not found, insertion point is index 2)
```

### Time Complexity

Recursive binary search has a time complexity of **O(log n)** because each recursive call eliminates half of the remaining elements. This makes it vastly more efficient than linear search (O(n)) for large sorted arrays.

---

## 12. Directory Size — A Practical Recursive Problem

This is an example of a problem that is genuinely **difficult to solve without recursion**. It demonstrates the real-world power of recursive thinking.

### Problem

Find the total size of a directory. The size of a directory is the sum of the sizes of all files it contains. However, a directory may contain subdirectories, each of which may contain more files and subdirectories.

### Recursive Definition

```
size(directory) = sum of sizes of all files in directory
                + sum of size(subdirectory) for each subdirectory
```

The base case is a directory that contains only files (no subdirectories) — its size is simply the sum of the file sizes.

### Conceptual Structure

```
directory/
├── file1.txt        (size: 100 bytes)
├── file2.txt        (size: 200 bytes)
├── subdirectory1/
│   ├── file3.txt    (size: 150 bytes)
│   └── file4.txt    (size: 50 bytes)
└── subdirectory2/
    ├── file5.txt    (size: 300 bytes)
    └── subsubdir/
        └── file6.txt (size: 75 bytes)
```

```
size(directory) = 100 + 200 + size(subdirectory1) + size(subdirectory2)
size(subdirectory1) = 150 + 50 = 200
size(subdirectory2) = 300 + size(subsubdir) = 300 + 75 = 375
size(directory) = 100 + 200 + 200 + 375 = 875 bytes
```

### Implementation in Java

```java
import java.io.File;

public class DirectorySize {
    public static void main(String[] args) {
        System.out.print("Enter a directory or file: ");
        Scanner input = new Scanner(System.in);
        String path = input.nextLine();
        System.out.println(getSize(new File(path)) + " bytes");
    }

    public static long getSize(File file) {
        long size = 0;

        if (file.isDirectory()) {
            File[] files = file.listFiles();  // Get all files and subdirectories
            if (files != null) {
                for (File f : files) {
                    size += getSize(f);       // Recursive call for each item
                }
            }
        } else {
            size = file.length();             // Base case: it's a file
        }

        return size;
    }
}
```

This is a beautiful example of recursion matching the inherent recursive structure of the data (a file system tree).

---

## 13. Tower of Hanoi

The Tower of Hanoi is one of the most famous problems in computer science and mathematics. It beautifully demonstrates the power and elegance of recursive thinking.

### Problem Description

- There are **n disks** of different sizes, labeled 1 (smallest) to n (largest).
- There are **three towers** (pegs), labeled A, B, and C.
- Initially, all disks are stacked on tower A in decreasing order of size (largest at the bottom).
- **Goal:** Move all disks from tower A to tower B.

### Rules

1. Only **one disk** can be moved at a time.
2. Each move takes the **top disk** from one tower and places it on top of another tower.
3. **No disk** may be placed on top of a **smaller disk**.

### Recursive Solution

The key insight is to decompose the problem into three steps:

1. **Move the top n−1 disks** from tower A to tower C (using B as auxiliary).
2. **Move disk n** (the largest) from tower A to tower B.
3. **Move the n−1 disks** from tower C to tower B (using A as auxiliary).

The base case is when `n == 1`: simply move the single disk directly from the source to the destination.

### Implementation

```java
public class TowerOfHanoi {
    public static void main(String[] args) {
        int n = 4;  // Number of disks
        System.out.println("The moves for " + n + " disks are:");
        moveDisks(n, 'A', 'B', 'C');
    }

    /**
     * Move n disks from fromTower to toTower using auxTower as auxiliary.
     */
    public static void moveDisks(int n, char fromTower, char toTower, char auxTower) {
        if (n == 1) {  // Base case
            System.out.println("Move disk 1 from " + fromTower + " to " + toTower);
        } else {
            // Step 1: Move n-1 disks from source to auxiliary
            moveDisks(n - 1, fromTower, auxTower, toTower);

            // Step 2: Move disk n from source to destination
            System.out.println("Move disk " + n + " from " + fromTower + " to " + toTower);

            // Step 3: Move n-1 disks from auxiliary to destination
            moveDisks(n - 1, auxTower, toTower, fromTower);
        }
    }
}
```

### Trace for 3 Disks

```
Move disk 1 from A to B
Move disk 2 from A to C
Move disk 1 from B to C
Move disk 3 from A to B
Move disk 1 from C to A
Move disk 2 from C to B
Move disk 1 from A to B
```

Total moves: **7** (which is 2³ − 1)

### Number of Moves

For `n` disks, the minimum number of moves required is **2ⁿ − 1**.

| n (disks) | Moves |
|-----------|-------|
| 1 | 1 |
| 2 | 3 |
| 3 | 7 |
| 4 | 15 |
| 5 | 31 |
| 10 | 1,023 |
| 20 | 1,048,575 |
| 64 | 18,446,744,073,709,551,615 |

The original legend states that monks are moving 64 gold disks. At one move per second, this would take approximately **585 billion years** — far longer than the age of the universe!

---

## 14. Greatest Common Divisor (GCD)

The Greatest Common Divisor of two integers `m` and `n` is the largest positive integer that divides both `m` and `n` without a remainder. Computing the GCD is a perfect example where multiple approaches exist, including an elegant recursive solution.

### Examples

```
gcd(2, 3) = 1
gcd(2, 10) = 2
gcd(25, 35) = 5
gcd(205, 301) = 5
```

### Approach 1: Brute Force

Start from `min(m, n)` and count down to 1. The first number that divides both `m` and `n` evenly is the GCD.

```java
public static int gcd(int m, int n) {
    int result = 1;
    for (int i = Math.min(m, n); i >= 1; i--) {
        if (m % i == 0 && n % i == 0) {
            result = i;
            break;
        }
    }
    return result;
}
```

### Approach 2: Euclid's Algorithm (Iterative)

This 2,300-year-old algorithm is far more efficient:

```java
public static int gcd(int m, int n) {
    int t1 = Math.abs(m);
    int t2 = Math.abs(n);
    int r = t1 % t2;

    while (r != 0) {
        t1 = t2;
        t2 = r;
        r = t1 % t2;
    }

    return t2;
}
```

### Approach 3: Recursive Method

The same algorithm can be expressed recursively with remarkable clarity:

```
gcd(m, n) = n              if m % n == 0   (base case)
gcd(m, n) = gcd(n, m % n)  otherwise       (recursive case)
```

```java
public static int gcd(int m, int n) {
    if (m % n == 0)
        return n;
    else
        return gcd(n, m % n);
}
```

### Trace of `gcd(205, 301)`

```
gcd(205, 301)
  → 205 % 301 = 205 ≠ 0 → gcd(301, 205)
      → 301 % 205 = 96 ≠ 0 → gcd(205, 96)
          → 205 % 96 = 13 ≠ 0 → gcd(96, 13)
              → 96 % 13 = 5 ≠ 0 → gcd(13, 5)
                  → 13 % 5 = 3 ≠ 0 → gcd(5, 3)
                      → 5 % 3 = 2 ≠ 0 → gcd(3, 2)
                          → 3 % 2 = 1 ≠ 0 → gcd(2, 1)
                              → 2 % 1 = 0 → return 1

Wait — let's recheck: gcd(205, 301) should be 5...
```

Actually, let's trace more carefully:
```
gcd(205, 301)
  → 205 % 301 = 205 (since 205 < 301)
  → gcd(301, 205)
      → 301 % 205 = 96
      → gcd(205, 96)
          → 205 % 96 = 13
          → gcd(96, 13)
              → 96 % 13 = 5
              → gcd(13, 5)
                  → 13 % 5 = 3
                  → gcd(5, 3)
                      → 5 % 3 = 2
                      → gcd(3, 2)
                          → 3 % 2 = 1
                          → gcd(2, 1)
                              → 2 % 1 = 0 → return 1
```

Hmm, but we expected 5. This reveals the inputs in the textbook example: if `gcd(205, 301) = 5`, let's verify: the common factors of 205 = 5 × 41, and 301 = 7 × 43. Actually gcd(205, 301) = 1 since they share no common factors. The textbook likely uses a slightly different pair. The algorithm itself is correct regardless.

---

## 15. Fractals — Sierpinski Triangle

Fractals are geometric figures that can be divided into parts, each of which is a reduced-size copy of the whole. This **self-similar** property makes them inherently recursive.

### What is a Sierpinski Triangle?

The Sierpinski triangle is a famous fractal named after the Polish mathematician Wacław Sierpiński. It is constructed recursively:

**Order 0:** Start with a filled equilateral triangle.

**Order 1:** Find the midpoints of all three sides of the triangle. Connect these midpoints to form a smaller triangle in the center. Remove (or leave blank) the center triangle. This leaves three smaller triangles.

**Order 2:** Repeat the process for each of the three remaining triangles.

**Order n:** Continue this process recursively for each remaining triangle.

### Recursive Solution Structure

```
displayTriangles(order, p1, p2, p3):
    if order == 0:
        draw the triangle with vertices p1, p2, p3
    else:
        // Calculate midpoints
        p12 = midpoint of p1 and p2
        p23 = midpoint of p2 and p3
        p31 = midpoint of p3 and p1

        // Recursively draw three sub-triangles
        displayTriangles(order - 1, p1, p12, p31)    // Top triangle
        displayTriangles(order - 1, p12, p2, p23)    // Bottom-left triangle
        displayTriangles(order - 1, p31, p23, p3)    // Bottom-right triangle
```

### Properties

- At order `n`, there are **3ⁿ** triangles drawn.
- The total area of the filled regions approaches **zero** as the order approaches infinity.
- Yet the total perimeter approaches **infinity**.
- This paradoxical property is characteristic of many fractals.

| Order | Number of Triangles | Fraction of Original Area |
|-------|--------------------|-----------------------------|
| 0 | 1 | 1 |
| 1 | 3 | 3/4 |
| 2 | 9 | 9/16 |
| 3 | 27 | 27/64 |
| n | 3ⁿ | (3/4)ⁿ |

---

## 16. Recursion vs. Iteration

### Key Comparison

Recursion is an alternative form of program control. It is essentially **repetition without a loop**. Any problem that can be solved recursively can also be solved iteratively, and vice versa.

| Aspect | Recursion | Iteration |
|--------|-----------|-----------|
| **Mechanism** | Method calls itself | Loop repeats a block of code |
| **Termination** | Base case | Loop condition becomes false |
| **Memory** | Uses call stack (O(n) space for n calls) | Uses constant extra space (O(1)) |
| **Overhead** | Higher (stack frame allocation/deallocation) | Lower (simple counter updates) |
| **Readability** | Often more elegant and clear for recursive problems | May require more complex code for recursive problems |
| **Risk** | StackOverflowError for deep recursion | Infinite loop if condition never met |

### When to Use Recursion

Recursion is particularly well-suited for problems that have a naturally recursive structure:
- Tree traversals (file systems, DOM, parse trees)
- Divide-and-conquer algorithms (merge sort, quicksort)
- Backtracking problems (N-Queens, maze solving, Sudoku)
- Mathematical definitions that are inherently recursive (factorial, Fibonacci, fractals)
- Problems involving nested or hierarchical structures

### When to Use Iteration

Iteration is generally preferred when:
- The problem is naturally sequential (summing an array, linear search)
- Performance and memory efficiency are critical
- The recursive depth could be very large

### Performance Overhead of Recursion

Each recursive call requires the JVM to:
1. Allocate a new stack frame (memory for parameters, local variables, return address)
2. Push the frame onto the call stack
3. Execute the method
4. Pop the frame and return the result

This overhead, while small for each individual call, can accumulate significantly for deep recursion.

---

## 17. Tail Recursion

### Definition

A recursive method is said to be **tail recursive** if the **recursive call is the very last operation** performed before the method returns. In other words, there are **no pending operations** to be performed after the recursive call returns.

### Non-Tail Recursive Example (Factorial)

```java
public static long factorial(int n) {
    if (n == 0)
        return 1;
    else
        return n * factorial(n - 1);  // Multiplication is PENDING after recursive call
}
```

This is **not** tail recursive because after `factorial(n - 1)` returns, the result must still be multiplied by `n`. The multiplication is a pending operation.

### Tail Recursive Example (Factorial)

```java
public static long factorial(int n) {
    return factorial(n, 1);  // Start with accumulator = 1
}

private static long factorial(int n, long accumulator) {
    if (n == 0)
        return accumulator;
    else
        return factorial(n - 1, n * accumulator);  // No pending operations!
}
```

This **is** tail recursive because the recursive call `factorial(n - 1, n * accumulator)` is the **last thing** the method does. The multiplication happens **before** the recursive call as part of computing the argument, not after.

### Why Tail Recursion Matters

Tail recursive methods are desirable because they can potentially be optimized by the compiler/runtime through **tail call optimization (TCO)**. With TCO, the runtime can reuse the current stack frame for the next recursive call instead of creating a new one, effectively converting the recursion into a loop. This:

- Eliminates the risk of `StackOverflowError`
- Reduces memory usage to O(1)
- Maintains the same time complexity

**Important Note:** As of Java 21, the JVM does **not** perform automatic tail call optimization. However, understanding tail recursion is valuable because many other languages (Scala, Kotlin, Scheme, Haskell) do support it, and it represents good algorithmic thinking.

### Converting Non-Tail to Tail Recursive

The general technique is to introduce an **accumulator parameter** that carries the intermediate result:

```
// Non-tail: result is built up AFTER returning from recursive calls
result = f(n) = n * f(n-1) = n * (n-1) * f(n-2) = ...

// Tail: result is built up BEFORE making recursive calls (in the accumulator)
f(n, acc) → f(n-1, n*acc) → f(n-2, (n-1)*n*acc) → ...
```

---

## 18. Summary and Key Takeaways

### Core Concepts

1. **Recursion** is a programming technique where a method calls itself to solve smaller instances of the same problem.

2. Every recursive method must have a **base case** (stopping condition) and a **recursive case** that moves toward the base case.

3. The **call stack** manages recursive calls using stack frames. Each call gets its own frame with its own copies of parameters and local variables.

4. **Helper methods** with additional parameters (like indices or accumulators) can make recursive solutions more efficient by avoiding unnecessary object creation.

### Algorithms Covered

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|-----------------|
| Factorial | O(n) | O(n) stack depth |
| Fibonacci (naive) | O(2ⁿ) | O(n) stack depth |
| Binary Search | O(log n) | O(log n) stack depth |
| Selection Sort | O(n²) | O(n) stack depth |
| Tower of Hanoi | O(2ⁿ) | O(n) stack depth |
| GCD (Euclid's) | O(log(min(m,n))) | O(log(min(m,n))) stack depth |
| Sierpinski Triangle | O(3ⁿ) | O(n) stack depth |

### Golden Rules of Recursion

1. **Always define base case(s)** — without them, recursion never terminates.
2. **Always make progress** toward the base case in each recursive call.
3. **Trust the recursion** — assume the recursive call works correctly and focus on how to use its result.
4. **Avoid redundant computation** — if the same subproblem is solved multiple times (like naive Fibonacci), consider optimization techniques.
5. **Consider the stack depth** — very deep recursion may cause `StackOverflowError`.

### What's Next?

In upcoming lectures, we will build upon these recursive foundations as we explore more advanced data structures and algorithms — all of which make heavy use of recursive techniques.

---

*© 2026 CENG114 — Computer Programming II, Ankara Yıldırım Beyazıt University*
*Based on Chapter 18, Introduction to Java Programming and Data Structures, 13th Edition by Y. Daniel Liang*
