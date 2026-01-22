    # JAVA Fundamentals — Control Flow (Selection & Repetition)

> Goal: master **decision-making** and **repetition** in Java so you can write correct, readable programs under exam pressure.

---

## Table of contents

* [1. Introduction & Motivation](#1-introduction--motivation)

  * [1.1 Why does control flow exist?](#11-why-does-control-flow-exist)
  * [1.2 Prerequisites you must be comfortable with](#12-prerequisites-you-must-be-comfortable-with)
  * [1.3 Terminology (defined before use)](#13-terminology-defined-before-use)
* [2. Fundamentals (building blocks)](#2-fundamentals-building-blocks)

  * [2.1 Booleans and conditions](#21-booleans-and-conditions)
  * [2.2 Relational operators](#22-relational-operators)
  * [2.3 Logical operators + short-circuiting](#23-logical-operators--short-circuiting)
  * [2.4 Operator precedence (the “silent bug-maker”)](#24-operator-precedence-the-silent-bug-maker)
* [3. Core Concepts — Selection](#3-core-concepts--selection)

  * [3.1 `if` statement](#31-if-statement)
  * [3.2 `if-else` and `else-if` ladder](#32-if-else-and-else-if-ladder)
  * [3.3 Nested conditionals (and how to avoid deep nesting)](#33-nested-conditionals-and-how-to-avoid-deep-nesting)
  * [3.4 `switch` statement](#34-switch-statement)
  * [3.5 Modern `switch` expression (recommended when available)](#35-modern-switch-expression-recommended-when-available)
  * [3.6 Ternary operator `?:`](#36-ternary-operator-)
* [4. Core Concepts — Repetition](#4-core-concepts--repetition)

  * [4.1 Why loops exist](#41-why-loops-exist)
  * [4.2 `while` loop](#42-while-loop)
  * [4.3 `do-while` loop](#43-do-while-loop)
  * [4.4 `for` loop](#44-for-loop)
  * [4.5 Enhanced `for-each` loop](#45-enhanced-for-each-loop)
  * [4.6 `break`, `continue`, and labels](#46-break-continue-and-labels)
  * [4.7 Nested loops + complexity](#47-nested-loops--complexity)
* [5. Integration & Combinations (real programs)](#5-integration--combinations-real-programs)

  * [5.1 Robust input loops (Scanner pitfalls + fixes)](#51-robust-input-loops-scanner-pitfalls--fixes)
  * [5.2 Menu-driven console program (selection + repetition)](#52-menu-driven-console-program-selection--repetition)
  * [5.3 Search + early exit patterns](#53-search--early-exit-patterns)
* [6. Best Practices & Modern Approaches](#6-best-practices--modern-approaches)
* [7. Common Pitfalls (with ❌ wrong → ✅ right)](#7-common-pitfalls-with--wrong--right)
* [8. Reference & Summary](#8-reference--summary)

  * [8.1 Quick reference tables](#81-quick-reference-tables)
  * [8.2 Decision trees (which construct to use?)](#82-decision-trees-which-construct-to-use)
  * [8.3 Mini-cheat sheet](#83-mini-cheat-sheet)
* [9. Practice Exercises (easy → hard) + Solutions](#9-practice-exercises-easy--hard--solutions)
* [10. Meta-commentary: critique of the source material](#10-meta-commentary-critique-of-the-source-material)

---

# 1. Introduction & Motivation

## 1.1 Why does control flow exist?

A computer executes instructions **one by one** in order. That default is called **sequence**.

But real problems are not purely sequential:

* Sometimes you must **choose** an action (e.g., “if password correct, log in; else show error”).
* Sometimes you must **repeat** an action (e.g., “keep asking until input is valid”).

**Control flow** exists so programs can adapt to **conditions** and **repetition**, turning static code into responsive behavior.

### Mental model

Think of your program like a train on tracks:

* **Sequence**: train goes forward on one track.
* **Selection**: there’s a junction; you pick a path.
* **Repetition**: there’s a loop track; you keep circling until an exit condition opens.

---

## 1.2 Prerequisites you must be comfortable with

If these feel shaky, fix them first:

* Variables and assignment (`int x = 5;`, `x = x + 1;`)
* Types: `int`, `double`, `char`, `boolean`, `String`
* Expressions vs statements
* Input/output basics: `System.out.println`, `Scanner`
* Arrays (at least: `int[] a = {1,2,3};` and `a.length`)

---

## 1.3 Terminology (defined before use)

* **Expression**: produces a value. Example: `x + 1`, `a > b`, `name.equals("Jon")`.
* **Statement**: an action. Example: `x = 3;`, `System.out.println(x);`, `if (...) {...}`.
* **Block**: a group of statements inside `{ ... }`.
* **Condition**: a boolean expression (`true`/`false`) used to decide flow.
* **Branch**: one possible path of execution (true-branch / false-branch).
* **Iteration**: one cycle of a loop.
* **Loop invariant**: something that remains true at a key point in each iteration (useful for proving correctness).
* **Sentinel**: a special value that means “stop” (e.g., user enters `-1` to end input).

---

# 2. Fundamentals (building blocks)

## 2.1 Booleans and conditions

A **boolean** is either `true` or `false`.

Conditions in Java must be boolean expressions.

✅ Good:

```java
int age = 20;
if (age >= 18) {
    System.out.println("Adult");
}
```

❌ Not allowed (Java is not Python/C in this way):

```java
int x = 5;
if (x) { // Compile error: x is not boolean
}
```

---

## 2.2 Relational operators

Relational operators compare two values and produce a boolean.

| Operator | Meaning          | Example  | Notes                                                                      |
| -------- | ---------------- | -------- | -------------------------------------------------------------------------- |
| `==`     | equal to         | `a == b` | For primitives: compares value. For objects: compares reference (address). |
| `!=`     | not equal to     | `a != b` | Same `==` note.                                                            |
| `>`      | greater than     | `x > y`  | Numeric only                                                               |
| `>=`     | greater or equal | `x >= y` | Numeric only                                                               |
| `<`      | less than        | `x < y`  | Numeric only                                                               |
| `<=`     | less or equal    | `x <= y` | Numeric only                                                               |

### String comparison warning

* `==` checks *whether two references point to the same object*, not whether contents match.
* Use `equals` / `equalsIgnoreCase` for content.

```java
String a = "hi";
String b = new String("hi");

System.out.println(a == b);        // often false
System.out.println(a.equals(b));   // true
```

---

## 2.3 Logical operators + short-circuiting

Logical operators combine boolean conditions.

| Operator | Name | Meaning                | Short-circuits? |                           |       |
| -------- | ---- | ---------------------- | --------------- | ------------------------- | ----- |
| `&&`     | AND  | true only if both true | ✅ Yes           |                           |       |
| `        |      | `                      | OR              | true if at least one true | ✅ Yes |
| `!`      | NOT  | flips boolean          | N/A             |                           |       |

### Why short-circuiting matters (mechanism)

Java evaluates `&&` and `||` **left to right**:

* `A && B`: if `A` is false, Java **skips** evaluating `B`.
* `A || B`: if `A` is true, Java **skips** evaluating `B`.

This prevents errors and saves time.

#### Example: null-safety

```java
String s = null;

// ✅ safe: if s == null is true, Java never runs s.length()
if (s == null || s.length() == 0) {
    System.out.println("Empty or null");
}

// ❌ unsafe: would crash with NullPointerException
// if (s.length() == 0 || s == null) { }
```

> [!TIP]
> **Order your conditions** from cheapest/safest → most expensive/risky.

---

## 2.4 Operator precedence (the “silent bug-maker”)

Common order (high → low):

1. `!`
2. `* / %`
3. `+ -`
4. `< <= > >=`
5. `== !=`
6. `&&`
7. `||`

✅ Use parentheses to communicate intent.

```java
boolean ok = (age >= 18) && (score > 70 || isPremium);
```

---

# 3. Core Concepts — Selection

## 3.1 `if` statement

### Core explanation

* **Definition**: executes a block only if a condition is true.
* **Purpose**: pick an action when *something holds*.
* **Motivation**: avoids writing two separate programs for two scenarios.
* **Mechanism**: the condition is evaluated; if true, control jumps into the block; otherwise it skips it.

### Syntax

```java
if (condition) {
    // statements
}
```

> [!IMPORTANT]
> Best practice: **always** use braces `{}` even for single statements.

### Minimal example

```java
public class IfMinimal {
    public static void main(String[] args) {
        int x = 5;
        if (x > 0) {
            System.out.println("Positive");
        }
    }
}
```

### Realistic example (input validation)

```java
import java.util.Scanner;

public class IfRealistic {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter your age: ");
        int age = sc.nextInt();

        if (age < 0) {
            System.out.println("Age cannot be negative.");
        }

        // In a small demo program, closing is fine:
        sc.close();
    }
}
```

### Advanced example (guard clauses)

Guard clauses reduce nesting by handling “bad” cases early.

```java
public class IfAdvancedGuard {
    public static void main(String[] args) {
        System.out.println(formatUsername(null));
        System.out.println(formatUsername("  jon  "));
    }

    static String formatUsername(String raw) {
        if (raw == null) {
            return "<missing>";
        }

        String trimmed = raw.trim();
        if (trimmed.isEmpty()) {
            return "<blank>";
        }

        return trimmed.toLowerCase();
    }
}
```

### When to use

* Single condition gating a piece of code
* Early input validation
* Guarding against `null`

### When NOT to use

* Many discrete cases on one value → `switch`
* Complex branching logic → consider refactoring into methods or strategy pattern

---

## 3.2 `if-else` and `else-if` ladder

### Core explanation

* **Definition**: choose between two (or many) mutually exclusive branches.
* **Purpose**: ensure exactly one branch runs.
* **Mechanism**: Java tests conditions in order; first true branch executes; rest skipped.

### Syntax

```java
if (cond1) {
    ...
} else if (cond2) {
    ...
} else {
    ...
}
```

### Minimal example

```java
public class IfElseMinimal {
    public static void main(String[] args) {
        int x = -2;
        if (x >= 0) {
            System.out.println("Non-negative");
        } else {
            System.out.println("Negative");
        }
    }
}
```

### Realistic example (grade classification)

```java
public class GradeClassifier {
    public static void main(String[] args) {
        int score = 83;

        String grade;
        if (score >= 90) {
            grade = "A";
        } else if (score >= 80) {
            grade = "B";
        } else if (score >= 70) {
            grade = "C";
        } else if (score >= 60) {
            grade = "D";
        } else {
            grade = "F";
        }

        System.out.println("Score: " + score + " => " + grade);
    }
}
```

### Advanced example (range checks done correctly)

A common beginner mistake is chaining like `0 <= mark <= 100` (not valid in Java).

✅ Correct approach:

```java
public class RangeCheck {
    public static void main(String[] args) {
        int mark = 105;

        if (mark < 0 || mark > 100) {
            System.out.println("Invalid mark");
        } else {
            System.out.println("Valid mark");
        }
    }
}
```

### Anti-pattern: overlapping conditions

Bad ordering can make later cases unreachable.

❌ Wrong:

```java
int x = 5;
if (x > 0) {
    System.out.println(">0");
} else if (x > 10) {
    System.out.println(">10"); // never runs
}
```

✅ Right:

```java
int x = 5;
if (x > 10) {
    System.out.println(">10");
} else if (x > 0) {
    System.out.println(">0");
}
```

---

## 3.3 Nested conditionals (and how to avoid deep nesting)

### Why nesting happens

When decisions depend on earlier decisions.

### Why deep nesting is bad

* Hard to read
* Hard to test
* Bugs hide in indentation

### Fix strategies

1. **Guard clauses** (return early)
2. **Extract methods** (name the logic)
3. **Use boolean variables** for readability

### Before/After

❌ Deep nesting:

```java
public class NestedBad {
    public static void main(String[] args) {
        String user = "admin";
        String pass = "123";

        if (user != null) {
            if (user.equals("admin")) {
                if (pass != null) {
                    if (pass.equals("123")) {
                        System.out.println("Access granted");
                    } else {
                        System.out.println("Wrong pass");
                    }
                } else {
                    System.out.println("Pass missing");
                }
            } else {
                System.out.println("User not found");
            }
        } else {
            System.out.println("User missing");
        }
    }
}
```

✅ Cleaner with guards:

```java
public class NestedBetter {
    public static void main(String[] args) {
        System.out.println(auth("admin", "123"));
        System.out.println(auth(null, "123"));
        System.out.println(auth("admin", null));
        System.out.println(auth("bob", "123"));
    }

    static String auth(String user, String pass) {
        if (user == null) return "User missing";
        if (!user.equals("admin")) return "User not found";
        if (pass == null) return "Pass missing";
        if (!pass.equals("123")) return "Wrong pass";
        return "Access granted";
    }
}
```

---

## 3.4 `switch` statement

### Core explanation

* **Definition**: multi-way selection based on a single expression.
* **Purpose**: replaces long `else-if` chains when comparing one variable against many discrete constant values.
* **Motivation**: readability + sometimes faster than many comparisons.
* **Mechanism**: evaluates the switch expression once; jumps to the matching `case`; runs until `break` or end (fall-through).

### Supported types (classic switch)

* `byte`, `short`, `char`, `int`
* `String`
* `enum`
* (and their wrapper types)

### Minimal example

```java
public class SwitchMinimal {
    public static void main(String[] args) {
        int day = 3;

        switch (day) {
            case 1:
                System.out.println("Monday");
                break;
            case 2:
                System.out.println("Tuesday");
                break;
            case 3:
                System.out.println("Wednesday");
                break;
            default:
                System.out.println("Invalid day");
        }
    }
}
```

### Fall-through (feature + footgun)

Sometimes you WANT fall-through to group cases.

```java
public class SwitchFallThrough {
    public static void main(String[] args) {
        int grade = 85;

        switch (grade / 10) {
            case 10:
            case 9:
                System.out.println("A");
                break;
            case 8:
                System.out.println("B");
                break;
            case 7:
                System.out.println("C");
                break;
            default:
                System.out.println("Below C");
        }
    }
}
```

> [!WARNING]
> Forgetting `break` is one of the most common `switch` bugs.

### When to use

* Many discrete cases on **one value**
* Menu options (`1..4`), commands (`"ADD"`, `"QUIT"`), enum states

### When NOT to use

* When conditions are ranges or complex expressions → `if-else`
* When each case has heavy logic → consider polymorphism/strategy (advanced)

---

## 3.5 Modern `switch` expression (recommended when available)

If your Java version supports it (Java 14+), prefer switch expressions for:

* returning values cleanly
* avoiding fall-through

```java
public class SwitchExpressionDemo {
    public static void main(String[] args) {
        String command = "start";

        int code = switch (command.toLowerCase()) {
            case "start" -> 1;
            case "stop"  -> 2;
            case "pause" -> 3;
            default       -> 0;
        };

        System.out.println("code = " + code);
    }
}
```

> [!NOTE]
> If your course/exam targets older Java, you can still write the classic `switch`.

---

## 3.6 Ternary operator `?:`

### Core explanation

* **Definition**: expression form of `if-else` that returns a value.
* **Purpose**: concise value assignment based on a condition.
* **Motivation**: reduce boilerplate for simple decisions.
* **Mechanism**: evaluate condition; choose left or right expression.

### Syntax

```java
condition ? valueIfTrue : valueIfFalse
```

### Minimal example

```java
public class TernaryMinimal {
    public static void main(String[] args) {
        int x = -3;
        int abs = (x >= 0) ? x : -x;
        System.out.println(abs);
    }
}
```

### Realistic example

```java
public class TernaryRealistic {
    public static void main(String[] args) {
        int age = 17;
        String msg = (age >= 18) ? "Can vote" : "Too young";
        System.out.println(msg);
    }
}
```

### Advanced example (why nested ternary is risky)

❌ Hard to read:

```java
int score = 85;
char grade = (score >= 90) ? 'A' : (score >= 80) ? 'B' : (score >= 70) ? 'C' : 'F';
```

✅ Better:

```java
public class TernaryBetter {
    public static void main(String[] args) {
        int score = 85;
        char grade;

        if (score >= 90) grade = 'A';
        else if (score >= 80) grade = 'B';
        else if (score >= 70) grade = 'C';
        else grade = 'F';

        System.out.println(grade);
    }
}
```

### When to use

* Simple assignments
* Short expressions

### When NOT to use

* Any time it becomes a “puzzle”

---

# 4. Core Concepts — Repetition

## 4.1 Why loops exist

Loops exist because:

* repeating code manually is error-prone
* the number of repetitions often depends on data

Two common loop styles:

* **Count-controlled**: repeat a known number of times (e.g., 10 times)
* **Sentinel-controlled**: repeat until a stop signal appears (e.g., input `-1`)

---

## 4.2 `while` loop

### Core explanation

* **Definition**: pre-test loop that repeats while condition is true.
* **Purpose**: repeat when you *don’t* know iterations in advance.
* **Mechanism**: check condition → if true run body → go back to check.

### Visual flow

```
check condition
   | true
   v
 run body
   |
   +----> back to condition

false -> exit loop
```

### Minimal example

```java
public class WhileMinimal {
    public static void main(String[] args) {
        int i = 1;
        while (i <= 3) {
            System.out.println(i);
            i++;
        }
    }
}
```

### Realistic example (sentinel-controlled sum)

```java
import java.util.Scanner;

public class WhileSentinelSum {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int sum = 0;

        while (true) {
            System.out.print("Enter a number (or -1 to stop): ");
            int x = sc.nextInt();
            if (x == -1) break;
            sum += x;
        }

        System.out.println("Sum = " + sum);
        sc.close();
    }
}
```

### Advanced example (loop invariant thinking)

We want to compute factorial n!.
Invariant idea: after k iterations, `result` equals k!.

```java
public class WhileFactorial {
    public static void main(String[] args) {
        int n = 5;
        long result = 1;
        int k = 1;

        while (k <= n) {
            result *= k;
            k++;
        }

        System.out.println(n + "! = " + result);
    }
}
```

### Edge cases & error handling

* Make sure your loop variable changes, or you get an **infinite loop**.
* Validate input and handle non-integers if using `Scanner`.

---

## 4.3 `do-while` loop

### Core explanation

* **Definition**: post-test loop; runs body **at least once**.
* **Purpose**: best for menus or prompts that must appear once.
* **Mechanism**: run body → check condition → repeat if true.

### Minimal example

```java
public class DoWhileMinimal {
    public static void main(String[] args) {
        int i = 1;
        do {
            System.out.println(i);
            i++;
        } while (i <= 3);
    }
}
```

### Realistic example (menu loop)

```java
import java.util.Scanner;

public class DoWhileMenu {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int choice;

        do {
            System.out.println("\nMenu:");
            System.out.println("1) Say hi");
            System.out.println("2) Add two numbers");
            System.out.println("3) Exit");
            System.out.print("Choice: ");

            while (!sc.hasNextInt()) {
                System.out.println("Please enter a number.");
                sc.next();
                System.out.print("Choice: ");
            }
            choice = sc.nextInt();

            switch (choice) {
                case 1:
                    System.out.println("Hi!");
                    break;
                case 2:
                    System.out.print("a: ");
                    int a = sc.nextInt();
                    System.out.print("b: ");
                    int b = sc.nextInt();
                    System.out.println("a + b = " + (a + b));
                    break;
                case 3:
                    System.out.println("Bye.");
                    break;
                default:
                    System.out.println("Invalid option.");
            }

        } while (choice != 3);

        sc.close();
    }
}
```

### When NOT to use

If you truly might need **zero** iterations, prefer `while`.

---

## 4.4 `for` loop

### Core explanation

* **Definition**: count-controlled loop with init, condition, update.
* **Purpose**: repeat a known or index-based number of times.
* **Mechanism** (exact order):

  1. run **initialization** once
  2. check **condition**
  3. run **body**
  4. run **update**
  5. go back to condition

### Minimal example

```java
public class ForMinimal {
    public static void main(String[] args) {
        for (int i = 1; i <= 5; i++) {
            System.out.println(i);
        }
    }
}
```

### Realistic example (sum array)

```java
public class ForSumArray {
    public static void main(String[] args) {
        int[] marks = {85, 90, 78, 92, 88};

        int total = 0;
        for (int i = 0; i < marks.length; i++) {
            total += marks[i];
        }

        double avg = (double) total / marks.length;
        System.out.println("Total = " + total);
        System.out.println("Average = " + avg);
    }
}
```

### Advanced example (multiple variables in a for-loop)

```java
public class ForAdvancedMultiVar {
    public static void main(String[] args) {
        // i goes up, j goes down
        for (int i = 0, j = 10; i <= 10; i++, j--) {
            System.out.println("i=" + i + ", j=" + j);
        }
    }
}
```

### Edge cases

* Off-by-one errors (`<` vs `<=`, start at 0 vs 1)
* Updating wrong direction (`i--` instead of `i++`)

---

## 4.5 Enhanced `for-each` loop

### Why it exists

Index-based loops are noisy when you only need “each element”.

### Syntax

```java
for (type item : collectionOrArray) {
    ...
}
```

### Minimal example

```java
public class ForEachMinimal {
    public static void main(String[] args) {
        int[] nums = {10, 20, 30};

        for (int x : nums) {
            System.out.println(x);
        }
    }
}
```

### When to use

* reading every element

### When NOT to use

* when you need the index
* when you need to modify array elements by index (you can still, but you’ll need index)

---

## 4.6 `break`, `continue`, and labels

### `break`

* exits the nearest loop (or `switch`).

### `continue`

* skips the rest of the current iteration and goes to the next iteration.

### Minimal `break` example

```java
public class BreakMinimal {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            if (i == 5) break;
            System.out.print(i + " ");
        }
        System.out.println();
    }
}
```

### Minimal `continue` example

```java
public class ContinueMinimal {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            if (i % 2 == 0) continue;
            System.out.print(i + " ");
        }
        System.out.println();
    }
}
```

### Labels (advanced; use sparingly)

A **label** names a loop so `break`/`continue` can target an outer loop.

```java
public class LabelDemo {
    public static void main(String[] args) {
        int targetRow = 3;
        int targetCol = 4;

        outer:
        for (int row = 1; row <= 5; row++) {
            for (int col = 1; col <= 5; col++) {
                if (row == targetRow && col == targetCol) {
                    System.out.println("Found at (" + row + "," + col + ")");
                    break outer; // breaks out of BOTH loops
                }
            }
        }
    }
}
```

> [!WARNING]
> Labels can make code harder to follow. Use them only when they significantly simplify nested-loop exits.

---

## 4.7 Nested loops + complexity

Nested loops are powerful for grids/matrices, but runtime can grow fast.

* Outer `n` and inner `m` → total ~ `n * m` iterations.

### Example: multiplication table

```java
public class MultiplicationTable {
    public static void main(String[] args) {
        for (int i = 1; i <= 5; i++) {
            for (int j = 1; j <= 5; j++) {
                System.out.print((i * j) + "\t");
            }
            System.out.println();
        }
    }
}
```

---

# 5. Integration & Combinations (real programs)

## 5.1 Robust input loops (Scanner pitfalls + fixes)

### Pitfall 1: `nextInt()` then `nextLine()`

After `nextInt()`, the newline remains in the buffer.

✅ Fix: consume the newline.

```java
import java.util.Scanner;

public class ScannerNewlineFix {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter age: ");
        int age = sc.nextInt();
        sc.nextLine(); // consume leftover newline

        System.out.print("Enter name: ");
        String name = sc.nextLine();

        System.out.println("Hello " + name + ", age " + age);
        sc.close();
    }
}
```

### Pitfall 2: parsing bad input

✅ Strategy: read as String, then parse with try/catch.

```java
import java.util.Scanner;

public class SafeIntInput {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int value = readInt(sc, "Enter an integer (0..100): ", 0, 100);
        System.out.println("You entered: " + value);

        sc.close();
    }

    static int readInt(Scanner sc, String prompt, int min, int max) {
        while (true) {
            System.out.print(prompt);
            String line = sc.nextLine();

            try {
                int x = Integer.parseInt(line.trim());
                if (x < min || x > max) {
                    System.out.println("Out of range (" + min + ".." + max + ").");
                    continue;
                }
                return x;
            } catch (NumberFormatException e) {
                System.out.println("Not an integer. Try again.");
            }
        }
    }
}
```

---

## 5.2 Menu-driven console program (selection + repetition)

A mini “ATM” demo that uses:

* `do-while` menu
* `switch`
* input validation

```java
import java.util.Scanner;

public class MiniATM {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int balance = 1000;
        int choice;

        do {
            System.out.println("\n=== Mini ATM ===");
            System.out.println("1) Check balance");
            System.out.println("2) Deposit");
            System.out.println("3) Withdraw");
            System.out.println("4) Exit");
            System.out.print("Choose: ");

            while (!sc.hasNextInt()) {
                System.out.println("Please enter 1..4");
                sc.next();
                System.out.print("Choose: ");
            }
            choice = sc.nextInt();

            switch (choice) {
                case 1:
                    System.out.println("Balance: " + balance);
                    break;
                case 2: {
                    System.out.print("Deposit amount: ");
                    int amt = sc.nextInt();
                    if (amt > 0) {
                        balance += amt;
                        System.out.println("Deposited. New balance: " + balance);
                    } else {
                        System.out.println("Invalid deposit.");
                    }
                    break;
                }
                case 3: {
                    System.out.print("Withdraw amount: ");
                    int amt = sc.nextInt();
                    if (amt <= 0) {
                        System.out.println("Invalid withdrawal.");
                    } else if (amt > balance) {
                        System.out.println("Insufficient funds.");
                    } else {
                        balance -= amt;
                        System.out.println("Withdrawn. New balance: " + balance);
                    }
                    break;
                }
                case 4:
                    System.out.println("Goodbye.");
                    break;
                default:
                    System.out.println("Invalid option.");
            }

        } while (choice != 4);

        sc.close();
    }
}
```

---

## 5.3 Search + early exit patterns

### Pattern: find first match

```java
public class FindFirst {
    public static void main(String[] args) {
        int[] nums = {10, 20, 30, 40, 50};
        int target = 30;

        int index = -1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                index = i;
                break;
            }
        }

        if (index == -1) {
            System.out.println("Not found");
        } else {
            System.out.println("Found at index " + index);
        }
    }
}
```

---

# 6. Best Practices & Modern Approaches

✅ **Always use braces `{}`** even for one-liners.

* Prevents bugs when you add lines later.

✅ Prefer **guard clauses** to reduce nesting.

✅ Prefer `switch` (or modern switch expression) when:

* one variable
* many discrete constant cases

✅ Prefer `for-each` when you don’t need indices.

✅ Think about **loop correctness** using 3 questions:

1. What starts the loop? (initialization)
2. What keeps it running? (condition)
3. What changes to eventually stop it? (update)

✅ Use meaningful names:

* `i` is fine for small loops; otherwise use `row`, `col`, `index`, `attempts`.

✅ Be careful closing `Scanner(System.in)` in larger apps.

* Closing it closes `System.in` and can break later input.
* In exams/small programs, closing at the end is fine.

✅ Modern randomness:

* For simple fundamentals, `new java.util.Random()` is fine.
* In multithreaded apps, `java.util.concurrent.ThreadLocalRandom` is better.

---

# 7. Common Pitfalls (with ❌ wrong → ✅ right)

## 7.1 String equality

❌ Wrong:

```java
if (s1 == s2) {
}
```

✅ Right:

```java
if (s1 != null && s1.equals(s2)) {
}
```

## 7.2 Assignment vs comparison

❌ Wrong:

```java
if (choice = 'Q') {
}
```

✅ Right:

```java
if (choice == 'Q') {
}
```

## 7.3 Chained comparisons

❌ Wrong:

```java
if (0 <= mark <= 100) {
}
```

✅ Right:

```java
if (0 <= mark && mark <= 100) {
}
```

## 7.4 Missing braces

❌ Wrong:

```java
if (x > 0)
    System.out.println("A");
    System.out.println("B"); // always runs
```

✅ Right:

```java
if (x > 0) {
    System.out.println("A");
    System.out.println("B");
}
```

## 7.5 Off-by-one

❌ Wrong (prints 0..5 when you wanted 1..5):

```java
for (int i = 0; i <= 5; i++) {
    System.out.println(i);
}
```

✅ Right:

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

## 7.6 Infinite loop (update missing)

❌ Wrong:

```java
int i = 0;
while (i < 10) {
    System.out.println(i);
}
```

✅ Right:

```java
int i = 0;
while (i < 10) {
    System.out.println(i);
    i++;
}
```

## 7.7 `switch` missing `break`

❌ Wrong:

```java
switch (n) {
    case 1:
        System.out.println("One");
    case 2:
        System.out.println("Two");
}
```

✅ Right:

```java
switch (n) {
    case 1:
        System.out.println("One");
        break;
    case 2:
        System.out.println("Two");
        break;
    default:
        System.out.println("Other");
}
```

---

# 8. Reference & Summary

## 8.1 Quick reference tables

### Selection constructs

| Construct        | Best for             | Runs body when    | Notes               |
| ---------------- | -------------------- | ----------------- | ------------------- |
| `if`             | single decision      | condition true    | simplest            |
| `if-else`        | two-way choice       | one of two        | good clarity        |
| `else-if` ladder | multi-way ranges     | first true branch | order matters       |
| `switch`         | many discrete values | case match        | beware fall-through |
| ternary `?:`     | assign small values  | condition chooses | keep short          |

### Loop constructs

| Loop       | Condition checked | Best for           | Guarantee          |
| ---------- | ----------------- | ------------------ | ------------------ |
| `while`    | before body       | unknown iterations | may run 0 times    |
| `do-while` | after body        | menus, prompts     | runs at least once |
| `for`      | before body       | count/index loops  | may run 0 times    |
| `for-each` | before body       | iterate all items  | no index           |

---

## 8.2 Decision trees (which construct to use?)

### Selection decision tree

```
Do you choose between actions?
  |
  +-- Only one condition? -> if
  |
  +-- Two branches? -> if-else
  |
  +-- Many cases based on ONE variable, discrete constants? -> switch
  |
  +-- Many cases but based on ranges/complex expressions? -> else-if ladder
  |
  +-- Assigning one value based on a simple condition? -> ternary
```

### Loop decision tree

```
Do you need repetition?
  |
  +-- Known number of times / index-based? -> for
  |
  +-- Need to visit each element (no index)? -> for-each
  |
  +-- Unknown repetitions, condition decides? -> while
  |
  +-- Must run at least once? -> do-while
```

---

## 8.3 Mini-cheat sheet

* String equality: `a.equals(b)` (null-safe: `a != null && a.equals(b)`)
* Range check: `min <= x && x <= max`
* Short-circuit safety: check null first `s == null || s.isEmpty()`
* `switch`: remember `break` unless you intentionally group cases
* Loops: init → condition → update (must eventually stop)

---

# 9. Practice Exercises (easy → hard) + Solutions

## Conceptual questions

1. Why is `0 <= x <= 10` invalid in Java? Rewrite it correctly.
2. What is short-circuit evaluation? Give a real bug it prevents.
3. When is `do-while` better than `while`?
4. What happens if you forget `break` in a `switch` case?
5. Why is `==` wrong for String content comparison?

## Coding exercises

### Easy

1. Print all even numbers from 2 to 20.
2. Read integers until user enters `-1`, then print their average.

### Medium

3. Create a menu that repeats until user chooses Exit; options: (1) add, (2) subtract.
4. Given an array, find the max value and its index.

### Hard

5. Number guessing game: random 1..100, count attempts, validate inputs.
6. Print a right triangle AND an inverted triangle using nested loops.

---

## Solutions

### 1) Print even numbers (2..20)

```java
public class EvenNumbers {
    public static void main(String[] args) {
        for (int i = 2; i <= 20; i += 2) {
            System.out.print(i + " ");
        }
        System.out.println();
    }
}
```

### 2) Sentinel average

```java
import java.util.Scanner;

public class SentinelAverage {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int sum = 0;
        int count = 0;

        while (true) {
            System.out.print("Enter integer (-1 to stop): ");
            int x = sc.nextInt();
            if (x == -1) break;
            sum += x;
            count++;
        }

        if (count == 0) {
            System.out.println("No numbers entered.");
        } else {
            double avg = (double) sum / count;
            System.out.println("Average = " + avg);
        }

        sc.close();
    }
}
```

### 3) Simple calculator menu

```java
import java.util.Scanner;

public class MenuCalc {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int choice;

        do {
            System.out.println("\n1) Add\n2) Subtract\n3) Exit");
            System.out.print("Choice: ");

            while (!sc.hasNextInt()) {
                System.out.println("Enter 1..3");
                sc.next();
                System.out.print("Choice: ");
            }
            choice = sc.nextInt();

            if (choice == 1 || choice == 2) {
                System.out.print("a: ");
                int a = sc.nextInt();
                System.out.print("b: ");
                int b = sc.nextInt();

                int result = (choice == 1) ? (a + b) : (a - b);
                System.out.println("Result = " + result);
            } else if (choice == 3) {
                System.out.println("Exit.");
            } else {
                System.out.println("Invalid.");
            }

        } while (choice != 3);

        sc.close();
    }
}
```

### 4) Max value + index

```java
public class MaxInArray {
    public static void main(String[] args) {
        int[] a = {7, 2, 9, 4, 9, 1};

        int max = a[0];
        int idx = 0;

        for (int i = 1; i < a.length; i++) {
            if (a[i] > max) {
                max = a[i];
                idx = i;
            }
        }

        System.out.println("max=" + max + " at index " + idx);
    }
}
```

### 5) Number guessing game (robust)

```java
import java.util.Random;
import java.util.Scanner;

public class GuessGame {
    public static void main(String[] args) {
        Random rnd = new Random();
        Scanner sc = new Scanner(System.in);

        int target = rnd.nextInt(100) + 1;
        int attempts = 0;

        System.out.println("Guess a number (1..100)");

        while (true) {
            System.out.print("Your guess: ");

            if (!sc.hasNextInt()) {
                System.out.println("Please enter an integer.");
                sc.next();
                continue;
            }

            int guess = sc.nextInt();
            attempts++;

            if (guess < 1 || guess > 100) {
                System.out.println("Out of range.");
                continue;
            }

            if (guess < target) {
                System.out.println("Too low");
            } else if (guess > target) {
                System.out.println("Too high");
            } else {
                System.out.println("Correct! Attempts: " + attempts);
                break;
            }
        }

        sc.close();
    }
}
```

### 6) Two triangles

```java
public class TwoTriangles {
    public static void main(String[] args) {
        int n = 5;

        // Right triangle
        for (int row = 1; row <= n; row++) {
            for (int col = 1; col <= row; col++) {
                System.out.print("* ");
            }
            System.out.println();
        }

        System.out.println();

        // Inverted triangle
        for (int row = n; row >= 1; row--) {
            for (int col = 1; col <= row; col++) {
                System.out.print("* ");
            }
            System.out.println();
        }
    }
}
```

---

# 10. Meta-commentary: critique of the source material

## What the source did well

* Introduces the idea of selection and repetition and gives basic syntax examples.
* Includes a few common errors (e.g., missing brackets, assignment vs equality, missing `break`).
* Provides practice-style mini projects (quiz, guessing game).

## What was missing / unclear

* Little explanation of **how conditions are evaluated internally**, especially **short-circuiting**.
* Not enough emphasis on **braces always** (it mentions braces only for >1 statement, which encourages bugs).
* Limited coverage of **when to use what** (decision-making guidance).
* Switch coverage doesn’t highlight modern `switch` expression; loops don’t include `for-each`.
* Some examples are not fully robust for real input (Scanner newline issues, invalid input handling).

## How these notes improve it

* Rebuilds the topic from “why” → “how”, defines terminology, and adds mental models.
* Adds decision trees, comparison tables, and before/after refactors.
* Provides complete runnable examples for minimal/realistic/advanced cases.
* Adds robust input patterns and edge-case handling.

## Suggestions for further study

After you master this:

* Methods (parameter passing, return values)
* Arrays & 2D arrays deeply
* Object-oriented basics (classes, objects, constructors)
* Exception handling (try/catch) beyond input parsing
* Collections (`ArrayList`), and iteration with iterators
