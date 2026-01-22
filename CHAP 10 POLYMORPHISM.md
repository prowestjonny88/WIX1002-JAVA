# Java Fundamentals: Polymorphism (Master Notes)

**WIX1002 / Java Fundamentals**

> Goal: Master polymorphism in Java so you can read, write, and debug OOP code confidently—without needing the original slides.

---

## Table of Contents

1. [Introduction & Motivation](#1-introduction--motivation)
2. [Prerequisites & Key Terminology](#2-prerequisites--key-terminology)
3. [Fundamentals: What Polymorphism *Is*](#3-fundamentals-what-polymorphism-is)
4. [Compile-Time Polymorphism: Method Overloading](#4-compile-time-polymorphism-method-overloading)
5. [Runtime Polymorphism: Method Overriding & Dynamic Dispatch](#5-runtime-polymorphism-method-overriding--dynamic-dispatch)
6. [Upcasting, Downcasting, `instanceof`, and Safe Design](#6-upcasting-downcasting-instanceof-and-safe-design)
7. [Abstract Classes: Partial Blueprints](#7-abstract-classes-partial-blueprints)
8. [Interfaces: Contracts & Multiple Inheritance of Type](#8-interfaces-contracts--multiple-inheritance-of-type)
9. [`Comparable` vs `Comparator`: Sorting the Modern Way](#9-comparable-vs-comparator-sorting-the-modern-way)
10. [Copying Objects: `clone()` vs Modern Alternatives](#10-copying-objects-clone-vs-modern-alternatives)
11. [Inner / Nested Classes + Polymorphism (Anonymous Classes & Lambdas)](#11-inner--nested-classes--polymorphism-anonymous-classes--lambdas)
12. [Integration Patterns](#12-integration-patterns)
13. [Best Practices (Modern, Industry-Standard)](#13-best-practices-modern-industry-standard)
14. [Common Mistakes & Fixes](#14-common-mistakes--fixes)
15. [Quick Reference & Cheat Sheets](#15-quick-reference--cheat-sheets)
16. [Practice: Questions, Exercises, and Full Solutions](#16-practice-questions-exercises-and-full-solutions)
17. [Meta-Commentary: Critique of the Source Material](#17-meta-commentary-critique-of-the-source-material)

---

## 1. Introduction & Motivation

### Why does polymorphism exist?

Without polymorphism, programs that deal with “a family of related things” usually become **giant `if-else` chains**, duplicated code, and fragile logic.

Polymorphism solves this by letting you:

* **Write code once** against a *general type* (parent class or interface)
* **Plug in different behaviors** at runtime (different subclasses)
* **Extend** the program by adding new classes **without rewriting** old logic

### The problem it solves (in one line)

**“Same message, different behavior”** depending on the object you’re dealing with.

### Real-world analogy

Think of a universal **power socket adapter**:

* You interact with the *same* socket (interface)
* Different plugs (implementations) work differently internally

---

## 2. Prerequisites & Key Terminology

### You should already know

* Classes & objects
* Inheritance (`extends`)
* Access modifiers: `public`, `protected`, package-private, `private`
* Method signatures (name + parameter types)
* `static`, `final`

### Terms (define before use)

* **Polymorphism**: “many forms”; one interface/type can represent multiple concrete classes.
* **Binding**: connecting a method call to a method body.
* **Early binding (static binding)**: resolved at compile time (common for overloading).
* **Late binding (dynamic binding)**: resolved at runtime (overriding / virtual methods).
* **Overloading**: same method name, different parameters (compile-time).
* **Overriding**: subclass replaces parent implementation (runtime).
* **Reference type**: the declared type of a variable (left side).
* **Object type**: the actual class instantiated with `new` (right side).

✅ Memory aid:

> **OverLOAD** = different “LOAD” (parameters)
>
> **OverRIDE** = you “RIDE over” parent behavior

---

## 3. Fundamentals: What Polymorphism *Is*

### Definition

Polymorphism is the ability to treat different concrete objects through a **single common type**, while still executing behavior that matches the **actual object**.

### Mechanism (what happens internally)

At runtime, the JVM chooses which method body to execute based on the object’s **runtime class**, not the variable’s reference type.

A simplified mental model:

```
Reference type (compile time):   Animal a
Runtime object (run time):       new Dog()
Method call:                      a.sound()
Actual executed method:           Dog.sound()
```

### What polymorphism is NOT

* It is **not** “everything has the same method name”
* It is **not** “automatic casting”
* It does **not** apply to **fields** (variables) the same way it applies to methods

---

## 4. Compile-Time Polymorphism: Method Overloading

### Core explanation

**Overloading** means multiple methods share the same name in the **same class**, but differ in **parameters**.

#### Why it exists

* Improves readability: one name for “the same concept”
* Avoids creating many method names like `addInt`, `addDouble`, `addThreeNumbers`

#### Rules (must know)

✅ Valid overload differences:

* Different number of parameters
* Different parameter types
* Different parameter order

❌ Not an overload:

* Only changing return type

### How the compiler chooses (important)

Overload resolution is decided at **compile time**, using:

* the **declared types** of arguments
* conversion rules (widening > boxing > varargs)

### Minimal example (simplest case)

```java
public class Main {
    static int add(int a, int b) { return a + b; }
    static double add(double a, double b) { return a + b; }

    public static void main(String[] args) {
        System.out.println(add(2, 3));       // int version
        System.out.println(add(2.5, 3.5));   // double version
    }
}
```

### Realistic example (practical use)

A logging utility that accepts different inputs:

```java
import java.time.LocalDateTime;

public class Main {
    static void log(String message) {
        System.out.println(LocalDateTime.now() + " | " + message);
    }

    static void log(String tag, String message) {
        log("[" + tag + "] " + message);
    }

    static void log(Exception e) {
        log("ERROR", e.getClass().getSimpleName() + ": " + e.getMessage());
    }

    public static void main(String[] args) {
        log("App started");
        log("DB", "Connected");
        log(new IllegalArgumentException("Bad input"));
    }
}
```

### Advanced example (edge rules: boxing + varargs)

```java
public class Main {
    static void f(long x) { System.out.println("long"); }
    static void f(Integer x) { System.out.println("Integer"); }
    static void f(int... xs) { System.out.println("varargs"); }

    public static void main(String[] args) {
        f(5); // chooses f(long) via widening (int -> long)

        Integer n = 5;
        f(n); // chooses f(Integer)

        f();  // chooses varargs
    }
}
```

### When to use / when NOT to

✅ Use when:

* Same conceptual action, different inputs

❌ Avoid when:

* Overloads are too many and confusing
* Overloads behave *very differently* (surprising API)

---

## 5. Runtime Polymorphism: Method Overriding & Dynamic Dispatch

### Core explanation

**Overriding** happens when a subclass provides a new implementation for a method that already exists in its superclass.

#### Why it exists

* Enables reuse + customization
* Supports “program to a supertype” (parent class/interface)

### Overriding rules (must memorize)

To override, the subclass method must have:

* **Same name**
* **Same parameter types**
* **Same return type** (or **covariant**: a more specific subtype)
* **Not more restrictive** access modifier

Also:

* `static` methods are **not overridden** (they are *hidden*)
* `final` methods **cannot be overridden**
* `private` methods are **not overridden** (not visible to subclass)

### Mechanism: dynamic method dispatch

At runtime, JVM selects the method based on the **actual object type**.

### Minimal example

```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}

public class Main {
    public static void main(String[] args) {
        Animal a = new Dog();
        a.sound(); // Bark (Dog implementation)
    }
}
```

### Realistic example: a polymorphic payment workflow

```java
interface PaymentMethod {
    boolean pay(double amount);
}

class CardPayment implements PaymentMethod {
    @Override
    public boolean pay(double amount) {
        System.out.println("Charging card: RM" + amount);
        return amount <= 5000; // pretend limit
    }
}

class EWalletPayment implements PaymentMethod {
    @Override
    public boolean pay(double amount) {
        System.out.println("Paying via eWallet: RM" + amount);
        return amount <= 3000; // pretend balance
    }
}

class CheckoutService {
    public void checkout(PaymentMethod method, double amount) {
        boolean ok = method.pay(amount); // runtime dispatch
        System.out.println(ok ? "Payment OK" : "Payment FAILED");
    }
}

public class Main {
    public static void main(String[] args) {
        CheckoutService checkout = new CheckoutService();

        PaymentMethod m1 = new CardPayment();
        PaymentMethod m2 = new EWalletPayment();

        checkout.checkout(m1, 120.0);
        checkout.checkout(m2, 3200.0);
    }
}
```

### Advanced example: covariant return type

```java
class Animal { }
class Dog extends Animal { }

class Shelter {
    Animal adopt() { return new Animal(); }
}

class DogShelter extends Shelter {
    @Override
    Dog adopt() { return new Dog(); } // covariant return type
}

public class Main {
    public static void main(String[] args) {
        Shelter s = new DogShelter();
        Animal a = s.adopt();
        System.out.println(a.getClass().getSimpleName());
    }
}
```

### Edge case: fields are NOT polymorphic

```java
class Parent {
    String name = "Parent";
}
class Child extends Parent {
    String name = "Child";
}

public class Main {
    public static void main(String[] args) {
        Parent p = new Child();
        System.out.println(p.name); // Parent (field access uses reference type)
    }
}
```

---

## 6. Upcasting, Downcasting, `instanceof`, and Safe Design

### Why casting appears in polymorphism

Polymorphism often uses a **general type reference**:

* `Animal a = new Dog();` (upcasting)

Sometimes you *think* you need the specific type:

* “I want to call a Dog-only method”

### Upcasting (safe)

Upcasting = assigning subclass object to superclass reference.

* **Implicit** (no cast needed)
* Always safe

```java
Animal a = new Dog(); // upcasting
```

### Downcasting (risky)

Downcasting = treating a parent reference as a child type.

* Requires explicit cast
* Can crash with `ClassCastException`

❌ Wrong (can crash)

```java
Animal a = new Animal();
Dog d = (Dog) a; // ClassCastException
```

✅ Safer with `instanceof`

```java
Animal a = new Dog();
if (a instanceof Dog) {
    Dog d = (Dog) a;
    // use d safely
}
```

✅ Modern Java (pattern matching `instanceof`) — if supported in your course version:

```java
if (a instanceof Dog d) {
    // d is already casted
}
```

### But… the best fix is often: avoid downcasting

If you need downcasting a lot, your design is usually wrong.

#### Before (bad design: type-checking chain)

```java
static void makeSound(Animal a) {
    if (a instanceof Dog) System.out.println("Bark");
    else if (a instanceof Cat) System.out.println("Meow");
    else System.out.println("???");
}
```

#### After (good design: polymorphism)

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    @Override void sound() { System.out.println("Bark"); }
}

class Cat extends Animal {
    @Override void sound() { System.out.println("Meow"); }
}

static void makeSound(Animal a) {
    a.sound();
}
```

### Decision tree: should I downcast?

```
Need child-specific behavior?
  ├─ Yes → Can you move that behavior into the parent type (method)?
  │         ├─ Yes → Add/override method → no cast
  │         └─ No  → Is it truly optional / rare?
  │                  ├─ Yes → instanceof + cast (localized)
  │                  └─ No  → redesign (interface / composition)
  └─ No  → never downcast
```

---

## 7. Abstract Classes: Partial Blueprints

### Definition

An **abstract class** cannot be instantiated. It can mix:

* abstract methods (no body)
* concrete methods (with body)
* fields + constructors

### Why it exists

Use abstract classes when:

* You want a **shared base state** (fields) or shared implementation
* You want to **force** subclasses to implement key methods

### Minimal example

```java
abstract class Employee {
    protected final String name;

    protected Employee(String name) {
        this.name = name;
    }

    abstract double getPay();

    void printPaySlip() {
        System.out.printf("%s earns RM%.2f%n", name, getPay());
    }
}

class SalariedEmployee extends Employee {
    private final double monthlySalary;

    SalariedEmployee(String name, double monthlySalary) {
        super(name);
        this.monthlySalary = monthlySalary;
    }

    @Override
    double getPay() {
        return monthlySalary;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee e = new SalariedEmployee("Aina", 4200);
        e.printPaySlip();
    }
}
```

### Realistic example: Template Method pattern (polymorphism + abstract)

```java
abstract class ReportGenerator {
    // template method (fixed algorithm)
    public final void generate() {
        String data = fetchData();
        String formatted = format(data);
        export(formatted);
    }

    protected abstract String fetchData();
    protected abstract String format(String data);

    protected void export(String formatted) {
        System.out.println("Exporting report:\n" + formatted);
    }
}

class SalesReport extends ReportGenerator {
    @Override
    protected String fetchData() {
        return "sales=120, profit=30";
    }

    @Override
    protected String format(String data) {
        return "SALES REPORT → " + data;
    }
}

public class Main {
    public static void main(String[] args) {
        ReportGenerator r = new SalesReport();
        r.generate();
    }
}
```

### When NOT to use abstract classes

❌ Avoid if:

* You only need a contract (no shared state/implementation) → interface is better
* You are creating deep inheritance trees just to reuse code → prefer composition

---

## 8. Interfaces: Contracts & Multiple Inheritance of Type

### Definition

An **interface** defines *what a class can do* (a contract). A class can implement **multiple** interfaces.

### Why interfaces exist

* Java classes can only `extends` **one** class
* But real objects often need multiple “roles”

Example: a `Student` can be `Payable`, `Comparable`, `Runnable`, etc.

### Minimal example

```java
interface Drawable {
    void draw();
}

class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing circle");
    }
}

public class Main {
    public static void main(String[] args) {
        Drawable d = new Circle();
        d.draw();
    }
}
```

### Realistic example: Strategy pattern with an interface

```java
interface DiscountPolicy {
    double apply(double price);
}

class StudentDiscount implements DiscountPolicy {
    @Override
    public double apply(double price) {
        return price * 0.9;
    }
}

class NoDiscount implements DiscountPolicy {
    @Override
    public double apply(double price) {
        return price;
    }
}

class BillingService {
    public double finalPrice(double price, DiscountPolicy policy) {
        return policy.apply(price);
    }
}

public class Main {
    public static void main(String[] args) {
        BillingService billing = new BillingService();

        System.out.println(billing.finalPrice(100, new StudentDiscount()));
        System.out.println(billing.finalPrice(100, new NoDiscount()));
    }
}
```

### Advanced: default + static methods (modern Java)

```java
interface Auditable {
    default void audit(String msg) {
        System.out.println("AUDIT: " + msg);
    }

    static String now() {
        return java.time.LocalDateTime.now().toString();
    }
}

class Account implements Auditable {
    void deposit(double amount) {
        audit("Deposit " + amount + " at " + Auditable.now());
    }
}

public class Main {
    public static void main(String[] args) {
        new Account().deposit(50);
    }
}
```

### Default-method conflict (edge case)

If two interfaces provide the same default method, the class must resolve it:

```java
interface A { default void hello() { System.out.println("A"); } }
interface B { default void hello() { System.out.println("B"); } }

class C implements A, B {
    @Override
    public void hello() {
        A.super.hello(); // or B.super.hello(); or your own implementation
    }
}

public class Main {
    public static void main(String[] args) {
        new C().hello();
    }
}
```

### When to choose interface vs abstract class

| Need                           | Choose           | Why                                   |
| ------------------------------ | ---------------- | ------------------------------------- |
| Multiple roles                 | `interface`      | class can implement many interfaces   |
| Shared fields/state            | `abstract class` | interfaces can’t have instance fields |
| Shared code + enforced methods | `abstract class` | mix concrete + abstract methods       |
| Mostly a contract              | `interface`      | clean separation                      |

Decision tree:

```
Do you need shared state (fields) or partial implementation?
  ├─ Yes → abstract class
  └─ No  → interface

Do you need multiple types/roles?
  ├─ Yes → interface
  └─ No  → either (choose based on state/implementation needs)
```

---

## 9. `Comparable` vs `Comparator`: Sorting the Modern Way

### Why this matters

Sorting is a real-world place where polymorphism is everywhere:

* `Collections.sort(list)` depends on either:

  * objects implementing `Comparable<T>` (natural order)
  * or you providing a `Comparator<T>` (custom order)

### `Comparable<T>`

**Purpose**: define the object’s *natural ordering*.

**Signature**:

```java
public interface Comparable<T> {
    int compareTo(T other);
}
```

Return rule:

* `< 0` → this comes before other
* `> 0` → this comes after other
* `== 0` → considered equal in ordering

### Minimal example

```java
import java.util.*;

class Student implements Comparable<Student> {
    private final String name;
    private final int score;

    Student(String name, int score) {
        this.name = name;
        this.score = score;
    }

    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.score, other.score); // ascending by score
    }

    @Override
    public String toString() {
        return name + "(" + score + ")";
    }
}

public class Main {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Aina", 85));
        students.add(new Student("Ben", 92));
        students.add(new Student("Chong", 70));

        Collections.sort(students); // uses compareTo
        System.out.println(students);
    }
}
```

### `Comparator<T>`

**Purpose**: define different ordering *without changing the class*.

### Realistic example: multiple sort orders

```java
import java.util.*;

class Student {
    final String name;
    final int score;

    Student(String name, int score) {
        this.name = name;
        this.score = score;
    }

    @Override
    public String toString() { return name + "(" + score + ")"; }
}

public class Main {
    public static void main(String[] args) {
        List<Student> s = new ArrayList<>();
        s.add(new Student("Aina", 85));
        s.add(new Student("Ben", 92));
        s.add(new Student("Chong", 70));

        // sort by score descending
        s.sort((a, b) -> Integer.compare(b.score, a.score));
        System.out.println("By score desc: " + s);

        // sort by name ascending
        s.sort(Comparator.comparing(st -> st.name));
        System.out.println("By name asc: " + s);
    }
}
```

### Trade-offs

| Feature                   | `Comparable`                  | `Comparator`               |
| ------------------------- | ----------------------------- | -------------------------- |
| Where order is defined    | inside the class              | outside the class          |
| Number of possible orders | usually 1                     | many                       |
| Good for                  | “natural” identity (e.g., ID) | UI sorting, multiple views |

### Common pitfalls

* `compareTo` inconsistent with `equals` → can break `TreeSet`/`TreeMap`
* subtracting ints (`a.score - b.score`) can overflow → use `Integer.compare`

---

## 10. Copying Objects: `clone()` vs Modern Alternatives

### The truth about `clone()`

Java’s built-in `Object.clone()` is:

* `protected` in `Object`
* tied to the **marker interface** `Cloneable`
* commonly misunderstood

### Why `clone()` is tricky

* Default clone is usually **shallow** (references copied, not deep objects)
* `Cloneable` doesn’t force you to implement anything (it’s just a flag)
* Many modern Java codebases avoid `clone()` entirely

### Modern alternatives (recommended)

✅ Use one of these instead:

1. **Copy constructor**
2. **Static factory** `copyOf(...)`
3. **`copy()` method** with clear semantics
4. For immutable objects: just reuse the same object

### Minimal example: copy constructor

```java
class Address {
    final String city;

    Address(String city) {
        this.city = city;
    }

    Address(Address other) {
        this.city = other.city;
    }
}

class Person {
    final String name;
    final Address address;

    Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    // deep copy constructor
    Person(Person other) {
        this.name = other.name;
        this.address = new Address(other.address);
    }
}

public class Main {
    public static void main(String[] args) {
        Person p1 = new Person("Aina", new Address("KL"));
        Person p2 = new Person(p1);

        System.out.println(p1.address == p2.address); // false (deep copy)
    }
}
```

### Advanced: a safe `copy()` contract

```java
interface Copyable<T> {
    T copy();
}

class Point implements Copyable<Point> {
    final int x, y;

    Point(int x, int y) { this.x = x; this.y = y; }

    @Override
    public Point copy() {
        return new Point(x, y);
    }
}

public class Main {
    public static void main(String[] args) {
        Copyable<Point> p = new Point(3, 4);
        Point q = p.copy();
        System.out.println(q.x + "," + q.y);
    }
}
```

---

## 11. Inner / Nested Classes + Polymorphism (Anonymous Classes & Lambdas)

### Why inner classes show up in polymorphism

Polymorphism is often used via:

* event handlers
* callbacks
* strategies

Anonymous classes and lambdas let you implement an interface “on the spot”.

### Types of nested classes (quick)

* **Static nested class**: like a normal class, namespaced inside another
* **Inner class**: tied to an instance of the outer class
* **Local class**: defined inside a method
* **Anonymous class**: unnamed implementation

### Minimal example: anonymous class

```java
interface Action {
    void run();
}

public class Main {
    public static void main(String[] args) {
        Action a = new Action() {
            @Override
            public void run() {
                System.out.println("Running anonymously");
            }
        };
        a.run();
    }
}
```

### Modern version: lambda (functional interface)

A **functional interface** has exactly **one abstract method**.

```java
@FunctionalInterface
interface Action {
    void run();
}

public class Main {
    public static void main(String[] args) {
        Action a = () -> System.out.println("Running with lambda");
        a.run();
    }
}
```

### Realistic example: sorting with lambdas (`Comparator`)

(See section 9.)

---

## 12. Integration Patterns

These patterns are basically “polymorphism in real life”.

### 12.1 Strategy Pattern (choose behavior at runtime)

Use when you want to swap algorithms without `if-else`.

```java
interface RouteStrategy {
    int estimateMinutes(int distanceKm);
}

class FastestRoute implements RouteStrategy {
    @Override
    public int estimateMinutes(int distanceKm) {
        return distanceKm * 2;
    }
}

class ScenicRoute implements RouteStrategy {
    @Override
    public int estimateMinutes(int distanceKm) {
        return distanceKm * 3;
    }
}

class Navigator {
    private RouteStrategy strategy;

    Navigator(RouteStrategy strategy) {
        this.strategy = strategy;
    }

    void setStrategy(RouteStrategy strategy) {
        this.strategy = strategy;
    }

    void navigate(int distanceKm) {
        System.out.println("ETA: " + strategy.estimateMinutes(distanceKm) + " minutes");
    }
}

public class Main {
    public static void main(String[] args) {
        Navigator nav = new Navigator(new FastestRoute());
        nav.navigate(10);

        nav.setStrategy(new ScenicRoute());
        nav.navigate(10);
    }
}
```

### 12.2 Factory (create polymorphic objects cleanly)

```java
interface Notification {
    void send(String msg);
}

class EmailNotification implements Notification {
    @Override public void send(String msg) { System.out.println("EMAIL: " + msg); }
}

class SmsNotification implements Notification {
    @Override public void send(String msg) { System.out.println("SMS: " + msg); }
}

class NotificationFactory {
    static Notification create(String type) {
        return switch (type.toLowerCase()) {
            case "email" -> new EmailNotification();
            case "sms" -> new SmsNotification();
            default -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}

public class Main {
    public static void main(String[] args) {
        Notification n = NotificationFactory.create("email");
        n.send("Hello!");
    }
}
```

---

## 13. Best Practices (Modern, Industry-Standard)

✅ **1) Program to an interface, not an implementation**

* Use `List` not `ArrayList` in variable types
* Use interfaces for roles/behaviors

✅ **2) Prefer composition over inheritance**

* Inheritance is powerful but can become rigid
* Use Strategy pattern when you only need interchangeable behaviors

✅ **3) Always use `@Override` when overriding**

* Prevents silent bugs (accidental overloading)

✅ **4) Keep downcasting rare and localized**

* If casting spreads everywhere, redesign

✅ **5) Define clear ordering rules**

* If using `Comparable`, ensure it’s consistent and stable
* Prefer `Comparator` for multiple sort orders

✅ **6) Avoid `clone()` for most beginner projects**

* Use copy constructors or `copy()`

Performance note:

* Virtual method calls are extremely optimized by the JVM/JIT in real programs.
* Choose readability and correctness first.

---

## 14. Common Mistakes & Fixes

### Mistake 1: “I thought I overrode, but it didn’t work” (accidental overloading)

❌ Wrong

```java
class Parent { void f(int x) {} }
class Child extends Parent {
    void f(Integer x) {} // NOT overriding (different parameter)
}
```

✅ Correct

```java
class Parent { void f(int x) {} }
class Child extends Parent {
    @Override
    void f(int x) {}
}
```

### Mistake 2: Assuming `static` methods are polymorphic

❌ Wrong idea: `static` is decided by reference type.

```java
class Parent {
    static void hello() { System.out.println("Parent"); }
}
class Child extends Parent {
    static void hello() { System.out.println("Child"); }
}

public class Main {
    public static void main(String[] args) {
        Parent p = new Child();
        p.hello(); // Parent (method hiding, not overriding)
    }
}
```

### Mistake 3: Downcasting without checking

❌ Wrong

```java
Animal a = new Cat();
Dog d = (Dog) a; // crash
```

✅ Correct

```java
if (a instanceof Dog) {
    Dog d = (Dog) a;
}
```

### Mistake 4: Interface method not `public`

❌ Wrong

```java
class Circle implements Drawable {
    void draw() {} // compile error (must be public)
}
```

✅ Correct

```java
class Circle implements Drawable {
    @Override
    public void draw() {}
}
```

### Mistake 5: Bad compareTo implementation (overflow)

❌ Wrong

```java
return this.score - other.score;
```

✅ Correct

```java
return Integer.compare(this.score, other.score);
```

---

## 15. Quick Reference & Cheat Sheets

### Overloading vs Overriding (fast table)

| Topic                   | Overloading          | Overriding                 |
| ----------------------- | -------------------- | -------------------------- |
| Same class or subclass? | same class           | subclass vs superclass     |
| Signature               | must differ (params) | must match (name + params) |
| Decided when?           | compile time         | runtime                    |
| Annotation              | not used             | `@Override` recommended    |

### What participates in runtime polymorphism?

✅ Yes: instance methods (not `private`, not `final`, not `static`)

❌ No:

* fields
* `static` methods
* `private` methods
* `final` methods
* constructors

### Must-know signatures

```java
// interface
interface X { void m(); }

// abstract method
abstract class A { abstract void m(); }

// Comparable
class C implements Comparable<C> {
    public int compareTo(C other) { return 0; }
}

// Comparator
Comparator<C> comp = (a, b) -> 0;
```

---

## 16. Practice: Questions, Exercises, and Full Solutions

### 16.1 Conceptual Questions

1. Why does Java allow a parent reference to point to a child object?
2. Why is method overriding decided at runtime but overloading decided at compile time?
3. Why are `static` methods not polymorphic?
4. When is downcasting justified, and when is it a design smell?
5. When should you choose an interface over an abstract class?

### 16.2 Coding Exercises (Easy → Hard)

#### Exercise A (Easy): Animal sounds

**Task:** Create `Animal` base class and two subclasses. Store them in an array and call `sound()`.

✅ Solution

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    @Override void sound() { System.out.println("Bark"); }
}

class Cat extends Animal {
    @Override void sound() { System.out.println("Meow"); }
}

public class Main {
    public static void main(String[] args) {
        Animal[] animals = { new Dog(), new Cat(), new Dog() };
        for (Animal a : animals) {
            a.sound();
        }
    }
}
```

#### Exercise B (Medium): Discount calculator

**Task:** Implement at least 3 discount policies and compute final price.

✅ Solution

```java
interface DiscountPolicy {
    double apply(double price);
}

class StudentDiscount implements DiscountPolicy {
    @Override public double apply(double price) { return price * 0.9; }
}

class MemberDiscount implements DiscountPolicy {
    @Override public double apply(double price) { return price * 0.85; }
}

class NoDiscount implements DiscountPolicy {
    @Override public double apply(double price) { return price; }
}

class Checkout {
    static double finalPrice(double price, DiscountPolicy policy) {
        return policy.apply(price);
    }
}

public class Main {
    public static void main(String[] args) {
        System.out.println(Checkout.finalPrice(200, new StudentDiscount()));
        System.out.println(Checkout.finalPrice(200, new MemberDiscount()));
        System.out.println(Checkout.finalPrice(200, new NoDiscount()));
    }
}
```

#### Exercise C (Hard): Plugin-style command system

**Task:** Create an interface `Command` with `execute(String[] args)`. Implement `AddCommand`, `EchoCommand`, and a runner that chooses commands by name.

✅ Solution (complete)

```java
import java.util.*;

interface Command {
    String name();
    void execute(String[] args);
}

class EchoCommand implements Command {
    @Override public String name() { return "echo"; }
    @Override public void execute(String[] args) {
        System.out.println(String.join(" ", args));
    }
}

class AddCommand implements Command {
    @Override public String name() { return "add"; }
    @Override public void execute(String[] args) {
        if (args.length < 2) {
            System.out.println("Usage: add <a> <b>");
            return;
        }
        try {
            int a = Integer.parseInt(args[0]);
            int b = Integer.parseInt(args[1]);
            System.out.println(a + b);
        } catch (NumberFormatException e) {
            System.out.println("Numbers only!");
        }
    }
}

class CommandRunner {
    private final Map<String, Command> commands = new HashMap<>();

    void register(Command c) {
        commands.put(c.name(), c);
    }

    void runLine(String line) {
        String[] parts = line.trim().split("\\s+");
        if (parts.length == 0 || parts[0].isEmpty()) return;

        String cmdName = parts[0];
        Command cmd = commands.get(cmdName);
        if (cmd == null) {
            System.out.println("Unknown command: " + cmdName);
            return;
        }

        String[] args = Arrays.copyOfRange(parts, 1, parts.length);
        cmd.execute(args); // polymorphic call
    }
}

public class Main {
    public static void main(String[] args) {
        CommandRunner runner = new CommandRunner();
        runner.register(new EchoCommand());
        runner.register(new AddCommand());

        runner.runLine("echo hello world");
        runner.runLine("add 10 20");
        runner.runLine("add ten 20");
        runner.runLine("unknown 1 2");
    }
}
```

### 16.3 Challenge Problems

1. Implement `Comparable` for a `Book` class with ordering by ISBN, then sort by price using a `Comparator`.
2. Build a `ShapeCalculator` that supports **at least 4 shapes** and calculates total area and largest area shape.
3. Refactor a long `if-else` chain that checks types (`instanceof`) into clean polymorphism.

---

## 17. Meta-Commentary: Critique of the Source Material

### What was well-explained

* The slides correctly connect polymorphism to *dynamic binding* and show upcasting/downcasting as key ideas.
* The Chapter 9 PDF explains overloading vs overriding and dynamic dispatch with helpful analogies.

### What was missing / unclear

* **Critical code correctness**: several snippets in the slides are incomplete (missing parentheses, missing variables, missing braces).
* **Modern Java reality**: the slides describe interfaces as having “no method definitions”, but Java supports default/static methods.
* **`clone()` risks**: the slides mention `clone()` but don’t explain `Cloneable`, shallow vs deep copy, or why many developers avoid it.
* **`Comparable` modern signature**: should use generics `Comparable<T>` rather than `compareTo(Object)`.
* **Polymorphism boundaries**: static method hiding vs overriding, and “fields aren’t polymorphic” are usually exam traps, but not covered.

### How these notes improve it

* Reorganized from *why → what → how → pitfalls → practice*
* Added decision trees, before/after refactors, complete runnable code
* Included modern best practices and realistic patterns used in industry

### Suggested further study

* Collections Framework (`List`, `Map`, interfaces everywhere)
* Design patterns: Strategy, Factory, Template Method
* Immutability + copy strategies
* Testing polymorphic behavior (unit tests)
