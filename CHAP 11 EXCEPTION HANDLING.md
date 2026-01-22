# Java Fundamentals — Exception Handling (Publication‑Quality Study Notes)

> Designed for WIX1002 / FOP level, but written with *industry-grade* clarity and best practices.

---

## Table of Contents

1. [Meta-Commentary on the Source Material](#meta-commentary-on-the-source-material)
2. [Introduction & Motivation](#introduction--motivation)
3. [Prerequisites & Background](#prerequisites--background)
4. [Fundamentals: What an Exception *Is*](#fundamentals-what-an-exception-is)
5. [Core Concepts](#core-concepts)

   1. [try / catch](#try--catch)
   2. [Multiple catch & catch ordering](#multiple-catch--catch-ordering)
   3. [Nested try/catch](#nested-trycatch)
   4. [finally](#finally)
   5. [throw vs throws](#throw-vs-throws)
6. [Checked vs Unchecked Exceptions](#checked-vs-unchecked-exceptions)
7. [Exception Hierarchy](#exception-hierarchy)
8. [Creating Your Own Exceptions](#creating-your-own-exceptions)
9. [Exceptions in Methods: Propagate vs Handle](#exceptions-in-methods-propagate-vs-handle)
10. [Modern Java: try-with-resources](#modern-java-try-with-resources)
11. [Patterns, Best Practices, and Trade-offs](#patterns-best-practices-and-trade-offs)
12. [Common Mistakes (❌ → ✅)](#common-mistakes---)
13. [Quick Reference & Cheat Sheets](#quick-reference--cheat-sheets)
14. [Practice & Application (with Solutions)](#practice--application-with-solutions)
15. [Further Study](#further-study)

---

## Meta-Commentary on the Source Material

Your lecture slides cover the **bare minimum** topics: *exception handling*, *try-catch*, *exception class*, *exceptions in methods*, and *finally* fileciteturn0file0.

### ✅ What was well-explained

* The idea that exceptions help build **robust software** and that exceptions are **thrown** then **caught and processed** fileciteturn0file0.
* Basic `try { ... } catch (...) { ... }` syntax and multiple/nested catch patterns fileciteturn0file0.

### ⚠️ Gaps / unclear / missing

1. **Missing core vocabulary**

   * No clear definitions of: **Throwable, Error vs Exception, checked vs unchecked, stack trace, propagate, rethrow, exception chaining**.
2. **Missing mechanism explanation**

   * Slides don’t explain *what Java actually does* (stack unwinding + handler search).
3. **Outdated / incomplete practices**

   * No mention of **try-with-resources** (modern standard for closing files).
   * No mention of **multi-catch** (`catch (A | B e)`) and **suppressed exceptions**.
4. **Error / misleading statements**

   * `DivisionByZeroException` is **not** a standard Java exception class. Division by zero for integers throws **`ArithmeticException`**.
   * “The finally always execute” is **not always true** (see [finally](#finally)).
5. **Custom exception code issues**

   * The constructor uses `super(S)` (capital `S`) which would not compile if the parameter is `String s`.
   * Naming style in slides doesn’t follow Java conventions (class names should be **PascalCase**).

### How these notes improve it

* Adds the missing foundations (why it exists, terminology, hierarchy, how it works).
* Adds modern Java practices + best-practice patterns.
* Adds complete runnable examples (minimal → realistic → advanced) and edge cases.
* Adds decision trees, comparisons, and common pitfalls with fixes.

---

## Introduction & Motivation

### Why does exception handling exist?

Because programs must deal with **unexpected situations** without crashing or producing wrong results.

**Without exceptions**, you’d have to:

* return special values everywhere (`-1`, `null`) and constantly check them,
* mix error-handling logic with normal logic,
* easily forget checks → bugs.

**With exceptions**, Java lets you:

* separate **normal flow** from **error flow**,
* *propagate* an error to a place that can handle it properly,
* keep code readable and safer.

### Real-world analogy

Think of a restaurant:

* Normal flow: take order → cook → serve.
* Exceptions: ingredient missing, stove breaks, customer allergic.

Instead of every worker guessing what to do, the problem is escalated to the right person:

* waiter handles “wrong dish”,
* manager handles “refund”,
* emergency handles “allergy”.

---

## Prerequisites & Background

You should already know:

* Methods (calling, returning, parameters)
* Classes and inheritance (extends)
* Basic I/O idea (file reading can fail)
* Basic flow control (if/else, loops)

Helpful mental model:

* **Call stack** = stack of method calls. When an exception happens, Java may **unwind** this stack.

---

## Fundamentals: What an Exception *Is*

### Definition

An **exception** is an *object* that represents an abnormal condition during program execution.

### Purpose

* Communicate **what went wrong**
* Provide context (message, cause)
* Allow a program to **recover** or **fail safely**

### Mechanism (what happens internally)

1. Something goes wrong → Java **creates** an exception object.
2. The exception is **thrown**.
3. Java searches for a matching `catch` block in the current method.
4. If not found, Java **unwinds** the call stack (returns from methods automatically) until it finds a handler.
5. If never handled → program terminates and prints a **stack trace**.

> **Stack trace** = a list of method calls showing how the program reached the error.

---

## Core Concepts

### try / catch

#### Core explanation

* `try` block: code that might fail.
* `catch` block: code that handles a specific exception type.

#### Minimal example (simplest case)

```java
public class TryCatchMinimal {
    public static void main(String[] args) {
        try {
            int x = 10 / 0; // integer division by zero
            System.out.println(x);
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero.");
        }

        System.out.println("Program continues.");
    }
}
```

#### Realistic example (practical use)

Parse user input safely:

```java
import java.util.Scanner;

public class ParseIntExample {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter an integer: ");
        String input = sc.nextLine();

        try {
            int value = Integer.parseInt(input);
            System.out.println("You entered: " + value);
        } catch (NumberFormatException e) {
            System.out.println("That was not a valid integer: '" + input + "'.");
        }
    }
}
```

#### Advanced example (complex scenario)

Validate multiple fields, produce helpful error messages:

```java
public class RegisterUserAdvanced {

    static class InvalidInputException extends Exception {
        public InvalidInputException(String message) {
            super(message);
        }
    }

    public static void main(String[] args) {
        try {
            register("", "abc");
            System.out.println("Registered successfully");
        } catch (InvalidInputException e) {
            System.out.println("Registration failed: " + e.getMessage());
        }
    }

    static void register(String username, String ageText) throws InvalidInputException {
        if (username == null || username.isBlank()) {
            throw new InvalidInputException("Username must not be blank");
        }

        int age;
        try {
            age = Integer.parseInt(ageText);
        } catch (NumberFormatException e) {
            throw new InvalidInputException("Age must be an integer");
        }

        if (age < 13) {
            throw new InvalidInputException("Age must be at least 13");
        }
    }
}
```

#### Edge cases

* Exception thrown inside `catch` can also propagate.
* Catching too broad a type hides problems.

---

### Multiple catch & catch ordering

#### Core explanation

You can have multiple `catch` blocks for different exception types.

**Rule:** Catch more specific exceptions **before** more general ones.

#### Minimal example

```java
public class MultipleCatchMinimal {
    public static void main(String[] args) {
        try {
            int[] arr = {1, 2, 3};
            System.out.println(arr[10]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Index out of range");
        } catch (RuntimeException e) {
            System.out.println("Some runtime problem");
        }
    }
}
```

#### ❗ Why ordering matters

If you put `catch (RuntimeException e)` first, the later `catch (ArrayIndexOutOfBoundsException e)` becomes **unreachable** (compile-time error).

#### Modern variation: multi-catch (Java 7+)

Use when handling is identical:

```java
import java.io.*;

public class MultiCatchExample {
    public static void main(String[] args) {
        try {
            readFile("missing.txt");
        } catch (FileNotFoundException | SecurityException e) {
            System.out.println("Cannot open file: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("I/O problem: " + e.getMessage());
        }
    }

    static void readFile(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            System.out.println(br.readLine());
        }
    }
}
```

---

### Nested try/catch

#### When to use

* When a small operation inside a bigger operation needs a different handling strategy.

#### When NOT to use

* If nesting makes code unreadable. Prefer extracting methods.

#### Example (from concept shown in slides) fileciteturn0file0

```java
public class NestedTryExample {
    public static void main(String[] args) {
        try {
            System.out.println("Outer try start");

            try {
                int x = Integer.parseInt("not-a-number");
                System.out.println(x);
            } catch (NumberFormatException e) {
                System.out.println("Inner: invalid number format");
            }

            int y = 10 / 0;
            System.out.println(y);

        } catch (ArithmeticException e) {
            System.out.println("Outer: division by zero");
        }

        System.out.println("Done");
    }
}
```

---

### finally

#### Core explanation

A `finally` block runs **after** `try`/`catch` *whether or not* an exception happens.

Typical uses:

* releasing resources (closing files, sockets)
* cleaning up (resetting state)

> Slides mention this role fileciteturn0file0, but the wording “finally always execute” is not 100% accurate.

#### Important truth: when `finally` might not run

`finally` usually runs, **except** in rare cases such as:

* `System.exit(...)`
* JVM crash / power loss
* fatal errors that stop the VM

#### Minimal example

```java
public class FinallyMinimal {
    public static void main(String[] args) {
        try {
            System.out.println("In try");
            int x = 10 / 0;
            System.out.println(x);
        } catch (ArithmeticException e) {
            System.out.println("In catch");
        } finally {
            System.out.println("In finally (cleanup)");
        }
    }
}
```

#### Edge case: return + finally

```java
public class FinallyReturnEdgeCase {
    public static void main(String[] args) {
        System.out.println(compute());
    }

    static int compute() {
        try {
            return 1;
        } finally {
            System.out.println("finally still runs");
        }
    }
}
```

#### ⚠️ Anti-pattern: return in finally

```java
public class ReturnInFinallyBad {
    static int bad() {
        try {
            return 1;
        } finally {
            return 2; // overrides the try return (confusing!)
        }
    }

    public static void main(String[] args) {
        System.out.println(bad()); // prints 2
    }
}
```

---

### throw vs throws

#### Definitions

* `throw` (statement): **throws an exception now**.
* `throws` (method signature): declares that a method **may throw** exceptions to its caller.

#### Minimal examples

**throw**:

```java
public class ThrowMinimal {
    public static void main(String[] args) {
        validateAge(10);
    }

    static void validateAge(int age) {
        if (age < 13) {
            throw new IllegalArgumentException("Age must be at least 13");
        }
        System.out.println("OK");
    }
}
```

**throws**:

```java
import java.io.*;

public class ThrowsMinimal {
    public static void main(String[] args) {
        try {
            readFirstLine("missing.txt");
        } catch (IOException e) {
            System.out.println("Failed: " + e.getMessage());
        }
    }

    static void readFirstLine(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            System.out.println(br.readLine());
        }
    }
}
```

#### Comparison table: handle here vs propagate

| Choice                 | Best when                                      | Pros                   | Cons                                |
| ---------------------- | ---------------------------------------------- | ---------------------- | ----------------------------------- |
| **Catch here**         | You can *recover* or show user-friendly output | Keeps failure local    | Can hide issues if done too broadly |
| **throws / propagate** | Caller has better context (UI/service layer)   | Cleaner low-level code | Forces callers to handle or wrap    |

---

## Checked vs Unchecked Exceptions

### Definitions

* **Checked exception**: compiler forces you to **catch or declare** (`throws`).

  * Examples: `IOException`, `FileNotFoundException`.
* **Unchecked exception**: subclass of `RuntimeException`. No need to declare.

  * Examples: `NullPointerException`, `ArithmeticException`, `NumberFormatException`.

### Why this exists

To force handling of errors that are:

* likely,
* recoverable,
* external to your program (I/O, network).

### Practical decision guide

* Use **unchecked** for programmer mistakes (invalid args, nulls, broken invariants).
* Use **checked** for operations that can fail due to environment (files, network).

#### Decision tree: Should this be checked or unchecked?

```
Is it caused by a programming bug (invalid state/argument)?
 ├─ Yes → Unchecked (RuntimeException)
 └─ No → Is recovery realistic for the caller?
        ├─ Yes → Checked (Exception)
        └─ No → Often Unchecked + log/abort gracefully
```

---

## Exception Hierarchy

### Visual hierarchy (simplified)

```
Throwable
├── Error                (serious VM/system problems; usually don't catch)
│   ├── OutOfMemoryError
│   └── StackOverflowError
└── Exception            (conditions apps may handle)
    ├── RuntimeException (unchecked)
    │   ├── NullPointerException
    │   ├── IllegalArgumentException
    │   ├── ArithmeticException
    │   └── NumberFormatException
    └── (checked)
        ├── IOException
        │   └── FileNotFoundException
        └── ClassNotFoundException
```

### Rule of thumb

* **Don’t catch `Error`** unless you truly know what you’re doing.
* Prefer catching the **most specific** exception you can handle.

---

## Creating Your Own Exceptions

### Why create a custom exception?

Because raw exceptions like `IllegalArgumentException` may not express domain meaning.

Example: In a banking app, “insufficient funds” is not a *programming bug*—it’s a business rule.

### Options (trade-offs)

| Extend             | Type      | Use when                                                  | Example                      |
| ------------------ | --------- | --------------------------------------------------------- | ---------------------------- |
| `Exception`        | checked   | caller *must* handle                                      | `InsufficientFundsException` |
| `RuntimeException` | unchecked | misuse/programmer error or you prefer not forcing callers | `InvalidAccountException`    |

### Minimal custom exception (checked)

```java
public class InvalidScoreException extends Exception {
    public InvalidScoreException(String message) {
        super(message);
    }
}
```

### Realistic use

```java
public class ScoreValidator {

    public static void main(String[] args) {
        try {
            setScore(120);
        } catch (InvalidScoreException e) {
            System.out.println("Cannot set score: " + e.getMessage());
        }
    }

    static void setScore(int score) throws InvalidScoreException {
        if (score < 0 || score > 100) {
            throw new InvalidScoreException("Score must be between 0 and 100");
        }
        System.out.println("Score set to " + score);
    }
}
```

### Advanced: exception chaining (keep original cause)

```java
import java.io.*;

public class ChainingExample {

    static class DataLoadException extends Exception {
        public DataLoadException(String message, Throwable cause) {
            super(message, cause); // preserve root cause
        }
    }

    public static void main(String[] args) {
        try {
            loadData("missing.txt");
        } catch (DataLoadException e) {
            System.out.println("Load failed: " + e.getMessage());
            e.printStackTrace();
        }
    }

    static void loadData(String path) throws DataLoadException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            System.out.println(br.readLine());
        } catch (IOException e) {
            throw new DataLoadException("Unable to load data from: " + path, e);
        }
    }
}
```

---

## Exceptions in Methods: Propagate vs Handle

Slides mention that a method can throw without catching, using `throws` fileciteturn0file0.

### Core idea

* Low-level method: do the work, **throw meaningful exceptions**.
* Higher-level method (UI/main): **decide how to respond**.

### Realistic layered example

```java
import java.io.*;

public class LayeredHandling {

    public static void main(String[] args) {
        try {
            String line = readFirstLine("config.txt");
            System.out.println("Config: " + line);
        } catch (IOException e) {
            // higher-level decision
            System.out.println("Cannot start app: config file missing or unreadable.");
        }
    }

    // low-level: declare the real failure
    static String readFirstLine(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        }
    }
}
```

---

## Modern Java: try-with-resources

### Why it exists

Closing resources is error-prone:

* you might forget `close()`
* `close()` might fail too

**try-with-resources** automatically closes any `AutoCloseable` resource.

### Before (old, error-prone)

```java
import java.io.*;

public class OldCloseStyle {
    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("data.txt"));
            System.out.println(br.readLine());
        } catch (IOException e) {
            System.out.println("Read error: " + e.getMessage());
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    System.out.println("Close error: " + e.getMessage());
                }
            }
        }
    }
}
```

### After (modern, recommended)

```java
import java.io.*;

public class TryWithResourcesStyle {
    public static void main(String[] args) {
        try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
            System.out.println(br.readLine());
        } catch (IOException e) {
            System.out.println("I/O error: " + e.getMessage());
        }
    }
}
```

### Advanced: multiple resources

```java
import java.io.*;

public class CopyFile {
    public static void main(String[] args) {
        try (FileInputStream in = new FileInputStream("a.bin");
             FileOutputStream out = new FileOutputStream("b.bin")) {

            byte[] buffer = new byte[1024];
            int n;
            while ((n = in.read(buffer)) != -1) {
                out.write(buffer, 0, n);
            }
            System.out.println("Copied!");

        } catch (IOException e) {
            System.out.println("Copy failed: " + e.getMessage());
        }
    }
}
```

> The attached example notes for File I/O already model this modern approach and style fileciteturn0file1.

---

## Patterns, Best Practices, and Trade-offs

### ✅ Best practices (with justification)

1. **Catch the most specific exception you can handle**

   * Improves correctness + avoids hiding bugs.

2. **Don’t swallow exceptions**

   * An empty catch block makes debugging painful.

3. **Add context when rethrowing**

   * Wrap with a message and keep the cause.

4. **Use exceptions for exceptional situations only**

   * Don’t use exceptions as normal control flow (slow + unclear).

5. **Fail fast with argument checks**

   * Use `IllegalArgumentException` for invalid parameters.

6. **Prefer try-with-resources for I/O**

   * Less code, safer cleanup.

### Performance considerations

* Creating exceptions + stack traces is relatively expensive.
* Don’t throw exceptions inside tight loops for normal logic.

### Security implications

* Don’t show raw stack traces to end users (could leak file paths, internal details).
* It’s okay in learning/console programs, but in real apps: log safely and show friendly messages.

---

## Common Mistakes (❌ → ✅)

### 1) Catching `Exception` everywhere

❌ Wrong:

```java
try {
    int x = Integer.parseInt("abc");
} catch (Exception e) {
    System.out.println("Error");
}
```

✅ Correct:

```java
try {
    int x = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    System.out.println("Please enter a valid integer.");
}
```

### 2) Empty catch block (swallowing)

❌ Wrong:

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
}
```

✅ Correct:

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

### 3) Losing the root cause when wrapping

❌ Wrong:

```java
catch (IOException e) {
    throw new RuntimeException("Failed");
}
```

✅ Correct:

```java
catch (IOException e) {
    throw new RuntimeException("Failed", e);
}
```

### 4) Wrong catch order

❌ Wrong (won’t compile):

```java
try {
    int[] a = {1};
    System.out.println(a[5]);
} catch (RuntimeException e) {
    System.out.println("runtime");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("index");
}
```

✅ Correct:****

```java
try {
    int[] a = {1};
    System.out.println(a[5]);
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("index");
} catch (RuntimeException e) {
    System.out.println("runtime");
}
```

### 5) Using exceptions for normal flow

❌ Wrong:

```java
int i = 0;
try {
    while (true) {
        System.out.println(arr[i++]);
    }
} catch (ArrayIndexOutOfBoundsException e) {
    // stop
}
```

✅ Correct:

```java
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

---

## Quick Reference & Cheat Sheets

### Common templates

**Basic try/catch**

```java
try {
    // risky code
} catch (SpecificException e) {
    // handle
}
```

**try/catch/finally**

```java
try {
    // risky
} catch (SpecificException e) {
    // handle
} finally {
    // cleanup
}
```

**Throwing your own exception**

```java
if (badCondition) {
    throw new IllegalArgumentException("Explain what's wrong");
}
```

**Declaring checked exceptions**

```java
public void read() throws IOException {
    // ...
}
```

### Common exception types (FOP-friendly)

| Exception                        | Checked? | Typical cause           | Example                     |
| -------------------------------- | -------: | ----------------------- | --------------------------- |
| `ArithmeticException`            |       No | divide by 0 (int)       | `10 / 0`                    |
| `NumberFormatException`          |       No | parse invalid number    | `Integer.parseInt("abc")`   |
| `ArrayIndexOutOfBoundsException` |       No | invalid array index     | `a[a.length]`               |
| `NullPointerException`           |       No | dereference null        | `s.length()` when `s==null` |
| `IOException`                    |      Yes | file/network I/O issues | file read/write             |
| `FileNotFoundException`          |      Yes | file missing            | `new FileReader("x")`       |

---

## Practice & Application (with Solutions)

### Conceptual questions

1. Explain the difference between `throw` and `throws`.
2. Why must specific catch blocks come before general ones?
3. When is using `finally` better than try-with-resources?
4. Give 2 examples of checked exceptions and 2 of unchecked exceptions.

### Coding exercises (easy → hard)

#### Exercise 1 (Easy): Safe division

**Task:** Ask for two integers, print `a/b`. If dividing by 0, print a friendly message.

✅ Solution:

```java
import java.util.Scanner;

public class SafeDivision {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter a: ");
        int a = sc.nextInt();

        System.out.print("Enter b: ");
        int b = sc.nextInt();

        try {
            int result = a / b;
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero.");
        }
    }
}
```

#### Exercise 2 (Medium): Custom exception

**Task:** Create `InvalidMarkException`. A mark must be 0–100.

✅ Solution:

```java
public class MarkChecker {

    static class InvalidMarkException extends Exception {
        public InvalidMarkException(String message) {
            super(message);
        }
    }

    public static void main(String[] args) {
        try {
            setMark(150);
        } catch (InvalidMarkException e) {
            System.out.println("Invalid mark: " + e.getMessage());
        }
    }

    static void setMark(int mark) throws InvalidMarkException {
        if (mark < 0 || mark > 100) {
            throw new InvalidMarkException("Mark must be between 0 and 100");
        }
        System.out.println("Mark is valid: " + mark);
    }
}
```

#### Exercise 3 (Hard): Robust file reading with fallback

**Task:** Try to read the first line of `config.txt`. If missing, use default config and continue.

✅ Solution:

```java
import java.io.*;

public class ConfigLoader {

    public static void main(String[] args) {
        String config = loadConfigOrDefault("config.txt", "DEFAULT_CONFIG");
        System.out.println("Using config: " + config);
    }

    static String loadConfigOrDefault(String path, String defaultValue) {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            String line = br.readLine();
            return (line == null || line.isBlank()) ? defaultValue : line;
        } catch (FileNotFoundException e) {
            System.out.println("Config file not found. Using default.");
            return defaultValue;
        } catch (IOException e) {
            System.out.println("Config file unreadable. Using default.");
            return defaultValue;
        }
    }
}
```

### Challenge problems

1. Write a program that reads integers from a file. If any token is not an integer, skip it and continue.
2. Create a `BankAccount` class where `withdraw(amount)` throws a custom checked exception if balance is insufficient.
3. Create a menu system that never crashes from invalid input.

---

## Further Study

* Java’s official docs: `Throwable`, `Exception`, `RuntimeException` (focus on hierarchy and constructors)
* `try-with-resources`, `AutoCloseable`, and *suppressed exceptions*
* Input validation patterns and defensive programming

---

*End of notes.*
