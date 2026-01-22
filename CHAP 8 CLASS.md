# Java Fundamentals: **Classes** (WIX1002 / FOP)

> Goal: After this, you can *design*, *write*, *read*, and *debug* Java classes confidently—without needing the original slides.

---

## Table of Contents

1. [Introduction & Motivation](#1-introduction--motivation)
2. [Fundamentals](#2-fundamentals)

   1. [Key Terms (defined before use)](#21-key-terms-defined-before-use)
   2. [How Java represents objects internally (mental model)](#22-how-java-represents-objects-internally-mental-model)
3. [Core Concepts](#3-core-concepts)

   1. [Defining a class](#31-defining-a-class)
   2. [Creating objects (`new`) and references](#32-creating-objects-new-and-references)
   3. [Fields / instance variables](#33-fields--instance-variables)
   4. [Constructors (default, parameterized, overloading)](#34-constructors-default-parameterized-overloading)
   5. [`this` keyword](#35-this-keyword)
   6. [Static members (`static`, `final`)](#36-static-members-static-final)
   7. [Methods: instance vs static](#37-methods-instance-vs-static)
   8. [Encapsulation (data hiding + invariants)](#38-encapsulation-data-hiding--invariants)
   9. [Method overloading (compile-time polymorphism)](#39-method-overloading-compile-time-polymorphism)
   10. [Wrapper classes (primitives ↔ objects)](#310-wrapper-classes-primitives--objects)
   11. [Packages & imports (organization + namespaces)](#311-packages--imports-organization--namespaces)
4. [Advanced Topics](#4-advanced-topics)

   1. [Copying objects: shallow vs deep copy](#41-copying-objects-shallow-vs-deep-copy)
   2. [`toString`, `equals`, `hashCode` (object identity vs equality)](#42-tostring-equals-hashcode-object-identity-vs-equality)
   3. [Immutability and defensive copying](#43-immutability-and-defensive-copying)
   4. [Modern Java note: `record` as a data carrier](#44-modern-java-note-record-as-a-data-carrier)
5. [Integration & Combinations](#5-integration--combinations)
6. [Best Practices & Patterns](#6-best-practices--patterns)
7. [Common Pitfalls (❌ wrong → ✅ right)](#7-common-pitfalls--wrong--right)
8. [Quick Reference & Cheat Sheets](#8-quick-reference--cheat-sheets)
9. [Practice (Questions + Coding Exercises + Solutions)](#9-practice-questions--coding-exercises--solutions)
10. [Source Material Critique](#10-source-material-critique)

---

# 1. Introduction & Motivation

## Why do classes exist?

When programs become bigger, **functions + primitive variables** stop being enough to keep things organized.

A **class** exists to solve these problems:

* **Grouping**: Keep related data + behavior together (e.g., `balance` + `deposit()`).
* **Safety**: Prevent invalid states (e.g., balance cannot be negative).
* **Reuse**: Create many similar objects from one blueprint.
* **Abstraction**: Let users interact with a clean interface, not your internal details.

✅ A good class gives you:

* **A clear contract** (what it can do)
* **A protected state** (how it stores data)
* **Predictable behavior** (validations + invariants)

### Real-world analogy

A **class** is a *recipe*; an **object** is a *cake baked from that recipe*. Multiple cakes can exist at once.

---

# 2. Fundamentals

## 2.1 Key Terms (defined before use)

| Term                          | Meaning                                     | Example                           |
| ----------------------------- | ------------------------------------------- | --------------------------------- |
| **Class**                     | Blueprint / type definition                 | `class Student { ... }`           |
| **Object / Instance**         | A concrete thing created from the class     | `new Student(...)`                |
| **Field / Instance variable** | Data stored inside each object              | `private int age;`                |
| **Method**                    | Function inside a class (behavior)          | `public void enroll()`            |
| **Constructor**               | Special method that initializes new objects | `public Student(...) { ... }`     |
| **Reference**                 | A “handle” pointing to an object            | `Student s = new Student(...);`   |
| **Encapsulation**             | Hiding internal data + controlling access   | `private fields + public methods` |
| **Static**                    | Belongs to the class itself, not one object | `static int count;`               |
| **Invariant**                 | Rule that must always be true               | `balance >= 0`                    |

## 2.2 How Java represents objects internally (mental model)

Java uses **references**.

* Variables of class types store a **reference**, not the actual object.
* The actual object lives in **heap memory**.

```
Stack (method call)         Heap (objects)
------------------         -------------------------
Student s  --------->       Student object
                            name="Aina"
                            age=20
```

✅ Important consequence:

* Assigning `s2 = s1;` does **not copy** the object. It copies the reference.

---

# 3. Core Concepts

## 3.1 Defining a class

### Core explanation

* **Definition**: A class is a template that defines fields + methods.
* **Purpose**: Create structured objects with behavior.
* **Mechanism**: JVM loads class metadata; `new` allocates an instance on heap.

### Minimal example (simplest)

```java
// File: HelloClass.java
public class HelloClass {
    private int value;

    public HelloClass(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    public static void main(String[] args) {
        HelloClass hc = new HelloClass(10);
        System.out.println(hc.getValue());
    }
}
```

### When to use

* Whenever you have data + operations that logically belong together.

### When NOT to use (anti-pattern)

* Making a class just to hold random unrelated methods (that’s usually a utility class or bad design).

---

## 3.2 Creating objects (`new`) and references

### Key idea

```java
Student s = new Student("Ali", "S001", 19, "CS");
```

* Left side: a **reference variable** `s`
* Right side: creates an **object** on heap

### Before/After: reference copy vs object copy

❌ Wrong assumption (thinks this creates a new object)

```java
Student a = new Student("Ali", "S001", 19, "CS");
Student b = a;           // b is NOT a separate student
b.setMajor("Math");      // changes the same object
```

✅ Correct (create a separate object)

```java
Student a = new Student("Ali", "S001", 19, "CS");
Student b = new Student(a); // copy constructor (you implement)
```

---

## 3.3 Fields / instance variables

### Core explanation

* **Definition**: Per-object data.
* **Purpose**: Store state.
* **Mechanism**: Each object has its own copy of fields.

### Best practice

* Prefer `private` fields.
* Expose behavior via methods.

### Minimal example

```java
public class Counter {
    private int count; // field

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

### Common pitfall

Local variables must be initialized; fields get default values.

```java
public class Demo {
    private int x; // default 0

    public void test() {
        int y; // NOT initialized
        // System.out.println(y); // compile error
    }
}
```

---

## 3.4 Constructors (default, parameterized, overloading)

### Core explanation

* **Definition**: Special method with same name as class, no return type.
* **Purpose**: Build objects in a valid initial state.
* **Mechanism**: Runs automatically right after memory allocation.

### Decision tree: which constructor style?

```
Do you need to enforce invariants at creation?
   ├─ Yes → Use parameterized constructor + validation
   └─ No  → Default constructor is ok (but still may set defaults)

Do you want multiple ways to create objects?
   ├─ Yes → Constructor overloading + constructor chaining
   └─ No  → Keep it simple
```

### Minimal example

```java
public class Person {
    private String name;
    private int age;

    public Person() {
        this("Unknown", 0); // constructor chaining
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### Realistic example (validation)

```java
// File: BankAccount.java
public class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Initial balance cannot be negative");
        }
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Deposit must be positive");
        }
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Withdraw must be positive");
        }
        if (amount > balance) {
            throw new IllegalStateException("Insufficient funds");
        }
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

### Advanced example (constructor overloading + chaining)

```java
// File: Student.java
import java.util.Objects;

public class Student {
    private final String name;
    private final String studentId;
    private final int age;
    private final String major;

    public Student(String name, String studentId, int age, String major) {
        this.name = Objects.requireNonNull(name, "name");
        this.studentId = Objects.requireNonNull(studentId, "studentId");
        if (age < 0) throw new IllegalArgumentException("age must be >= 0");
        this.age = age;
        this.major = Objects.requireNonNull(major, "major");
    }

    // Convenience constructor
    public Student(String name, String studentId) {
        this(name, studentId, 0, "Undeclared");
    }

    // Copy constructor
    public Student(Student other) {
        this(other.name, other.studentId, other.age, other.major);
    }

    public String getStudentId() {
        return studentId;
    }

    @Override
    public String toString() {
        return String.format("Student{name='%s', id='%s', age=%d, major='%s'}", name, studentId, age, major);
    }
}
```

### Edge cases + error handling

* Null values → `Objects.requireNonNull`
* Invalid ranges → `IllegalArgumentException`
* Business rules violated → `IllegalStateException`

---

## 3.5 `this` keyword

### What is `this`?

* A reference to the **current object**.

### Why it exists

* Disambiguate: field vs parameter
* Enable chaining: `return this;`
* Constructor chaining: `this(...)`

### Minimal example (disambiguation)

```java
public class Car {
    private String make;

    public Car(String make) {
        this.make = make; // field = parameter
    }
}
```

### Realistic example (method chaining)

```java
// File: FluentCar.java
public class FluentCar {
    private String make;
    private String model;
    private String color;

    public FluentCar(String make, String model) {
        this.make = make;
        this.model = model;
        this.color = "Unknown";
    }

    public FluentCar setColor(String color) {
        this.color = color;
        return this;
    }

    public void display() {
        System.out.println(make + " " + model + " (" + color + ")");
    }

    public static void main(String[] args) {
        FluentCar c = new FluentCar("Toyota", "Corolla")
                .setColor("Red");
        c.display();
    }
}
```

### Advanced example (`this(...)` constructor chaining)

```java
public class Rectangle {
    private final int width;
    private final int height;

    public Rectangle(int size) {
        this(size, size);
    }

    public Rectangle(int width, int height) {
        if (width <= 0 || height <= 0) {
            throw new IllegalArgumentException("width and height must be positive");
        }
        this.width = width;
        this.height = height;
    }
}
```

⚠️ Rule: `this(...)` must be the **first line** in a constructor.

---

## 3.6 Static members (`static`, `final`)

### Core explanation

* **static field**: one copy shared by all objects.
* **static method**: belongs to the class; can be called without an object.
* **final**: cannot change after initialization.

### Comparison table: instance vs static

| Feature         | Instance       | Static                          |
| --------------- | -------------- | ------------------------------- |
| Belongs to      | Each object    | The class                       |
| Memory          | Per object     | One shared copy                 |
| Access          | Needs object   | `ClassName.member`              |
| Can use `this`? | Yes            | No                              |
| Example         | `student.name` | `StudentRegistry.totalStudents` |

### Minimal example

```java
public class StudentRegistry {
    private static int totalStudents = 0;

    public StudentRegistry() {
        totalStudents++;
    }

    public static int getTotalStudents() {
        return totalStudents;
    }
}
```

### Realistic example (constants)

```java
public class MathConstants {
    public static final double PI_APPROX = 3.141592653589793;
}
```

### Advanced example (static factory method)

```java
import java.util.Objects;

public class Email {
    private final String value;

    private Email(String value) {
        this.value = value;
    }

    public static Email of(String value) {
        Objects.requireNonNull(value, "email");
        if (!value.contains("@")) {
            throw new IllegalArgumentException("Invalid email: " + value);
        }
        return new Email(value);
    }

    @Override
    public String toString() {
        return value;
    }
}
```

✅ Why factories are useful:

* Can validate
* Can return cached objects
* Can have meaningful names

---

## 3.7 Methods: instance vs static

### Mechanism

* Instance methods receive an implicit `this` reference.
* Static methods do not.

❌ Wrong

```java
public class A {
    private int x = 10;

    public static void show() {
        // System.out.println(x); // compile error (no instance)
    }
}
```

✅ Correct

```java
public class A {
    private int x = 10;

    public void show() {
        System.out.println(x);
    }

    public static void main(String[] args) {
        new A().show();
    }
}
```

---

## 3.8 Encapsulation (data hiding + invariants)

### Definition

Encapsulation = **hide fields** + **provide controlled access** via methods.

### Why it exists

* Prevent invalid states
* Centralize validation
* Improve maintainability
* Reduce security risks (no direct access to sensitive data)

### Minimal example

```java
public class Temperature {
    private double celsius;

    public Temperature(double celsius) {
        setCelsius(celsius);
    }

    public double getCelsius() {
        return celsius;
    }

    public void setCelsius(double celsius) {
        if (celsius < -273.15) {
            throw new IllegalArgumentException("Below absolute zero");
        }
        this.celsius = celsius;
    }
}
```

### Realistic example (BankAccount)

See [BankAccount](#34-constructors-default-parameterized-overloading) example.

### Advanced example (read-only view)

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Gradebook {
    private final List<Double> grades = new ArrayList<>();

    public void addGrade(double g) {
        if (g < 0 || g > 100) throw new IllegalArgumentException("grade must be 0..100");
        grades.add(g);
    }

    // Defensive: returns an unmodifiable view
    public List<Double> getGrades() {
        return Collections.unmodifiableList(grades);
    }
}
```

---

## 3.9 Method overloading (compile-time polymorphism)

### Definition

Same method name, different parameter list.

### Why it exists

* Convenience: different inputs, same concept
* Clean API: fewer method names to memorize

### Rules

* Overloading is decided at **compile time**.
* Changing only the return type is **NOT** overloading.

### Minimal example

```java
public class Printer {
    public void print(int x) {
        System.out.println("int: " + x);
    }

    public void print(String s) {
        System.out.println("String: " + s);
    }
}
```

### Realistic example (constructor overloading)

See [Student](#34-constructors-default-parameterized-overloading).

### Advanced example (overload resolution pitfalls)

```java
public class OverloadTrap {
    public void f(int x) {
        System.out.println("int");
    }

    public void f(Integer x) {
        System.out.println("Integer");
    }

    public static void main(String[] args) {
        OverloadTrap t = new OverloadTrap();
        t.f(10);            // picks int
        t.f(Integer.valueOf(10)); // picks Integer
        // t.f(null);       // ambiguous? actually chooses Integer overload (int can't be null)
    }
}
```

---

## 3.10 Wrapper classes (primitives ↔ objects)

### Why wrappers exist

Java collections (like `ArrayList`) store **objects**, not primitives.
Wrappers allow primitives to be treated as objects.

### Common wrappers

| Primitive | Wrapper     |
| --------- | ----------- |
| `int`     | `Integer`   |
| `double`  | `Double`    |
| `char`    | `Character` |
| `boolean` | `Boolean`   |

### Modern approach: use autoboxing

✅ Good

```java
Integer a = 50;  // autoboxing
int b = a;       // unboxing
```

⚠️ Outdated style (avoid in modern Java)

```java
Integer x = new Integer(100); // avoid
```

### Parsing strings

```java
int n = Integer.parseInt("123");
double d = Double.parseDouble("3.14");
String s = Integer.toString(99);
```

### Edge case

```java
Integer maybe = null;
// int x = maybe; // NullPointerException during unboxing
```

---

## 3.11 Packages & imports (organization + namespaces)

### Why packages exist

* Prevent class name collisions (`com.app.Student` vs `edu.uni.Student`)
* Organize code for large projects

### File structure rule (very important)

If you write:

```java
package com.example.school;
```

Then the file must be in:

```
.../com/example/school/FileName.java
```

### Minimal example

```java
// File: src/com/example/app/Main.java
package com.example.app;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello package!");
    }
}
```

### Compile + run (terminal)

```bash
# from project root
javac -d out src/com/example/app/Main.java
java -cp out com.example.app.Main
```

### Imports

* `import` does **not** import subpackages.

✅

```java
import java.util.ArrayList;
```

✅ wildcard (ok, but use sparingly)

```java
import java.util.*;
```

❌ default package (avoid)

* Classes in the default package cannot be imported nicely; in real projects, always use packages.

---

# 4. Advanced Topics

## 4.1 Copying objects: shallow vs deep copy

### Shallow copy

Copies references.

### Deep copy

Copies referenced objects too.

Example:

```java
import java.util.ArrayList;
import java.util.List;

public class Team {
    private final List<String> members;

    public Team(List<String> members) {
        // defensive copy
        this.members = new ArrayList<>(members);
    }

    public Team(Team other) {
        this(other.members);
    }

    public List<String> getMembers() {
        return List.copyOf(members); // unmodifiable copy
    }
}
```

---

## 4.2 `toString`, `equals`, `hashCode` (object identity vs equality)

### Identity vs equality

* `==` checks if two references point to the same object.
* `.equals(...)` checks logical equality (if you implement it).

### Best practice: override all three together

```java
import java.util.Objects;

public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return "(" + x + "," + y + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Point)) return false;
        Point p = (Point) o;
        return x == p.x && y == p.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}
```

---

## 4.3 Immutability and defensive copying

### Why immutability helps

* Fewer bugs (no surprise changes)
* Safer in multithreading (later topic)

Rules:

* fields `private final`
* no setters
* validate in constructor
* defensive copy for mutable inputs

---

## 4.4 Modern Java note: `record` as a data carrier

If you’re on modern Java:

```java
public record StudentRecord(String name, String id, int age, String major) {}
```

Why it matters:

* auto-generates constructor, getters, `toString`, `equals`, `hashCode`

(But for WIX1002 exams, you may still be expected to write the full class.)

---

# 5. Integration & Combinations

## Mini-project: Class roster + file save/load (classes working together)

This uses:

* a `Student` class
* a `Roster` class managing a list
* a `Main` tester

### 5.1 Student

```java
// File: Student.java
import java.util.Objects;

public class Student {
    private final String id;
    private String name;
    private double grade; // 0..100

    public Student(String id, String name, double grade) {
        this.id = Objects.requireNonNull(id, "id");
        setName(name);
        setGrade(grade);
    }

    public Student(Student other) {
        this(other.id, other.name, other.grade);
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("name cannot be blank");
        }
        this.name = name;
    }

    public double getGrade() {
        return grade;
    }

    public void setGrade(double grade) {
        if (grade < 0 || grade > 100) {
            throw new IllegalArgumentException("grade must be 0..100");
        }
        this.grade = grade;
    }

    public String toCsv() {
        return id + "," + name.replace(",", " ") + "," + grade;
    }

    public static Student fromCsv(String line) {
        String[] parts = line.split(",");
        if (parts.length != 3) {
            throw new IllegalArgumentException("Bad CSV line: " + line);
        }
        String id = parts[0].trim();
        String name = parts[1].trim();
        double grade = Double.parseDouble(parts[2].trim());
        return new Student(id, name, grade);
    }

    @Override
    public String toString() {
        return String.format("%s (%s) grade=%.2f", name, id, grade);
    }
}
```

### 5.2 Roster

```java
// File: Roster.java
import java.io.*;
import java.util.*;

public class Roster {
    private final List<Student> students = new ArrayList<>();

    public void add(Student s) {
        Objects.requireNonNull(s, "student");
        if (findById(s.getId()) != null) {
            throw new IllegalArgumentException("Duplicate id: " + s.getId());
        }
        students.add(s);
    }

    public Student findById(String id) {
        for (Student s : students) {
            if (s.getId().equals(id)) return s;
        }
        return null;
    }

    public double average() {
        if (students.isEmpty()) return 0.0;
        double sum = 0;
        for (Student s : students) sum += s.getGrade();
        return sum / students.size();
    }

    public List<Student> topN(int n) {
        if (n < 0) throw new IllegalArgumentException("n must be >= 0");
        List<Student> copy = new ArrayList<>();
        for (Student s : students) copy.add(new Student(s)); // copy to protect encapsulation
        copy.sort((a, b) -> Double.compare(b.getGrade(), a.getGrade()));
        if (n > copy.size()) n = copy.size();
        return copy.subList(0, n);
    }

    public void saveToFile(String filename) throws IOException {
        try (PrintWriter out = new PrintWriter(new FileOutputStream(filename))) {
            for (Student s : students) {
                out.println(s.toCsv());
            }
        }
    }

    public void loadFromFile(String filename) throws IOException {
        students.clear();
        File f = new File(filename);
        if (!f.exists()) return;

        try (Scanner sc = new Scanner(f)) {
            while (sc.hasNextLine()) {
                String line = sc.nextLine().trim();
                if (line.isEmpty()) continue;
                add(Student.fromCsv(line));
            }
        }
    }

    public void printAll() {
        for (Student s : students) {
            System.out.println(s);
        }
    }
}
```

### 5.3 Main

```java
// File: Main.java
public class Main {
    public static void main(String[] args) {
        try {
            Roster r = new Roster();
            r.add(new Student("S001", "Aina", 88.5));
            r.add(new Student("S002", "Farid", 72.0));
            r.add(new Student("S003", "Mei", 93.2));

            System.out.println("=== All Students ===");
            r.printAll();

            System.out.printf("Average: %.2f%n", r.average());

            System.out.println("\nTop 2:");
            for (Student s : r.topN(2)) {
                System.out.println(s);
            }

            String file = "roster.csv";
            r.saveToFile(file);

            Roster r2 = new Roster();
            r2.loadFromFile(file);
            System.out.println("\n=== Loaded from file ===");
            r2.printAll();

        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

---

# 6. Best Practices & Patterns

✅ **Design rules you can use immediately**

1. **Keep fields private** (encapsulation).
2. **Validate inputs** at boundaries (constructors + setters).
3. **Keep invariants true** always (e.g., balance never negative).
4. Prefer **constructor chaining** to avoid duplication.
5. Avoid global state: be careful with `static` mutable fields.
6. Override `toString()` for debugging.
7. If you override `equals()`, also override `hashCode()`.
8. Use meaningful names + follow Java conventions:

   * Class: `UpperCamelCase`
   * Method/variable: `lowerCamelCase`
   * Constant: `UPPER_SNAKE_CASE`

---

# 7. Common Pitfalls (❌ wrong → ✅ right)

## 7.1 Using `System.exit(0)` for “error handling”

❌ Wrong

```java
public Person(Person p) {
    if (p == null) System.exit(0);
}
```

✅ Better

```java
public Person(Person p) {
    if (p == null) throw new IllegalArgumentException("p cannot be null");
}
```

## 7.2 Getter/Setter syntax mistakes

❌ Wrong

```java
public String getName() }
    return name;
}
```

✅ Correct

```java
public String getName() {
    return name;
}
```

## 7.3 Confusing `==` and `.equals()`

❌ Wrong

```java
String a = new String("hi");
String b = new String("hi");
System.out.println(a == b); // false
```

✅ Correct

```java
System.out.println(a.equals(b)); // true
```

## 7.4 Forgetting that reference assignment doesn’t copy

❌ Wrong

```java
Student b = a;
```

✅ Correct

```java
Student b = new Student(a);
```

## 7.5 Unboxing null wrappers

❌ Wrong

```java
Integer x = null;
int y = x; // NullPointerException
```

✅ Avoid

```java
Integer x = null;
int y = (x == null) ? 0 : x;
```

---

# 8. Quick Reference & Cheat Sheets

## 8.1 Class skeleton

```java
public class ClassName {
    // 1) fields
    private int x;

    // 2) constructors
    public ClassName() {
        this(0);
    }

    public ClassName(int x) {
        this.x = x;
    }

    // 3) methods
    public int getX() {
        return x;
    }

    public void setX(int x) {
        this.x = x;
    }
}
```

## 8.2 Access modifiers table

| Modifier    | Same class | Same package | Subclass (diff package) | Other package |
| ----------- | ---------: | -----------: | ----------------------: | ------------: |
| `private`   |          ✅ |            ❌ |                       ❌ |             ❌ |
| *(default)* |          ✅ |            ✅ |                       ❌ |             ❌ |
| `protected` |          ✅ |            ✅ |                       ✅ |             ❌ |
| `public`    |          ✅ |            ✅ |                       ✅ |             ✅ |

## 8.3 `static` quick rules

* Static method: no `this`
* Static method: cannot directly access instance fields
* `static final`: constant

## 8.4 Wrapper quick list

* Parse: `Integer.parseInt`, `Double.parseDouble`
* Convert to string: `String.valueOf(x)` or `Integer.toString(x)`
* Constants: `Integer.MAX_VALUE`, `Integer.MIN_VALUE`

---

# 9. Practice (Questions + Coding Exercises + Solutions)

## 9.1 Conceptual questions

1. Explain the difference between a class and an object.
2. Why does Java use references for objects?
3. What is an invariant? Give an example.
4. Why is `private` usually preferred for fields?
5. What is constructor chaining and why is it useful?
6. Explain the difference between `==` and `.equals()`.
7. What is the difference between a static field and an instance field?
8. Why do wrapper classes exist?
9. Why should you avoid the default package?

## 9.2 Coding exercises (easy → hard)

### Exercise A (Easy): `Book`

Create a `Book` class with `title` and `pages`. Enforce `pages > 0`. Print a book.

✅ Solution

```java
public class Book {
    private String title;
    private int pages;

    public Book(String title, int pages) {
        setTitle(title);
        setPages(pages);
    }

    public String getTitle() { return title; }
    public int getPages() { return pages; }

    public void setTitle(String title) {
        if (title == null || title.isBlank()) throw new IllegalArgumentException("title");
        this.title = title;
    }

    public void setPages(int pages) {
        if (pages <= 0) throw new IllegalArgumentException("pages must be > 0");
        this.pages = pages;
    }

    @Override
    public String toString() {
        return "Book{" + title + ", pages=" + pages + "}";
    }

    public static void main(String[] args) {
        System.out.println(new Book("Clean Code", 464));
    }
}
```

### Exercise B (Medium): Static counter

Create a class `Ticket` that auto-increments ticket numbers.

✅ Solution

```java
public class Ticket {
    private static int next = 1;
    private final int number;

    public Ticket() {
        this.number = next++;
    }

    public int getNumber() {
        return number;
    }

    public static void main(String[] args) {
        System.out.println(new Ticket().getNumber());
        System.out.println(new Ticket().getNumber());
    }
}
```

### Exercise C (Hard): Deep copy

Build a `Playlist` class that stores a list of song titles. Copy constructor must be deep copy.

✅ Solution

```java
import java.util.*;

public class Playlist {
    private final List<String> songs;

    public Playlist(List<String> songs) {
        this.songs = new ArrayList<>(songs);
    }

    public Playlist(Playlist other) {
        this(other.songs);
    }

    public void add(String s) {
        songs.add(s);
    }

    public List<String> getSongs() {
        return Collections.unmodifiableList(songs);
    }

    public static void main(String[] args) {
        Playlist p1 = new Playlist(List.of("A", "B"));
        Playlist p2 = new Playlist(p1);
        p2.add("C");

        System.out.println(p1.getSongs()); // [A, B]
        System.out.println(p2.getSongs()); // [A, B, C]
    }
}
```

### Challenge (Very hard): `equals`/`hashCode`

Implement a `StudentKey` class where equality depends only on `id`.

✅ Reference solution

```java
import java.util.Objects;

public class StudentKey {
    private final String id;

    public StudentKey(String id) {
        this.id = Objects.requireNonNull(id);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof StudentKey)) return false;
        StudentKey other = (StudentKey) o;
        return id.equals(other.id);
    }

    @Override
    public int hashCode() {
        return id.hashCode();
    }

    @Override
    public String toString() {
        return id;
    }
}
```

---

# 10. Source Material Critique

## What the slides did well

* Covered the **topic list**: constructors, fields, static, encapsulation, overloading, wrapper, packages.

## Gaps / unclear points (and how these notes fix them)

* Slides define concepts but don’t explain **internal mental model** (heap vs reference).
* Copy constructor example uses `System.exit(0)` (bad practice); notes replace with exceptions.
* Getter syntax shown contains a bracket/brace error; notes provide fully runnable code.
* Wrapper class example uses `new Integer(100)` (outdated); notes explain autoboxing.
* Package section relies heavily on CLASSPATH; notes show modern compile/run structure.
* Missing: object copying types, equality vs identity, defensive copying, immutability.

## Suggestions for further study

* Inheritance + polymorphism (runtime overriding)
* Interfaces + abstract classes
* Collections (ArrayList/HashMap) + generics
* Exceptions & custom exceptions
* Testing (JUnit) and build tools (Maven/Gradle)
