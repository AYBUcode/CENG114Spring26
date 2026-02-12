# CENG114 — Search and Sort Algorithms (Iterative)

This document covers the 5 fundamental algorithms with advanced examples. All examples use primitive types and Strings only. Next week, we will move on to **recursive** versions of these algorithms.

---

## 1. Linear Search

Scans the array from beginning to end, one element at a time. Works on **unsorted** arrays.

**Time Complexity:** O(n)

### 1.1 Finding the Last Occurrence

Standard linear search returns at the first match. This version finds the last match — it does not return immediately upon finding a match, but continues scanning.

```java
public static int linearSearchLast(int[] arr, int target) {
    int lastIndex = -1;
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            lastIndex = i; // don't return, keep searching
        }
    }
    return lastIndex;
}
```

### 1.2 Counting Occurrences

Counts how many times the target value appears in the array.

```java
public static int countOccurrences(int[] arr, int target) {
    int count = 0;
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            count++;
        }
    }
    return count;
}
```

### 1.3 Finding All Indices

Returns a new array containing **all** indices where the target appears. Requires two passes: the first determines the size, the second fills in the indices.

```java
public static int[] findAllIndices(int[] arr, int target) {
    int count = countOccurrences(arr, target);
    int[] indices = new int[count];

    int idx = 0;
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            indices[idx] = i;
            idx++;
        }
    }
    return indices;
}
```

### 1.4 Case-Insensitive String Search

Searches a String array ignoring case. Uses `equalsIgnoreCase` instead of `equals`.

```java
public static int linearSearchString(String[] arr, String target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i].equalsIgnoreCase(target)) {
            return i;
        }
    }
    return -1;
}
```

```
String[] cities = {"Ankara", "Istanbul", "Izmir", "Bursa"};
linearSearchString(cities, "istanbul");  // → 1
```

### 1.5 Threshold Search (Approximate Matching)

Performs **approximate** matching in a `double` array. For example, "find the first temperature reading close to 36.6 (±0.5)".

```java
public static int searchWithThreshold(double[] arr, double target, double threshold) {
    for (int i = 0; i < arr.length; i++) {
        if (Math.abs(arr[i] - target) <= threshold) {
            return i;
        }
    }
    return -1;
}
```

```
double[] temps = {36.1, 37.8, 38.5, 36.7, 35.9};
searchWithThreshold(temps, 36.6, 0.5);  // → 0 (36.1, difference = 0.5)
```

### 1.6 Parallel Array Search

Two related arrays are used together: one holds keys, the other holds values. Search one array and return the corresponding value from the other.

```java
public static int findGradeByID(int[] studentIDs, int[] grades, int targetID) {
    for (int i = 0; i < studentIDs.length; i++) {
        if (studentIDs[i] == targetID) {
            return grades[i];
        }
    }
    return -1;
}
```

```
int[] ids    = {1001, 1002, 1003, 1004};
int[] grades = {85,   92,   78,   95};
findGradeByID(ids, grades, 1003);  // → 78
```

---

## 2. Binary Search

Searches by repeatedly dividing the array in half. **The array must be sorted!**

**Time Complexity:** O(log n)

### 2.1 Classic Binary Search + Step Counter

Tracks the number of comparisons to demonstrate the difference with linear search.

```java
public static int binarySearchWithSteps(int[] arr, int target) {
    int left = 0, right = arr.length - 1;
    int steps = 0;

    while (left <= right) {
        steps++;
        int mid = left + (right - left) / 2; // overflow protection

        if (arr[mid] == target) {
            System.out.println("Found after " + steps + " comparisons.");
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    System.out.println("Not found after " + steps + " comparisons.");
    return -1;
}
```

| Array Size | Linear Search | Binary Search |
|:----------:|:-------------:|:-------------:|
| 100        | ~50           | ~7            |
| 1,000      | ~500          | ~10           |
| 10,000     | ~5,000        | ~14           |
| 100,000    | ~50,000       | ~17           |

### 2.2 Binary Search on a String Array

`compareTo()` performs lexicographic comparison. A negative return means the element comes before the target; positive means it comes after.

```java
public static int binarySearchString(String[] arr, String target) {
    int left = 0, right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        int cmp = arr[mid].compareToIgnoreCase(target);

        if (cmp == 0) return mid;
        else if (cmp < 0) left = mid + 1;   // arr[mid] comes BEFORE target
        else right = mid - 1;                // arr[mid] comes AFTER target
    }
    return -1;
}
```

### 2.3 Finding the First and Last Occurrence

Standard binary search may find any occurrence among duplicates. These variants guarantee finding the **first** and **last** occurrence respectively.

```java
// First occurrence: after finding, shrink right to continue searching left
public static int binarySearchFirst(int[] arr, int target) {
    int left = 0, right = arr.length - 1, result = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) {
            result = mid;
            right = mid - 1;    // ← keep searching left
        } else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return result;
}

// Last occurrence: after finding, increase left to continue searching right
public static int binarySearchLast(int[] arr, int target) {
    int left = 0, right = arr.length - 1, result = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) {
            result = mid;
            left = mid + 1;     // → keep searching right
        } else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return result;
}
```

```
int[] arr = {1, 3, 5, 5, 5, 5, 5, 8, 9};
binarySearchFirst(arr, 5);  // → 2
binarySearchLast(arr, 5);   // → 6
// Occurrence count = 6 - 2 + 1 = 5
```

### 2.4 Finding the Insertion Point

If the element is not found, returns the index where it **should be inserted** to maintain sorted order.

```java
public static int findInsertionPoint(int[] arr, int target) {
    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return left; // insertion point
}
```

```
int[] data = {10, 20, 30, 40, 50};
findInsertionPoint(data, 35);  // → 3 (between 30 and 40)
```

### 2.5 Binary Search on a Descending Array

The comparison direction is reversed.

```java
public static int binarySearchDescending(int[] arr, int target) {
    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] > target) left = mid + 1;   // larger values on the left
        else right = mid - 1;
    }
    return -1;
}
```

---

## 3. Selection Sort

In each pass, finds the smallest element among the remaining elements and places it in the correct position.

**Time Complexity:** Always O(n²) — no best-case advantage.
**Advantage:** Performs the fewest number of **swaps** (at most n-1).

### 3.1 Selection Sort with Trace

Shows the array state after each pass along with the total operation count.

```java
public static void selectionSortTrace(int[] arr) {
    int n = arr.length;
    int swapCount = 0, compCount = 0;

    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {
            compCount++;
            if (arr[j] < arr[minIndex]) minIndex = j;
        }
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
            swapCount++;
        }
        // Print current array state here
    }
    System.out.println("Comparisons: " + compCount + ", Swaps: " + swapCount);
}
```

```
Original: [64, 25, 12, 22, 11]
Pass 1:   [11, 25, 12, 22, 64]   ← 11 selected
Pass 2:   [11, 12, 25, 22, 64]   ← 12 selected
Pass 3:   [11, 12, 22, 25, 64]   ← 22 selected
Pass 4:   [11, 12, 22, 25, 64]   ← already correct
```

### 3.2 Descending Sort

Finds the **maximum** instead of the minimum.

```java
for (int i = 0; i < n - 1; i++) {
    int maxIndex = i;
    for (int j = i + 1; j < n; j++) {
        if (arr[j] > arr[maxIndex]) maxIndex = j;  // > operator
    }
    // swap arr[i] and arr[maxIndex]
}
```

### 3.3 String Sorting

Uses `compareToIgnoreCase()` for lexicographic ordering.

```java
for (int j = i + 1; j < n; j++) {
    if (arr[j].compareToIgnoreCase(arr[minIndex]) < 0) {
        minIndex = j;
    }
}
```

### 3.4 Sorting by String Length

Instead of alphabetical order, shorter strings come first.

```java
if (arr[j].length() < arr[minIndex].length()) {
    minIndex = j;
}
```

```
Before: ["Java", "Programming", "is", "fun", "to", "learn"]
After:  ["is", "to", "fun", "Java", "learn", "Programming"]
```

### 3.5 Parallel Array Sorting

Student names are moved in sync when sorting by grade.

```java
public static void sortByGradeDescending(String[] names, int[] grades) {
    int n = grades.length;
    for (int i = 0; i < n - 1; i++) {
        int maxIndex = i;
        for (int j = i + 1; j < n; j++) {
            if (grades[j] > grades[maxIndex]) maxIndex = j;
        }
        // swap grades
        int tempG = grades[i]; grades[i] = grades[maxIndex]; grades[maxIndex] = tempG;
        // swap names (keep in sync!)
        String tempN = names[i]; names[i] = names[maxIndex]; names[maxIndex] = tempN;
    }
}
```

### 3.6 Partial Sort (Top-K)

Instead of sorting the entire array, places only the **K smallest elements**. Only K passes are needed.

```java
public static void partialSelectionSort(int[] arr, int k) {
    for (int i = 0; i < k && i < arr.length - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIndex]) minIndex = j;
        }
        int temp = arr[i]; arr[i] = arr[minIndex]; arr[minIndex] = temp;
    }
}
```

```
Original:        [45, 12, 78, 3, 56, 23, 9, 67]
After 3 passes:  [3, 9, 12, 78, 56, 45, 23, 67]
Top 3 smallest: 3, 9, 12 ✓  (rest remains unsorted)
```

---

## 4. Insertion Sort

Inserts each element into its correct position within the sorted portion by **shifting** elements. Think of sorting playing cards in your hand.

**Time Complexity:** Best O(n) — Worst O(n²)
**Advantage:** Extremely fast on nearly sorted data!

### 4.1 Insertion Sort with Trace

Shows which element is being inserted and where at each step.

```java
public static void insertionSortTrace(int[] arr) {
    int n = arr.length;
    int shiftCount = 0;

    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;

        System.out.print("Inserting " + key + ": ");

        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j]; // shift right
            shiftCount++;
            j--;
        }
        arr[j + 1] = key;
        // print current array state
    }
    System.out.println("Total shifts: " + shiftCount);
}
```

```
Original:     [5, 3, 8, 1, 2]
Inserting 3:  [3, 5, 8, 1, 2]
Inserting 8:  [3, 5, 8, 1, 2]   ← already in correct position
Inserting 1:  [1, 3, 5, 8, 2]
Inserting 2:  [1, 2, 3, 5, 8]
```

### 4.2 Sorting by Absolute Value

Useful for sorting error margins, deviations, etc.

```java
while (j >= 0 && Math.abs(arr[j]) > Math.abs(key)) {
    arr[j + 1] = arr[j];
    j--;
}
```

```
Before: [-5, 2, -8, 1, -3, 7]
After:  [1, 2, -3, -5, 7, -8]
        (|1|=1, |2|=2, |-3|=3, |-5|=5, |7|=7, |-8|=8)
```

### 4.3 Even Numbers First, Odd Numbers Last

A custom comparison function performs grouping and sorting simultaneously.

```java
private static boolean shouldSwap(int a, int b) {
    boolean aEven = (a % 2 == 0);
    boolean bEven = (b % 2 == 0);

    if (aEven && !bEven) return false; // even before odd: correct order
    if (!aEven && bEven) return true;  // odd before even: swap needed
    return a > b;                      // same parity: ascending order
}
```

```
Before: [7, 2, 9, 4, 1, 6, 3, 8]
After:  [2, 4, 6, 8, 1, 3, 7, 9]
```

### 4.4 Nearly Sorted Data Analysis

Insertion sort's greatest strength: it performs very few shifts on nearly sorted data.

```java
int[] nearlySorted = {1, 2, 4, 3, 5, 7, 6, 8, 9, 10};
// Only ~3 shifts needed (nearly O(n))

int[] randomArr = {9, 3, 7, 1, 5, 8, 2, 10, 4, 6};
// ~25 shifts needed (approaches O(n²))
```

---

## 5. Bubble Sort

Compares adjacent elements and "bubbles" the larger ones toward the end.

**Time Complexity:** Best O(n) — Worst O(n²)

### 5.1 Bubble Sort with Trace

Shows how many swaps were made in each pass.

```java
public static void bubbleSortTrace(int[] arr) {
    int n = arr.length;

    for (int i = 0; i < n - 1; i++) {
        int passSwaps = 0;
        for (int j = 0; j < n - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                passSwaps++;
            }
        }
        // Print array state after each pass
        if (passSwaps == 0) {
            System.out.println("Array is sorted, early exit!");
            break;
        }
    }
}
```

### 5.2 Optimized Bubble Sort (Early Termination)

If no swaps occur during a pass, the array is already sorted — exit early.

```java
for (int i = 0; i < n - 1; i++) {
    boolean swapped = false;
    for (int j = 0; j < n - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
            // swap
            swapped = true;
        }
    }
    if (!swapped) break; // ← EARLY EXIT
}
```

This optimization makes a significant difference on **nearly sorted** data:

| Data Pattern     | Without Optimization | With Optimization |
|:----------------:|:--------------------:|:-----------------:|
| Nearly sorted    | 9 passes             | 2 passes          |
| Already sorted   | 9 passes             | 1 pass            |
| Reverse sorted   | 9 passes             | 9 passes          |

### 5.3 Sorting by Digit Sum

Sorts numbers based on the sum of their digits — an interesting custom comparison example.

```java
public static int digitSum(int num) {
    num = Math.abs(num);
    int sum = 0;
    while (num > 0) {
        sum += num % 10;
        num /= 10;
    }
    return sum;
}
```

```
Before:      [91, 52, 38, 100, 19, 73, 46]
Digit sums:  [10,  7, 11,   1, 10, 10, 10]
After:       [100, 52, 38, 91, 19, 73, 46]
Digit sums:  [1,   7, 10, 10, 10, 10, 11]
```

### 5.4 Cocktail Shaker Sort (Bidirectional Bubble Sort)

Standard bubble sort only scans left to right. This variant alternates between scanning **forward and backward** each turn. It solves the "turtle" problem (a small element at the end of the array taking too long to reach the beginning).

```java
public static void cocktailShakerSort(int[] arr) {
    boolean swapped = true;
    int start = 0, end = arr.length - 1;

    while (swapped) {
        swapped = false;

        // → Forward pass: push large elements to the end
        for (int i = start; i < end; i++) {
            if (arr[i] > arr[i + 1]) {
                int temp = arr[i]; arr[i] = arr[i + 1]; arr[i + 1] = temp;
                swapped = true;
            }
        }
        end--;
        if (!swapped) break;

        swapped = false;

        // ← Backward pass: push small elements to the start
        for (int i = end; i > start; i--) {
            if (arr[i] < arr[i - 1]) {
                int temp = arr[i]; arr[i] = arr[i - 1]; arr[i - 1] = temp;
                swapped = true;
            }
        }
        start++;
    }
}
```

---

## Algorithm Comparison

### Search Algorithms

| Property | Linear Search | Binary Search |
|:---------|:------------:|:-------------:|
| Time (average) | O(n) | O(log n) |
| Requires sorted array? | No | **Yes** |
| Comparisons for 100K elements | ~50,000 | ~17 |

### Sorting Algorithms (n = 1000)

| Data Pattern | Selection Sort | Insertion Sort | Bubble Sort |
|:-------------|:--------------:|:--------------:|:-----------:|
| Already sorted | ~500,000 | ~999 | ~999 |
| Nearly sorted | ~500,000 | ~1,100 | ~1,100 |
| Random | ~500,000 | ~250,000 | ~250,000 |
| Reverse sorted | ~500,000 | ~500,000 | ~500,000 |

### Summary

| Algorithm | Best | Average | Worst | Stable? |
|:----------|:----:|:-------:|:-----:|:-------:|
| Selection Sort | O(n²) | O(n²) | O(n²) | No |
| Insertion Sort | **O(n)** | O(n²) | O(n²) | Yes |
| Bubble Sort | **O(n)** | O(n²) | O(n²) | Yes |

**Key Takeaways:**

- **Selection Sort** always performs the same number of comparisons, but makes the fewest **swaps**. Preferred when swap cost is high.
- **Insertion Sort** is the best choice for nearly sorted data. In practice, it is frequently used for small arrays.
- **Bubble Sort** is fast on sorted data with early termination, but is generally the slowest of the three.

> **Next Week:** Recursive approaches and O(n log n) algorithms (Merge Sort, Quick Sort).
