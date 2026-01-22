# Java Fundamentals (Chapter 2) — Publication‑Quality Study Notes

> Goal: Master Java’s *building blocks* (variables, types, operators, casting, strings, I/O, comments, random) so you can write correct, readable programs and *predict what the JVM will do*.

---

## Table of Contents

* [0. How to Use These Notes](#0-how-to-use-these-notes)
* [1. Introduction & Motivation](#1-introduction--motivation)

  * [1.1 Why Java’s “fundamentals” exist](#11-why-javas-fundamentals-exist)
  * [1.2 Prerequisites (what you should already know)](#12-prerequisites-what-you-should-already-know)
  * [1.3 Mental model: memory, values, and evaluation](#13-mental-model-memory-values-and-evaluation)
* [2. Fundamentals (Building Blocks)](#2-fundamentals-building-blocks)

  * [2.1 Variables](#21-variables)
  * [2.2 Data Types](#22-data-types)
  * [2.3 Literals](#23-literals)
  * [2.4 Constants (`final`)](#24-constants-final)
* [3. Core Concepts](#3-core-concepts)

  * [3.1 Operators (Arithmetic, Assignment, Comparison, Logical)](#31-operators-arithmetic-assignment-comparison-logical)
  * [3.2 Operator Precedence & Evaluation Order](#32-operator-precedence--evaluation-order)
  * [3.3 Type Conversion & Casting](#33-type-conversion--casting)
  * [3.4 Strings](#34-strings)
  * [3.5 Console Input (`Scanner`)](#35-console-input-scanner)
  * [3.6 Console Output (`print`, `println`, `printf`)](#36-console-output-print-println-printf)
  * [3.7 Comments & Javadoc](#37-comments--javadoc)
  * [3.8 Random Numbers](#38-random-numbers)
* [4. Integration & Combinations](#4-integration--combinations)

  * [4.1 A Robust Console Calculator (Full Project)](#41-a-robust-console-calculator-full-project)
  * [4.2 Formatting + Input Validation + Randomness](#42-formatting--input-validation--randomness)
* [5. Best Practices & Modern Approaches](#5-best-practices--modern-approaches)
* [6. Common Pitfalls (With Fixes)](#6-common-pitfalls-with-fixes)
* [7. Quick Reference & Cheat Sheets](#7-quick-reference--cheat-sheets)
* [8. Practice Exercises (Easy → Hard) + Solutions](#8-practice-exercises-easy--hard--solutions)
* [9. Meta‑Commentary: Critique of the Source Material](#9-meta-commentary-critique-of-the-source-material)

---

## 0. How to Use These Notes

1. **Read sections in order**. Java is a stack of ideas; skipping fundamentals creates confusion later.
2. **Run every “Minimal” example**, then modify it.
3. For exams: learn to **predict output**, spot **type promotion**, and avoid **Scanner traps**.
4. Use the [Cheat Sheets](#7-quick-reference--cheat-sheets) for quick recall.

> [!TIP]
> You’ll learn faster if you treat each concept as: **Why** → **What** → **How** → **When** → **Mistakes**.

---

## 1. Introduction & Motivation

### 1.1 Why Java’s “fundamentals” exist

Programming is **controlling changes**:

* variables store changing values,
* data types prevent nonsense operations,
* operators transform values,
* casting resolves mismatches between types,
* strings represent text,
* input/output connects programs to humans,
* comments connect programs to *future you*.

Java adds two big promises:

* **Safety** (strong typing, predictable behavior)
* **Portability** (“write once, run anywhere” via the JVM)

### 1.2 Prerequisites (what you should already know)

You should already be comfortable with:

* Basic algebra: `+ - * / %`
* Using a text editor / IDE
* Files and folders

Helpful (not required):

* The idea that a CPU executes instructions sequentially
* “Memory” as a set of storage locations

### 1.3 Mental model: memory, values, and evaluation

**Key vocabulary (define before use):**

* **Value**: the actual data (e.g., `10`, `3.14`, `'A'`, `true`)
* **Variable**: a named storage location holding a value
* **Type**: rules for what values exist + operations allowed
* **Expression**: something Java evaluates to produce a value (e.g., `a + 3`)
* **Statement**: an instruction (often ends with `;`), e.g., `a = a + 1;`

Visual model:

```
RAM (memory)
┌──────────────┐
│ age  →  20   │  (int)
│ pi   →  3.14 │  (double)
│ ch   →  'A'  │  (char)
└──────────────┘
```

> [!NOTE]
> In Java, *primitive variables store the value directly*. Reference variables store a **reference** (like an address) to an object.

---

## 2. Fundamentals (Building Blocks)

### 2.1 Variables

#### 2.1.1 Definition (what it is)

A **variable** is a named container that holds a value of a **specific type**.

#### 2.1.2 Purpose (why it exists)

Without variables, programs would only use hard‑coded values (“magic numbers”), making them inflexible.

#### 2.1.3 Motivation (problem it solves)

* Store inputs
* Store intermediate results
* Keep state across steps

#### 2.1.4 Mechanism (how it works internally)

When you declare a variable, Java reserves memory appropriate to its type.

#### 2.1.5 Syntax

```java
int count;
double price;
boolean isReady;
char grade;
```

* **Declaration**: `type name;`
* **Initialization**: `type name = value;`

```java
int count = 0;
double speed = 2.6;
char letter = 'A';
boolean ok = true;
```

> [!IMPORTANT]
> Local variables **must be initialized before use**. Fields (class variables) get default values.

#### 2.1.6 Naming rules & conventions

Rules (compiler enforced):

* may contain letters, digits, `_`, `$`
* cannot start with a digit
* cannot be a reserved keyword

Conventions (human enforced):

* `camelCase` for variables: `totalScore`, `firstName`
* Use meaningful names: `minutes` > `m`

#### 2.1.7 When to use / When not to

✅ Use variables for:

* values that change
* values used multiple times

❌ Avoid:

* `a`, `b`, `c` as names in real programs (except tiny math demos)
* over‑wide scope variables (declaring at top “just in case”)

#### 2.1.8 Complete working examples

**Minimal example (simplest case)**

```java
public class VariablesMinimal {
    public static void main(String[] args) {
        int age = 20;
        System.out.println("Age = " + age);
        age = age + 1;
        System.out.println("Next year = " + age);
    }
}
```

**Realistic example (practical use)**

```java
import java.util.Scanner;

public class BMICalculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter weight in kg: ");
        double weightKg = sc.nextDouble();

        System.out.print("Enter height in meters: ");
        double heightM = sc.nextDouble();

        double bmi = weightKg / (heightM * heightM);

        System.out.printf("Your BMI is %.2f%n", bmi);

        sc.close();
    }
}
```

**Advanced example (complex scenario + edge handling)**

```java
import java.util.Scanner;

public class SafeAverage {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("How many numbers? ");
        int n = sc.nextInt();

        if (n <= 0) {
            System.out.println("n must be positive.");
            sc.close();
            return;
        }

        double sum = 0.0;
        for (int i = 1; i <= n; i++) {
            System.out.print("Enter number " + i + ": ");
            double x = sc.nextDouble();
            sum += x;
        }

        double avg = sum / n;
        System.out.printf("Average = %.3f%n", avg);

        sc.close();
    }
}
```

**Common mistakes**

* ❌ Using a variable before initialization

```java
public class UninitializedVar {
    public static void main(String[] args) {
        int x;
        // System.out.println(x); // ERROR: variable x might not have been initialized
        x = 5;
        System.out.println(x);
    }
}
```

* ✅ Fix: assign first.

---

### 2.2 Data Types

#### 2.2.1 Why types exist

Types prevent nonsense:

* “add a number to a boolean”
* “store a huge number into a tiny container”

Java is **strongly typed**: every variable has a type.

#### 2.2.2 Two big categories

```
Data Types
├─ Primitive (8 built-in value types)
└─ Reference (objects, arrays, classes)
```

##### Primitive types (8)

* Integers: `byte`, `short`, `int`, `long`
* Floating point: `float`, `double`
* Character: `char`
* Boolean: `boolean`

##### Reference types

* `String`
* arrays like `int[]`
* any class type (`Scanner`, `Random`, your own classes)

> [!NOTE]
> A reference type variable stores a **reference** to an object, not the object itself.

#### 2.2.3 Primitive sizes & typical use

| Type      |        Bits | Range (approx)       | Typical use                                  |
| --------- | ----------: | -------------------- | -------------------------------------------- |
| `byte`    |           8 | -128 to 127          | raw bytes, file/network, memory‑tight arrays |
| `short`   |          16 | -32k to 32k          | rare today                                   |
| `int`     |          32 | ±2.1B                | default integer choice                       |
| `long`    |          64 | huge                 | timestamps (ms), big counts                  |
| `float`   |          32 | ~7 digits precision  | graphics, memory-sensitive                   |
| `double`  |          64 | ~15 digits precision | default decimal choice                       |
| `char`    |          16 | 0 to 65535           | UTF‑16 code unit                             |
| `boolean` | JVM-defined | `true/false`         | conditions                                   |

> [!IMPORTANT]
> `double` is usually preferred over `float` for accuracy.

#### 2.2.4 Choosing the right type (decision tree)

```
Need whole numbers?
├─ Yes → Use int
│   ├─ Might exceed 2.1B? → long
│   └─ Need raw bytes? → byte
└─ No → Need decimals
    ├─ Normal calculations → double
    └─ Memory-critical huge arrays → float (with caution)
```

#### 2.2.5 Minimal / realistic / advanced examples

**Minimal: basic primitives**

```java
public class TypesMinimal {
    public static void main(String[] args) {
        int a = 10;
        double b = 3.5;
        char c = 'Z';
        boolean ok = true;
        System.out.println(a + ", " + b + ", " + c + ", " + ok);
    }
}
```

**Realistic: avoiding overflow**

```java
public class OverflowDemo {
    public static void main(String[] args) {
        int max = Integer.MAX_VALUE;
        System.out.println("max = " + max);
        System.out.println("max + 1 = " + (max + 1)); // overflow wraps around

        long safe = (long) max + 1;
        System.out.println("safe (as long) = " + safe);
    }
}
```

**Advanced: primitive vs reference memory idea**

```java
import java.util.Arrays;

public class ReferenceVsPrimitive {
    public static void main(String[] args) {
        int x = 5;
        int y = x;
        y++;
        System.out.println("x=" + x + ", y=" + y); // x unchanged

        int[] arr1 = {1, 2, 3};
        int[] arr2 = arr1; // same array reference
        arr2[0] = 99;
        System.out.println(Arrays.toString(arr1)); // [99, 2, 3]
    }
}
```

> [!NOTE]
> This explains why arrays/objects behave differently as parameters: the **reference** is copied, but both point to the same object.

---

### 2.3 Literals

**Literal** = a fixed value written directly in code.

| Type      | Examples                  | Notes                                           |
| --------- | ------------------------- | ----------------------------------------------- |
| `int`     | `0`, `42`, `-7`           | default integer literal type is `int`           |
| `long`    | `5L`, `10000000000L`      | must add `L` for long literals beyond int range |
| `double`  | `3.14`, `2.0`             | default decimal literal type is `double`        |
| `float`   | `3.14f`                   | must add `f` or `F`                             |
| `char`    | `'A'`, `'\n'`, `'\u263A'` | single quotes                                   |
| `String`  | `"Hi"`                    | double quotes                                   |
| `boolean` | `true`, `false`           | lowercase                                       |

**Minimal demo**

```java
public class LiteralsDemo {
    public static void main(String[] args) {
        long big = 10_000_000_000L;
        float piF = 3.14159f;
        double piD = 3.14159;
        char smile = '\u263A';
        System.out.println(big);
        System.out.println(piF);
        System.out.println(piD);
        System.out.println(smile);
    }
}
```

---

### 2.4 Constants (`final`)

#### Definition

A **constant** is a value that should not change after assignment.

#### Purpose

Prevents “accidental changes” and removes magic numbers.

#### Mechanism

`final` means “assign once.” After assignment, reassignment is a compile error.

#### Syntax

```java
final int MIN = 0;
final int programMaxScore = 100;
```

Conventions:

* constants: `UPPER_SNAKE_CASE` (e.g., `MAX_USERS`)

#### When to use

✅ Use for:

* fixed configuration values
* mathematical constants
* repeated “meaningful numbers” (like grading thresholds)

❌ Avoid using `final` as a substitute for good design.

#### Examples

**Minimal**

```java
public class FinalMinimal {
    public static void main(String[] args) {
        final int MAX_USERS = 100;
        System.out.println(MAX_USERS);
        // MAX_USERS = 200; // compile-time error
    }
}
```

**Realistic**

```java
public class Grading {
    private static final int PASS_MARK = 50;

    public static void main(String[] args) {
        int score = 68;
        System.out.println(score >= PASS_MARK ? "PASS" : "FAIL");
    }
}
```

**Advanced: constants grouped**

```java
public class AppConfig {
    public static final String APP_NAME = "StudyApp";
    public static final int DEFAULT_TIMEOUT_MS = 3000;

    private AppConfig() { } // prevent instantiation
}
```

---

## 3. Core Concepts

### 3.1 Operators (Arithmetic, Assignment, Comparison, Logical)

#### 3.1.1 Why operators exist

Operators are compact, readable ways to transform values.

#### 3.1.2 Terminology

* **Operand**: the value(s) an operator works on
* **Unary** operator: 1 operand (`-x`, `++x`)
* **Binary** operator: 2 operands (`a + b`)
* **Ternary** operator: 3 operands (`cond ? x : y`)

#### 3.1.3 Arithmetic operators

`+  -  *  /  %`

**Minimal**

```java
public class ArithmeticMinimal {
    public static void main(String[] args) {
        int a = 10, b = 3;
        System.out.println(a + b); // 13
        System.out.println(a - b); // 7
        System.out.println(a * b); // 30
        System.out.println(a / b); // 3 (integer division!)
        System.out.println(a % b); // 1
    }
}
```

> [!IMPORTANT]
> If both operands are integers, `/` does **integer division** (fraction is discarded).

**Realistic: computing change**

```java
public class ChangeCalculator {
    public static void main(String[] args) {
        int cents = 287; // RM 2.87
        int rm = cents / 100;
        int remaining = cents % 100;
        System.out.println("RM" + rm + "." + (remaining < 10 ? "0" : "") + remaining);
    }
}
```

**Edge case: division by zero**

```java
public class DivisionByZero {
    public static void main(String[] args) {
        int a = 5;
        // System.out.println(a / 0); // throws ArithmeticException
        double x = 5.0;
        System.out.println(x / 0);  // Infinity
        System.out.println(0.0 / 0.0); // NaN
    }
}
```

#### 3.1.4 Assignment operators

* Basic: `=`
* Compound: `+= -= *= /= %=`

**Minimal**

```java
public class AssignmentMinimal {
    public static void main(String[] args) {
        int n = 10;
        n += 5; // 15
        n *= 2; // 30
        System.out.println(n);
    }
}
```

#### 3.1.5 Increment / Decrement (postfix vs prefix)

* `n++` (postfix): use current value, then increment
* `++n` (prefix): increment first, then use

**Minimal demo**

```java
public class IncDec {
    public static void main(String[] args) {
        int n = 5;
        System.out.println(n++); // prints 5, n becomes 6
        System.out.println(++n); // n becomes 7, prints 7
    }
}
```

> [!WARNING]
> Avoid using `++` inside complex expressions. It hurts readability and causes exam-trick bugs.

#### 3.1.6 Comparison operators

Return a boolean: `== != > < >= <=`

**Minimal**

```java
public class ComparisonMinimal {
    public static void main(String[] args) {
        int a = 5, b = 10;
        System.out.println(a < b);  // true
        System.out.println(a == b); // false
    }
}
```

**String comparison pitfall**

* ❌ Wrong: `==` for Strings
* ✅ Right: `.equals()`

```java
public class StringCompare {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = new String("Hello");

        System.out.println(s1 == s2);        // false (different objects)
        System.out.println(s1.equals(s2));   // true  (same content)
    }
}
```

#### 3.1.7 Logical operators

* `&&` AND (short-circuit)
* `||` OR (short-circuit)
* `!`  NOT

**Short-circuit matters**

```java
public class ShortCircuit {
    public static void main(String[] args) {
        int x = 0;
        boolean ok = (x != 0) && (10 / x > 1);
        System.out.println(ok); // safe: second part not evaluated
    }
}
```

> [!IMPORTANT]
> `&&` and `||` do **short-circuit evaluation**: if the result is already determined, Java skips the rest.

#### 3.1.8 When to use / When NOT to

✅ Use logical operators to guard unsafe operations (like division, array access).

❌ Avoid deeply nested boolean expressions. Prefer clear helper variables:

**Before (hard to read)**

```java
boolean eligible = age >= 18 && (hasId || hasGuardian) && !isBanned;
```

**After (clearer)**

```java
boolean adult = age >= 18;
boolean hasAccessProof = hasId || hasGuardian;
boolean eligible = adult && hasAccessProof && !isBanned;
```

---

### 3.2 Operator Precedence & Evaluation Order

#### 3.2.1 Why precedence exists

Without precedence, every expression would need parentheses.

#### 3.2.2 Practical precedence summary (most important)

1. Parentheses: `( )`
2. Unary: `!  ++  --  +  -` (unary)
3. Multiplicative: `*  /  %`
4. Additive: `+  -`
5. Comparison: `< <= > >=`
6. Equality: `== !=`
7. Logical AND: `&&`
8. Logical OR: `||`
9. Ternary: `?:`
10. Assignment: `= += -= ...`

> [!TIP]
> If you’re not 100% sure, **add parentheses**. Clarity beats cleverness.

#### 3.2.3 Evaluation order

* Java evaluates operands **left to right**.
* Precedence decides **which operator groups first**.

**Exam-style example**

```java
public class PrecedenceQuiz {
    public static void main(String[] args) {
        int a = 2 + 3 * 4;      // 2 + (3*4) = 14
        int b = (2 + 3) * 4;    // 20
        System.out.println(a);
        System.out.println(b);
    }
}
```

---

### 3.3 Type Conversion & Casting

#### 3.3.1 Why this exists

Different types store different kinds of values. You often need to:

* avoid integer division
* store `int` results into `double`
* convert user input into the needed type

#### 3.3.2 Terminology

* **Widening conversion** (safe): smaller → larger range (e.g., `int → double`)
* **Narrowing conversion** (risky): larger → smaller (e.g., `double → int`)

#### 3.3.3 Implicit conversion (widening)

Java does this automatically.

```java
public class Widening {
    public static void main(String[] args) {
        int a = 100;
        double d = a; // 100.0
        System.out.println(d);
    }
}
```

#### 3.3.4 Explicit casting (narrowing)

You must write the cast.

```java
public class Narrowing {
    public static void main(String[] args) {
        double x = 9.78;
        int n = (int) x; // 9 (decimal truncated)
        System.out.println(n);
    }
}
```

> [!WARNING]
> Casting doesn’t “round” by default. It **truncates** toward zero.

#### 3.3.5 Type promotion in expressions (very testable)

Rules you’ll see in exams:

* If either operand is `double`, result is `double`.
* If either is `float`, result is `float` (unless one is double).
* Otherwise, integers promote at least to `int`.

**Integer division trap**

```java
public class DivisionTrap {
    public static void main(String[] args) {
        int a = 8, b = 3;
        double ans1 = a / b;           // 2.0 (because a/b is 2)
        double ans2 = a / (double) b;  // 2.666...
        System.out.println(ans1);
        System.out.println(ans2);
    }
}
```

#### 3.3.6 Casting with objects (inheritance)

* **Upcasting**: subclass → superclass (safe, implicit)
* **Downcasting**: superclass → subclass (risky, explicit)

```java
class Animal {
    void makeSound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void makeSound() { System.out.println("Bark"); }

    void wagTail() { System.out.println("Wagging tail"); }
}

public class ObjectCasting {
    public static void main(String[] args) {
        Animal a = new Dog(); // upcast
        a.makeSound();        // Bark (dynamic dispatch)

        if (a instanceof Dog) {
            Dog d = (Dog) a;  // downcast
            d.wagTail();
        }
    }
}
```

> [!IMPORTANT]
> Use `instanceof` before downcasting to avoid `ClassCastException`.

#### 3.3.7 When to use / When NOT to

✅ Use casts when you understand the range/precision.

❌ Don’t cast to “force it to compile” if you don’t understand the data loss.

---

### 3.4 Strings

#### 3.4.1 What is a String

A **String** is a sequence of characters representing text.

#### 3.4.2 Why Strings exist

Humans read/write text, and programs must:

* store names, messages
* parse input
* format output

#### 3.4.3 How Strings work internally (important concept: immutability)

Strings are **immutable**:

* once created, the content cannot change
* operations that “modify” a String actually create a **new String**

```java
public class StringImmutability {
    public static void main(String[] args) {
        String s = "Hello";
        s = s + " World"; // creates new String
        System.out.println(s);
    }
}
```

#### 3.4.4 Common operations

| Task            | Method / Operator            | Notes              |
| --------------- | ---------------------------- | ------------------ |
| length          | `s.length()`                 | returns `int`      |
| char at index   | `s.charAt(i)`                | 0-based            |
| substring       | `s.substring(a, b)`          | end exclusive      |
| contains        | `s.contains("x")`            | boolean            |
| starts/ends     | `startsWith`, `endsWith`     |                    |
| find            | `indexOf`, `lastIndexOf`     | -1 if not found    |
| compare content | `equals`, `equalsIgnoreCase` | ✅ use these        |
| trim spaces     | `trim()` / `strip()`         | `strip()` is newer |
| split           | `split(" ")`                 | returns array      |

#### 3.4.5 Minimal / realistic / advanced examples

**Minimal**

```java
public class StringMinimal {
    public static void main(String[] args) {
        String first = "Ada";
        String last = "Lovelace";
        String full = first + " " + last;
        System.out.println(full);
    }
}
```

**Realistic: parsing full name**

```java
import java.util.Scanner;

public class NameParser {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter full name: ");
        String full = sc.nextLine().trim();

        int space = full.indexOf(' ');
        if (space == -1) {
            System.out.println("Please enter at least first and last name.");
        } else {
            String first = full.substring(0, space);
            String last = full.substring(space + 1).trim();
            System.out.println("First: " + first);
            System.out.println("Last: " + last);
        }

        sc.close();
    }
}
```

**Advanced: efficient building with StringBuilder**

> Why? Repeated `+` in a loop creates many temporary Strings.

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        for (int i = 1; i <= 5; i++) {
            sb.append(i);
            if (i < 5) sb.append(", ");
        }
        System.out.println(sb.toString());
    }
}
```

**Edge cases**

* `null` vs empty string `""`

```java
public class NullVsEmpty {
    public static void main(String[] args) {
        String a = null;
        String b = "";

        System.out.println(b.length()); // 0

        // System.out.println(a.length()); // NullPointerException

        System.out.println(a == null ? "a is null" : "a not null");
    }
}
```

#### 3.4.6 Common mistakes (wrong → right)

❌ Wrong: using `==` to compare Strings

```java
String s1 = "Hi";
String s2 = new String("Hi");
if (s1 == s2) {
    System.out.println("Same");
}
```

✅ Correct

```java
if (s1.equals(s2)) {
    System.out.println("Same content");
}
```

---

### 3.5 Console Input (`Scanner`)

#### 3.5.1 Why console input exists

Programs often need data at runtime.

#### 3.5.2 What Scanner is

`Scanner` reads tokens from an input stream (like `System.in`).

#### 3.5.3 Required import

```java
import java.util.Scanner;
```

#### 3.5.4 How it works internally (important)

Scanner splits input by **delimiters** (default: whitespace). Methods like `nextInt()` read the next token that matches an integer.

#### 3.5.5 Core methods

| Method         | Reads                     | Common use            |
| -------------- | ------------------------- | --------------------- |
| `nextInt()`    | one integer token         | counts, menu choices  |
| `nextLong()`   | one long token            | large ids             |
| `nextDouble()` | one double token          | prices, measurements  |
| `next()`       | one word token            | single word           |
| `nextLine()`   | whole line (can be empty) | full names, sentences |
| `hasNextInt()` | boolean                   | validation            |

#### 3.5.6 Minimal / realistic / advanced examples

**Minimal**

```java
import java.util.Scanner;

public class ScannerMinimal {
    public static void main(String[] args) {
        Scanner keyboard = new Scanner(System.in);

        System.out.print("Enter an integer: ");
        int num = keyboard.nextInt();

        System.out.println("You entered: " + num);

        keyboard.close();
    }
}
```

**Realistic: reading a full line**

```java
import java.util.Scanner;

public class ReadLine {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter your address: ");
        String address = sc.nextLine();

        System.out.println("Address: " + address);

        sc.close();
    }
}
```

**Advanced: robust input validation**

```java
import java.util.Scanner;

public class ReadIntSafely {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int value;
        while (true) {
            System.out.print("Enter an integer between 1 and 10: ");

            if (!sc.hasNextInt()) {
                System.out.println("That is not an integer. Try again.");
                sc.nextLine(); // discard the bad line
                continue;
            }

            value = sc.nextInt();
            sc.nextLine(); // consume leftover newline

            if (value < 1 || value > 10) {
                System.out.println("Out of range. Try again.");
                continue;
            }

            break;
        }

        System.out.println("Accepted: " + value);
        sc.close();
    }
}
```

#### 3.5.7 The #1 Scanner pitfall: `nextInt()` then `nextLine()`

**Why it happens**: `nextInt()` reads the number token but leaves the newline `\n` in the buffer. Then `nextLine()` immediately reads that leftover newline and returns an empty line.

❌ Wrong

```java
Scanner sc = new Scanner(System.in);
int age = sc.nextInt();
String name = sc.nextLine(); // name becomes "" (empty)
```

✅ Fix

```java
int age = sc.nextInt();
sc.nextLine(); // consume leftover newline
String name = sc.nextLine();
```

> [!WARNING]
> In many beginner programs, **don’t close** `System.in` if your program continues and other code needs input. For single-file console programs, closing is fine at the end.

---

### 3.6 Console Output (`print`, `println`, `printf`)

#### 3.6.1 What it is

`System.out` is a `PrintStream` connected to standard output.

#### 3.6.2 `print` vs `println`

* `print`: stays on same line
* `println`: prints then adds a newline

**Minimal**

```java
public class PrintVsPrintln {
    public static void main(String[] args) {
        System.out.print("A");
        System.out.print("B");
        System.out.println("C");
        System.out.println("D");
    }
}
```

Output:

```
ABC
D
```

#### 3.6.3 `printf` (formatted output)

Format specifiers:

| Specifier | Meaning             | Example                         |
| --------- | ------------------- | ------------------------------- |
| `%d`      | decimal integer     | `%6d` (width 6)                 |
| `%f`      | fixed decimal float | `%.2f` (2 dp)                   |
| `%e`      | scientific notation | `%.3e`                          |
| `%s`      | string              | `%20s`                          |
| `%c`      | character           | `%c`                            |
| `%n`      | newline (portable)  | recommended over `\n` in printf |

**Minimal**

```java
public class PrintfMinimal {
    public static void main(String[] args) {
        double piApprox = 22 / 7.0;
        System.out.printf("pi ≈ %.2f%n", piApprox);
    }
}
```

**Realistic: aligned table**

```java
public class GradeTable {
    public static void main(String[] args) {
        System.out.printf("%-10s %6s%n", "Name", "Score");
        System.out.printf("%-10s %6d%n", "Amina", 87);
        System.out.printf("%-10s %6d%n", "Jon", 92);
        System.out.printf("%-10s %6d%n", "Wei", 75);
    }
}
```

#### 3.6.4 Escape sequences

| Escape | Meaning      |
| ------ | ------------ |
| `\n`   | newline      |
| `\t`   | tab          |
| `\\`   | backslash    |
| `\"`   | double quote |
| `\'`   | single quote |

**Demo**

```java
public class Escapes {
    public static void main(String[] args) {
        System.out.println("Line1\nLine2");
        System.out.println("Tabbed\tText");
        System.out.println("A backslash: \\");
        System.out.println("Quote: \"Hi\"");
    }
}
```

---

### 3.7 Comments & Javadoc

#### 3.7.1 Why comments exist

Code explains **how**, comments explain **why**.

#### 3.7.2 Types of comments

* Single line: `// ...`
* Block: `/* ... */`
* Javadoc: `/** ... */` (generates documentation)

**Minimal**

```java
public class CommentsMinimal {
    public static void main(String[] args) {
        // This is a single-line comment
        /* This is a
           multi-line comment */
        System.out.println("Hello");
    }
}
```

#### 3.7.3 Javadoc best practices

Use tags:

* `@param` `@return` `@throws`

**Advanced example**

```java
/**
 * Utility methods for math operations.
 */
public class MathUtil {

    /**
     * Computes the average of two integers as a double.
     *
     * @param a first integer
     * @param b second integer
     * @return the average as a double
     */
    public static double average(int a, int b) {
        return (a + b) / 2.0;
    }

    public static void main(String[] args) {
        System.out.println(average(3, 4));
    }
}
```

> [!TIP]
> Good comments explain intent: “why this approach,” “why this check,” “why this constant.”

---

### 3.8 Random Numbers

#### 3.8.1 Why randomness exists

Useful for:

* games
* simulations
* randomized testing

#### 3.8.2 `Random` basics

Import:

```java
import java.util.Random;
```

**Minimal: 0..99**

```java
import java.util.Random;

public class RandomMinimal {
    public static void main(String[] args) {
        Random g = new Random();
        int max = 100;
        int num = g.nextInt(max); // 0..99
        System.out.println(num);
    }
}
```

#### 3.8.3 Common “range” patterns (very important)

* `g.nextInt(bound)` gives `0..bound-1`

To generate **min..max inclusive**:

```java
int value = min + g.nextInt(max - min + 1);
```

**Realistic: dice (1..6)**

```java
import java.util.Random;

public class Dice {
    public static void main(String[] args) {
        Random g = new Random();
        int roll = 1 + g.nextInt(6);
        System.out.println("Rolled: " + roll);
    }
}
```

#### 3.8.4 Advanced: when `Random` is NOT enough (security)

`Random` is **not** suitable for security tokens/passwords.
Use `SecureRandom`.

```java
import java.security.SecureRandom;

public class SecureToken {
    public static void main(String[] args) {
        SecureRandom sr = new SecureRandom();
        byte[] token = new byte[16];
        sr.nextBytes(token);

        StringBuilder sb = new StringBuilder();
        for (byte b : token) {
            sb.append(String.format("%02x", b));
        }

        System.out.println(sb.toString());
    }
}
```

---

## 4. Integration & Combinations

### 4.1 A Robust Console Calculator (Full Project)

What this integrates:

* variables + types
* operators
* casting
* `Scanner`
* `switch`
* error handling
* `printf`

```java
import java.util.Scanner;

public class RobustCalculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.println("=== Console Calculator ===");

        while (true) {
            System.out.print("Enter first number (or 'q' to quit): ");
            String first = sc.nextLine().trim();
            if (first.equalsIgnoreCase("q")) break;

            System.out.print("Enter second number: ");
            String second = sc.nextLine().trim();

            double a, b;
            try {
                a = Double.parseDouble(first);
                b = Double.parseDouble(second);
            } catch (NumberFormatException e) {
                System.out.println("Invalid number format. Try again.");
                continue;
            }

            System.out.print("Operation (+, -, *, /, %): ");
            String opLine = sc.nextLine().trim();
            if (opLine.isEmpty()) {
                System.out.println("No operation entered. Try again.");
                continue;
            }
            char op = opLine.charAt(0);

            Double result = null;
            switch (op) {
                case '+':
                    result = a + b;
                    break;
                case '-':
                    result = a - b;
                    break;
                case '*':
                    result = a * b;
                    break;
                case '/':
                    if (b == 0.0) {
                        System.out.println("Error: division by zero.");
                    } else {
                        result = a / b;
                    }
                    break;
                case '%':
                    if (b == 0.0) {
                        System.out.println("Error: modulo by zero.");
                    } else {
                        result = a % b;
                    }
                    break;
                default:
                    System.out.println("Unknown operation: " + op);
            }

            if (result != null) {
                System.out.printf("Result: %.6f%n", result);
            }
        }

        System.out.println("Bye!");
        sc.close();
    }
}
```

### 4.2 Formatting + Input Validation + Randomness

**Scenario**: A mini quiz that asks random arithmetic questions and formats output nicely.

```java
import java.util.Random;
import java.util.Scanner;

public class MiniMathQuiz {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Random g = new Random();

        int correct = 0;
        final int QUESTIONS = 5;

        for (int i = 1; i <= QUESTIONS; i++) {
            int a = 1 + g.nextInt(20);
            int b = 1 + g.nextInt(20);
            int expected = a + b;

            System.out.printf("Q%d) %d + %d = ? ", i, a, b);

            if (!sc.hasNextInt()) {
                System.out.println("(invalid input)");
                sc.nextLine();
                continue;
            }
            int ans = sc.nextInt();
            sc.nextLine();

            if (ans == expected) {
                System.out.println("✅ correct");
                correct++;
            } else {
                System.out.println("❌ wrong. Answer = " + expected);
            }
        }

        System.out.printf("Score: %d/%d%n", correct, QUESTIONS);
        sc.close();
    }
}
```

---

## 5. Best Practices & Modern Approaches

### 5.1 Readability & maintainability

* Prefer clear names over short names.
* Prefer parentheses for clarity when mixing operators.
* Keep variables in the **smallest scope**.

### 5.2 Modern Java notes (useful context)

* `var` (Java 10) allows local type inference:

  ```java
  var x = 10;        // x is int
  var name = "Jon";  // name is String
  ```

  Use it when the type is obvious; avoid when it hides meaning.

* Prefer `StringBuilder` in loops.

### 5.3 Performance considerations

* Repeated string concatenation in loops is costly → use `StringBuilder`.
* `printf` is slower than simple `println`, but is worth it for formatted output.

### 5.4 Security implications

* Don’t use `Random` for tokens/keys/passwords → use `SecureRandom`.
* Don’t print sensitive data to console in real systems.

---

## 6. Common Pitfalls (With Fixes)

### 6.1 Smart quotes in code

❌ Wrong (won’t compile): `System.out.println(“Hello”);`

✅ Correct:

```java
System.out.println("Hello");
```

### 6.2 Integer division surprise

❌ Wrong

```java
double avg = (3 + 4) / 2; // 3.0, not 3.5
```

✅ Correct

```java
double avg = (3 + 4) / 2.0;
```

### 6.3 `nextInt()` then `nextLine()`

See [Scanner pitfall](#357-the-1-scanner-pitfall-nextint-then-nextline).

### 6.4 Comparing Strings with `==`

Use `.equals()`.

### 6.5 Overflow isn’t an error

Know integer ranges and promote to `long` when needed.

---

## 7. Quick Reference & Cheat Sheets

### 7.1 Imports you’ll repeatedly need

```java
import java.util.Scanner;
import java.util.Random;
import java.security.SecureRandom;
```

### 7.2 `Scanner` quick map

```java
sc.nextInt();    // reads int token
sc.nextDouble(); // reads double token
sc.next();       // reads one word
sc.nextLine();   // reads entire line
sc.hasNextInt(); // validation
```

### 7.3 `printf` patterns

```java
System.out.printf("%.2f%n", value);   // 2 decimal places
System.out.printf("%6d%n", number);   // width 6
System.out.printf("%-10s%n", text);   // left aligned width 10
```

### 7.4 Random ranges

```java
g.nextInt(10);          // 0..9
1 + g.nextInt(6);       // 1..6
min + g.nextInt(max-min+1); // min..max inclusive
```

---

## 8. Practice Exercises (Easy → Hard) + Solutions

### 8.1 Conceptual questions

1. Why does `a / b` sometimes produce an integer even if assigned to a `double`?
2. Explain the difference between `++n` and `n++`.
3. Why is `String` immutable, and what benefits does it provide?
4. Why does `nextLine()` behave strangely after `nextInt()`?
5. Why shouldn’t `Random` be used for passwords?

### 8.2 Coding exercises

#### Easy

1. Read two integers and print their sum, difference, product, quotient, remainder.
2. Read a full name and print initials.

#### Medium

3. Build a menu-driven calculator with `switch`.
4. Generate 10 random numbers in the range 50..100 inclusive.

#### Hard

5. Read N numbers safely (with validation) and print min, max, average (formatted).

### 8.3 Complete solutions

#### Solution 1 (Easy)

```java
import java.util.Scanner;

public class OpsPractice {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter a: ");
        int a = sc.nextInt();

        System.out.print("Enter b: ");
        int b = sc.nextInt();

        System.out.println("a+b = " + (a + b));
        System.out.println("a-b = " + (a - b));
        System.out.println("a*b = " + (a * b));

        if (b != 0) {
            System.out.println("a/b = " + (a / b));
            System.out.println("a%b = " + (a % b));
        } else {
            System.out.println("Cannot divide by zero.");
        }

        sc.close();
    }
}
```

#### Solution 2 (Easy)

```java
import java.util.Scanner;

public class Initials {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter full name: ");
        String full = sc.nextLine().trim();

        if (full.isEmpty()) {
            System.out.println("No name entered.");
            sc.close();
            return;
        }

        String[] parts = full.split("\\s+");
        StringBuilder initials = new StringBuilder();
        for (String p : parts) {
            initials.append(Character.toUpperCase(p.charAt(0)));
        }

        System.out.println("Initials: " + initials);
        sc.close();
    }
}
```

#### Solution 4 (Medium)

```java
import java.util.Random;

public class RandomRangePractice {
    public static void main(String[] args) {
        Random g = new Random();
        int min = 50, max = 100;

        for (int i = 1; i <= 10; i++) {
            int value = min + g.nextInt(max - min + 1);
            System.out.println(value);
        }
    }
}
```

#### Solution 5 (Hard)

```java
import java.util.Scanner;

public class StatsCalculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter N: ");
        if (!sc.hasNextInt()) {
            System.out.println("N must be an integer.");
            sc.close();
            return;
        }
        int n = sc.nextInt();
        sc.nextLine();

        if (n <= 0) {
            System.out.println("N must be positive.");
            sc.close();
            return;
        }

        double min = Double.POSITIVE_INFINITY;
        double max = Double.NEGATIVE_INFINITY;
        double sum = 0.0;

        for (int i = 1; i <= n; i++) {
            while (true) {
                System.out.print("Enter number " + i + ": ");
                String line = sc.nextLine().trim();
                try {
                    double x = Double.parseDouble(line);
                    sum += x;
                    if (x < min) min = x;
                    if (x > max) max = x;
                    break;
                } catch (NumberFormatException e) {
                    System.out.println("Invalid number. Try again.");
                }
            }
        }

        double avg = sum / n;
        System.out.printf("Min = %.3f%n", min);
        System.out.printf("Max = %.3f%n", max);
        System.out.printf("Avg = %.3f%n", avg);

        sc.close();
    }
}
```

---

## 9. Meta‑Commentary: Critique of the Source Material

### What was well‑explained

* Topic list is clear: variables → operators → casting → strings → I/O → comments → random.
* Basic syntax examples are provided for key features.

### Gaps / unclear parts

* Missing deep explanation of **integer division** and **type promotion** (a major beginner trap).
* Missing differences between **primitive vs reference** types (critical for later chapters).
* `Scanner` pitfalls (especially `nextInt()` then `nextLine()`) are not addressed.
* `String` is introduced, but **immutability**, `.equals()` vs `==`, and performance (`StringBuilder`) are not fully covered.
* Output formatting shows specifiers, but lacks practical tables and common patterns.

### Possible errors / issues

* Some code samples may contain “smart quotes” which break compilation.
* Some examples have naming inconsistencies (e.g., `firstName` vs `firstname`).

### How these notes improve it

* Adds “Why it exists” + the internal mental model for each concept.
* Provides complete runnable programs (minimal → realistic → advanced).
* Adds decision trees, comparisons, and common mistakes with fixes.
* Adds modern practices (`StringBuilder`, safe casting, secure randomness).

### Suggestions for further study

* Control flow (`if`, loops), arrays, methods, and OOP fundamentals.
* Exception handling basics.
* Testing mindset: predict output, then run to verify.
