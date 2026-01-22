# JAVA FUNDAMENTAL — ARRAYS (Publication‑Quality Study Notes)

> Target: **WIX1002 / Fundamentals of Programming** level (but written so you can *master* arrays for exams + real code).

---

## Table of Contents

1. [Source Material Critique (What’s good / missing / unclear)](#source-material-critique)
2. [Introduction: Why Arrays Exist](#introduction-why-arrays-exist)
3. [Prerequisites & Mental Models](#prerequisites--mental-models)
4. [Fundamentals (Building Blocks)](#fundamentals-building-blocks)

   * [Terminology](#terminology)
   * [Arrays in Java Memory (What “reference type” really means)](#arrays-in-java-memory-what-reference-type-really-means)
   * [Declaration vs Creation vs Initialization](#declaration-vs-creation-vs-initialization)
   * [Indexing & `.length`](#indexing--length)
   * [Default Values](#default-values)
5. [Core Concepts (Main Topics)](#core-concepts-main-topics)

   * [Traversing Arrays (for vs enhanced for)](#traversing-arrays-for-vs-enhanced-for)
   * [Updating Elements Safely](#updating-elements-safely)
   * [Arrays as Parameters & Return Values](#arrays-as-parameters--return-values)
   * [Copying Arrays (shallow vs deep)](#copying-arrays-shallow-vs-deep)
   * [Comparing Arrays](#comparing-arrays)
   * [Arrays of Objects](#arrays-of-objects)
6. [Multidimensional Arrays](#multidimensional-arrays)

   * [2D Arrays as “Array of Arrays”](#2d-arrays-as-array-of-arrays)
   * [Rectangular vs Ragged (Jagged)](#rectangular-vs-ragged-jagged)
7. [Searching](#searching)

   * [Linear Search](#linear-search)
   * [Binary Search](#binary-search)
   * [Library Alternatives](#library-alternatives)
8. [Sorting](#sorting)

   * [Bubble Sort (for learning)](#bubble-sort-for-learning)
   * [Modern Sorting Alternatives](#modern-sorting-alternatives)
9. [Integration & Combinations](#integration--combinations)

   * [Arrays + Input (Scanner)](#arrays--input-scanner)
   * [Arrays + File I/O pattern](#arrays--file-io-pattern)
   * [Arrays vs ArrayList (When size changes)](#arrays-vs-arraylist-when-size-changes)
10. [Best Practices & Modern Approaches](#best-practices--modern-approaches)
11. [Common Mistakes (❌ Wrong → ✅ Correct)](#common-mistakes--wrong--correct)
12. [Quick Reference (Cheat Sheets)](#quick-reference-cheat-sheets)
13. [Practice: Conceptual + Coding (Easy → Hard) + Solutions](#practice-conceptual--coding-easy--hard--solutions)
14. [Further Study](#further-study)

---

## Source Material Critique

### What was well‑explained ✅

* Basic definition + indexing rules (0 to N‑1).
* Syntax of declaring arrays with `new`, and initializer literals like `{1,2,3}`.
* The idea of `.length` and out‑of‑bounds exceptions.
* Intro to 2D arrays + ragged arrays.
* Bubble sort + linear/binary search were introduced at a high level.

### Gaps / missing content ❗

These are the main things you **need** to master arrays but were missing or under‑explained:

1. **Java memory model for arrays (critical)**

   * Arrays are *objects*; variables store references.
   * “Contiguous memory” is an oversimplification in Java (especially for 2D arrays).

2. **Copying vs assigning arrays**

   * `b = a;` does **not** copy elements.
   * Shallow vs deep copy (especially for object arrays and 2D arrays).

3. **Comparing arrays**

   * `==` compares references, not contents.
   * Correct ways: `Arrays.equals`, `Arrays.deepEquals`.

4. **Arrays of objects** (the slides mention it, but don’t teach it)

   * How to create `Student[]`, handle `null` elements, etc.

5. **Library tools students actually use**

   * Printing: `Arrays.toString`, `Arrays.deepToString`
   * Sorting/searching: `Arrays.sort`, `Arrays.binarySearch`

6. **Complexity & decision‑making**

   * When to use linear vs binary search.
   * Why bubble sort is rarely used outside learning.

7. **Common pitfalls**

   * Off‑by‑one errors.
   * Enhanced for‑loop “can’t modify” nuance (what it really means).

### Organization issues 🧩

* The flow jumps quickly from “arrays exist” → “bubble sort/search” without building deeper fundamentals like copying, comparing, and array‑method interaction.
* Multidimensional arrays were introduced but not connected to real problems (tables, grids, score matrices).

### Errors / outdated practices ⚠️

* **Bubble sort implementation** shown is correct but **unoptimized** (does extra comparisons each pass). In practice, you shrink the inner loop bound and optionally early‑exit.
* “Contiguous memory locations” should be treated as a *conceptual* mental model for 1D primitive arrays, not a strict guarantee for all Java arrays.

### How these notes improve it ✨

* Start with **why** arrays exist and when to choose them.
* Build a correct mental model: **reference semantics**, default values, bounds.
* Add missing “real exam traps”: copying, comparing, method passing.
* Provide **complete runnable programs** (minimal → realistic → advanced) with edge cases.
* Include decision trees, comparison tables, best practices, and wrong/right examples.

---

## Introduction: Why Arrays Exist

### The problem arrays solve

You often need to store **many values of the same type** (scores, prices, IDs, sensor readings, etc.). Without arrays, you’d write:

```java
int score1, score2, score3, score4, score5;
```

That becomes impossible at scale.

### What arrays give you

* **One name** for many values.
* **Indexed access**: access element `i` instantly.
* **Fast traversal** with loops.

### Real‑world analogy

Think of an array like **numbered lockers** in a hallway:

* One “locker set name”: `scores`
* Each locker has an index: `scores[0]`, `scores[1]`, ...
* `.length` = number of lockers

---

## Prerequisites & Mental Models

You should already be comfortable with:

* Variables + primitive types (`int`, `double`, `char`, `boolean`)
* `if/else`
* Loops (`for`, `while`)
* Methods (parameters + return)
* `String` basics

> **Key mental model (must memorize):**
> In Java, an array variable stores a **reference** to an array object.

---

## Fundamentals (Building Blocks)

### Terminology

* **Array**: A fixed‑size container holding **N** elements of the **same type**.
* **Element**: A single value stored in the array.
* **Index / subscript**: The position number, starting from `0`.
* **Bounds**: Valid indices are `0` to `length - 1`.
* **Length**: Number of elements (a *field*): `arr.length`.
* **Reference**: A “pointer‑like” value that refers to an object in memory.

---

### Arrays in Java Memory (What “reference type” really means)

#### 1D array memory model

```text
Stack (local variables)                 Heap (objects)

int[] a  ────────────────▶    [ 10 ][ 20 ][ 30 ]
                              index: 0    1    2
```

* `a` is **not** the data. It is a reference to the array object.

#### What happens on assignment

```java
int[] a = {10, 20, 30};
int[] b = a;          // NOT a copy
b[0] = 999;
System.out.println(a[0]); // 999
```

Because both `a` and `b` point to the *same array*.

> **Memory aid:** `b = a;` means “b becomes another label for the same lockers.”

---

### Declaration vs Creation vs Initialization

#### 1) Declaration (create the variable)

```java
int[] arr;   // arr exists, but points to nothing yet
```

At this point, `arr` is uninitialized → using it causes a compile error.

#### 2) Creation (allocate the array object)

```java
arr = new int[5];  // creates 5 ints, all default to 0
```

#### 3) Initialization (give specific values)

```java
int[] nums = {1, 2, 3, 4};
```

#### Preferred style

```java
int[] nums = new int[10]; // recommended
int nums2[] = new int[10]; // allowed, but less common
```

---

### Indexing & `.length`

* Access element: `arr[i]`
* Update element: `arr[i] = value;`
* Size of array: `arr.length` (**field**, not method)

```java
int[] a = {5, 6, 7};
System.out.println(a.length); // 3
System.out.println(a[0]);     // 5
System.out.println(a[a.length - 1]); // last element → 7
```

> **⚠️ Pitfall:** `a.length()` is wrong. Arrays use `a.length`.

---

### Default Values

When you allocate with `new`, elements auto‑initialize:

| Type                                | Default                     |
| ----------------------------------- | --------------------------- |
| `int`, `short`, `byte`, `long`      | `0`                         |
| `double`, `float`                   | `0.0`                       |
| `char`                              | `\u0000` (looks like empty) |
| `boolean`                           | `false`                     |
| Reference types (`String`, objects) | `null`                      |

```java
String[] names = new String[3];
System.out.println(names[0]); // null
```

---

## Core Concepts (Main Topics)

### Traversing Arrays (for vs enhanced for)

#### Why traversal matters

Most array work is “do something to every element”: sum, count, transform, search.

#### 1) Index‑based `for` loop (most powerful)

* Use when you need the index or want to modify elements.

```java
import java.util.Arrays;

public class ForLoopTraversal {
    public static void main(String[] args) {
        int[] a = {10, 20, 30};

        for (int i = 0; i < a.length; i++) {
            a[i] = a[i] * 2; // modify
        }

        System.out.println(Arrays.toString(a)); // [20, 40, 60]
    }
}
```

#### 2) Enhanced `for` loop (for‑each)

* Use when you only need values and won’t replace elements.

```java
public class ForEachTraversal {
    public static void main(String[] args) {
        int[] a = {10, 20, 30};
        int sum = 0;

        for (int value : a) {
            sum += value;
        }

        System.out.println(sum); // 60
    }
}
```

#### Why for‑each “can’t modify” (the real reason)

This is the common confusion:

```java
int[] a = {1, 2, 3};
for (int v : a) {
    v = 999; // modifies only the local variable v
}
System.out.println(a[0]); // still 1
```

Because `v` is a **copy** of the element value.

> **Nuance:** For arrays of objects, you *can* modify the object’s fields via `for-each` (because the copied value is a reference), but you still can’t replace the array slot cleanly.

---

### Updating Elements Safely

#### Minimal example: clamp negative values to 0

```java
import java.util.Arrays;

public class ClampNegatives {
    public static void main(String[] args) {
        int[] a = {5, -2, 7, -1};

        for (int i = 0; i < a.length; i++) {
            if (a[i] < 0) {
                a[i] = 0;
            }
        }

        System.out.println(Arrays.toString(a)); // [5, 0, 7, 0]
    }
}
```

#### Edge cases checklist

* Empty arrays: `new int[0]` (loops should still work)
* `null` array references (must check before `.length`)

```java
public class NullArrayGuard {
    public static void main(String[] args) {
        int[] a = null;

        if (a == null) {
            System.out.println("Array not initialized!");
            return;
        }

        System.out.println(a.length);
    }
}
```

---

### Arrays as Parameters & Return Values

#### Why this exists

Functions (methods) become reusable when they can receive or return collections.

#### Key mechanism

Java is **call‑by‑value**.

* For primitives: value is copied.
* For arrays: the **reference** is copied.

  * Both caller and method refer to the same array object.

#### Minimal example: in‑place modification

```java
import java.util.Arrays;

public class ArrayParameterInPlace {
    public static void main(String[] args) {
        int[] a = {1, 2, 3};
        doubleAll(a);
        System.out.println(Arrays.toString(a)); // [2, 4, 6]
    }

    static void doubleAll(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            arr[i] *= 2;
        }
    }
}
```

#### Reassignment does *not* affect caller

```java
import java.util.Arrays;

public class ArrayParameterReassign {
    public static void main(String[] args) {
        int[] a = {1, 2, 3};
        reassign(a);
        System.out.println(Arrays.toString(a)); // still [1, 2, 3]
    }

    static void reassign(int[] arr) {
        arr = new int[] {999, 999}; // only local reference changes
    }
}
```

#### Returning arrays (common pattern)

```java
import java.util.Arrays;

public class ReturnArrayExample {
    public static void main(String[] args) {
        int[] squares = buildSquares(5);
        System.out.println(Arrays.toString(squares)); // [0, 1, 4, 9, 16]
    }

    static int[] buildSquares(int n) {
        if (n < 0) {
            throw new IllegalArgumentException("n must be >= 0");
        }

        int[] a = new int[n];
        for (int i = 0; i < n; i++) {
            a[i] = i * i;
        }
        return a;
    }
}
```

---

### Copying Arrays (shallow vs deep)

#### Why this exists

You often need a **copy** so changes don’t affect the original.

#### 0) The trap: assignment is NOT copying

```java
int[] a = {1, 2, 3};
int[] b = a; // alias, same array
```

#### 1) Shallow copy for 1D primitive arrays (good enough)

✅ Options (all copy elements):

* `a.clone()`
* `Arrays.copyOf(a, a.length)`
* `System.arraycopy(...)`

```java
import java.util.Arrays;

public class Copy1DPrimitives {
    public static void main(String[] args) {
        int[] a = {1, 2, 3};
        int[] b = a.clone();

        b[0] = 999;
        System.out.println(Arrays.toString(a)); // [1, 2, 3]
        System.out.println(Arrays.toString(b)); // [999, 2, 3]
    }
}
```

#### 2) Shallow copy for object arrays (⚠️ still shares objects)

```java
import java.util.Arrays;

class Box {
    int value;
    Box(int value) { this.value = value; }
}

public class ShallowCopyObjects {
    public static void main(String[] args) {
        Box[] a = { new Box(1), new Box(2) };
        Box[] b = a.clone(); // copies references

        b[0].value = 999; // modifies the same Box object

        System.out.println(a[0].value); // 999
        System.out.println(b[0].value); // 999
        System.out.println(a == b);     // false (arrays different)
        System.out.println(a[0] == b[0]); // true (same Box)
    }
}
```

#### 3) Deep copy (when you need independent objects)

Deep copy means you also copy the objects.

```java
class Box2 {
    int value;
    Box2(int value) { this.value = value; }
    Box2(Box2 other) { this.value = other.value; }
}

public class DeepCopyObjects {
    public static void main(String[] args) {
        Box2[] a = { new Box2(1), new Box2(2) };
        Box2[] b = new Box2[a.length];

        for (int i = 0; i < a.length; i++) {
            b[i] = new Box2(a[i]);
        }

        b[0].value = 999;
        System.out.println(a[0].value); // 1
        System.out.println(b[0].value); // 999
    }
}
```

---

### Comparing Arrays

#### The trap

```java
int[] a = {1, 2};
int[] b = {1, 2};
System.out.println(a == b); // false
```

Because `==` compares references.

#### Correct approaches

```java
import java.util.Arrays;

public class CompareArrays {
    public static void main(String[] args) {
        int[] a = {1, 2};
        int[] b = {1, 2};

        System.out.println(Arrays.equals(a, b)); // true
    }
}
```

2D arrays use `deepEquals`:

```java
import java.util.Arrays;

public class Compare2DArrays {
    public static void main(String[] args) {
        int[][] x = {{1,2},{3,4}};
        int[][] y = {{1,2},{3,4}};

        System.out.println(Arrays.equals(x, y));      // false
        System.out.println(Arrays.deepEquals(x, y));  // true
    }
}
```

---

### Arrays of Objects

#### Why this matters

Real programs store objects: `Student[]`, `Contact[]`, etc.

#### Minimal example

```java
class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class ObjectArrayMinimal {
    public static void main(String[] args) {
        Student[] students = new Student[2];
        students[0] = new Student("Ali", 19);
        students[1] = new Student("Tan", 20);

        for (Student s : students) {
            System.out.println(s.name + ": " + s.age);
        }
    }
}
```

#### Common pitfall: `null` elements

```java
Student[] students = new Student[3];
System.out.println(students[0].name); // NullPointerException
```

Fix: initialize each element before using.

---

## Multidimensional Arrays

### 2D Arrays as “Array of Arrays”

Java 2D arrays are not one big grid; they are arrays whose elements are arrays:

```text
int[][] grid
  |
  +--> grid[0] ---> int[] row0
  +--> grid[1] ---> int[] row1
  +--> grid[2] ---> int[] row2
```

This explains ragged arrays.

---

### Rectangular vs Ragged (Jagged)

#### Rectangular (same columns each row)

```java
int[][] table = new int[3][4]; // 3 rows, 4 columns
```

Traversal:

```java
public class Rectangular2D {
    public static void main(String[] args) {
        int[][] table = {
            {1, 2, 3, 4},
            {5, 6, 7, 8},
            {9, 10, 11, 12}
        };

        int sum = 0;
        for (int r = 0; r < table.length; r++) {
            for (int c = 0; c < table[r].length; c++) {
                sum += table[r][c];
            }
        }

        System.out.println(sum); // 78
    }
}
```

#### Ragged (different columns per row)

```java
int[][] num = new int[3][];
num[0] = new int[3];
num[1] = new int[10];
num[2] = new int[5];
```

Complete runnable example:

```java
import java.util.Arrays;

public class Ragged2D {
    public static void main(String[] args) {
        int[][] num = new int[3][];
        num[0] = new int[3];
        num[1] = new int[2];
        num[2] = new int[5];

        // Fill each row with its column index
        for (int r = 0; r < num.length; r++) {
            for (int c = 0; c < num[r].length; c++) {
                num[r][c] = c;
            }
        }

        // Print
        for (int r = 0; r < num.length; r++) {
            System.out.println(Arrays.toString(num[r]));
        }
    }
}
```

> **⚠️ Pitfall:** `num[r].length` can be different for each row. Never assume a fixed column size unless you created it rectangular.

---

## Searching

### Linear Search

#### Why it exists

Works even if the array is **unsorted**.

#### Mechanism

Check each element from left to right until found.

#### Minimal example

```java
public class LinearSearchMinimal {
    public static void main(String[] args) {
        int[] a = {7, 3, 9, 1};
        System.out.println(linearSearch(a, 9));  // 2
        System.out.println(linearSearch(a, 100)); // -1
    }

    static int linearSearch(int[] arr, int key) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == key) {
                return i;
            }
        }
        return -1;
    }
}
```

#### When to use ✅

* Array is small
* Array is unsorted
* You only search once or a few times

#### When NOT to use ❌

* Large array with many repeated searches → sort once, then binary search

---

### Binary Search

#### Why it exists

For **sorted** arrays, it finds items fast by eliminating half each step.

#### Key requirement

✅ Array must be sorted in ascending order.

#### Minimal example (iterative)

```java
public class BinarySearchMinimal {
    public static void main(String[] args) {
        int[] a = {1, 3, 5, 7, 9, 11};
        System.out.println(binarySearch(a, 7));  // 3
        System.out.println(binarySearch(a, 2));  // -1
    }

    static int binarySearch(int[] arr, int key) {
        int low = 0;
        int high = arr.length - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2; // safer than (low+high)/2
            if (arr[mid] == key) {
                return mid;
            } else if (key < arr[mid]) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }

        return -1;
    }
}
```

#### When to use ✅

* Array is already sorted
* Many searches
* Large data

#### When NOT to use ❌

* Array is unsorted and you only search once

---

### Library Alternatives

In real Java code, you often use:

* `Arrays.sort(arr)`
* `Arrays.binarySearch(arr, key)`
* `Arrays.toString(arr)`

Example:

```java
import java.util.Arrays;

public class ArraysLibraryDemo {
    public static void main(String[] args) {
        int[] a = {9, 1, 7, 3};

        Arrays.sort(a);
        System.out.println(Arrays.toString(a)); // [1, 3, 7, 9]

        int idx = Arrays.binarySearch(a, 7);
        System.out.println(idx); // 2
    }
}
```

---

## Sorting

### Bubble Sort (for learning)

#### Why it exists

It’s easy to understand swapping adjacent elements.

#### How it works

* Multiple passes
* Each pass “bubbles” the largest remaining element to the end

#### Correct but better implementation (shrinking inner loop + early exit)

```java
import java.util.Arrays;

public class BubbleSortImproved {
    public static void main(String[] args) {
        int[] b = {5, 1, 4, 2, 8};
        bubbleSort(b);
        System.out.println(Arrays.toString(b)); // [1, 2, 4, 5, 8]
    }

    static void bubbleSort(int[] b) {
        for (int pass = 0; pass < b.length - 1; pass++) {
            boolean swapped = false;

            for (int i = 0; i < b.length - 1 - pass; i++) {
                if (b[i] > b[i + 1]) {
                    int tmp = b[i];
                    b[i] = b[i + 1];
                    b[i + 1] = tmp;
                    swapped = true;
                }
            }

            if (!swapped) {
                break; // already sorted
            }
        }
    }
}
```

#### Complexity

* Worst: **O(n²)** → slow for large arrays

> **✅ Exam tip:** You should understand bubble sort because it tests loops + swapping logic.
> **Industry tip:** Prefer `Arrays.sort`.

---

### Modern Sorting Alternatives

| Goal                                        | Best option                      | Why             |
| ------------------------------------------- | -------------------------------- | --------------- |
| Sort primitive arrays (`int[]`, `double[]`) | `Arrays.sort(a)`                 | Fast, optimized |
| Sort objects (`Student[]`)                  | `Arrays.sort(a, comparator)`     | Custom ordering |
| Need dynamic size                           | `ArrayList` + `Collections.sort` | Resizable list  |

Example sorting objects:

```java
import java.util.Arrays;
import java.util.Comparator;

class Student2 {
    String name;
    int score;

    Student2(String name, int score) {
        this.name = name;
        this.score = score;
    }

    @Override
    public String toString() {
        return name + "(" + score + ")";
    }
}

public class SortObjectArray {
    public static void main(String[] args) {
        Student2[] s = {
            new Student2("Ali", 78),
            new Student2("Tan", 92),
            new Student2("Fong", 85)
        };

        Arrays.sort(s, Comparator.comparingInt(stu -> stu.score));
        System.out.println(Arrays.toString(s));
    }
}
```

---

## Integration & Combinations

### Arrays + Input (Scanner)

#### Realistic example: read 5 scores and compute average

```java
import java.util.Scanner;

public class ScoresAverage {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int[] scores = new int[5];

        for (int i = 0; i < scores.length; i++) {
            System.out.print("Enter score " + (i + 1) + ": ");
            scores[i] = sc.nextInt();
        }

        int sum = 0;
        for (int x : scores) {
            sum += x;
        }

        double avg = (double) sum / scores.length;
        System.out.println("Average = " + avg);

        sc.close();
    }
}
```

### Arrays + File I/O pattern

A common pattern when reading from a file is:

1. read unknown amount → store in `ArrayList`
2. convert to array if needed

```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class FileToArray {
    public static void main(String[] args) throws FileNotFoundException {
        // numbers.txt contains integers separated by spaces/newlines
        List<Integer> temp = new ArrayList<>();

        try (Scanner sc = new Scanner(new File("numbers.txt"))) {
            while (sc.hasNextInt()) {
                temp.add(sc.nextInt());
            }
        }

        int[] a = new int[temp.size()];
        for (int i = 0; i < temp.size(); i++) {
            a[i] = temp.get(i);
        }

        System.out.println("Read " + a.length + " numbers.");
    }
}
```

---

### Arrays vs ArrayList (When size changes)

#### Why this matters

Arrays are **fixed size**. If you don’t know the final size, arrays can become painful.

#### Quick decision tree

```text
Do you know the size in advance?
  ├─ Yes → Use array
  └─ No  → Use ArrayList (then convert if needed)

Do you need maximum performance & primitives?
  ├─ Yes → int[] / double[]
  └─ No  → ArrayList<Integer> / List<T>
```

#### Comparison table

| Feature           | Array (`int[]`)   | ArrayList (`ArrayList<Integer>`) |
| ----------------- | ----------------- | -------------------------------- |
| Size changes      | ❌ No              | ✅ Yes (`add/remove`)             |
| Stores primitives | ✅ Yes             | ❌ No (uses wrappers)             |
| Speed             | ✅ Fast            | ⚠️ Slight overhead               |
| Syntax            | ✅ Simple          | ✅ Rich methods                   |
| Best for          | fixed collections | dynamic collections              |

---

## Best Practices & Modern Approaches

1. **Always loop with `< arr.length`** (not `<=`).
2. **Use `Arrays.toString`** for debugging instead of manual prints.
3. Prefer **`Arrays.sort`** over bubble sort in real code.
4. Use **defensive copies** when returning arrays from classes (avoid exposing internals).
5. Validate inputs:

   * `null` arrays
   * invalid indices
   * negative sizes
6. For 2D arrays, always use `arr[r].length` for columns.

> **Security/robustness idea:** exposing your internal array lets other code mutate it. Prefer returning a copy.

---

## Common Mistakes (❌ Wrong → ✅ Correct)

### 1) Off‑by‑one loop

❌

```java
for (int i = 0; i <= a.length; i++) {
    System.out.println(a[i]);
}
```

✅

```java
for (int i = 0; i < a.length; i++) {
    System.out.println(a[i]);
}
```

### 2) Thinking assignment copies arrays

❌

```java
int[] b = a;
```

✅

```java
int[] b = a.clone();
```

### 3) Comparing arrays with `==`

❌

```java
if (a == b) { ... }
```

✅

```java
import java.util.Arrays;
if (Arrays.equals(a, b)) { ... }
```

### 4) Using for‑each to modify elements

❌

```java
for (int v : a) { v *= 2; }
```

✅

```java
for (int i = 0; i < a.length; i++) { a[i] *= 2; }
```

### 5) Assuming 2D arrays are rectangular

❌

```java
for (int c = 0; c < grid[0].length; c++) {
    // might crash if a later row is shorter
}
```

✅

```java
for (int r = 0; r < grid.length; r++) {
    for (int c = 0; c < grid[r].length; c++) {
        ...
    }
}
```

---

## Quick Reference (Cheat Sheets)

### Syntax quick‑map

```java
// declare
int[] a;

// create (allocate)
a = new int[10];

// declare+create
int[] b = new int[10];

// initialize with values
int[] c = {1, 2, 3};

// access
int x = c[0];

// update
c[0] = 999;

// length
int n = c.length;
```

### Useful `java.util.Arrays` methods

| Method                          | What it does               |
| ------------------------------- | -------------------------- |
| `Arrays.toString(int[])`        | prints 1D arrays nicely    |
| `Arrays.deepToString(Object[])` | prints nested arrays       |
| `Arrays.equals(a, b)`           | compare 1D contents        |
| `Arrays.deepEquals(a, b)`       | compare nested contents    |
| `Arrays.copyOf(a, newLen)`      | copy + resize              |
| `Arrays.sort(a)`                | efficient sort             |
| `Arrays.binarySearch(a, key)`   | binary search sorted array |

Import:

```java
import java.util.Arrays;
```

---

## Practice: Conceptual + Coding (Easy → Hard) + Solutions

### Conceptual Questions

1. Why does `arr.length` exist as a field, not a method?
2. What’s the difference between `int[] a = new int[3];` and `int[] a = {0,0,0};`?
3. Explain why `b = a;` is not a copy.
4. When is binary search invalid even if the code is correct?
5. Why are 2D arrays called “arrays of arrays” in Java?

### Coding Exercises (Easy)

#### E1 — Count females

Create a `char[]` of size 100 filled with random `'M'` and `'F'`, count females.

✅ Solution:

```java
import java.util.Random;

public class CountFemales {
    public static void main(String[] args) {
        Random r = new Random();
        char[] gender = new char[100];

        for (int i = 0; i < gender.length; i++) {
            gender[i] = r.nextBoolean() ? 'F' : 'M';
        }

        int countF = 0;
        for (char g : gender) {
            if (g == 'F') countF++;
        }

        System.out.println("Female count = " + countF);
    }
}
```

#### E2 — Split CSV string into array

Given `String str = "Ang,Tan,Fong,Ahmad,Ali";` print names line by line.

✅ Solution:

```java
public class SplitNames {
    public static void main(String[] args) {
        String str = "Ang,Tan,Fong,Ahmad,Ali";
        String[] names = str.split(",");

        for (String n : names) {
            System.out.println(n);
        }
    }
}
```

### Coding Exercises (Medium)

#### M1 — Linear search with user input

* Create `int[]` size 20, fill with random 0–99
* Ask user for a number
* Print found index or “not found”

✅ Solution:

```java
import java.util.Random;
import java.util.Scanner;

public class LinearSearchUser {
    public static void main(String[] args) {
        Random r = new Random();
        int[] a = new int[20];

        for (int i = 0; i < a.length; i++) {
            a[i] = r.nextInt(100);
        }

        Scanner sc = new Scanner(System.in);
        System.out.print("Enter number to search: ");
        int key = sc.nextInt();

        int idx = linearSearch(a, key);
        if (idx == -1) {
            System.out.println("Not found");
        } else {
            System.out.println("Found at index " + idx);
        }

        sc.close();
    }

    static int linearSearch(int[] arr, int key) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == key) return i;
        }
        return -1;
    }
}
```

#### M2 — Bubble sort + then binary search

* Fill random array
* Sort (bubble sort)
* Ask user for key
* Binary search

✅ Solution:

```java
import java.util.Random;
import java.util.Scanner;

public class SortThenSearch {
    public static void main(String[] args) {
        Random r = new Random();
        int[] a = new int[20];

        for (int i = 0; i < a.length; i++) {
            a[i] = r.nextInt(100);
        }

        bubbleSort(a);

        Scanner sc = new Scanner(System.in);
        System.out.print("Enter number to search: ");
        int key = sc.nextInt();

        int idx = binarySearch(a, key);
        if (idx == -1) {
            System.out.println("Not found");
        } else {
            System.out.println("Found at index " + idx);
        }

        sc.close();
    }

    static void bubbleSort(int[] b) {
        for (int pass = 0; pass < b.length - 1; pass++) {
            boolean swapped = false;
            for (int i = 0; i < b.length - 1 - pass; i++) {
                if (b[i] > b[i + 1]) {
                    int tmp = b[i];
                    b[i] = b[i + 1];
                    b[i + 1] = tmp;
                    swapped = true;
                }
            }
            if (!swapped) break;
        }
    }

    static int binarySearch(int[] arr, int key) {
        int low = 0;
        int high = arr.length - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] == key) return mid;
            if (key < arr[mid]) high = mid - 1;
            else low = mid + 1;
        }

        return -1;
    }
}
```

### Challenge (Hard)

#### H1 — Frequency table

Given an `int[]` with values 0–99, build a `int[100] freq` where `freq[v]` counts how often `v` appears.

✅ Solution:

```java
import java.util.Random;

public class FrequencyTable {
    public static void main(String[] args) {
        Random r = new Random();
        int[] a = new int[200];
        for (int i = 0; i < a.length; i++) {
            a[i] = r.nextInt(100);
        }

        int[] freq = new int[100];
        for (int v : a) {
            freq[v]++;
        }

        for (int v = 0; v < freq.length; v++) {
            if (freq[v] > 0) {
                System.out.println(v + " occurs " + freq[v] + " times");
            }
        }
    }
}
```

---

## Further Study

If you want to go beyond exam‑level arrays:

* `Collections` & `ArrayList` deeper usage (iterators, remove patterns)
* Sorting with custom `Comparator`
* Streams (`IntStream`, `Arrays.stream`)
* Defensive copying + immutability patterns

---

### Mini mnemonic set 🧠

* **0..N‑1**: “Index starts at 0, ends at length‑1.”
* **Alias vs Copy**: “`=` is alias, `clone()` is copy.”
* **Binary needs sorted**: “No sort → no binary.”

---

> If you want, I can also generate an exam‑style quiz bank (MCQ + coding) specifically targeting array traps: off‑by‑one, copying, 2D traversal, sorting/searching, and parameter passing.
