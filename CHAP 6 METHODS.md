# Java Fundamentals — Methods (Master Notes)

> Goal: help you **master Java methods** (design, usage, and common exam traps) without needing the original slides.

---

## Table of Contents

1. [Why Methods Exist](#why-methods-exist)
2. [Prerequisites](#prerequisites)
3. [Key Terminology Glossary](#key-terminology-glossary)
4. [Method Fundamentals](#method-fundamentals)

   1. [Anatomy of a Method](#anatomy-of-a-method)
   2. [Declaring vs Calling a Method](#declaring-vs-calling-a-method)
   3. [Return Types and `return`](#return-types-and-return)
   4. [Parameters and Arguments](#parameters-and-arguments)
   5. [Scope, Lifetime, and Local Variables](#scope-lifetime-and-local-variables)
5. [How Method Calls Work Internally](#how-method-calls-work-internally)

   1. [The Call Stack (Stack Frames)](#the-call-stack-stack-frames)
   2. [Java Is Always Call-by-Value](#java-is-always-call-by-value)
   3. [Reference Types: Arrays & Objects as Parameters](#reference-types-arrays--objects-as-parameters)
6. [Static Methods and `main`](#static-methods-and-main)
7. [Method Overloading](#method-overloading)
8. [Design by Contract: Preconditions & Postconditions](#design-by-contract-preconditions--postconditions)
9. [Modern Documentation: Javadoc](#modern-documentation-javadoc)
10. [Common Patterns and Anti-Patterns](#common-patterns-and-anti-patterns)
11. [Common Mistakes (Wrong ➜ Right)](#common-mistakes-wrong--right)
12. [Best Practices & Modern Approaches](#best-practices--modern-approaches)
13. [Quick Reference](#quick-reference)
14. [Practice: Questions, Exercises, Solutions](#practice-questions-exercises-solutions)
15. [Meta-Commentary on the Source Material](#meta-commentary-on-the-source-material)

---

## Why Methods Exist

### Definition (What)

A **method** is a named block of code inside a class that performs a specific task. You can **call** it to run that task.

### Purpose (Why)

Methods exist to solve 4 core problems:

1. **Repetition** → avoid writing the same code again and again.
2. **Complexity** → break big problems into small, understandable pieces.
3. **Maintainability** → fix logic in one place instead of 10 places.
4. **Communication** → method names describe intent (“what”), while code describes mechanics (“how”).

### Real-world analogy

* A method is like a **recipe step**: “Bake for 20 minutes.”
* Parameters are the **ingredients**: flour amount, sugar amount.
* Return value is the **output dish**.

---

## Prerequisites

Before methods, you should already be comfortable with:

* Variables, primitive types, `String`
* `if/else`, loops (`for`, `while`)
* Arrays
* Basic class structure
* Basic input/output (`System.out.println`)

> If you’re shaky on arrays, methods with arrays will feel confusing—review arrays first.

---

## Key Terminology Glossary

* **Method declaration / definition**: the code that *creates* the method.
* **Method call / invocation**: using the method (running it).
* **Signature**: method name + parameter types (order matters). Example: `sum(int, int)`.
* **Parameters**: variables listed in method header (inputs).
* **Arguments**: actual values passed during a call.
* **Return type**: the type of value returned (or `void`).
* **Local variable**: variable declared inside a method.
* **Scope**: where a variable can be used.
* **Lifetime**: how long a variable exists in memory.
* **Stack frame**: memory created for one method call.
* **Overloading**: same method name, different parameter list.

---

## Method Fundamentals

### Anatomy of a Method

General form:

```java
accessModifier static? returnType methodName(parameterList) {
    // body
}
```

Example:

```java
public static int add(int a, int b) {
    return a + b;
}
```

#### What each part means

* `public`: who can call it (access control).
* `static`: belongs to the class (no object needed).
* `int`: the method must return an `int`.
* `add`: method name (verb-like is common).
* `(int a, int b)`: inputs.
* `return a + b;`: outputs value and ends the method.

---

### Declaring vs Calling a Method

#### Minimal example (simplest case)

```java
public class Demo {
    public static void greet() {
        System.out.println("Hello!");
    }

    public static void main(String[] args) {
        greet();
        greet();
    }
}
```

**What happens**:

* `main` calls `greet()`.
* `greet` prints, then returns control back to `main`.

#### Realistic example (practical use)

```java
import java.util.Scanner;

public class LoginDemo {
    public static boolean isValidPassword(String password) {
        return password.length() >= 8 && password.contains("@");
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter password: ");
        String p = sc.nextLine();

        if (isValidPassword(p)) {
            System.out.println("✅ Accepted");
        } else {
            System.out.println("❌ Too weak (need length>=8 and contain @)");
        }

        sc.close();
    }
}
```

---

### Return Types and `return`

#### Core idea

* If a method is **not** `void`, it **must** return a value of the declared type on every path.
* `return` **immediately ends** the current method and sends control back to the caller.

#### Minimal example

```java
public class ReturnDemo {
    public static int doubleIt(int x) {
        return x * 2;
    }

    public static void main(String[] args) {
        int y = doubleIt(7);
        System.out.println(y);
    }
}
```

#### `void` method and `return;`

Use `return;` to end early (no value).

```java
public class EarlyExit {
    public static void printPositive(int x) {
        if (x <= 0) {
            System.out.println("Not positive");
            return; // ends method early
        }
        System.out.println("Positive: " + x);
    }

    public static void main(String[] args) {
        printPositive(-2);
        printPositive(5);
    }
}
```

#### Edge cases & error handling

**Common compile error**: missing return.

❌ Wrong:

```java
public static int sign(int x) {
    if (x > 0) return 1;
    if (x < 0) return -1;
    // missing return for x==0
}
```

✅ Correct:

```java
public static int sign(int x) {
    if (x > 0) return 1;
    if (x < 0) return -1;
    return 0;
}
```

---

### Parameters and Arguments

#### Core explanation

* **Parameters** are placeholders.
* **Arguments** are real values.

Example:

```java
public class ParamsDemo {
    public static void orderCoffee(String type, int sugarSpoons) {
        System.out.println("Making " + type + " with " + sugarSpoons + " spoons of sugar.");
    }

    public static void main(String[] args) {
        orderCoffee("Espresso", 2); // arguments
        orderCoffee("Latte", 1);
    }
}
```

#### When to use parameters

* When the same logic should work for different data.

#### When NOT to

* If the method always uses the same fixed values → constants are simpler.

---

### Scope, Lifetime, and Local Variables

#### Core explanation

A variable declared in a method is **local** to that method.

```java
public class ScopeExample {
    public static void myMethod() {
        int localVar = 10;
        System.out.println(localVar);
    }

    public static void main(String[] args) {
        myMethod();
        // System.out.println(localVar); // ❌ does not compile
    }
}
```

#### Why it exists

* Prevents accidental interference between parts of your program.
* Saves memory: locals exist only while the method runs.

#### Common trap: shadowing

```java
public class Shadowing {
    static int x = 100;

    public static void main(String[] args) {
        int x = 5;              // shadows class x
        System.out.println(x);  // prints 5
        System.out.println(Shadowing.x); // prints 100
    }
}
```

---

## How Method Calls Work Internally

### The Call Stack (Stack Frames)

When you call a method, Java creates a **stack frame** containing:

* parameters
* local variables
* return address (where to continue afterward)

**Visual (top grows downward):**

```
Top of stack
┌─────────────────────────┐
│ frame for methodB()      │
│ locals: ...              │
└─────────────────────────┘
┌─────────────────────────┐
│ frame for methodA()      │
│ locals: ...              │
└─────────────────────────┘
┌─────────────────────────┐
│ frame for main()         │
│ locals: ...              │
└─────────────────────────┘
Bottom of stack
```

### Java Is Always Call-by-Value

**Rule:** Java *always* copies the value of each argument into the method’s parameter.

* For **primitives** (`int`, `double`, `boolean`): the copied value is the actual number/boolean.
* For **reference types** (`String`, arrays, objects): the copied value is the **reference** (address-like pointer).

> So Java is call-by-value even for objects — it’s just that the value being copied is a reference.

#### Minimal example (primitive)

```java
public class CallByValuePrimitive {
    public static void change(int x) {
        x = 999;
    }

    public static void main(String[] args) {
        int a = 10;
        change(a);
        System.out.println(a); // still 10
    }
}
```

#### Realistic example (why “swap” fails)

❌ Wrong (does NOT swap):

```java
public class SwapFail {
    public static void swap(int x, int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    public static void main(String[] args) {
        int a = 3, b = 7;
        swap(a, b);
        System.out.println(a + ", " + b); // 3, 7
    }
}
```

✅ Correct approach: return both values (or use an array/object container)

```java
import java.util.Arrays;

public class SwapReturn {
    public static int[] swapped(int a, int b) {
        return new int[]{b, a};
    }

    public static void main(String[] args) {
        int a = 3, b = 7;
        int[] res = swapped(a, b);
        a = res[0];
        b = res[1];
        System.out.println(a + ", " + b); // 7, 3
        System.out.println(Arrays.toString(res));
    }
}
```

### Reference Types: Arrays & Objects as Parameters

#### Key mechanism

When you pass an array/object to a method:

* The **reference** is copied.
* Both caller and callee now point to the **same object**.
* Mutating the object affects what the caller sees.

#### Minimal example (array mutation)

```java
import java.util.Arrays;

public class ArrayMutation {
    public static void bumpFirst(int[] arr) {
        arr[0] += 1; // mutates the shared array
    }

    public static void main(String[] args) {
        int[] a = {10, 20, 30};
        bumpFirst(a);
        System.out.println(Arrays.toString(a)); // [11, 20, 30]
    }
}
```

#### Critical edge case: reassigning the parameter

Reassigning the parameter does **not** change the caller’s reference.

```java
import java.util.Arrays;

public class ArrayReassign {
    public static void replaceArray(int[] arr) {
        arr = new int[]{1, 2, 3}; // only changes local copy of the reference
    }

    public static void main(String[] args) {
        int[] a = {10, 20, 30};
        replaceArray(a);
        System.out.println(Arrays.toString(a)); // still [10, 20, 30]
    }
}
```

#### Realistic example (object mutation)

```java
public class ObjectMutation {
    static class BankAccount {
        private double balance;
        public BankAccount(double balance) { this.balance = balance; }
        public void deposit(double amount) { balance += amount; }
        public double getBalance() { return balance; }
    }

    public static void depositBonus(BankAccount acc, double bonus) {
        acc.deposit(bonus); // mutates the object
    }

    public static void main(String[] args) {
        BankAccount a = new BankAccount(100);
        depositBonus(a, 25);
        System.out.println(a.getBalance()); // 125.0
    }
}
```

#### When NOT to mutate

* If the caller doesn’t expect their data to change.
* If you want predictability and easier debugging.

✅ Safer alternative: return a new array (functional style)

```java
import java.util.Arrays;

public class NoMutation {
    public static int[] bumpedCopy(int[] arr) {
        int[] copy = Arrays.copyOf(arr, arr.length);
        copy[0] += 1;
        return copy;
    }

    public static void main(String[] args) {
        int[] a = {10, 20, 30};
        int[] b = bumpedCopy(a);
        System.out.println(Arrays.toString(a)); // [10, 20, 30]
        System.out.println(Arrays.toString(b)); // [11, 20, 30]
    }
}
```

---

## Static Methods and `main`

### Core explanation

A **static method** belongs to the **class**, not a particular object.

* Call: `ClassName.methodName(...)`
* Use when: logic doesn’t depend on object state.

### Minimal example

```java
public class MathTools {
    public static int max(int a, int b) {
        return (a > b) ? a : b;
    }

    public static void main(String[] args) {
        System.out.println(max(3, 8));
    }
}
```

### Realistic example: utility library

A “utility class” is a class full of `static` helper methods.

```java
public class UtilityLibrary {
    public static int findMax(int a, int b) {
        return (a > b) ? a : b;
    }

    public static String reverse(String s) {
        StringBuilder sb = new StringBuilder(s);
        return sb.reverse().toString();
    }

    public static void main(String[] args) {
        System.out.println(findMax(10, 7));
        System.out.println(reverse("hello"));
    }
}
```

### `main` method

Entry point:

```java
public static void main(String[] args)
```

* Must be `public` so JVM can call it.
* Must be `static` because JVM calls it without creating an object first.
* `String[] args` holds command-line arguments.

---

## Method Overloading

### Definition

**Overloading**: same name, **different parameter list**.

Allowed differences:

* number of parameters
* parameter types
* parameter order

Not allowed:

* only changing return type ❌

### Why it exists

* You get a clean, consistent API: same “action name,” different inputs.

### Minimal example

```java
public class OverloadDemo {
    public static int add(int a, int b) {
        return a + b;
    }

    public static double add(double a, double b) {
        return a + b;
    }

    public static void main(String[] args) {
        System.out.println(add(2, 3));
        System.out.println(add(2.5, 3.1));
    }
}
```

### Realistic example (shape areas)

✅ Correct overloading must create different signatures:

```java
public class AreaCalculator {
    public static double calculateArea(double radius) {
        return Math.PI * radius * radius;
    }

    public static double calculateArea(double length, double width) {
        return length * width;
    }

    // Different signature: 3 params instead of 2
    public static double calculateArea(double base, double height, boolean isTriangle) {
        if (!isTriangle) {
            throw new IllegalArgumentException("Set isTriangle=true for triangle area");
        }
        return 0.5 * base * height;
    }

    public static void main(String[] args) {
        System.out.println(calculateArea(5.0));
        System.out.println(calculateArea(4.0, 5.0));
        System.out.println(calculateArea(3.0, 4.0, true));
    }
}
```

### Advanced: varargs (variable number of arguments)

```java
public class VarargsDemo {
    public static int sum(int... nums) {
        int total = 0;
        for (int n : nums) total += n;
        return total;
    }

    public static void main(String[] args) {
        System.out.println(sum());
        System.out.println(sum(1));
        System.out.println(sum(1, 2, 3, 4));
    }
}
```

**Trade-off:** varargs is convenient but can create ambiguity with other overloads.

---

## Design by Contract: Preconditions & Postconditions

### What

* **Precondition**: what must be true before the method is called.
* **Postcondition**: what will be true after the method finishes.

### Why

* Makes code safer and easier to reason about.

### How (practical)

Use input validation:

```java
public class ContractDemo {
    /**
     * Preconditions: n >= 0
     * Postcondition: returns n! (factorial)
     */
    public static long factorial(int n) {
        if (n < 0) {
            throw new IllegalArgumentException("n must be >= 0");
        }
        long result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    }

    public static void main(String[] args) {
        System.out.println(factorial(5));
        // System.out.println(factorial(-1)); // would throw
    }
}
```

---

## Modern Documentation: Javadoc

### Why Javadoc beats “random comments”

* Tools can generate HTML docs.
* It standardizes how you document methods.

### Example

```java
public class JavadocDemo {

    /**
     * Calculates the average of an array of integers.
     *
     * @param numbers array of integers; must not be null and must have length > 0
     * @return average as a double
     * @throws IllegalArgumentException if numbers is null or empty
     */
    public static double average(int[] numbers) {
        if (numbers == null || numbers.length == 0) {
            throw new IllegalArgumentException("numbers must not be null/empty");
        }

        long sum = 0;
        for (int n : numbers) sum += n;
        return (double) sum / numbers.length;
    }

    public static void main(String[] args) {
        int[] a = {85, 90, 78, 92, 88};
        System.out.println(average(a));
    }
}
```

---

## Common Patterns and Anti-Patterns

### Pattern: “Compute then return”

✅ Prefer methods that **return values** instead of printing inside.

**Why:** caller can reuse result anywhere (store it, compare it, print it later).

### Anti-pattern: “Do everything in one method”

❌ giant `main()` with 200 lines.

✅ break into small helpers:

* `readInput()`
* `compute()`
* `printReport()`

### Pattern: guard clauses

Use early exits to reduce nesting:

```java
public static String gradeLetter(int mark) {
    if (mark < 0 || mark > 100) return "INVALID";
    if (mark >= 80) return "A";
    if (mark >= 70) return "B";
    if (mark >= 60) return "C";
    if (mark >= 50) return "D";
    return "F";
}
```

---

## Common Mistakes (Wrong ➜ Right)

### 1) Confusing parameter change with caller change (primitives)

❌ Wrong assumption: “If I change the parameter, the variable outside changes.”

✅ Fix: return the new value.

```java
public class FixPrimitive {
    public static int increase(int x) {
        return x + 1;
    }

    public static void main(String[] args) {
        int a = 10;
        a = increase(a);
        System.out.println(a);
    }
}
```

### 2) Overloading mistake: same signature

❌ Wrong (won’t compile): two methods with same name + `(double, double)`.

✅ Fix: change parameter list (count/type/order).

### 3) Returning the wrong type

❌ Wrong:

```java
public static int half(int x) {
    return x / 2.0; // double returned, int expected
}
```

✅ Correct:

```java
public static double half(int x) {
    return x / 2.0;
}
```

### 4) `String` immutability confusion

Strings don’t change in-place.

```java
public class StringTrap {
    public static void makeUpper(String s) {
        s = s.toUpperCase(); // changes local reference only
    }

    public static void main(String[] args) {
        String name = "jon";
        makeUpper(name);
        System.out.println(name); // still "jon"
    }
}
```

✅ Fix: return the new string.

```java
public class StringFix {
    public static String upper(String s) {
        return s.toUpperCase();
    }

    public static void main(String[] args) {
        String name = "jon";
        name = upper(name);
        System.out.println(name);
    }
}
```

---

## Best Practices & Modern Approaches

### 1) Make methods small and single-purpose

* Easier to test
* Easier to read

### 2) Use meaningful names

* `calculateAverage` ✅
* `doStuff` ❌

### 3) Prefer returning values over printing

Printing is a *side effect* that reduces reuse.

### 4) Validate inputs (security + correctness)

Even in beginner projects, check assumptions:

* null checks
* range checks

### 5) Avoid exposing internal arrays

If you store an array inside a class, consider defensive copies.

### 6) Test methods

Even without JUnit, you can write simple test calls in `main`.

---

## Quick Reference

### Method declaration cheat sheet

| Goal            | Example                                |
| --------------- | -------------------------------------- |
| No return       | `public static void greet()`           |
| Return int      | `public static int sum(int a, int b)`  |
| Return object   | `public static String upper(String s)` |
| Array parameter | `public static double avg(int[] a)`    |
| Varargs         | `public static int sum(int... nums)`   |

### Common signatures you’ll see

```java
public static void main(String[] args)
public static int method(int x)
public static double method(double a, double b)
public static boolean method(String s)
public static void method(int[] arr)
```

---

## Practice: Questions, Exercises, Solutions

### Conceptual questions

1. What is the difference between **parameter** and **argument**?
2. Why does swapping two `int` values in a method not work?
3. In Java, what is copied when you pass an object to a method?
4. Why can’t you overload a method by changing only its return type?
5. What is the purpose of `static` in `main`?

---

### Coding exercises (Easy ➜ Hard)

#### Exercise 1 (Easy): max of 3

**Task:** write `max3(int a, int b, int c)`.

✅ Solution:

```java
public class Ex1 {
    public static int max3(int a, int b, int c) {
        int m = (a > b) ? a : b;
        return (m > c) ? m : c;
    }

    public static void main(String[] args) {
        System.out.println(max3(3, 9, 5));
    }
}
```

#### Exercise 2 (Easy): count vowels

**Task:** `countVowels(String s)`.

✅ Solution:

```java
public class Ex2 {
    public static int countVowels(String s) {
        if (s == null) return 0;
        int count = 0;
        String lower = s.toLowerCase();
        for (int i = 0; i < lower.length(); i++) {
            char c = lower.charAt(i);
            if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u') {
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        System.out.println(countVowels("Fundamentals"));
    }
}
```

#### Exercise 3 (Medium): array average with validation

**Task:** `average(int[] a)` throws if null/empty.

✅ Solution: (see Javadoc section)

#### Exercise 4 (Medium): overload `printLine`

**Task:** create three overloads:

* `printLine()` prints a blank line
* `printLine(String s)`
* `printLine(String s, int times)`

✅ Solution:

```java
public class Ex4 {
    public static void printLine() {
        System.out.println();
    }

    public static void printLine(String s) {
        System.out.println(s);
    }

    public static void printLine(String s, int times) {
        if (times < 0) throw new IllegalArgumentException("times must be >= 0");
        for (int i = 0; i < times; i++) {
            System.out.println(s);
        }
    }

    public static void main(String[] args) {
        printLine();
        printLine("Hello");
        printLine("Repeat", 3);
    }
}
```

#### Exercise 5 (Hard): stable “normalize scores” without mutation

**Task:** Given `int[] scores`, return a new array where each score is scaled to 0–100.

✅ Solution:

```java
import java.util.Arrays;

public class Ex5 {
    public static int[] normalizeTo100(int[] scores) {
        if (scores == null) {
            throw new IllegalArgumentException("scores must not be null");
        }
        if (scores.length == 0) {
            return new int[0];
        }

        int max = scores[0];
        for (int s : scores) {
            if (s > max) max = s;
        }

        if (max == 0) {
            return new int[scores.length]; // all zeros
        }

        int[] out = Arrays.copyOf(scores, scores.length);
        for (int i = 0; i < out.length; i++) {
            out[i] = (int) Math.round(out[i] * 100.0 / max);
        }
        return out;
    }

    public static void main(String[] args) {
        int[] scores = {40, 80, 20};
        int[] normalized = normalizeTo100(scores);
        System.out.println(Arrays.toString(scores));
        System.out.println(Arrays.toString(normalized));
    }
}
```

---

### Challenge problem (Exam-style)

**Problem:** Implement a menu-driven program using methods:

* `readScores()` returns an `int[]`
* `average(int[])` returns `double`
* `max(int[])` returns `int`
* `printReport(int[], double, int)` returns `void`

✅ Complete runnable solution:

```java
import java.util.Arrays;
import java.util.Scanner;

public class ChallengeReport {
    public static int[] readScores(Scanner sc) {
        System.out.print("How many scores? ");
        int n = sc.nextInt();
        if (n < 0) throw new IllegalArgumentException("n must be >= 0");

        int[] a = new int[n];
        for (int i = 0; i < n; i++) {
            System.out.print("Score " + (i + 1) + ": ");
            a[i] = sc.nextInt();
        }
        return a;
    }

    public static double average(int[] a) {
        if (a == null || a.length == 0) return 0.0;
        long sum = 0;
        for (int x : a) sum += x;
        return (double) sum / a.length;
    }

    public static int max(int[] a) {
        if (a == null || a.length == 0) {
            throw new IllegalArgumentException("array must not be null/empty");
        }
        int m = a[0];
        for (int x : a) if (x > m) m = x;
        return m;
    }

    public static void printReport(int[] a, double avg, int mx) {
        System.out.println("\n=== REPORT ===");
        System.out.println("Scores: " + Arrays.toString(a));
        System.out.printf("Average: %.2f%n", avg);
        System.out.println("Max: " + mx);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int[] scores = readScores(sc);
        double avg = average(scores);
        int mx = (scores.length == 0) ? 0 : max(scores);
        printReport(scores, avg, mx);

        sc.close();
    }
}
```

---

## Meta-Commentary on the Source Material

### What was well-explained

* The motivation (methods modularize programs)
* Call-by-value idea and the “reference type” intuition
* Introducing `static` and `main`

### What was missing / unclear

* A clear explanation of **method signatures** and overload rules (this causes real compile-time errors).
* The internal model: call stack + “reference copied by value” is not shown visually.
* No strong guidance on *design*: when to return vs when to print, mutation vs no mutation.
* Too few realistic examples and almost no edge cases.

### How these notes improve it

* Adds progressive learning (simple → realistic → advanced)
* Adds decision patterns, pitfalls, and full runnable programs
* Adds modern documentation style (Javadoc)
* Fixes common misconceptions (swap, reassignment, overloading)

### Further study suggestions

* Recursion and stack traces
* Method overriding (polymorphism) after you learn inheritance
* Exception design (`throws` vs handling inside)
* Unit testing (JUnit)
