# Java Fundamentals: Inheritance (Master Notes)

> **Goal:** Make you *independent* of the original slides—by building the “why”, “how”, and “when” of inheritance, with runnable examples, diagrams, pitfalls, best practices, and practice sets.

---

## Table of Contents

1. [Introduction & Motivation](#1-introduction--motivation)
2. [Prerequisites & Key Terms](#2-prerequisites--key-terms)
3. [Fundamentals](#3-fundamentals)

   1. [What Inheritance Is (and Is Not)](#31-what-inheritance-is-and-is-not)
   2. [The `extends` Relationship](#32-the-extends-relationship)
   3. [Type System: “is-a” and Upcasting](#33-type-system-is-a-and-upcasting)
4. [Core Concepts](#4-core-concepts)

   1. [Constructors, `super()`, and Initialization Order](#41-constructors-super-and-initialization-order)
   2. [Access Control in Inheritance (`private`, `protected`, package-private, `public`)](#42-access-control-in-inheritance-private-protected-package-private-public)
   3. [Method Overriding](#43-method-overriding)
   4. [`super` vs `this`](#44-super-vs-this)
   5. [The `Object` Superclass](#45-the-object-superclass)
   6. [`instanceof`, Casting, and Runtime Types](#46-instanceof-casting-and-runtime-types)
5. [Advanced Topics](#5-advanced-topics)

   1. [Runtime Polymorphism & Dynamic Dispatch](#51-runtime-polymorphism--dynamic-dispatch)
   2. [Abstract Classes](#52-abstract-classes)
   3. [Interfaces and “Multiple Inheritance of Type”](#53-interfaces-and-multiple-inheritance-of-type)
   4. [`final`, Method Hiding, and Common Traps](#54-final-method-hiding-and-common-traps)
   5. [Modern Java: `sealed` Classes, Pattern Matching `instanceof`](#55-modern-java-sealed-classes-pattern-matching-instanceof)
6. [Integration & Combinations](#6-integration--combinations)
7. [Best Practices & Patterns](#7-best-practices--patterns)
8. [Common Mistakes (❌ → ✅)](#8-common-mistakes--)
9. [Quick Reference & Cheat Sheets](#9-quick-reference--cheat-sheets)
10. [Practice & Application (with Solutions)](#10-practice--application-with-solutions)
11. [Meta-Commentary: Critique of the Source Material](#11-meta-commentary-critique-of-the-source-material)

---

## 1. Introduction & Motivation

### Why does inheritance exist?

Inheritance is Java’s way to model **shared behavior + specialization**.

* **Problem it solves:** You don’t want to copy/paste the same fields and methods into many classes.
* **What you gain:**

  * ✅ **Code reuse** (shared methods/fields live in one place)
  * ✅ **Polymorphism** (treat different subclasses as a single base type)
  * ✅ **Extensibility** (add new types without rewriting old code)

### Real-world analogy

Think of **Employee** as a general template:

* All employees have `name`, `id`, and can `getPay()`.
* But **HourlyEmployee** and **SalariedEmployee** compute pay differently.

Inheritance lets you say:

> “Every HourlyEmployee is an Employee (is-a relationship), but with extra details.”

---

## 2. Prerequisites & Key Terms

### Prerequisites you must know

* Classes, objects, fields (instance variables), methods
* Constructors
* `static` vs instance members
* Access modifiers: `public`, `private`, etc.
* Basic packages and imports

### Terms (define before using)

| Term                           | Meaning                                                                                |
| ------------------------------ | -------------------------------------------------------------------------------------- |
| **Superclass / Base / Parent** | The class being inherited from                                                         |
| **Subclass / Derived / Child** | The class that inherits                                                                |
| **Inheritance**                | Creating a new class based on an existing class using `extends`                        |
| **Override**                   | Subclass supplies a new implementation of an inherited method                          |
| **Overload**                   | Same method name, different parameters (NOT inheritance-specific)                      |
| **Upcasting**                  | Treating a subclass object as a superclass reference                                   |
| **Downcasting**                | Casting a superclass reference to a subclass type (unsafe unless it’s truly that type) |
| **Polymorphism**               | “Many forms”: same method call, different runtime behavior                             |

---

## 3. Fundamentals

### 3.1 What Inheritance Is (and Is Not)

✅ **Inheritance IS:**

* A way to *extend* a class by adding new members or overriding behavior.
* A way to express **substitutability**: if `B extends A`, a `B` should be usable wherever an `A` is expected.

❌ **Inheritance is NOT:**

* A way to “reuse code” when the relationship isn’t truly *is-a*.
* A way to break encapsulation by using `protected` everywhere.

> **Rule of thumb:** If you can’t honestly say “B is a kind of A”, don’t use inheritance—use **composition**.

---

### 3.2 The `extends` Relationship

#### Syntax

```java
class SubClass extends SuperClass {
    // new fields
    // new methods
    // overridden methods
}
```

#### How Java supports inheritance

* Java supports **single inheritance** for classes: a class can extend **only one** superclass.
* But a class can implement **multiple interfaces**.

---

### 3.3 Type System: “is-a” and Upcasting

If `HourlyEmployee extends Employee`:

* `HourlyEmployee` **is-a** `Employee`
* A variable of type `Employee` can refer to an `HourlyEmployee` object.

```java
Employee e = new HourlyEmployee(); // upcasting
```

✅ Why it matters:

* You can write code that works for *any kind of Employee*.

---

## 4. Core Concepts

## 4.1 Constructors, `super()`, and Initialization Order

### Why do constructors matter in inheritance?

When creating a subclass object, Java **must** build the superclass part first.

### Key rule

Every constructor starts by calling a superclass constructor:

* If you **don’t** call `super(...)` explicitly, Java inserts `super();` automatically.
* But that only works if the superclass has an accessible **no-arg constructor**.

### Initialization order (important!)

When you run `new SubClass(...)`, Java does:

1. **Memory allocated** for the whole object
2. **Superclass fields initialized**
3. **Superclass constructor runs**
4. **Subclass fields initialized**
5. **Subclass constructor runs**

> ⚠️ Avoid calling overridable methods from constructors—because subclass state may not be ready yet.

---

### Minimal example (constructor chaining)

```java
class A {
    A() {
        System.out.println("A constructor");
    }
}

class B extends A {
    B() {
        super(); // optional here (compiler inserts)
        System.out.println("B constructor");
    }
}

public class Main {
    public static void main(String[] args) {
        new B();
    }
}
```

**Output**

```
A constructor
B constructor
```

---

### Realistic example (superclass has no no-arg constructor)

```java
class Person {
    private final String name;

    Person(String name) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("name must not be blank");
        }
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

class Student extends Person {
    private final String matricNo;

    Student(String name, String matricNo) {
        super(name); // REQUIRED: Person has no Person()
        if (matricNo == null || matricNo.isBlank()) {
            throw new IllegalArgumentException("matricNo must not be blank");
        }
        this.matricNo = matricNo;
    }

    public String getMatricNo() {
        return matricNo;
    }
}

public class Main {
    public static void main(String[] args) {
        Student s = new Student("Jon", "A12345");
        System.out.println(s.getName() + " " + s.getMatricNo());
    }
}
```

---

### Advanced example (constructor + avoiding overridable calls)

❌ **Wrong:** superclass constructor calls overridable method

```java
class Base {
    Base() {
        init(); // calls overridden method BEFORE subclass constructor runs
    }

    void init() {
        System.out.println("Base init");
    }
}

class Derived extends Base {
    private String msg;

    Derived() {
        msg = "ready";
    }

    @Override
    void init() {
        System.out.println("Derived init: msg=" + msg); // msg is still null here!
    }
}

public class Main {
    public static void main(String[] args) {
        new Derived();
    }
}
```

✅ **Right:** use private/final initialization helpers or factory methods

```java
class Base {
    Base() {
        // do not call overridable methods here
    }

    final void baseInit() {
        // safe: final means it can't be overridden
        System.out.println("Base safe init");
    }
}

class Derived extends Base {
    private final String msg;

    Derived() {
        this.msg = "ready";
        baseInit();
        initAfterConstruction();
    }

    private void initAfterConstruction() {
        System.out.println("Derived init: msg=" + msg);
    }
}

public class Main {
    public static void main(String[] args) {
        new Derived();
    }
}
```

---

## 4.2 Access Control in Inheritance (`private`, `protected`, package-private, `public`)

### Why does access control exist?

To protect class invariants and prevent external code from breaking internal rules.

### The 4 access levels (memorize this)

| Modifier            | Same class | Same package | Subclass (diff package) | Anywhere |
| ------------------- | ---------: | -----------: | ----------------------: | -------: |
| `private`           |          ✅ |            ❌ |                       ❌ |        ❌ |
| *(package-private)* |          ✅ |            ✅ |                       ❌ |        ❌ |
| `protected`         |          ✅ |            ✅ |                       ✅ |        ❌ |
| `public`            |          ✅ |            ✅ |                       ✅ |        ✅ |

> **Package-private** = *no modifier*.

### Key inheritance truth

* A subclass **does not** directly access superclass `private` fields.
* But it can access them via **public/protected methods** (getters/setters), if provided.

---

### Minimal example (private is not accessible)

```java
class Account {
    private double balance;

    Account(double balance) {
        this.balance = balance;
    }

    protected void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("amount must be > 0");
        balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}

class SavingsAccount extends Account {
    SavingsAccount(double balance) {
        super(balance);
    }

    public void addInterest(double rate) {
        // balance += ...  // ❌ can't access private field
        double interest = getBalance() * rate;
        deposit(interest); // ✅ uses protected method
    }
}

public class Main {
    public static void main(String[] args) {
        SavingsAccount sa = new SavingsAccount(1000);
        sa.addInterest(0.05);
        System.out.println(sa.getBalance());
    }
}
```

---

### Edge case: `protected` is not “public for subclasses”

In a different package, you can access a `protected` member **only through inheritance**, not via “random object reference.”

> If your course doesn’t cover packages deeply, remember this simplified safe rule:
> **Use `protected` sparingly; prefer `private` + protected/public methods.**

---

## 4.3 Method Overriding

### Why does overriding exist?

So subclasses can customize behavior while still being usable as the base type.

### Overriding checklist (must be true)

✅ Same **method name**
✅ Same **parameter list**
✅ Return type is:

* the same, or
* **covariant** (subclass return type)
  ✅ Access level becomes **same or less restrictive** (never more restrictive)
  ✅ If the superclass method throws checked exceptions, override can throw:
* the same, or
* **fewer / narrower** checked exceptions

### What cannot be overridden?

* `final` methods
* `static` methods (they are **hidden**, not overridden)
* `private` methods (not inherited; a “same-signature” method is a new method)

> ✅ Always use `@Override` to let the compiler catch mistakes.

---

### Minimal example (overriding)

```java
class Animal {
    public void sound() {
        System.out.println("Some generic animal sound");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("Bark!");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal a = new Dog(); // upcasting
        a.sound();            // Bark! (runtime dispatch)
    }
}
```

---

### Realistic example (Employee pay calculation)

```java
abstract class Employee {
    private final String name;

    protected Employee(String name) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("name must not be blank");
        }
        this.name = name;
    }

    public String getName() {
        return name;
    }

    // Subclasses must implement
    public abstract double getWeeklyPay();
}

class HourlyEmployee extends Employee {
    private final double hourlyRate;
    private final int hours;

    public HourlyEmployee(String name, double hourlyRate, int hours) {
        super(name);
        if (hourlyRate < 0) throw new IllegalArgumentException("hourlyRate must be >= 0");
        if (hours < 0) throw new IllegalArgumentException("hours must be >= 0");
        this.hourlyRate = hourlyRate;
        this.hours = hours;
    }

    @Override
    public double getWeeklyPay() {
        int overtimeHours = Math.max(0, hours - 40);
        int normalHours = hours - overtimeHours;
        return normalHours * hourlyRate + overtimeHours * hourlyRate * 1.5;
    }
}

class SalariedEmployee extends Employee {
    private final double annualSalary;

    public SalariedEmployee(String name, double annualSalary) {
        super(name);
        if (annualSalary < 0) throw new IllegalArgumentException("annualSalary must be >= 0");
        this.annualSalary = annualSalary;
    }

    @Override
    public double getWeeklyPay() {
        return annualSalary / 52.0;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee[] staff = {
            new HourlyEmployee("Aina", 20.0, 45),
            new SalariedEmployee("Ben", 52000.0)
        };

        for (Employee e : staff) {
            System.out.printf("%s weekly pay: %.2f%n", e.getName(), e.getWeeklyPay());
        }
    }
}
```

---

### Advanced example (covariant return types)

```java
class EmployeeRecord {
    public EmployeeRecord copy() {
        return new EmployeeRecord();
    }
}

class HourlyEmployeeRecord extends EmployeeRecord {
    @Override
    public HourlyEmployeeRecord copy() {
        return new HourlyEmployeeRecord();
    }
}

public class Main {
    public static void main(String[] args) {
        EmployeeRecord r = new HourlyEmployeeRecord();
        EmployeeRecord copy = r.copy();
        System.out.println(copy.getClass().getSimpleName());
    }
}
```

---

## 4.4 `super` vs `this`

### Why do we need `super`?

* To access superclass constructors and methods that are hidden/overridden.

| Keyword | Refers to                         | Common uses                                                                       |
| ------- | --------------------------------- | --------------------------------------------------------------------------------- |
| `this`  | current object                    | call another constructor `this(...)`, disambiguate fields, call current methods   |
| `super` | superclass part of current object | call superclass constructor `super(...)`, call overridden method `super.method()` |

### Example: call superclass method from an override

```java
class Logger {
    public void log(String msg) {
        System.out.println("[LOG] " + msg);
    }
}

class TimestampLogger extends Logger {
    @Override
    public void log(String msg) {
        super.log("2026-01-22 " + msg);
    }
}

public class Main {
    public static void main(String[] args) {
        Logger l = new TimestampLogger();
        l.log("Hello");
    }
}
```

---

## 4.5 The `Object` Superclass

### Why does `Object` exist?

Java needs a universal base type so every object shares a core set of behaviors.

### Key `Object` methods you should know

| Method             | Why it matters                                 | Common override?    |
| ------------------ | ---------------------------------------------- | ------------------- |
| `toString()`       | readable representation for printing/debugging | ✅ Yes               |
| `equals(Object o)` | logical equality                               | ✅ Yes               |
| `hashCode()`       | required for hash-based collections            | ✅ Yes (with equals) |
| `getClass()`       | runtime class info                             | ❌ No (it’s final)   |
| `clone()`          | copying (legacy; tricky)                       | ⚠️ Rare             |
| `finalize()`       | cleanup (deprecated/removed direction)         | ❌ Don’t use         |

### Minimal example: `getClass()` and `toString()`

```java
class Point {
    private final int x;
    private final int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return "Point(" + x + "," + y + ")";
    }
}

public class Main {
    public static void main(String[] args) {
        Object p = new Point(2, 3);
        System.out.println(p); // calls toString()
        System.out.println(p.getClass().getName());
    }
}
```

### Realistic: correct `equals`/`hashCode`

```java
import java.util.Objects;

class Student {
    private final String matricNo;
    private final String name;

    Student(String matricNo, String name) {
        if (matricNo == null || matricNo.isBlank()) throw new IllegalArgumentException("matricNo required");
        if (name == null || name.isBlank()) throw new IllegalArgumentException("name required");
        this.matricNo = matricNo;
        this.name = name;
    }

    public String getMatricNo() { return matricNo; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Student)) return false;
        Student other = (Student) o;
        return matricNo.equals(other.matricNo);
    }

    @Override
    public int hashCode() {
        return Objects.hash(matricNo);
    }

    @Override
    public String toString() {
        return "Student{" + matricNo + ", " + name + "}";
    }
}

public class Main {
    public static void main(String[] args) {
        Student a = new Student("A1", "Ali");
        Student b = new Student("A1", "Ali (duplicate)");
        System.out.println(a.equals(b));
        System.out.println(a.hashCode() == b.hashCode());
    }
}
```

---

## 4.6 `instanceof`, Casting, and Runtime Types

### Why do we need `instanceof`?

Because a reference type can be a superclass, but the runtime object might be a subclass.

### Rules

* `obj instanceof Type` is `true` if `obj` is an instance of `Type` or its subclasses.
* `null instanceof Type` is always `false`.

### Minimal safe downcast

```java
class Animal { }
class Dog extends Animal {
    void fetch() { System.out.println("Fetching..."); }
}

public class Main {
    public static void main(String[] args) {
        Animal a = new Dog();

        if (a instanceof Dog) {
            Dog d = (Dog) a; // safe
            d.fetch();
        }
    }
}
```

### Edge case: unsafe cast throws `ClassCastException`

```java
class Animal { }
class Dog extends Animal { }
class Cat extends Animal { }

public class Main {
    public static void main(String[] args) {
        Animal a = new Cat();

        // Dog d = (Dog) a; // ❌ compiles, but crashes at runtime

        if (a instanceof Dog) {
            Dog d = (Dog) a;
        } else {
            System.out.println("Not a Dog; avoid casting.");
        }
    }
}
```

---

## 5. Advanced Topics

## 5.1 Runtime Polymorphism & Dynamic Dispatch

### What problem does it solve?

You want **one piece of code** to work with many related types.

### Mechanism (how it works internally)

* Variables have a **reference type** (compile-time)
* Objects have a **runtime type**
* For **overridden instance methods**, Java chooses the method implementation based on the **runtime type**.

> ✅ This is why `Employee e = new HourlyEmployee(...); e.getWeeklyPay()` calls the subclass version.

---

## 5.2 Abstract Classes

### Why do abstract classes exist?

To define a **common base** that cannot be instantiated directly, but can provide:

* shared fields and methods
* abstract methods (must be implemented by subclasses)

```java
abstract class Shape {
    public abstract double area();

    public void printArea() {
        System.out.println("Area=" + area());
    }
}

class Circle extends Shape {
    private final double r;

    Circle(double r) {
        if (r < 0) throw new IllegalArgumentException("r must be >= 0");
        this.r = r;
    }

    @Override
    public double area() {
        return Math.PI * r * r;
    }
}

public class Main {
    public static void main(String[] args) {
        Shape s = new Circle(2);
        s.printArea();
    }
}
```

**When to use:**

* multiple subclasses share *state* and *default behavior*

**When NOT to use:**

* if you only need a contract (use an interface)

---

## 5.3 Interfaces and “Multiple Inheritance of Type”

### Why interfaces exist?

Java forbids multiple inheritance of classes (to avoid ambiguity), but interfaces allow:

* multiple “capabilities”
* loose coupling

```java
interface Payable {
    double getWeeklyPay();
}

interface Identifiable {
    String getId();
}

class Contractor implements Payable, Identifiable {
    private final String id;
    private final double weeklyFee;

    Contractor(String id, double weeklyFee) {
        this.id = id;
        this.weeklyFee = weeklyFee;
    }

    @Override
    public double getWeeklyPay() {
        return weeklyFee;
    }

    @Override
    public String getId() {
        return id;
    }
}

public class Main {
    public static void main(String[] args) {
        Payable p = new Contractor("C-01", 1500);
        System.out.println(p.getWeeklyPay());
    }
}
```

**Modern note:** Interfaces can have `default` and `static` methods (Java 8+).

---

## 5.4 `final`, Method Hiding, and Common Traps

### `final` (modern best practice for safety)

* `final class`: cannot be extended
* `final method`: cannot be overridden
* `final field`: assigned once

#### Why it exists

To keep behavior stable and prevent unsafe extension.

---

### Static methods are **hidden**, not overridden

❌ Confusing behavior:

```java
class Base {
    static void hello() { System.out.println("Base"); }
}

class Derived extends Base {
    static void hello() { System.out.println("Derived"); }
}

public class Main {
    public static void main(String[] args) {
        Base b = new Derived();
        b.hello(); // prints "Base" because static is chosen by reference type
        Derived.hello(); // prints "Derived"
    }
}
```

✅ Best practice:

* call static methods using the **class name** (`Base.hello()`), not via objects.

---

### Fields are not polymorphic (field hiding)

```java
class Base {
    public String name = "Base";
}

class Derived extends Base {
    public String name = "Derived";
}

public class Main {
    public static void main(String[] args) {
        Base b = new Derived();
        System.out.println(b.name); // "Base" (field access uses reference type)
        System.out.println(((Derived) b).name); // "Derived"
    }
}
```

✅ Prefer private fields + getters.

---

## 5.5 Modern Java: `sealed` Classes, Pattern Matching `instanceof`

> If your course uses older Java, treat this as “industry context,” not exam core.

### Sealed classes (Java 17+)

Restrict which classes can extend a base type.

### Pattern matching `instanceof` (Java 16+)

```java
if (obj instanceof String s) {
    System.out.println(s.toUpperCase());
}
```

This avoids manual casting.

---

## 6. Integration & Combinations

### Visual hierarchy: a typical payroll model

```
Employee (abstract)
├── HourlyEmployee
├── SalariedEmployee
└── CommissionEmployee

Payable (interface)
└── Employee implements Payable
```

### Combining inheritance + composition (healthy design)

* Use inheritance for **shared identity & substitutability**
* Use composition for **pluggable behavior**

Example: `Employee` has a `PayPolicy` (composition) rather than subclasses exploding.

---

## 7. Best Practices & Patterns

### Decision tree: Inheritance vs Composition

```
Do you need an "is-a" relationship?
  ├─ No → Use composition (HAS-A)
  └─ Yes
      ├─ Will base class be stable and designed for extension?
      │    ├─ No → Prefer composition
      │    └─ Yes → Inheritance is OK
```

### Best practices (with justification)

* ✅ Prefer `private` fields in base classes (preserves invariants)
* ✅ Expose controlled extension points (`protected` methods) *sparingly*
* ✅ Use `@Override` always
* ✅ Don’t call overridable methods from constructors
* ✅ Document subclass expectations (what must remain true?)
* ✅ Prefer interfaces for API boundaries (looser coupling)

### Industry patterns powered by inheritance

* **Template Method pattern:** base class defines algorithm skeleton; subclasses fill steps
* **Framework callbacks:** base class calls methods you override

---

## 8. Common Mistakes (❌ → ✅)

### Mistake 1: Using inheritance when relationship is not “is-a”

❌

```java
class Engine { }
class Car extends Engine { } // wrong: car is not an engine
```

✅

```java
class Engine { }
class Car {
    private final Engine engine = new Engine();
}
```

---

### Mistake 2: Thinking you can override `private` methods

❌ (this is NOT overriding)

```java
class Base {
    private void f() { }
}
class Derived extends Base {
    public void f() { } // new method, not override
}
```

✅

```java
class Base {
    protected void f() { }
}
class Derived extends Base {
    @Override
    public void f() { }
}
```

---

### Mistake 3: Downcasting without checking

❌

```java
Animal a = new Cat();
Dog d = (Dog) a; // runtime crash
```

✅

```java
if (a instanceof Dog) {
    Dog d = (Dog) a;
}
```

---

### Mistake 4: Forgetting superclass constructor requirements

❌

```java
class A { A(int x) {} }
class B extends A {
    B() { } // compile error: implicit super() doesn't exist
}
```

✅

```java
class A { A(int x) {} }
class B extends A {
    B() { super(10); }
}
```

---

## 9. Quick Reference & Cheat Sheets

### Overriding rules (cheat)

| Rule                                               | Allowed?               |
| -------------------------------------------------- | ---------------------- |
| Override and reduce visibility (public → private)  | ❌                      |
| Override and widen visibility (protected → public) | ✅                      |
| Override and change parameter list                 | ❌ (that’s overloading) |
| Override and change return type (primitive)        | ❌                      |
| Override and change return type (covariant object) | ✅                      |
| Override `final` method                            | ❌                      |
| Override `static` method                           | ❌ (hiding happens)     |
| “Override” `private` method                        | ❌ (new method)         |

### Access modifiers (micro mnemonic)

* **P**rivate = **P**ersonal (class only)
* **(none)** = **P**ackage (same folder)
* **Pro**tected = **Pro**geny + package (subclasses + package)
* **Pub**lic = **Pub**lic (everyone)

---

## 10. Practice & Application (with Solutions)

### A. Concept questions

1. Explain the difference between **overriding** and **overloading**.
2. Why does Java enforce calling a superclass constructor first?
3. Why can’t we override `static` methods?
4. What does `instanceof` return for `null`?
5. When should you prefer composition over inheritance?

---

### B. Coding exercises (easy → hard)

#### Exercise 1 (Easy): Basic `extends`

Create `Vehicle` with `speed`, and subclass `Car` with `brand`. Print both.

✅ Solution

```java
class Vehicle {
    private final int speed;

    Vehicle(int speed) {
        if (speed < 0) throw new IllegalArgumentException("speed must be >= 0");
        this.speed = speed;
    }

    public int getSpeed() {
        return speed;
    }
}

class Car extends Vehicle {
    private final String brand;

    Car(int speed, String brand) {
        super(speed);
        if (brand == null || brand.isBlank()) throw new IllegalArgumentException("brand required");
        this.brand = brand;
    }

    public String getBrand() {
        return brand;
    }
}

public class Main {
    public static void main(String[] args) {
        Car c = new Car(120, "Toyota");
        System.out.println(c.getBrand() + " speed=" + c.getSpeed());
    }
}
```

---

#### Exercise 2 (Medium): Overriding + polymorphism

Make `Shape` with `area()`. Create `Circle` and `Rectangle`. Put them in an array and sum areas.

✅ Solution

```java
abstract class Shape {
    public abstract double area();
}

class Circle extends Shape {
    private final double r;

    Circle(double r) {
        if (r < 0) throw new IllegalArgumentException("r must be >= 0");
        this.r = r;
    }

    @Override
    public double area() {
        return Math.PI * r * r;
    }
}

class Rectangle extends Shape {
    private final double w;
    private final double h;

    Rectangle(double w, double h) {
        if (w < 0 || h < 0) throw new IllegalArgumentException("w/h must be >= 0");
        this.w = w;
        this.h = h;
    }

    @Override
    public double area() {
        return w * h;
    }
}

public class Main {
    public static void main(String[] args) {
        Shape[] shapes = { new Circle(2), new Rectangle(3, 4) };

        double total = 0;
        for (Shape s : shapes) {
            total += s.area();
        }

        System.out.printf("Total area = %.3f%n", total);
    }
}
```

---

#### Exercise 3 (Hard): Safe downcasting + behavior

Create `Employee` hierarchy. Add `printDetails(Employee e)`:

* prints name
* if employee is `HourlyEmployee`, prints hours and rate

✅ Solution

```java
abstract class Employee {
    private final String name;

    protected Employee(String name) {
        if (name == null || name.isBlank()) throw new IllegalArgumentException("name required");
        this.name = name;
    }

    public String getName() { return name; }
    public abstract double getWeeklyPay();
}

class HourlyEmployee extends Employee {
    private final double rate;
    private final int hours;

    HourlyEmployee(String name, double rate, int hours) {
        super(name);
        if (rate < 0) throw new IllegalArgumentException("rate must be >= 0");
        if (hours < 0) throw new IllegalArgumentException("hours must be >= 0");
        this.rate = rate;
        this.hours = hours;
    }

    public double getRate() { return rate; }
    public int getHours() { return hours; }

    @Override
    public double getWeeklyPay() {
        return rate * hours;
    }
}

class SalariedEmployee extends Employee {
    private final double weekly;

    SalariedEmployee(String name, double weekly) {
        super(name);
        if (weekly < 0) throw new IllegalArgumentException("weekly must be >= 0");
        this.weekly = weekly;
    }

    @Override
    public double getWeeklyPay() {
        return weekly;
    }
}

public class Main {
    public static void printDetails(Employee e) {
        System.out.println("Name=" + e.getName() + ", pay=" + e.getWeeklyPay());

        if (e instanceof HourlyEmployee) {
            HourlyEmployee he = (HourlyEmployee) e;
            System.out.println("  (hourly) hours=" + he.getHours() + ", rate=" + he.getRate());
        }
    }

    public static void main(String[] args) {
        Employee[] staff = {
            new HourlyEmployee("Aina", 25, 10),
            new SalariedEmployee("Ben", 1000)
        };

        for (Employee e : staff) {
            printDetails(e);
        }
    }
}
```

---

### C. Challenge problems (exam-style)

1. Explain precisely why `b.hello()` prints the base static method in the hiding example.
2. Give an example where a subclass override **cannot** compile because of checked exceptions.
3. Design a base class intended for extension. What methods should be `protected`, `final`, or `public`?

---

## 11. Meta-Commentary: Critique of the Source Material

### What was well-explained

* The slides correctly emphasize inheritance as a key OOP technique and mention code reuse.
* The slides highlight `super()` and basic overriding and access modifiers.

### What was missing or unclear

* **Constructor rules** were too brief: missing the “superclass constructor must run first” *and* the common compile error when no no-arg constructor exists.
* **Overriding vs private methods**: slides imply overriding a private method by making it public; in reality, private methods aren’t inherited—same signature becomes a new method.
* **Static methods & fields**: missing the crucial idea that static methods are **hidden**, not overridden; and fields are not polymorphic.
* **Polymorphism & dynamic dispatch**: mentioned indirectly but not built step-by-step.
* **Design guidance**: no “when not to use inheritance” or composition alternative.
* **Realistic runnable examples**: the slides show fragments but not full runnable programs.
* **Object class**: only `getClass()`/`instanceof` were mentioned; missing `toString`, `equals`, `hashCode`, and best practices.

### How these notes improve upon it

* Builds from “why” → “how” → “when to use”
* Adds decision trees, comparison tables, and visual hierarchies
* Provides minimal + realistic + advanced runnable examples
* Adds pitfalls, modern context, and practice exercises with solutions

### Suggestions for further study

* Practice designing a hierarchy that follows the **Liskov Substitution Principle**.
* Learn common patterns: **Template Method**, **Strategy**, **Decorator**.
* Explore Java’s standard library hierarchies (e.g., `Exception`, `Number`, `Collection`).
