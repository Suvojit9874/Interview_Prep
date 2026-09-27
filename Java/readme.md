# Java & Spring Boot Interview Preparation

> A deep, interview-focused revision guide for a **~3-year experienced Java backend developer** preparing for a company switch.
> Priority: **DEPTH > FLUFF**, **WHY + HOW > memorization**, **counter-questions > one-line answers**.

**How to use this document**
- Read a section, then cover the answers and try to *speak* them aloud.
- For every concept, be ready for the "Why?" chain — the interviewer will keep drilling.
- The **Interview Answer** blocks are 30-60s spoken answers you can deliver verbatim.
- Jump to [Last 24 Hours Before Interview](#last-24-hours-before-interview) the night before.

---

## Table of Contents

1. [Java Fundamentals](#1-java-fundamentals)
2. [JVM & Memory](#2-jvm--memory)
3. [OOP](#3-oop)
4. [String](#4-string)
5. [Wrapper Classes](#5-wrapper-classes)
6. [Casting](#6-casting)
7. [Java Core Language](#7-java-core-language)
8. [Exception Handling](#8-exception-handling)
9. [Collections](#9-collections)
10. [HashMap Internals](#10-hashmap-internals)
11. [Comparable & Comparator](#11-comparable--comparator)
12. [Generics](#12-generics)
13. [Java 8+ Features](#13-java-8-features)
14. [Stream API](#14-stream-api)
15. [Optional](#15-optional)
16. [Multithreading & Concurrency](#16-multithreading--concurrency)
17. [CompletableFuture](#17-completablefuture)
18. [I/O, NIO & Serialization](#18-io-nio--serialization)
19. [JDBC](#19-jdbc)
20. [POJO / JavaBean / DTO](#20-pojo--javabean--dto)
21. [Hibernate](#21-hibernate)
22. [JPA](#22-jpa)
23. [Spring Core (IoC / DI)](#23-spring-core-ioc--di)
24. [Spring Beans](#24-spring-beans)
25. [Spring AOP](#25-spring-aop)
26. [Spring MVC](#26-spring-mvc)
27. [Spring Boot](#27-spring-boot)
28. [Spring Data JPA](#28-spring-data-jpa)
29. [Transactions](#29-transactions)
30. [REST API](#30-rest-api)
31. [Spring Security](#31-spring-security)
32. [Microservices](#32-microservices)
33. [Redis & Caching](#33-redis--caching)
34. [SQL](#34-sql)
35. [Testing](#35-testing)
36. [Design Patterns](#36-design-patterns)
37. [SOLID Principles](#37-solid-principles)
38. [Coding Questions](#38-coding-questions)
39. [Project-Based Questions](#39-project-based-questions)
40. [Tricky Questions](#40-tricky-questions)
41. [Why Questions](#41-why-questions)
42. [Counter-Question Training](#42-counter-question-training)
43. [Rapid Fire](#43-rapid-fire)
44. [Mock Interviews](#44-mock-interviews)
45. [Cheat Sheets](#45-cheat-sheets)
46. [Important Comparisons](#46-important-comparisons)
47. [Last 24 Hours Before Interview](#last-24-hours-before-interview)

---

# 1. Java Fundamentals

## 1.1 JDK vs JRE vs JVM

### What is it?
- **JVM (Java Virtual Machine)** — an abstract *specification* plus a concrete runtime that executes Java bytecode. It loads classes, verifies bytecode, allocates memory, runs the code (interpret + JIT), and manages garbage collection.
- **JRE (Java Runtime Environment)** — JVM + core class libraries (`java.lang`, `java.util`, etc.) + supporting files. It is enough to **run** Java apps but not to **compile** them.
- **JDK (Java Development Kit)** — JRE + development tools: `javac` (compiler), `jar`, `javadoc`, `jdb`, `jshell`, etc. It is enough to **develop, compile, and run**.

**Relationship:** `JDK ⊃ JRE ⊃ JVM`.

### Why does it exist?
Java's promise is *"write once, run anywhere"*. That needs a layer (JVM) that abstracts away the OS/CPU. The JDK is for developers; the JRE was for end users who only run programs. (Note: since Java 11, Oracle no longer ships a standalone public JRE — you package a runtime with `jlink` or ship the full JDK.)

### How does it work? (compile → run)
```text
Hello.java  --javac-->  Hello.class (bytecode)  --java--> JVM loads, verifies,
                                                            interprets + JIT-compiles
                                                            -> native machine code -> CPU
```
- `javac Hello.java` compiles source to **bytecode** (`.class`), a platform-neutral instruction set.
- `java Hello` starts a JVM, which loads the class, verifies it, and executes it.
- **Bytecode is NOT machine code.** It's an intermediate representation. The JVM's interpreter runs it, and the **JIT** compiles hot paths to native code at runtime.

### Platform independence
- **Java (bytecode) is platform independent** — the same `.class`/`.jar` runs on any OS with a compatible JVM.
- **The JVM itself is platform dependent** — you download a different JVM binary for Windows/Linux/Mac. That's *the point*: the platform-specific part is isolated inside the JVM so your code stays portable.

### Common Interview Questions

#### Q1. Explain JDK, JRE and JVM.
**Answer:** JVM executes bytecode and is the runtime engine. JRE is JVM plus the standard libraries needed to run applications. JDK is JRE plus development tools like the compiler. So to run you need a JRE/JVM; to build you need a JDK.

**Counter Q:** If Java is platform independent, why do we need different JVMs?
**Answer:** Because *portability is achieved by pushing the platform-specific logic into the JVM*. Bytecode stays identical everywhere; the JVM translates it to whatever native instructions and OS calls the host needs. Different OS/CPU → different JVM build, but the same bytecode.

**Counter Q:** Is bytecode machine code?
**Answer:** No. Bytecode is a JVM-specific intermediate instruction set. Machine code is CPU-specific. The JVM interprets bytecode and JIT-compiles the hot parts into machine code at runtime.

**Counter Q:** Who converts bytecode to machine code?
**Answer:** The JVM's **execution engine** — the interpreter runs bytecode instruction by instruction, and the **JIT compiler** compiles frequently executed ("hot") methods to native code.

**Counter Q:** What is JIT and what happens before it kicks in?
**Answer:** JIT (Just-In-Time) compiler converts hot bytecode to optimized native code at runtime based on profiling. Before JIT compiles a method, the interpreter runs it. The JVM counts invocations/loop iterations; once a threshold is crossed the method is compiled. This "mixed mode" gives fast startup (interpreter) plus high steady-state performance (JIT).

### Tricky Questions
- *"Is the JVM platform independent?"* → No, the JVM is platform **dependent**; **bytecode** is platform independent.
- *"Does Java compile to machine code?"* → `javac` compiles to bytecode, not machine code. Native machine code is produced later by JIT (or ahead-of-time with GraalVM native-image).

### Common Mistakes
- Saying "the JVM is platform independent" (it isn't).
- Saying "Java is interpreted" (it's *interpreted + JIT-compiled*; "mixed mode").
- Confusing `.java` (source) with `.class` (bytecode).

### Quick Revision
- JDK ⊃ JRE ⊃ JVM.
- `javac` → bytecode; `java` → runs it.
- Bytecode = portable; JVM = platform-specific.
- Interpreter + JIT = mixed mode execution.
- Since Java 11 no standalone public JRE from Oracle.

### Interview Answer (speak this)
> "The JVM is the runtime that loads and executes bytecode; the JRE bundles the JVM with the standard libraries so you can run apps; the JDK adds the compiler and dev tools so you can build them. Java is platform independent because `javac` produces bytecode that runs on any JVM — the platform-specific work is hidden inside the JVM, which is itself platform dependent. At runtime the interpreter runs the bytecode while the JIT compiles hot methods to native code for speed."

---

# 2. JVM & Memory

## 2.1 ClassLoader

### What is it?
A ClassLoader loads `.class` bytecode into the JVM at runtime, on demand. Classes are loaded lazily — the first time they're referenced.

### The three built-in loaders (Java 9+)
1. **Bootstrap ClassLoader** — loads core JDK classes (`java.base` module: `java.lang.*`, etc.). Written in native code; its parent is `null`.
2. **Platform ClassLoader** (was "Extension" pre-Java 9) — loads additional platform modules.
3. **Application/System ClassLoader** — loads classes from the application classpath (your code and dependencies).

### Parent Delegation Model
When a loader is asked to load a class, it **first delegates to its parent**; only if the parent can't find it does the child try.
```text
App -> Platform -> Bootstrap  (delegation goes UP)
```
**Why it exists:**
- **Security** — you can't override core classes. If you write your own `java.lang.String`, the Bootstrap loader loads the real one first, so your fake is never used.
- **Uniqueness** — a class is loaded once by the highest possible loader, avoiding duplicate `Class` objects.

**Class identity** = fully-qualified name **+** the classloader that loaded it. The *same* class loaded by two different loaders are two different types (a common cause of `ClassCastException` in app servers / plugin systems).

### Custom ClassLoader
Extend `ClassLoader` and override `findClass()`. Used by app servers (Tomcat isolates each webapp), plugin frameworks, hot-reloading tools, and to load classes from DB/network/encrypted sources.

### Class loading lifecycle
```text
Loading -> Linking (Verification -> Preparation -> Resolution) -> Initialization
```
- **Loading** — read bytecode, create the `Class` object in the Method Area.
- **Verification** — bytecode is checked for safety (valid types, no stack overflow tricks). This is a key JVM security feature.
- **Preparation** — static fields get **default** values (0/null/false), memory allocated.
- **Resolution** — symbolic references resolved to direct references (can be lazy).
- **Initialization** — static initializers and static field assignments run, top-down. This is when `static {}` blocks execute.

Initialization is triggered by first *active use*: `new`, static method call, static field access (non-constant), reflection, or subclass init.

### Interview Questions

#### Q1. What is the parent delegation model and why does it exist?
**Answer:** Before loading a class, a loader asks its parent to load it first; it only loads the class itself if all ancestors fail. This guarantees core classes are always loaded by the Bootstrap loader (security — you can't shadow `java.lang.*`) and that each class is loaded once (uniqueness).

**Counter Q:** How can two objects of "the same class" fail an `instanceof` / cause `ClassCastException`?
**Answer:** If the class was loaded by two different classloaders. Class identity is name + loader, so they're distinct runtime types even with identical bytecode. Common in OSGi/Tomcat/plugin setups.

**Counter Q:** When does a `static` block run?
**Answer:** During **initialization**, on the first active use of the class, exactly once, in textual order with static field initializers. Not at loading time — loading and initialization are separate phases.

### Tricky
- *"Do static fields have values after Preparation?"* → They have **default** values after preparation; explicit values are assigned during **initialization**.

---

## 2.2 Runtime Data Areas

| Area | Per? | Stores | Errors |
|---|---|---|---|
| **Heap** | Shared | All objects, arrays, instance fields | `OutOfMemoryError: Java heap space` |
| **Method Area / Metaspace** | Shared | Class metadata, method bytecode, static fields, runtime constant pool | `OutOfMemoryError: Metaspace` |
| **JVM Stack** | Per thread | Stack frames (local vars, operand stack, partial results) | `StackOverflowError` |
| **PC Register** | Per thread | Address of current executing instruction | — |
| **Native Method Stack** | Per thread | State for native (JNI) calls | — |

- **Shared** areas (Heap, Method Area) are visible to all threads → need synchronization.
- **Per-thread** areas (Stack, PC, Native stack) are private → inherently thread-safe.

## 2.3 Heap & Generations
The heap is generationally divided because most objects die young (the **weak generational hypothesis**):
```text
Young Generation:  [ Eden | Survivor S0 | Survivor S1 ]
Old (Tenured) Generation:  [ long-lived objects ]
```
- New objects go into **Eden**. When Eden fills → **Minor GC**: survivors move to a Survivor space; objects surviving enough cycles are **promoted** to Old gen.
- **Minor GC** (Young) is frequent and cheap; **Major/Full GC** (Old / whole heap) is rarer and more expensive.

## 2.4 Stack
- Each method call pushes a **stack frame** containing **local variables**, an **operand stack** (where bytecode operations compute), and a reference to the constant pool.
- Deep/infinite recursion exhausts the stack → **`StackOverflowError`**.
```java
void recurse() { recurse(); } // StackOverflowError
```

## 2.5 Metaspace (vs PermGen)
- **Metaspace** (Java 8+) stores class metadata. It lives in **native memory**, not the heap, and auto-grows (bounded by `-XX:MaxMetaspaceSize`).
- **PermGen** (≤ Java 7) was a fixed-size heap region for class metadata + interned strings. It caused frequent `OutOfMemoryError: PermGen space`, especially in app servers that redeploy often (classloader leaks).
- **Why removed:** fixed sizing was hard to tune, it coupled class metadata to heap GC, and it hurt app-server redeploys. Metaspace (native, auto-sizing) fixed this. Interned strings moved to the heap in Java 7.

## 2.6 Execution Engine
- **Interpreter** — executes bytecode instruction by instruction. Fast startup, slower steady state.
- **JIT compiler** — profiles running code, detects **hot** methods/loops, compiles them to optimized native code (inlining, escape analysis, loop unrolling). HotSpot uses tiered compilation: C1 (fast compile, light optimization) then C2 (heavy optimization).
- **Profiling** feeds speculative optimizations; if an assumption breaks, the JVM **deoptimizes** back to the interpreter.

## 2.7 Garbage Collection

### Why GC exists
Java manages memory automatically — no manual `free()`. GC reclaims objects that are no longer **reachable**, preventing most memory leaks and dangling-pointer bugs.

### Reachability
An object is live if reachable from a **GC root** (stack local variables, static fields, JNI references, active threads). Unreachable objects are eligible for collection. GC does *not* use reference counting (can't handle cycles); it uses **reachability/tracing** (mark-sweep-compact style).

### GC types
- **Minor GC** — collects Young gen. Frequent, short.
- **Major GC** — collects Old gen.
- **Full GC** — collects the entire heap (+ often metaspace); most expensive, longest pause.
- **Stop-the-world (STW)** — application threads pause while GC runs. Modern collectors minimize STW duration.

### Collectors
| Collector | Style | Best for |
|---|---|---|
| **Serial** | Single-threaded, STW | Small heaps, single-CPU, simple apps |
| **Parallel (Throughput)** | Multi-threaded STW | Batch jobs, throughput over latency |
| **CMS** (deprecated/removed) | Mostly concurrent, low pause | Legacy low-latency (replaced by G1) |
| **G1** (default since Java 9) | Region-based, incremental, predictable pauses | General server apps, large heaps |
| **ZGC** | Concurrent, sub-millisecond pauses, scalable to TB heaps | Very large heaps, strict latency |
| **Shenandoah** | Concurrent, low-pause (Red Hat) | Low-latency, similar goals to ZGC |

### GC tuning basics
- `-Xms` / `-Xmx` (initial/max heap), `-XX:MaxMetaspaceSize`, choose collector (`-XX:+UseG1GC`, `-XX:+UseZGC`).
- Tune for either **throughput** (Parallel) or **latency** (G1/ZGC/Shenandoah) — you rarely get both.
- Rule of thumb: don't tune prematurely; measure with GC logs first.

## 2.8 Memory Problems

| Problem | Meaning | Common cause |
|---|---|---|
| `StackOverflowError` | Thread stack exhausted | Deep/infinite recursion |
| `OutOfMemoryError: Java heap space` | Heap full, GC can't reclaim | Leak, undersized heap, huge allocations |
| `OutOfMemoryError: Metaspace` | Class metadata space full | Classloader leaks, dynamic class generation |
| `OutOfMemoryError: unable to create new native thread` | OS thread limit | Thread leak, too many threads |

### Memory leaks despite GC
GC only frees **unreachable** objects. A leak in Java = objects still **reachable** but never used again. Classic causes:
- Static collections / caches that grow forever.
- Unremoved listeners/callbacks.
- `ThreadLocal`s not cleaned in pooled threads.
- Keys with broken `hashCode`/`equals` in maps, or classloader leaks on redeploy.

### Diagnosing
- **Heap dump** (`jmap`, `-XX:+HeapDumpOnOutOfMemoryError`) → analyze with Eclipse MAT / VisualVM to find dominators/retained size.
- **Thread dump** (`jstack`) → diagnose deadlocks, stuck threads, high CPU.
- **Monitoring:** JVisualVM, JConsole, `jcmd`, Micrometer + Prometheus/Grafana, GC logs (`-Xlog:gc*`).

### Interview Questions

#### Q1. What's the difference between a memory leak in Java and in C++?
**Answer:** In C++ a leak is memory you allocated and never freed. In Java the GC frees unreachable memory automatically, so a "leak" means objects remain **reachable** (e.g., held by a static cache) so GC can't collect them, even though the app will never use them again.

**Counter Q:** If GC handles memory, how do you still get `OutOfMemoryError`?
**Answer:** Either a genuine leak (reachable but unused objects accumulate), an undersized heap for the workload, or a single huge allocation. GC can't help if everything is still reachable.

**Counter Q:** How would you debug an OOM in production?
**Answer:** Enable `-XX:+HeapDumpOnOutOfMemoryError`, capture the heap dump, open it in MAT, look at the largest retained sets and dominator tree to find what's holding memory, correlate with recent code/config changes, and check GC logs for the trend.

**Counter Q:** `StackOverflowError` vs `OutOfMemoryError`?
**Answer:** `StackOverflowError` is per-thread stack exhaustion (usually deep recursion). `OutOfMemoryError` is heap/metaspace/native exhaustion. Both are `Error`s, not exceptions — you generally don't catch them.

### Tricky
- *"Does `System.gc()` guarantee GC?"* → No, it's only a *hint*; the JVM may ignore it.
- *"Can you have a memory leak with a working GC?"* → Yes — reachable-but-unused objects.
- *"Where do interned Strings live in Java 8?"* → Heap (moved out of PermGen in Java 7).

### Quick Revision
- Heap = objects (shared); Stack = frames (per thread).
- Young (Eden+2 Survivors) → Old; minor vs major vs full GC.
- Metaspace = native, auto-sizing; replaced fixed PermGen.
- G1 is default (Java 9+); ZGC/Shenandoah for ultra-low pause.
- Leak = reachable but unused; diagnose with heap/thread dumps.

### Interview Answer (speak this)
> "The JVM splits memory into a shared heap for objects, a per-thread stack for method frames, and Metaspace in native memory for class metadata. The heap is generational — new objects live in Eden, survive into Survivor spaces, and get promoted to Old gen, which lets minor GCs be cheap and frequent. GC reclaims unreachable objects using tracing from GC roots. G1 is the default collector balancing throughput and pause time, while ZGC and Shenandoah target sub-millisecond pauses on huge heaps. A Java memory leak is objects that stay reachable but are never used again — I'd diagnose it with a heap dump in MAT."

---

# 3. OOP

The four pillars — but interviewers want the *reasoning*, not textbook lines.

## 3.1 The Four Pillars (fast)
- **Encapsulation** — bundle data + behavior, hide internal state behind a controlled API.
- **Inheritance** — reuse and specialize via an IS-A relationship.
- **Polymorphism** — one interface, many implementations; behavior chosen at compile time (overloading) or runtime (overriding).
- **Abstraction** — expose *what* an object does, hide *how*.

---

## 3.2 Encapsulation

### Why?
To protect invariants and allow the internal representation to change without breaking callers. Fields are `private`; access is through methods so you can validate, log, make thread-safe, or change storage later.

### Immutable objects & defensive copying
```java
public final class Money {                 // final: can't be subclassed to break invariants
    private final long amountCents;
    private final List<String> tags;

    public Money(long amountCents, List<String> tags) {
        this.amountCents = amountCents;
        this.tags = new ArrayList<>(tags); // defensive COPY IN (caller can't mutate our state)
    }
    public List<String> getTags() {
        return List.copyOf(tags);           // defensive COPY OUT (caller can't mutate internals)
    }
}
```
Without defensive copies, a caller holding the original list reference could mutate your "immutable" object.

### Interview Q&A
#### Q1. How do you make a class truly immutable?
**Answer:** Make the class `final`, all fields `private final`, no setters, initialize in the constructor, and defensively copy any mutable inputs/outputs (collections, dates, arrays). Return copies from getters, never the live reference.

**Counter Q:** Why make the class `final`?
**Answer:** So a subclass can't override methods or add mutable state that breaks immutability guarantees.

**Counter Q:** Are `final` fields enough for immutability?
**Answer:** No — `final` only prevents reassigning the reference. If the field points to a mutable object (e.g., a `List`), the contents can still change. You need defensive copies + unmodifiable views.

---

## 3.3 Inheritance

- **IS-A** relationship via `extends`. `super` accesses parent members/constructor.
- **Constructor chaining**: a subclass constructor implicitly calls `super()` first (unless `this(...)`/`super(...)` is explicit).
```java
class Animal { Animal() { System.out.println("Animal ctor"); } }
class Dog extends Animal { Dog() { /* implicit super() */ System.out.println("Dog ctor"); } }
// new Dog() prints: Animal ctor, then Dog ctor
```

### Limitations & Composition vs Inheritance
- Inheritance is **tight coupling** — subclass depends on parent's implementation; changes ripple down (fragile base class).
- Java has **no multiple class inheritance** (diamond problem) — you can only extend one class.
- **Prefer composition** ("HAS-A") when you just want reuse. Inheritance is for genuine IS-A + polymorphism.
```java
// Inheritance abuse:
class Stack extends ArrayList<Integer> {} // exposes add(index,..), remove(index) — breaks stack semantics
// Composition (better):
class Stack { private final List<Integer> list = new ArrayList<>(); public void push(int x){ list.add(x);} }
```

### Interview Q&A
#### Q1. Why prefer composition over inheritance?
**Answer:** Composition keeps coupling loose — you delegate to a member you can swap, and you only expose the API you want. Inheritance leaks the parent's entire API and ties you to its implementation, so a change in the base class can silently break subclasses (fragile base class problem). Use inheritance only for real IS-A relationships where polymorphism is needed.

**Counter Q:** Why can't Java have multiple class inheritance?
**Answer:** To avoid the diamond ambiguity — if two parents define the same method/state, which does the child inherit? Java avoids this for classes but allows multiple **interface** inheritance because interfaces traditionally had no state; with default methods, Java requires you to explicitly resolve conflicts.

---

## 3.4 Polymorphism

### Compile-time (overloading) vs Runtime (overriding)
- **Overloading** — same method name, different parameter list; resolved by the **compiler** using the *static* type. Return type alone can't distinguish overloads.
- **Overriding** — subclass redefines a superclass method with the same signature; resolved at **runtime** by the *actual object type* — this is **dynamic method dispatch**.

```java
class Animal { String sound() { return "..."; } }
class Dog extends Animal { @Override String sound() { return "Woof"; } }

Animal a = new Dog();
a.sound(); // "Woof" — runtime dispatch on actual type Dog
```

### Overloading rules
- Must differ in number/type/order of params.
- Can differ in return type/access/exceptions *only if* params already differ.
- Autoboxing, widening, and varargs affect resolution (widening preferred over boxing, boxing over varargs).

### Overriding rules
- Same name + parameters; return type same or **covariant** (subtype).
- Access modifier **cannot be more restrictive** (can be wider).
- Checked exceptions: can throw **same, narrower, or none** — not broader.
- `static`, `private`, `final` methods **cannot be overridden**.
- Constructors are **not** inherited or overridden.

### Static method hiding
```java
class A { static void f() { System.out.println("A"); } }
class B extends A { static void f() { System.out.println("B"); } }
A ref = new B();
ref.f(); // "A" — static methods are HIDDEN, resolved by reference type, not dispatched
```

### Covariant return type
```java
class Animal { Animal reproduce() { return new Animal(); } }
class Dog extends Animal { @Override Dog reproduce() { return new Dog(); } } // Dog is-a Animal -> allowed
```

### Interview Q&A
#### Q1. What is dynamic method dispatch?
**Answer:** At runtime the JVM picks the overridden method based on the object's **actual type**, not the reference type. It's how runtime polymorphism works — the method table (vtable) of the real object is consulted.

**Counter Q:** Can you override a static method?
**Answer:** No. Static methods belong to the class, not the instance. Declaring the same static method in a subclass **hides** it; resolution uses the reference type at compile time, not dynamic dispatch.

**Counter Q:** Can you override a private method?
**Answer:** No — private methods aren't visible to subclasses, so a same-named method in the subclass is a brand new, independent method (no `@Override`).

**Counter Q:** Can an overriding method throw a broader checked exception?
**Answer:** No. It may throw the same, a subclass, fewer, or no checked exceptions — but never a broader checked exception. (Unchecked exceptions are unrestricted.)

**Counter Q:** Can the overriding method reduce visibility?
**Answer:** No — it can only keep or widen access (e.g., `protected` → `public` is fine, `public` → `protected` is not).

### Tricky
- *"`Animal a = new Dog(); a.sound();` which runs?"* → `Dog.sound()` (overriding = dynamic).
- *"Same for a static method?"* → the reference type's version (hiding = static).
- *"Overloaded method with `null` argument?"* → most specific type wins; ambiguous → compile error.

---

## 3.5 Abstraction: Abstract Class vs Interface

| | Abstract class | Interface |
|---|---|---|
| State (fields) | Yes (instance fields) | Only `public static final` constants |
| Constructors | Yes | No |
| Multiple inheritance | No (single) | Yes (multiple) |
| Methods | abstract + concrete | abstract + `default` + `static` + `private` (Java 9+) |
| Use when | Shared state + partial impl, tight IS-A family | Contract/capability across unrelated types |

- **Default methods** (Java 8) let interfaces evolve without breaking implementers (e.g., `List.sort`).
- **Static interface methods** = utility methods on the interface itself.
- **Functional interface** = exactly one abstract method → target for lambdas (`@FunctionalInterface`).

### Interview Q&A
#### Q1. When abstract class vs interface?
**Answer:** Use an abstract class when implementations share state and common code and form a true IS-A family. Use an interface to define a capability/contract that possibly unrelated classes implement, or when you need multiple inheritance of type. With Java 8 default methods the line blurred, but interfaces still can't hold instance state.

**Counter Q:** Why were default methods added?
**Answer:** To add methods to existing interfaces (like `Collection.stream()`) without breaking every existing implementer — backward-compatible interface evolution.

**Counter Q:** Diamond problem with default methods — what happens?
**Answer:** If two interfaces provide the same default method, the implementing class must override it and can call `Interface.super.method()` to disambiguate. Compiler forces you to resolve it.

---

## 3.6 OOP Relationships
- **IS-A** — inheritance (`Dog` is-a `Animal`).
- **HAS-A** — composition/aggregation (`Car` has-a `Engine`).
- **Association** — general relationship between objects.
- **Aggregation** — "has-a" with independent lifecycles (a `Team` has `Player`s; players survive the team).
- **Composition** — "owns-a" with dependent lifecycle (a `House` has `Room`s; rooms die with the house).
- **Dependency** — "uses-a" transiently (a method takes a param it uses).

---

## 3.7 Constructors, `this`, `super`, `static`, `final`, Access Modifiers

### Constructors
- **Default constructor** is auto-generated only if you declare no constructor.
- `this(...)` calls another constructor in the same class; `super(...)` calls the parent's — must be the **first statement**.
- Constructors are **not inherited, not overridden, not polymorphic**; cannot be `final`, `static`, or `abstract`.
- Abstract classes **can** have constructors (called via subclass `super()`); interfaces **cannot**.

### `static`
- `static` members belong to the class, not instances. `static` methods can't access instance fields directly (no `this` — no specific instance exists). `static {}` block runs once at class init.
- **Static nested class** doesn't hold a reference to the outer instance (unlike an inner class).

### `final`
- `final` variable → assign once. `final` reference → can't reassign, but the object it points to may still mutate. `final` method → can't override. `final` class → can't extend (e.g., `String`).

### Access modifiers table
| Modifier | Same class | Same package | Subclass (diff pkg) | Anywhere |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| default (package-private) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

---

## 3.8 The `Object` Class

Every class extends `Object`. Key methods: `equals()`, `hashCode()`, `toString()`, `getClass()`, `clone()`, `wait()`, `notify()`, `notifyAll()`, `finalize()` (deprecated).

### The equals/hashCode contract
1. If `a.equals(b)` is true, then `a.hashCode() == b.hashCode()` **must** hold.
2. Equal `hashCode` does **not** require `equals` to be true (collisions allowed).
3. `equals` must be reflexive, symmetric, transitive, consistent, and `x.equals(null)` is false.

```java
@Override public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Employee e)) return false;
    return id == e.id && Objects.equals(name, e.name);
}
@Override public int hashCode() { return Objects.hash(id, name); }
```

### Interview Q&A
#### Q1. Why override `equals()` and `hashCode()` together?
**Answer:** Hash-based collections (`HashMap`, `HashSet`) locate objects first by `hashCode` (to find the bucket) then by `equals` (to match within the bucket). If two logically-equal objects have different hash codes, they land in different buckets and the map treats them as different keys — you get duplicates or fail to find an entry. So the contract *must* hold.

**Counter Q:** What breaks in a `HashMap` if `hashCode` is wrong?
**Answer:** `map.put(key, v)` then `map.get(equalKey)` can return `null` because the equal key hashes to a different bucket. You get "phantom" missing entries and duplicate keys.

**Counter Q:** `==` vs `equals()`?
**Answer:** `==` compares references (identity) for objects, or values for primitives. `equals()` compares logical equality as defined by the class. Default `Object.equals` is `==` until overridden.

**Counter Q:** Can `equals()` be overloaded?
**Answer:** Yes accidentally — if you write `equals(Employee e)` instead of `equals(Object o)`, that's an **overload**, not an override, and collections won't use it. Always override `equals(Object)` with `@Override`.

**Counter Q:** Can `hashCode()` return the same value for different objects?
**Answer:** Yes — that's a legal **collision**. Returning a constant is technically valid but degrades `HashMap` to O(n). Different objects may share a hash; equal objects must share a hash.

**Counter Q:** Two unequal objects with the same hashCode — what happens in a HashMap?
**Answer:** They go to the same bucket and are stored together (linked list, or a red-black tree after treeification); `equals` then distinguishes them. Correctness is fine, only performance degrades.

### Tricky
- *"Can you use a mutable object as a HashMap key?"* → You can, but if you mutate a field used in `hashCode`/`equals` after insertion, you'll never find it again. Use immutable keys.
- *"Does overriding `equals` require overriding `hashCode`?"* → Yes, to honor the contract.

### Quick Revision (OOP)
- Overloading = compile-time (static type); overriding = runtime (actual type).
- Static/private/final methods can't be overridden; static methods are hidden.
- Overriding: covariant returns OK, can't widen checked exceptions, can't reduce visibility.
- Composition > inheritance for reuse; inheritance for real IS-A + polymorphism.
- equals/hashCode contract is mandatory for hash collections.
- Abstract class = state + partial impl; interface = contract + multiple inheritance.

### Interview Answer (speak this — polymorphism)
> "Polymorphism has two forms. Overloading is compile-time — the compiler picks the method from the reference/static type based on the argument list. Overriding is runtime — when a subclass redefines a method, the JVM dispatches to the actual object's version via dynamic dispatch, which is what lets me program to a supertype and swap implementations. Static and private methods aren't dispatched — static methods are hidden and resolved by reference type."

---

# 4. String

## What is it?
`String` is an immutable sequence of characters. Backed by a `byte[]` (since Java 9, "compact strings" — Latin-1 for ASCII, UTF-16 otherwise) and marked `final`.

## Why is String immutable? (very common)
1. **String pool / caching** — literals are shared; immutability makes sharing safe (no one can mutate a shared literal).
2. **Security** — Strings are used for file paths, URLs, DB URLs, class names, credentials. If mutable, a validated value could be changed after the check (TOCTOU).
3. **Thread safety** — immutable objects are inherently thread-safe; no synchronization needed.
4. **HashCode caching** — String caches its hash code; safe only because contents never change → fast `HashMap` keys.
5. **Class loading** — class names are Strings; immutability prevents tampering.

## Why is String `final`?
So no subclass can override behavior and break immutability (e.g., add a setter or override `equals`). Immutability + `final` together guarantee the invariants.

## String Pool (String Constant Pool)
- A special region (in the **heap** since Java 7) storing unique string literals.
- `String s = "hello";` → reuses/creates a pooled instance.
- `new String("hello")` → **always** creates a new object on the heap, **plus** the literal "hello" in the pool → **2 objects** (one new heap object, and the pooled literal if not already present).

```java
String a = "hello";
String b = "hello";
String c = new String("hello");

a == b;          // true  — same pooled literal
a == c;          // false — c is a distinct heap object
a.equals(c);     // true  — same content
a == c.intern(); // true  — intern() returns the pooled reference
```

### `intern()`
Returns the canonical pooled reference for the string's content, adding it to the pool if absent. Lets you force pooling for runtime-built strings (use sparingly — pool pressure).

## Concatenation: compile-time vs runtime
- **Compile-time** (constant expressions) are folded: `"a" + "b"` becomes the literal `"ab"` at compile time and is pooled.
- **Runtime** concatenation (`a + b` with variables) compiles to `StringBuilder`/`invokedynamic` (Java 9+ `StringConcatFactory`).
- **In a loop**, `s = s + x` creates a new String + builder each iteration → O(n²). Use a single `StringBuilder`.

```java
// BAD: O(n^2), many temp objects
String r = "";
for (int i = 0; i < n; i++) r += i;
// GOOD: O(n)
StringBuilder sb = new StringBuilder();
for (int i = 0; i < n; i++) sb.append(i);
String r = sb.toString();
```

## String vs StringBuilder vs StringBuffer
| | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Mutable | No | Yes | Yes |
| Thread-safe | Yes (immutable) | No | Yes (synchronized) |
| Performance | Slow for many edits | Fastest | Slower (sync overhead) |
| Since | 1.0 | 5.0 | 1.0 |

Use `StringBuilder` by default; `StringBuffer` only when the buffer is genuinely shared across threads (rare — usually you'd synchronize at a higher level).

## Interview Q&A

#### Q1. Why is String immutable?
**Answer:** For safe pooling/sharing of literals, security (paths, credentials, class names can't be mutated after validation), thread safety without locks, and cached hash codes that make Strings excellent `HashMap` keys. `String` is also `final` so subclasses can't break these guarantees.

**Counter Q:** How many objects does `new String("abc")` create?
**Answer:** Up to two — the `"abc"` literal in the pool (if not already present) plus the new heap object created by `new`. If the literal already exists in the pool, only the new heap object is created.

**Counter Q:** Difference between `String s = "hello"` and `new String("hello")`?
**Answer:** The literal form reuses the pooled instance (reference equality with other identical literals). `new String(...)` forces a distinct heap object; `==` with the literal is false, but `equals` is true. Call `.intern()` to get back the pooled reference.

**Counter Q:** Is `StringBuilder` thread-safe?
**Answer:** No. It's unsynchronized for speed. Use `StringBuffer` (synchronized) or external synchronization if shared across threads — but usually you keep a `StringBuilder` local to a method, which is already thread-confined.

**Counter Q:** What happens internally with `+` concatenation in a loop?
**Answer:** Each `s = s + x` allocates a new `StringBuilder`, appends, and produces a new immutable `String`, discarding the old — O(n²) time and heavy garbage. A single reused `StringBuilder` is O(n).

**Counter Q:** Why is String a good HashMap key?
**Answer:** It's immutable (hash can't change after insertion) and caches its `hashCode`, so lookups are fast and correct. A mutable key whose hash changes would become unfindable.

### Tricky
- *"`"a"+"b"+"c"` — how many objects?"* → One; folded to `"abc"` at compile time.
- *"`s1 == s2` for two literals?"* → true (pooled). For `new String`? false.
- *"Does `substring` share the char array?"* → Not since Java 7u6; it copies (older versions shared, causing memory leaks).

### Common Mistakes
- Saying `new String("x")` creates one object (it can create two).
- Using `==` to compare String content.
- Building strings with `+` in loops.

### Quick Revision
- Immutable + final → poolable, secure, thread-safe, hash-cached.
- Literals pooled; `new String` = new heap object; `intern()` → pooled ref.
- `==` = identity; `.equals()` = content.
- StringBuilder (fast, unsynced) vs StringBuffer (synced).

### Interview Answer (speak this)
> "String is immutable and final. Immutability enables the string pool to safely share literals, guarantees thread safety without locks, lets String cache its hash code for fast map lookups, and keeps security-sensitive values like paths and credentials from being changed after validation. A literal comes from the pool, so identical literals are `==`-equal, while `new String()` forces a separate heap object. For heavy string building I use a local `StringBuilder` to avoid the O(n²) cost of `+` in loops."

---

# 5. Wrapper Classes

## What & Why
Wrappers (`Integer`, `Long`, `Double`, `Character`, `Boolean`, `Byte`, `Short`, `Float`) box primitives into objects so they can be used where objects are required — **generics and collections** (`List<Integer>`), nullability, and utility methods (`Integer.parseInt`). Primitives can't be generic type arguments; wrappers can.

- **Autoboxing** — automatic primitive → wrapper (`Integer i = 5;`).
- **Unboxing** — wrapper → primitive (`int x = i;`).

## Integer caching (the classic trap)
`Integer.valueOf` caches instances in `[-128, 127]` (autoboxing uses `valueOf`). Within that range, boxed values are the *same* cached object.
```java
Integer a = 100, b = 100;   // cached
Integer x = 200, y = 200;   // NOT cached (outside range)
a == b;  // true  — same cached object
x == y;  // false — different objects
a.equals(b); x.equals(y);   // both true — always compare with equals!
```
**Why:** small integers are extremely common; caching avoids millions of tiny allocations. The lower bound is fixed at -128; the upper bound is tunable via `-XX:AutoBoxCacheMax`.

## NPE during unboxing
```java
Integer n = null;
int v = n; // NullPointerException — unboxing null
```
A `Map.get()` returning `null` assigned to a primitive is a classic production NPE.

## Interview Q&A
#### Q1. Why does `==` behave differently for `Integer 100` vs `200`?
**Answer:** Autoboxing calls `Integer.valueOf`, which returns cached objects for -128..127. So two boxed `100`s are the same cached instance (`==` true), but two `200`s are separate objects (`==` false). Always use `.equals()` for wrapper comparison.

**Counter Q:** Is Integer caching guaranteed for all values?
**Answer:** No — only the range -128..127 by default (upper bound configurable). Outside it, each boxing creates a new object.

**Counter Q:** What happens when a `null` Integer is unboxed into an `int`?
**Answer:** `NullPointerException` at the unboxing point. Common with `map.get()` or nullable DB columns.

**Counter Q:** Are wrapper objects immutable?
**Answer:** Yes, all wrappers are immutable — which is why caching is safe.

### Tricky
- *"`Long l = 127; long p = 127; l == p?`"* → true — comparing wrapper to primitive triggers unboxing, so value compare.
- *"Performance of autoboxing in a tight loop?"* → Boxing allocates objects and adds GC pressure; use primitives in hot loops.

### Quick Revision
- Wrappers box primitives for generics/collections/nullability.
- `Integer` cache = -128..127; use `equals`, never `==`.
- Unboxing `null` → NPE.
- Wrappers are immutable; autoboxing has a cost in hot paths.

---

# 6. Casting

## Primitive casting
- **Widening** (implicit, safe): `byte → short → int → long → float → double`. No data loss (except possible precision on int→float/long→double).
- **Narrowing** (explicit, may lose data): `double d = 9.99; int i = (int) d; // 9`.

## Object (reference) casting
- **Upcasting** (implicit, always safe): child → parent.
- **Downcasting** (explicit, checked at runtime): parent → child; can throw `ClassCastException`.

```java
Dog d = new Dog();
Animal a = d;          // UPCAST — always safe (Dog IS-A Animal)

Animal a2 = new Dog();
Dog d2 = (Dog) a2;     // DOWNCAST — safe here because a2 actually holds a Dog

Animal a3 = new Cat();
Dog bad = (Dog) a3;    // ClassCastException at runtime — a3 is a Cat, not a Dog
```

**Why upcast is safe:** a `Dog` genuinely has everything an `Animal` has, so the reference is valid.
**Why downcast is risky:** the compiler allows it (types are related) but at runtime the object may not actually be that subtype — hence the runtime check.

### `instanceof` guard (+ pattern matching, Java 16+)
```java
if (a instanceof Dog dog) {   // checks AND binds in one step
    dog.bark();
}
```
Guarding with `instanceof` avoids `ClassCastException`.

## Interview Q&A
#### Q1. What exactly happens in `Dog d = (Dog) animal;`?
**Answer:** The compiler permits it because `Dog` and `Animal` are in the same hierarchy. At runtime the JVM checks whether the referenced object is actually a `Dog` (or subtype). If yes, the cast succeeds; if not, it throws `ClassCastException`. The cast doesn't convert the object — it just changes the reference type you view it through.

**Counter Q:** Why is `Animal a = new Dog();` allowed without a cast?
**Answer:** It's an upcast — every `Dog` is an `Animal`, so it's always type-safe; the compiler needs no explicit cast.

**Counter Q:** How is casting connected to runtime polymorphism?
**Answer:** You typically hold objects via a supertype reference (upcast) and rely on dynamic dispatch to call the right overridden method. Downcasting is a code smell often signaling you should have used polymorphism instead of type checks.

### Tricky
- *"Does casting change the object?"* → No, only the reference view. The object's actual type and behavior (overridden methods) are unchanged.

---

# 7. Java Core Language

## Primitives & variables
- 8 primitives: `byte, short, int, long, float, double, char, boolean`.
- **Local variables** — on the stack, no default value (must be initialized before use).
- **Instance variables** — per object on the heap, defaulted (0/null/false).
- **Static/class variables** — one per class, in Metaspace-referenced storage, defaulted.
- **Scope**: local (block), instance (object), class (static).

## Java is ALWAYS pass-by-value
Java copies the **value** of the argument. For objects, the value is the **reference** (a copy of the pointer), not the object.
```java
void mutate(List<Integer> list) { list.add(1); }   // affects caller's list (same object)
void reassign(List<Integer> list) { list = new ArrayList<>(); } // does NOT affect caller
```
- You can mutate the object through the copied reference (visible to caller).
- Reassigning the parameter only changes the local copy — the caller's reference is untouched.
- **Why people think it's pass-by-reference:** because mutations through the reference are visible. But since reassignment isn't, it's provably pass-by-value (of the reference).

#### Q1. Is Java pass-by-value or pass-by-reference?
**Answer:** Always pass-by-value. For primitives the value is copied; for objects the *reference* is copied. You can mutate the shared object via the copy, but reassigning the parameter doesn't affect the caller — which proves it's pass-by-value.

**Counter Q:** Then why does modifying a list inside a method affect the caller?
**Answer:** Because both references (caller's and the copied parameter) point to the *same* object. Mutating that object is visible everywhere. Reassigning the parameter reference is not.

## Mutable vs immutable
Immutable objects (String, wrappers, `LocalDate`, records with immutable fields) can be shared freely and are thread-safe. Mutable objects need care when shared.

## Modern Java features
- **`switch` expressions** (Java 14): arrow labels, yields a value, exhaustive.
```java
String type = switch (day) {
    case SAT, SUN -> "weekend";
    default -> "weekday";
};
```
- **Records** (Java 16) — immutable data carriers; auto `equals`/`hashCode`/`toString`/accessors.
```java
public record Point(int x, int y) {}
```
- **Sealed classes** (Java 17) — restrict which classes can extend/implement.
```java
public sealed interface Shape permits Circle, Square {}
```
- **Pattern matching** — `instanceof` patterns, switch patterns (Java 21).
- **Text blocks** (Java 15) — multi-line string literals with `"""`.
- **`var`** (Java 10) — local variable type inference (compile-time; still statically typed).
- **`enum`** — type-safe constants that can hold state/behavior.
- **varargs** — `void f(int... nums)`; treated as an array; must be the last parameter.

### Interview Answer (pass-by-value)
> "Java is strictly pass-by-value. The confusion is that for objects the value being copied is the reference, so a method can mutate the shared object and the caller sees it. But if the method reassigns the parameter to a new object, the caller's reference is unaffected — that only makes sense under pass-by-value."

---

# 8. Exception Handling

## Hierarchy
```text
Throwable
├── Error                 (unchecked, JVM-level, don't catch: OutOfMemoryError, StackOverflowError)
└── Exception
    ├── RuntimeException   (UNCHECKED: NPE, IllegalArgument, IndexOutOfBounds, ...)
    └── (all others)       (CHECKED: IOException, SQLException, ...)
```
- **Checked** — compiler forces handle-or-declare. Represent recoverable, expected conditions (file missing, network down).
- **Unchecked** (`RuntimeException`) — programming errors; not forced to handle.
- **Error** — serious JVM problems; generally not caught.

## Keywords
- `try/catch/finally`, `throw` (raise), `throws` (declare).
- **multi-catch**: `catch (IOException | SQLException e)` — one block, exceptions must not be in a subclass relationship.
- **try-with-resources** — auto-closes resources implementing `AutoCloseable`, in reverse order, even on exception.
```java
try (var conn = dataSource.getConnection();
     var ps = conn.prepareStatement(sql)) {
    // ...
} // conn & ps closed automatically
```

## Suppressed exceptions
If the body throws and `close()` also throws, the `close()` exception is **suppressed** and attached to the primary (`getSuppressed()`).

## finally behavior & the traps
- `finally` runs almost always (except `System.exit()`, JVM crash, infinite loop, killed thread).
- **`return` in `finally` overrides** any return/throw from try/catch — an anti-pattern that swallows exceptions:
```java
int f() {
    try { return 1; }
    finally { return 2; } // returns 2, swallows the try's return AND any exception!
}
```
- If `try` returns a value, that value is computed first, then `finally` runs, then the value is returned — unless `finally` itself returns/throws.

## Custom exceptions
Create when you need a domain-specific, catchable type carrying context.
```java
public class InsufficientBalanceException extends RuntimeException {
    private final BigDecimal shortfall;
    public InsufficientBalanceException(BigDecimal shortfall) {
        super("Short by " + shortfall);
        this.shortfall = shortfall;
    }
    public BigDecimal getShortfall() { return shortfall; }
}
```
Prefer **unchecked** for programming/business errors in modern Spring apps (checked exceptions clutter service signatures and don't play well with lambdas/streams).

## Interview Q&A
#### Q1. Checked vs unchecked?
**Answer:** Checked extend `Exception` (not `RuntimeException`) and are compiler-enforced — used for recoverable, expected conditions. Unchecked extend `RuntimeException` and represent programming bugs; not enforced. `Error` is for unrecoverable JVM issues.

**Counter Q:** When should you create a custom exception?
**Answer:** When callers need to distinguish and react to a specific domain failure (e.g., `InsufficientBalanceException`) or you want to attach context. Otherwise reuse standard exceptions like `IllegalArgumentException`/`IllegalStateException`.

**Counter Q:** Why not `catch (Exception e)` everywhere?
**Answer:** It hides bugs (catching NPE/`RuntimeException` you didn't anticipate), swallows information, and prevents proper recovery. Catch the most specific type you can actually handle; let the rest propagate to a central handler (`@ControllerAdvice`).

**Counter Q:** What happens if `finally` throws?
**Answer:** The exception from `finally` replaces any exception/return from try/catch — the original is lost unless you use try-with-resources (which suppresses rather than discards). Avoid throwing/returning from `finally`.

**Counter Q:** What happens when `try` returns and `finally` also executes?
**Answer:** The try's return value is evaluated and held, `finally` runs, then the value returns — unless `finally` itself returns/throws, which overrides it.

### Tricky
- *"`try { return 1; } finally { return 2; }` returns?"* → 2.
- *"Can `finally` be skipped?"* → Only via `System.exit()`, JVM crash, or daemon thread death.
- *"Multi-catch with parent+child?"* → Compile error; types must be disjoint.

### Quick Revision
- Throwable → Error / Exception → RuntimeException(unchecked) + checked.
- try-with-resources auto-closes + suppresses secondary exceptions.
- Never return/throw from `finally`; never blanket-catch `Exception`.
- Prefer unchecked for business errors in Spring.

---

# 9. Collections

## Hierarchy (simplified)
```text
Iterable
└── Collection
    ├── List   (ordered, indexed, duplicates)   -> ArrayList, LinkedList, Vector, Stack
    ├── Set    (no duplicates)                   -> HashSet, LinkedHashSet, TreeSet
    └── Queue  (FIFO/priority)                   -> PriorityQueue, ArrayDeque, LinkedList
Map (NOT a Collection)                           -> HashMap, LinkedHashMap, TreeMap, Hashtable, ConcurrentHashMap
```

## List
| | Backing | Access | Insert/Delete (middle) | Thread-safe | Notes |
|---|---|---|---|---|---|
| `ArrayList` | dynamic array | O(1) index | O(n) shift | No | Default choice; resizes ~1.5x |
| `LinkedList` | doubly linked | O(n) | O(1) at ends (if you have node) | No | Also a `Deque`; poor cache locality |
| `Vector` | array | O(1) | O(n) | Yes (synchronized) | Legacy |
| `Stack` | extends Vector | — | — | Yes | Legacy; prefer `ArrayDeque` |

## Set
| | Backing | Order | Null | Notes |
|---|---|---|---|---|
| `HashSet` | HashMap | none | one null | O(1) avg |
| `LinkedHashSet` | LinkedHashMap | insertion | one null | predictable iteration |
| `TreeSet` | Red-Black tree | sorted | no null | O(log n); needs Comparable/Comparator |

## Map
| | Order | Null key | Thread-safe | Notes |
|---|---|---|---|---|
| `HashMap` | none | 1 | No | Default; O(1) avg |
| `LinkedHashMap` | insertion/access | 1 | No | LRU cache via `accessOrder` |
| `TreeMap` | sorted keys | no | No | O(log n); navigable |
| `Hashtable` | none | no | Yes (method-level) | Legacy |
| `ConcurrentHashMap` | none | no | Yes (fine-grained) | Concurrent default |
| `WeakHashMap` | none | 1 | No | keys GC'd when weakly reachable (caches) |
| `IdentityHashMap` | none | 1 | No | uses `==` not `equals` |

## Queue / Deque
- `PriorityQueue` — heap; orders by natural/Comparator; not thread-safe.
- `ArrayDeque` — fast stack & queue; preferred over `Stack`/`LinkedList`.
- `BlockingQueue` (`ArrayBlockingQueue`, `LinkedBlockingQueue`) — producer/consumer, blocks on full/empty.

## Fail-fast vs fail-safe iterators
- **Fail-fast** (`ArrayList`, `HashMap`): throw `ConcurrentModificationException` if the collection is structurally modified during iteration (via a `modCount` check). Use the iterator's `remove()` or collect-then-remove.
- **Fail-safe** (`CopyOnWriteArrayList`, `ConcurrentHashMap`): iterate over a snapshot/segment; no CME, but may not reflect latest changes.

## Interview Q&A
#### Q1. ArrayList vs LinkedList?
**Answer:** `ArrayList` is a dynamic array — O(1) random access, O(n) insert/remove in the middle (shifting), better cache locality. `LinkedList` is a doubly-linked list — O(1) insert/remove *if you already have the node*, but O(n) to reach an index and poor cache performance. In practice `ArrayList` wins almost always; `LinkedList` is rarely the right call.

**Counter Q:** So when is LinkedList actually better?
**Answer:** Rarely — mainly as a `Deque`/queue with frequent add/remove at both ends, or when you iterate and remove via the iterator. Even then `ArrayDeque` usually beats it.

**Counter Q:** HashMap vs Hashtable?
**Answer:** `Hashtable` is legacy, fully synchronized on every method (coarse lock), and disallows null keys/values. `HashMap` is unsynchronized, allows one null key. For concurrency use `ConcurrentHashMap`, not `Hashtable`.

**Counter Q:** HashMap vs ConcurrentHashMap?
**Answer:** `ConcurrentHashMap` is thread-safe using fine-grained locking (bin-level `synchronized` + CAS in Java 8, segments pre-8), allows concurrent reads and limited concurrent writes, and forbids null keys/values (ambiguity with "absent"). `HashMap` is faster single-threaded but not safe under concurrent structural modification.

### Tricky
- *"Can HashMap have a null key?"* → Yes, one (stored in bucket 0). *ConcurrentHashMap?* → No.
- *"Why does ConcurrentHashMap forbid null?"* → `get()` returning null would be ambiguous (absent vs null value) in a concurrent setting where you can't atomically follow with `containsKey`.

---

# 10. HashMap Internals

The single most-drilled collections topic. Master the full chain.

## Structure
A `HashMap` is an **array of buckets** (`Node<K,V>[] table`). Each bucket holds either a **linked list** or (after treeification) a **red-black tree** of entries.

## put(key, value) — step by step
1. Compute `key.hashCode()`.
2. **Hash spreading**: `hash = h ^ (h >>> 16)` — XORs the high bits into the low bits so that even hashCodes with weak low-bit distribution spread across buckets.
3. **Index** = `(n - 1) & hash` where `n` = capacity (power of two). Using `&` instead of `%` is fast and works because `n` is a power of two.
4. If the bucket is empty → place the node.
5. If occupied (**collision**):
   - Walk the list/tree; if a node with an **equal key** (`hash` matches AND `equals` true) exists → **replace value**.
   - Else append a new node.
6. If a bucket's list length ≥ **8** *and* table capacity ≥ **64** → **treeify** that bucket into a red-black tree (O(log n) lookups). If capacity < 64, it **resizes** instead.
7. If `size > threshold` (`capacity * loadFactor`) → **resize**.

## get(key)
1. Spread hash, compute index.
2. In the bucket, find the node where `hash` matches and `key.equals(node.key)` is true. Return its value or null.

## Key parameters
- **Default capacity** = 16, **load factor** = 0.75 → **threshold** = 12.
- **Load factor 0.75** is a time/space tradeoff: lower = fewer collisions but more memory; higher = less memory but more collisions.
- **Resize**: when threshold exceeded, capacity **doubles**, a new table is allocated, and all entries are **rehashed** into new buckets. In Java 8 rehashing is optimized — an entry either stays at index `i` or moves to `i + oldCapacity`, decided by one bit, avoiding full recomputation and preserving order (fixes the Java 7 concurrent-resize infinite-loop bug).
- **Treeification threshold** = 8; **untreeify** back to list at 6; **min tree capacity** = 64.

## Why Java 8 improved HashMap
Before Java 8, a bucket was always a linked list → worst case O(n) when many keys collide (also a DoS vector via crafted hash collisions). Java 8 converts long chains to a **red-black tree** → worst case O(log n). This matters when `hashCode` is poor or under adversarial input.

## Why mutable keys are dangerous
If you mutate a field used in `hashCode`/`equals` after insertion, the key now hashes to a different bucket than where it's stored → `get` can't find it. Use immutable keys.

## Interview Q&A
#### Q1. How does HashMap work internally?
**Answer:** It's an array of buckets. On put, it takes the key's hashCode, spreads the high bits into the low bits, and maps it to a bucket index via `(n-1) & hash`. If the bucket is empty it stores the node; on collision it walks the bucket comparing hash and `equals`, replacing on match or appending otherwise. When a bucket's chain gets long (≥8 with capacity ≥64) it treeifies to a red-black tree, and when size exceeds capacity×0.75 it doubles capacity and rehashes.

**Counter Q:** What happens when two keys have the same hash?
**Answer:** They land in the same bucket (a collision) and are stored together in a list or tree. On lookup, `equals` distinguishes them. Correctness is preserved; only performance degrades.

**Counter Q:** What if `equals()` returns false for two keys with the same hash?
**Answer:** They're treated as distinct keys and both stored in that bucket. That's normal collision handling.

**Counter Q:** Why must equals and hashCode be consistent?
**Answer:** get() first finds the bucket by hash, then matches by equals. If equal objects had different hashes, they'd go to different buckets and the map would fail to find/dedupe them.

**Counter Q:** What is load factor and when does resizing happen?
**Answer:** Load factor (default 0.75) is the fullness threshold ratio. When `size > capacity × loadFactor` (12 for default 16), the table doubles and entries are rehashed to keep chains short.

**Counter Q:** What is treeification and why?
**Answer:** Converting a bucket's linked list to a red-black tree once it holds ≥8 nodes (and capacity ≥64), giving O(log n) worst-case lookup instead of O(n) — protects against pathological/adversarial collisions.

**Counter Q:** Why capacity as a power of two?
**Answer:** So `(n-1) & hash` (cheap AND) equals `hash % n` for distribution, and so resize can split each bucket into two using a single bit test.

### Tricky
- *"Why `h ^ (h >>> 16)`?"* → To mix high bits into the index computation, since only low bits are used by `(n-1)&hash`.
- *"Does treeification make HashMap sorted?"* → No; the tree is per-bucket and ordered by hash/comparable for lookup, not overall iteration order.
- *"Can two different keys be in the same bucket without colliding hashes?"* → Yes — different hashes can map to the same index after `(n-1)&hash`.

### Quick Revision
- Array of buckets; list → red-black tree at 8 (cap ≥ 64).
- index = `(n-1) & (h ^ h>>>16)`.
- cap 16, LF 0.75, threshold 12; resize doubles + rehashes.
- equals+hashCode contract mandatory; use immutable keys.
- Java 8: treeify (O(log n)) + safer resize.

### Interview Answer (speak this)
> "HashMap is a bucket array. For a key it computes hashCode, spreads the high bits with `h ^ (h>>>16)`, and indexes with `(n-1) & hash`. Empty bucket stores the node; on collision it walks the bucket comparing hash then equals — replacing on a match, appending otherwise. Long chains (≥8 with capacity ≥64) become red-black trees for O(log n) worst case, and when size passes capacity times 0.75 it doubles and rehashes. That's why equals and hashCode must be consistent and keys should be immutable."

---

# 11. Comparable & Comparator

| | Comparable | Comparator |
|---|---|---|
| Package | `java.lang` | `java.util` |
| Method | `int compareTo(T o)` | `int compare(T a, T b)` |
| Ordering | **natural** (one, inside the class) | **external/custom** (many, outside) |
| Modifies class? | Yes (implements it) | No |

`compareTo`/`compare` return negative/zero/positive for less/equal/greater.

```java
record Employee(int id, String name, double salary) {}

// Natural ordering by id (Comparable) — put inside the class if you own it.
class EmpById implements Comparator<Employee> {
    public int compare(Employee a, Employee b) { return Integer.compare(a.id(), b.id()); }
}

// Modern, composable comparators:
List<Employee> emps = ...;
emps.sort(Comparator.comparingDouble(Employee::salary)          // by salary
        .reversed()                                             // descending
        .thenComparing(Employee::name));                        // tie-break by name

emps.sort(Comparator.comparing(Employee::name,
        Comparator.nullsFirst(Comparator.naturalOrder())));     // null-safe
```

## Interview Q&A
#### Q1. Comparable vs Comparator?
**Answer:** `Comparable` defines a single natural ordering inside the class via `compareTo`. `Comparator` defines external, reusable orderings via `compare`, letting one class have many sort strategies without modifying it. Use `Comparable` for the obvious default order; `Comparator` for everything else.

**Counter Q:** Can one class have multiple Comparators?
**Answer:** Yes — that's the point. You define separate `Comparator`s (by id, salary, name) and pass whichever you need to `sort`/`TreeMap`/`TreeSet`.

**Counter Q:** Can Comparable and Comparator be used together?
**Answer:** Yes — a class can implement `Comparable` for its default order, and callers can still override with a `Comparator` where needed (e.g., `sort(comparator)`).

**Counter Q:** What if a Comparator is inconsistent with equals?
**Answer:** Sorting still works, but `TreeSet`/`TreeMap` (which use comparison, not equals, for uniqueness) may treat "unequal-by-equals" elements as duplicates or vice versa — leading to lost entries. The Javadoc *strongly recommends* consistency with equals for sorted sets/maps.

### Tricky
- *"Return `a - b` for int comparison?"* → Risky: integer overflow for large values. Use `Integer.compare`.
- *"`TreeSet` uses equals or compareTo for uniqueness?"* → `compareTo`/`compare` (==0 means duplicate), NOT `equals`.

---

# 12. Generics

## What & Why
Generics provide **compile-time type safety** and eliminate casts. Instead of `List` holding `Object`, `List<String>` guarantees only Strings, caught at compile time.
```java
List<String> list = new ArrayList<>();
list.add("x");
String s = list.get(0); // no cast, no ClassCastException risk
```

## Generic classes, methods, interfaces
```java
class Box<T> { private T val; T get(){return val;} void set(T v){val=v;} }
<T> T firstOrNull(List<T> list) { return list.isEmpty() ? null : list.get(0); } // generic method
interface Repository<T, ID> { Optional<T> findById(ID id); }
```

## Type erasure
Generics exist only at **compile time**; the compiler erases type parameters to their bounds (or `Object`) and inserts casts. At runtime `List<String>` and `List<Integer>` are both just `List`.
**Consequences:**
- No `new T()`, no `T[]`, no `instanceof List<String>`.
- Can't have two overloads differing only by generic type (`f(List<String>)` vs `f(List<Integer>)` — same erasure).
- Enables backward compatibility with pre-generics code (raw types).

## Bounded types & wildcards (PECS)
- `<T extends Number>` — upper bound; `T` is a Number (can read as Number).
- `<? extends T>` — **Producer**: you can *read* T out, can't add (except null).
- `<? super T>` — **Consumer**: you can *add* T, reads come out as Object.
- **PECS**: *Producer Extends, Consumer Super*.
```java
// copies FROM src (producer, extends) INTO dst (consumer, super)
static <T> void copy(List<? extends T> src, List<? super T> dst) {
    for (T t : src) dst.add(t);
}
```
- `<?>` — unbounded wildcard; unknown type, read-only as Object.

## Interview Q&A
#### Q1. What is type erasure?
**Answer:** The compiler enforces generic types at compile time then removes them, replacing type parameters with their bounds and adding casts. At runtime generic type info is gone, which is why you can't do `new T()`, create `T[]`, or check `instanceof List<String>`. It exists for backward compatibility with legacy raw-typed code.

**Counter Q:** Explain PECS.
**Answer:** Producer Extends, Consumer Super. Use `<? extends T>` when a structure produces T values you read; use `<? super T>` when it consumes T values you write. It maximizes flexibility of the API while staying type-safe.

**Counter Q:** Why can't you add to a `List<? extends Number>`?
**Answer:** Because the actual type could be `List<Integer>` or `List<Double>` — the compiler can't guarantee what's safe to add, so it forbids all adds (except `null`). You can only read elements as `Number`.

**Counter Q:** What are raw types and why avoid them?
**Answer:** Using `List` instead of `List<String>` opts out of generic checking, reintroducing `ClassCastException` risk. They exist only for backward compatibility.

### Tricky
- *"Can `List<Object>` accept a `List<String>`?"* → No; generics are invariant. Use `List<? extends Object>` / `List<?>`.
- *"Two methods `f(List<String>)` and `f(List<Integer>)`?"* → Compile error (same erasure).

### Quick Revision
- Generics = compile-time type safety, no casts.
- Type erasure → no `new T()`, no `T[]`, no reified generic `instanceof`.
- PECS: producer `extends`, consumer `super`.
- Generics are invariant; use wildcards for flexibility.

---

# 13. Java 8+ Features

## Lambdas & functional interfaces
A **functional interface** has exactly one abstract method; a lambda is its concise implementation.
```java
Runnable r = () -> System.out.println("run");
Comparator<Integer> c = (a, b) -> a - b;
```
`@FunctionalInterface` enforces the single-abstract-method rule at compile time.

## Built-in functional interfaces (`java.util.function`)
| Interface | Signature | Use |
|---|---|---|
| `Predicate<T>` | `boolean test(T)` | filtering |
| `Function<T,R>` | `R apply(T)` | mapping/transform |
| `Consumer<T>` | `void accept(T)` | side effect (forEach) |
| `Supplier<T>` | `T get()` | lazy provide/factory |
| `BiFunction<T,U,R>` | `R apply(T,U)` | two-arg transform |
| `UnaryOperator<T>` | `T apply(T)` | same-type transform |
| `BinaryOperator<T>` | `T apply(T,T)` | reduce |

## Method & constructor references
```java
list.forEach(System.out::println);   // instance method of arbitrary object
Function<String,Integer> len = String::length;
Supplier<ArrayList<String>> f = ArrayList::new; // constructor reference
```

## Default & static interface methods
Covered in OOP — enable interface evolution and utility methods.

## Also in Java 8+
- **Streams** (Part 14), **Optional** (Part 15).
- **`java.time`** (JSR-310): immutable, thread-safe `LocalDate`, `LocalDateTime`, `Instant`, `Duration`, `Period`, `ZonedDateTime` — replaced the broken, mutable `Date`/`Calendar`.

#### Q1. Why the new Date/Time API?
**Answer:** Old `Date`/`Calendar` were mutable (not thread-safe), had confusing zero-based months, and poor API design. `java.time` is immutable, thread-safe, clearly separates date/time/instant/duration, and is ISO-8601 based.

---

# 14. Stream API

## What & Why
A `Stream` is a pipeline for declarative, functional-style processing of a sequence of elements. It separates *what* you want from *how* to iterate, enables lazy evaluation, and supports easy parallelism.

## Pipeline anatomy
```text
source -> intermediate ops (lazy) -> terminal op (eager, triggers execution)
```
- **Intermediate** (return a Stream, lazy): `map`, `filter`, `flatMap`, `distinct`, `sorted`, `peek`, `limit`, `skip`.
- **Terminal** (produce result/side effect, eager): `collect`, `reduce`, `forEach`, `count`, `min`/`max`, `findFirst`/`findAny`, `anyMatch`/`allMatch`/`noneMatch`, `toArray`.

## Lazy evaluation
Nothing runs until a terminal op. Ops are fused and elements flow one at a time (not stage-by-stage over the whole collection), enabling **short-circuiting** (`findFirst`, `limit`, `anyMatch`).
```java
list.stream()
    .filter(x -> { System.out.println("filter " + x); return x > 2; })
    .map(x -> { System.out.println("map " + x); return x * 10; })
    .findFirst();  // processes elements one-by-one, stops at first match
```

## A stream can't be reused
Once a terminal op runs, the stream is consumed; reusing throws `IllegalStateException`. Create a new stream from the source.

## Common operations
```java
// map / filter / collect
List<String> names = emps.stream()
    .filter(e -> e.salary() > 50000)
    .map(Employee::name)
    .collect(Collectors.toList());        // or .toList() (Java 16+, unmodifiable)

// flatMap (flatten nested)
List<String> allTags = posts.stream()
    .flatMap(p -> p.tags().stream())
    .distinct().toList();

// reduce
int total = nums.stream().reduce(0, Integer::sum);

// groupingBy
Map<Dept, List<Employee>> byDept =
    emps.stream().collect(Collectors.groupingBy(Employee::dept));

Map<Dept, Double> avgSalary =
    emps.stream().collect(Collectors.groupingBy(Employee::dept,
                          Collectors.averagingDouble(Employee::salary)));

// partitioningBy (boolean split)
Map<Boolean, List<Employee>> parts =
    emps.stream().collect(Collectors.partitioningBy(e -> e.salary() > 50000));

// joining, counting, mapping
String csv = names.stream().collect(Collectors.joining(", "));
Map<Dept, Long> counts = emps.stream()
    .collect(Collectors.groupingBy(Employee::dept, Collectors.counting()));
```

## Sequential vs parallel
`stream().parallel()` splits work across the common ForkJoinPool.
**Pitfalls:**
- Overhead often outweighs benefit for small/cheap workloads.
- Must be **stateless & side-effect free**; shared mutable state → race conditions.
- The **common pool is shared** JVM-wide — a blocking parallel stream can starve everything else.
- Order-sensitive ops cost more.
Use parallel only for large, CPU-bound, independent work — and measure.

## Side effects
Avoid mutating external state in `map`/`filter`/`peek`. `peek` is for debugging, not logic.

## Interview Q&A
#### Q1. Intermediate vs terminal operations?
**Answer:** Intermediate ops (`map`, `filter`) are lazy and return a new stream — they build the pipeline but don't execute. Terminal ops (`collect`, `reduce`, `forEach`) are eager and trigger the whole pipeline to run, producing a result or side effect. Without a terminal op nothing happens.

**Counter Q:** What does lazy evaluation buy you?
**Answer:** Elements are processed one at a time through fused stages, enabling short-circuiting (stop at first match) and avoiding building intermediate collections — better performance and the ability to work with infinite streams.

**Counter Q:** Can you reuse a stream?
**Answer:** No. After a terminal op the stream is consumed; reusing throws `IllegalStateException`. Recreate it from the source.

**Counter Q:** When would parallel streams hurt?
**Answer:** Small datasets (overhead dominates), IO/blocking tasks (starve the shared common pool), operations needing ordering, or any shared mutable state (race conditions). Also they use the JVM-wide common pool, affecting other tasks.

**Counter Q:** `findFirst` vs `findAny`?
**Answer:** `findFirst` respects encounter order (deterministic); `findAny` may return any element and is cheaper in parallel streams.

### Coding-style stream problems
```java
// Word frequency
Map<String,Long> freq = Arrays.stream(text.split("\\s+"))
    .collect(Collectors.groupingBy(w -> w, Collectors.counting()));

// First non-repeating char
Character firstUnique = str.chars().mapToObj(c -> (char)c)
    .collect(Collectors.groupingBy(c -> c, LinkedHashMap::new, Collectors.counting()))
    .entrySet().stream().filter(e -> e.getValue() == 1)
    .map(Map.Entry::getKey).findFirst().orElse(null);

// Top 3 salaries
List<Employee> top3 = emps.stream()
    .sorted(Comparator.comparingDouble(Employee::salary).reversed())
    .limit(3).toList();
```

### Quick Revision
- Lazy intermediates + eager terminal; nothing runs without terminal.
- Stream single-use; short-circuiting via lazy eval.
- `groupingBy`/`partitioningBy`/`counting`/`joining` for aggregation.
- Parallel = large CPU-bound stateless work only; watch the shared common pool.

---

# 15. Optional

## Why?
To make "value may be absent" explicit in the type system and reduce `NullPointerException`s. It's a container that holds a value or nothing.

## Creation & use
```java
Optional<User> u = repo.findById(id);           // returns Optional
Optional.of(x);        // x must be non-null (NPE if null)
Optional.ofNullable(x);// null -> empty
Optional.empty();

u.isPresent(); u.isEmpty();
u.ifPresent(user -> log.info(user.name()));
String name = u.map(User::name).orElse("unknown");
User user = u.orElseThrow(() -> new NotFoundException(id));
```

## `orElse` vs `orElseGet` (very common)
- `orElse(x)` — `x` is **always evaluated**, even when the Optional has a value.
- `orElseGet(supplier)` — supplier runs **only if empty** (lazy).
```java
opt.orElse(expensiveCall());       // expensiveCall() ALWAYS runs — wasteful/buggy
opt.orElseGet(() -> expensiveCall()); // runs only when empty
```
Use `orElseGet` when the fallback is expensive or has side effects.

## `map` vs `flatMap`
`map` wraps the result in Optional; `flatMap` is for when the mapper itself returns an Optional (avoids `Optional<Optional<T>>`).

## Good vs bad usage
- ✅ Return type of methods that may find nothing (`findById`).
- ❌ **Entity/DTO fields** — not `Serializable`, breaks JPA/Jackson, wastes memory.
- ❌ **Method parameters** — use overloads or `@Nullable` instead.
- ❌ Collections — return an empty collection, not `Optional<List>`.
- ❌ `opt.get()` without checking — defeats the purpose; use `orElseThrow`.

## Interview Q&A
#### Q1. Difference between orElse and orElseGet?
**Answer:** `orElse` takes a value that's computed eagerly regardless of whether the Optional is present — so if it's an expensive call it runs unnecessarily. `orElseGet` takes a Supplier evaluated only when the Optional is empty. Prefer `orElseGet` for expensive or side-effecting fallbacks.

**Counter Q:** Should Optional be used as a field or method parameter?
**Answer:** No. Optional was designed as a return type. As a field it isn't serializable and breaks JPA/Jackson; as a parameter it adds noise and callers still pass null. Use overloads/`@Nullable` for params and plain fields for entities.

**Counter Q:** `Optional.of` vs `ofNullable`?
**Answer:** `of` throws NPE if the argument is null; `ofNullable` maps null to `empty()`. Use `of` only when you know it's non-null.

### Quick Revision
- Optional = explicit maybe-absent; reduces NPEs.
- `orElse` eager, `orElseGet` lazy.
- Return type only — not fields/params/collections.
- Prefer `map`/`orElseThrow`/`ifPresent` over `get()`.

---

# 16. Multithreading & Concurrency

## Process vs Thread
- **Process** — independent program with its own memory space.
- **Thread** — lightweight unit within a process; threads share heap/memory but have their own stack. Cheaper to create/switch; communication via shared memory (needs synchronization).

## Thread lifecycle (states)
`NEW → RUNNABLE → (RUNNING) → BLOCKED / WAITING / TIMED_WAITING → TERMINATED`.

## Creating tasks
```java
// Runnable (no result)
Runnable r = () -> doWork();
new Thread(r).start();

// Callable (returns value, can throw)
Callable<Integer> c = () -> compute();
Future<Integer> f = executor.submit(c);
Integer result = f.get(); // blocks until done
```

## ExecutorService & thread pools
Don't create raw threads in production — use pools to bound resources and reuse threads.
```java
ExecutorService fixed  = Executors.newFixedThreadPool(10);      // bounded
ExecutorService cached = Executors.newCachedThreadPool();        // grows/shrinks, unbounded (risky)
ScheduledExecutorService sched = Executors.newScheduledThreadPool(2);
ExecutorService single = Executors.newSingleThreadExecutor();
// Java 21: Executors.newVirtualThreadPerTaskExecutor() — virtual threads (Project Loom)
```
- **ThreadPoolExecutor** — core/max pool size, keep-alive, **work queue**, **rejection policy**. Prefer configuring it directly over `Executors` factory methods (which can create unbounded queues/pools → OOM).
- **ForkJoinPool** — divide-and-conquer work stealing; backs parallel streams.

## Synchronization
- **`synchronized`** method/block uses the object's **intrinsic lock (monitor)**. Only one thread holds it at a time; provides mutual exclusion + visibility (happens-before).
```java
synchronized (lockObj) { /* critical section */ }
```
- **ReentrantLock** — explicit lock; supports `tryLock`, timed lock, interruptible lock, fairness. Must `unlock()` in `finally`.
- **ReadWriteLock** — many readers OR one writer; good for read-heavy data.

## volatile vs atomic
- **`volatile`** — guarantees **visibility** and ordering (no caching in registers; reads/writes go to main memory) but **not atomicity** of compound ops (`count++` is still a race).
- **Atomic classes** (`AtomicInteger`, `AtomicLong`, `AtomicReference`) — lock-free atomic operations using **CAS** (Compare-And-Swap): a CPU instruction that updates only if the current value matches the expected value, retrying on failure.
```java
AtomicInteger counter = new AtomicInteger();
counter.incrementAndGet(); // atomic, lock-free
```

## Concurrency hazards
- **Race condition** — result depends on thread timing on shared mutable state.
- **Deadlock** — two threads each hold a lock the other needs (circular wait). Avoid by consistent lock ordering, `tryLock` with timeout.
- **Livelock** — threads keep reacting to each other, never progressing.
- **Starvation** — a thread never gets CPU/lock (e.g., unfair locks, low priority).

## wait/notify vs sleep vs join
- `wait()` — releases the lock and waits until `notify()`/`notifyAll()`; must be inside `synchronized`. Used for inter-thread coordination.
- `notify()` wakes one waiting thread; `notifyAll()` wakes all (safer, avoids missed signals).
- `sleep(ms)` — pauses the thread, **keeps its locks**; no coordination.
- `join()` — wait for another thread to finish.
- `interrupt()` — cooperative cancellation signal; the thread must check `isInterrupted()`/handle `InterruptedException`.
- **Daemon thread** — background thread; JVM exits when only daemons remain.

## Interview Q&A
#### Q1. synchronized vs Lock (ReentrantLock)?
**Answer:** `synchronized` is simpler — implicit acquire/release tied to a block/method, auto-released on exit/exception, but no timeout, no interruptibility, no fairness. `ReentrantLock` is explicit and flexible: `tryLock`, timed and interruptible locking, fairness policy, and multiple condition variables — at the cost of a mandatory `unlock()` in `finally`. Use `synchronized` by default; `Lock` when you need its extra capabilities.

**Counter Q:** volatile vs Atomic?
**Answer:** `volatile` only guarantees visibility/ordering, not atomicity — `volatile int x; x++` is still a race because it's read-modify-write. Atomic classes give atomic compound operations via CAS without locks. Use `volatile` for simple flags, Atomics for counters/accumulators.

**Counter Q:** What is CAS and its downside?
**Answer:** Compare-And-Swap atomically sets a value only if it currently equals an expected value, else retries. It's lock-free and fast under low contention, but under high contention threads spin retrying (wasted CPU), and it's vulnerable to the ABA problem (mitigated by `AtomicStampedReference`).

**Counter Q:** sleep vs wait?
**Answer:** `sleep` is a static `Thread` method that pauses the current thread and **keeps** any locks; `wait` is an `Object` method that **releases** the lock and suspends until notified. `wait/notify` is for coordination; `sleep` is just a timed pause.

**Counter Q:** notify vs notifyAll?
**Answer:** `notify` wakes a single arbitrary waiting thread; `notifyAll` wakes all, which then re-contend for the lock. `notifyAll` is safer when multiple conditions share a lock, to avoid lost wakeups.

**Counter Q:** Runnable vs Callable?
**Answer:** `Runnable.run()` returns void and can't throw checked exceptions; `Callable.call()` returns a value and can throw. Submit a `Callable` to an `ExecutorService` to get a `Future`.

**Counter Q:** How do you prevent deadlock?
**Answer:** Acquire locks in a consistent global order, use `tryLock` with timeouts, minimize lock scope, and prefer higher-level concurrency utilities over manual locking.

### Tricky
- *"Is `count++` thread-safe if `count` is volatile?"* → No; volatile ≠ atomic for read-modify-write.
- *"Can you call `wait()` outside synchronized?"* → No — `IllegalMonitorStateException`.
- *"Why bound thread pools?"* → Unbounded pools/queues can exhaust memory or threads under load.

### Quick Revision
- Threads share heap, own stacks; use pools not raw threads.
- synchronized = simple; ReentrantLock = flexible (tryLock/timeout/fair).
- volatile = visibility only; Atomic/CAS = lock-free atomicity.
- wait releases lock (needs synchronized); sleep keeps it.
- Deadlock: consistent lock ordering + timeouts.

### Interview Answer (speak this)
> "Threads within a process share memory but have separate stacks, so shared mutable state needs synchronization. I use `ExecutorService` with bounded pools rather than raw threads. For mutual exclusion `synchronized` is my default; `ReentrantLock` when I need tryLock, timeouts, or fairness. `volatile` gives visibility for flags but not atomicity, so for counters I use `AtomicInteger`, which uses lock-free CAS. Deadlocks I avoid with consistent lock ordering and timed locks."

---

# 17. CompletableFuture

## What & Why
`Future` (Java 5) lets you submit async work but only offers a blocking `get()` — no chaining, no combining, no callbacks. `CompletableFuture` (Java 8) is a composable, non-blocking async pipeline: chain, combine, and handle errors without blocking.

## Starting async work
```java
CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> fetchUser()); // returns value
CompletableFuture<Void> cf2   = CompletableFuture.runAsync(() -> doJob());        // no result
```
Without an Executor arg, tasks run on the **common ForkJoinPool** — fine for CPU-bound, dangerous for blocking IO. **Pass a custom Executor** for IO-bound work.

## Chaining & combining
```java
CompletableFuture.supplyAsync(() -> loadOrder(id))          // stage 1
    .thenApply(order -> enrich(order))                       // transform (sync w/ result)
    .thenCompose(o -> callInventoryServiceAsync(o))          // flatMap: chain another CF
    .thenCombine(pricingFuture, (order, price) -> merge(order, price)) // combine two CFs
    .thenAccept(result -> log.info("done {}", result))       // consume
    .exceptionally(ex -> { log.error("failed", ex); return null; }); // recover
```
- `thenApply` (map) vs `thenCompose` (flatMap — mapper returns a CF).
- `thenAccept` (consume result), `thenRun` (run, ignore result).
- **`allOf`** — wait for all; **`anyOf`** — complete when any completes.
- **Error handling**: `exceptionally` (recover from error), `handle` (handle both success/error), `whenComplete` (side-effect callback, doesn't transform).
- **async variants** (`thenApplyAsync`) run the callback on a pool instead of the completing thread.

## Real backend example: parallel service calls
```java
Executor ioPool = Executors.newFixedThreadPool(20);
CompletableFuture<Profile>  p = CompletableFuture.supplyAsync(() -> profileClient.get(id), ioPool);
CompletableFuture<Orders>   o = CompletableFuture.supplyAsync(() -> orderClient.get(id), ioPool);
CompletableFuture<Rewards>  r = CompletableFuture.supplyAsync(() -> rewardClient.get(id), ioPool);

CompletableFuture.allOf(p, o, r).join(); // wait for all in parallel
Dashboard dash = new Dashboard(p.join(), o.join(), r.join());
```
This runs three service calls concurrently instead of sequentially — a common latency optimization for aggregation endpoints.

## Interview Q&A
#### Q1. Future vs CompletableFuture?
**Answer:** `Future` only lets you submit a task and block on `get()` — no composition or callbacks. `CompletableFuture` is non-blocking and composable: you can chain transformations (`thenApply`/`thenCompose`), combine multiple futures (`thenCombine`/`allOf`), and handle errors (`exceptionally`/`handle`) as a pipeline, and complete it manually.

**Counter Q:** thenApply vs thenCompose?
**Answer:** `thenApply` maps the result to a plain value (like `map`). `thenCompose` is for when your function itself returns a `CompletableFuture` — it flattens to avoid nested futures (like `flatMap`). Use `thenCompose` to chain dependent async calls.

**Counter Q:** Why pass a custom Executor?
**Answer:** The default common ForkJoinPool is sized for CPU-bound work and shared JVM-wide. Blocking IO on it starves other tasks (parallel streams, other CFs). A dedicated pool isolates blocking work and lets you size it for IO concurrency.

**Counter Q:** exceptionally vs handle vs whenComplete?
**Answer:** `exceptionally` recovers only on error, returning a fallback. `handle` receives both result and exception and returns a new value (handles both paths). `whenComplete` observes result/exception for side effects but doesn't change the result.

### Quick Revision
- CF = composable, non-blocking async; Future = blocking only.
- supplyAsync (value) / runAsync (void); pass Executor for IO.
- thenApply=map, thenCompose=flatMap, thenCombine=zip.
- allOf/anyOf; exceptionally/handle/whenComplete for errors.

---

# 18. I/O, NIO & Serialization

## Classic I/O
- **Byte streams**: `InputStream`/`OutputStream` (binary).
- **Character streams**: `Reader`/`Writer` (text, charset-aware).
- **Buffered** wrappers (`BufferedReader`, `BufferedInputStream`) reduce syscalls by batching → big performance win.
- Blocking, stream-oriented (read sequentially).
```java
try (var br = new BufferedReader(new FileReader("in.txt"))) {
    String line; while ((line = br.readLine()) != null) process(line);
}
```

## NIO / NIO.2
- **Channels + Buffers** — read/write into `ByteBuffer`s; can be non-blocking; `Selector` allows one thread to manage many channels (scalable servers).
- **`Path` / `Files`** (NIO.2, Java 7) — modern file API:
```java
Path p = Path.of("data.txt");
List<String> lines = Files.readAllLines(p);
Files.writeString(p, "hello");
try (Stream<String> s = Files.lines(p)) { s.forEach(System.out::println); }
```
- **IO vs NIO**: IO is blocking/stream-oriented (simple, one thread per connection); NIO is buffer-oriented and can be non-blocking (fewer threads, scalable). Netty/Tomcat NIO connectors build on this.

## Serialization
Converting an object graph to bytes and back.
- Implement **`Serializable`** (marker interface).
- **`serialVersionUID`** — version identifier; if it doesn't match on deserialize → `InvalidClassException`. Declare it explicitly to control compatibility.
- **`transient`** — field excluded from serialization (e.g., passwords, caches, derived data).
- **`Externalizable`** — you implement `writeExternal`/`readExternal` for full manual control (faster/smaller, more code).

```java
class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private transient String password; // not serialized
}
```

## Interview Q&A
#### Q1. What is serialVersionUID?
**Answer:** A version stamp for a `Serializable` class. During deserialization the JVM compares the stream's UID with the class's; a mismatch throws `InvalidClassException`. If you don't declare it, the compiler generates one from the class structure, so any change breaks compatibility — declaring it explicitly gives you control.

**Counter Q:** What does `transient` do?
**Answer:** Marks a field to be skipped during serialization; on deserialize it gets the default value. Used for sensitive data, caches, or non-serializable/derived fields.

**Counter Q:** Serializable vs Externalizable?
**Answer:** `Serializable` is automatic (JVM handles it via reflection, with optional `writeObject`/`readObject` hooks). `Externalizable` requires you to implement the full read/write logic — more control and often better performance, but more code and you must handle versioning yourself.

**Counter Q:** IO vs NIO?
**Answer:** Classic IO is blocking and stream-oriented — simple but needs a thread per connection. NIO is buffer/channel-oriented and supports non-blocking selectors, letting one thread handle many connections — the basis for scalable servers.

### Tricky
- *"Are static fields serialized?"* → No; serialization is about instance state.
- *"What if a superclass isn't Serializable?"* → Its state isn't serialized and it must have a no-arg constructor (called on deserialize).
- Note: Java serialization has security risks (deserialization attacks); many teams prefer JSON/Protobuf for external data.

---

# 19. JDBC

## What is it?
JDBC (Java Database Connectivity) is the standard Java API for relational databases. It defines interfaces (`Connection`, `Statement`, `ResultSet`) that vendor **drivers** implement, so your code is DB-agnostic.

## Architecture
```text
App -> JDBC API -> DriverManager / DataSource -> JDBC Driver -> Database
```
- **Driver** — vendor implementation (e.g., PostgreSQL, MySQL). Loaded via SPI (auto-registered in JDBC 4+).
- **DriverManager / DataSource** — obtain `Connection`s. In apps, use a **`DataSource`** with a connection pool (HikariCP), not `DriverManager`.

## Core objects
- **Connection** — a session with the DB.
- **Statement** — static SQL (avoid for user input — SQL injection).
- **PreparedStatement** — precompiled, parameterized SQL (`?` placeholders); prevents injection, allows reuse, supports batching.
- **CallableStatement** — invokes stored procedures.
- **ResultSet** — cursor over query results.
- `executeQuery` (SELECT → ResultSet), `executeUpdate` (INSERT/UPDATE/DELETE → row count), `execute` (either).

## Complete example
```java
String sql = "SELECT id, name FROM users WHERE dept = ? AND active = ?";
try (Connection conn = dataSource.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql)) {

    ps.setString(1, dept);
    ps.setBoolean(2, true);

    try (ResultSet rs = ps.executeQuery()) {
        List<User> users = new ArrayList<>();
        while (rs.next()) {
            users.add(new User(rs.getLong("id"), rs.getString("name")));
        }
        return users;
    }
} catch (SQLException e) {
    throw new DataAccessException("query failed", e);
}
```

## Transactions
```java
conn.setAutoCommit(false);        // start a transaction
try {
    // multiple statements
    debit(conn, from, amount);
    credit(conn, to, amount);
    conn.commit();                // all-or-nothing
} catch (SQLException e) {
    conn.rollback();              // undo on failure
    throw e;
} finally {
    conn.setAutoCommit(true);
}
```
By default `autoCommit` is true (each statement commits). Set false to group statements atomically.

## Batch processing
```java
for (var u : users) { ps.setString(1, u.name()); ps.addBatch(); }
ps.executeBatch(); // one round trip, far faster than N individual inserts
```

## Why PreparedStatement (SQL injection)
Concatenating user input into SQL lets an attacker inject SQL (`'; DROP TABLE users; --`). `PreparedStatement` sends the query structure and parameters **separately**; parameters are treated as data, never parsed as SQL. It's also precompiled (reusable) and type-safe.

## Connection pooling
Opening a DB connection is expensive (TCP + auth + session setup). A pool (HikariCP) keeps warm connections and hands them out, drastically cutting latency and bounding concurrent connections. **Not closing a connection** leaks it from the pool → eventually pool exhaustion → new requests block/timeout.

## JDBC vs JPA vs Hibernate
- **JDBC** — low-level, manual SQL and mapping; full control, most boilerplate.
- **JPA** — a *specification* for ORM (annotations, `EntityManager`, JPQL).
- **Hibernate** — the most popular *implementation* of JPA (plus extra features). You typically code to JPA, running Hibernate underneath.

## Interview Q&A
#### Q1. Statement vs PreparedStatement?
**Answer:** `Statement` sends raw SQL and is vulnerable to injection and re-parsing every call. `PreparedStatement` is precompiled with `?` parameters bound separately from the SQL text, which prevents SQL injection, enables driver-side caching/reuse, and supports batching. Always prefer `PreparedStatement` for parameterized queries.

**Counter Q:** Why exactly does PreparedStatement prevent SQL injection?
**Answer:** Because the SQL structure is fixed and compiled before parameters are supplied. Parameters are bound as typed data, not concatenated into the SQL string, so input can never change the query's structure.

**Counter Q:** What is connection pooling and why?
**Answer:** A cache of reusable DB connections. Establishing a connection is costly, so the pool reuses warm ones, reducing latency and capping total connections. HikariCP is the common choice (Spring Boot default).

**Counter Q:** What happens if you don't close a connection?
**Answer:** It never returns to the pool — a connection leak. Under load the pool exhausts, and new requests block until timeout, causing cascading failures. Use try-with-resources or a framework that manages it.

**Counter Q:** How does a JDBC transaction work?
**Answer:** Set `autoCommit(false)`, run multiple statements, then `commit()` to persist all atomically or `rollback()` to undo on error. Isolation level controls concurrent visibility.

### Quick Revision
- JDBC = standard DB API; drivers implement it.
- PreparedStatement: precompiled, parameterized → no injection, batching.
- autoCommit off → manual commit/rollback for atomicity.
- Use a pooled DataSource (HikariCP); always close connections.
- JDBC < JPA (spec) ≈ Hibernate (impl).

### Interview Answer (speak this)
> "JDBC is the standard Java API where vendor drivers implement `Connection`, `PreparedStatement`, and `ResultSet`. I use `PreparedStatement` with bound parameters — the SQL is compiled separately from the data, so injection is impossible and I get batching and reuse. For transactions I turn off autoCommit, run the statements, then commit or rollback. In real apps I use a pooled `DataSource` like HikariCP because opening connections is expensive, and I always close via try-with-resources to avoid leaking connections and exhausting the pool."

---

# 20. POJO / JavaBean / DTO

## POJO (Plain Old Java Object)
A plain object with no framework-imposed restrictions — doesn't extend/implement framework classes, no forced conventions.
- Does **not** require getters/setters or a default constructor.
- **Can** have annotations (annotations don't disqualify a POJO; being tied to a framework base class/interface does).

## JavaBean
A POJO that follows specific conventions:
- `private` fields with **public getters/setters** (`getX`/`setX`, `isX` for boolean).
- **Public no-arg constructor**.
- Historically **`Serializable`**.
Beans are introspectable by tools/frameworks (e.g., Jackson, JSP, Spring).

**POJO vs JavaBean:** every JavaBean is a POJO, but not every POJO is a JavaBean. JavaBean adds the no-arg-constructor + accessor + serializable conventions.

## DTO (Data Transfer Object)
An object used to **carry data across boundaries** (API ↔ client, service ↔ service), decoupled from your persistence entities.
- **Request DTO** — shape of incoming payloads (with validation annotations).
- **Response DTO** — shape of outgoing payloads (only the fields the client should see).
- **Mapping** — convert Entity ↔ DTO manually or with MapStruct/ModelMapper.

## Why not expose the Entity directly?
- **Security** — entities may contain sensitive fields (password hash, internal flags).
- **Coupling** — exposing entities ties your API contract to your DB schema; a column rename breaks clients.
- **Lazy loading** — serializing a JPA entity can trigger lazy loads / `LazyInitializationException` outside a session, or accidental N+1.
- **Over-fetching** — clients get fields they shouldn't; DTOs expose exactly what's needed.
- **Validation/versioning** — DTOs let request/response evolve independently of the schema.

## Interview Q&A
#### Q1. POJO vs JavaBean vs DTO?
**Answer:** A POJO is any plain object not bound to a framework. A JavaBean is a POJO following conventions — private fields, public no-arg constructor, getters/setters, historically Serializable — so tools can introspect it. A DTO is a purpose-built object to transfer data across layers/boundaries, decoupling the API from entities.

**Counter Q:** Why use DTOs instead of returning entities?
**Answer:** To decouple the API contract from the DB schema, hide sensitive/internal fields, avoid lazy-loading serialization problems, prevent over-fetching, and let request/response models evolve and be validated independently.

**Counter Q:** Does a POJO need getters/setters?
**Answer:** No. That's a JavaBean convention. A POJO just means "not tied to a framework"; it can have public fields, no accessors, and no default constructor.

### Quick Revision
- POJO = plain, framework-free (annotations OK).
- JavaBean = POJO + no-arg ctor + accessors + (Serializable).
- DTO = boundary data carrier; never expose entities directly.

---

# 21. Hibernate

## What & Why
Hibernate is an **ORM** (Object-Relational Mapping) framework and the leading JPA implementation. It maps Java objects to DB tables, generates SQL, manages the persistence lifecycle, caching, and lazy loading — removing most JDBC boilerplate.

## Architecture
```text
SessionFactory (thread-safe, one per DB, expensive to build)
   └── Session / EntityManager (per unit-of-work, NOT thread-safe)
         └── Persistence Context (first-level cache, tracks managed entities)
               └── Transaction -> JDBC -> Database
```
- **SessionFactory** — heavyweight, immutable, shared. (`EntityManagerFactory` in JPA terms.)
- **Session** — short-lived unit of work per request/transaction. (`EntityManager` in JPA.)
- **Persistence Context** — the set of managed entities; provides the first-level cache and dirty checking.

## Entity lifecycle states
- **Transient** — new object, not associated with a session, no DB row.
- **Persistent (Managed)** — associated with a session; changes are auto-tracked and flushed.
- **Detached** — was persistent, but session closed; changes not tracked until reattached (`merge`).
- **Removed** — marked for deletion, removed on flush/commit.

## save / persist / update / merge
- `persist()` (JPA) — makes transient entity managed; no return; must be in a transaction.
- `save()` (Hibernate) — like persist but returns the generated id and can run outside a transaction.
- `merge()` — copies a **detached** entity's state into a managed instance and **returns the managed copy** (the argument stays detached).
- `update()` (Hibernate) — reattaches a detached entity; throws if an entity with that id is already managed.

## get vs load (Hibernate)
- `get()` — hits the DB immediately; returns `null` if not found.
- `load()` — returns a **proxy** without hitting the DB; DB accessed on first property use; throws `ObjectNotFoundException` if missing. (JPA equivalents: `find` = get, `getReference` = load.)

## Caching
- **First-level cache** — the persistence context; per-Session, always on. Repeated `find` by id in one session returns the cached instance (no second query).
- **Second-level cache** — optional, SessionFactory-wide, shared across sessions (EhCache/Caffeine/Redis). Caches entities/collections across transactions.
- **Query cache** — caches query result *ids*; needs the 2nd-level cache too.

## Dirty checking
Within a session, Hibernate compares managed entities against their loaded snapshot and auto-generates UPDATEs on flush for changed fields — you don't call `update()` explicitly on managed entities.

## Lazy vs eager loading & proxies
- **LAZY** — associated data loaded on first access via a proxy/collection wrapper. Default for `@OneToMany`/`@ManyToMany`.
- **EAGER** — loaded immediately with the parent. Default for `@ManyToOne`/`@OneToOne`.
- Accessing a LAZY association after the session closes → **`LazyInitializationException`**.

## The N+1 problem (critical)
Loading N parents then triggering 1 query per parent for a lazy association = **1 + N queries**.
```java
List<Order> orders = repo.findAll();        // 1 query
for (Order o : orders) o.getItems().size(); // N queries (one per order) -> N+1
```
**Fixes:**
- **JOIN FETCH** in JPQL: `SELECT o FROM Order o JOIN FETCH o.items`.
- **`@EntityGraph`** to declare fetch paths on repository methods.
- **Batch fetching** (`@BatchSize` / `hibernate.default_batch_fetch_size`) — loads associations in IN-clauses of N.
- Use projections/DTOs when you don't need full entities.

## Locking
- **Optimistic** (`@Version`) — no DB lock; on update Hibernate checks the version column; mismatch → `OptimisticLockException`. Best for low-contention, high-throughput.
- **Pessimistic** (`SELECT ... FOR UPDATE`) — DB row lock held for the transaction; blocks others. For high-contention critical sections.

## Cascade & orphanRemoval
- **Cascade** propagates operations (PERSIST/MERGE/REMOVE...) from parent to children.
- **orphanRemoval=true** — removing a child from the parent's collection deletes it from the DB (stronger than cascade REMOVE).

## Interview Q&A
#### Q1. Explain the entity lifecycle.
**Answer:** Transient — a new object with no DB identity and not managed. Persistent/managed — attached to a session, tracked, changes auto-flushed via dirty checking. Detached — session closed, no longer tracked until you `merge` it back. Removed — scheduled for deletion. Transitions happen via persist, find/query, session close, merge, and remove.

**Counter Q:** save vs persist vs merge?
**Answer:** `persist` makes a transient entity managed (void, needs a transaction). `save` is Hibernate-specific, returns the id. `merge` takes a detached (or transient) entity, copies its state into a managed instance, and returns that managed copy — the passed object remains detached. So after merge you must use the returned reference.

**Counter Q:** get vs load?
**Answer:** `get` queries the DB immediately and returns null if absent. `load` returns a lazy proxy without hitting the DB, loading on first access and throwing if the row doesn't exist. Use `load`/`getReference` when you only need a reference (e.g., to set a foreign key).

**Counter Q:** Why does the N+1 problem happen and how do you fix it?
**Answer:** It happens when you load a list of parents and then lazily access an association per parent, firing one query each — 1 + N total. Fix with `JOIN FETCH`, an `@EntityGraph`, batch fetching, or DTO projections that fetch everything in one/few queries.

**Counter Q:** First-level vs second-level cache?
**Answer:** First-level is the persistence context, per-session, always on — dedupes lookups within one unit of work. Second-level is shared across sessions at the SessionFactory level, opt-in, backed by a provider like EhCache/Redis, caching entities across transactions.

**Counter Q:** Optimistic vs pessimistic locking?
**Answer:** Optimistic uses a version column and detects conflicts at commit (no DB lock) — great for low contention. Pessimistic locks the row in the DB up front (`FOR UPDATE`), serializing access — for high-contention critical updates at the cost of blocking.

### Tricky
- *"Why LazyInitializationException?"* → Accessing a lazy association after the session/transaction closed. Fix by fetching within the transaction (JOIN FETCH/EntityGraph) or mapping to a DTO inside the transaction.
- *"Does dirty checking need `save()`?"* → No; managed entities are auto-updated on flush.

### Quick Revision
- SessionFactory (shared) → Session/EntityManager (per unit of work) → persistence context (L1 cache + dirty checking).
- States: transient/persistent/detached/removed.
- merge returns managed copy; get=DB now, load=proxy.
- N+1 → JOIN FETCH / EntityGraph / batch / DTO.
- Optimistic (@Version) vs pessimistic (FOR UPDATE).

### Interview Answer (speak this — N+1)
> "The N+1 problem is loading N parent entities in one query, then triggering one extra query per parent when I touch a lazy association — so 1 + N round trips that kill performance. I detect it in SQL logs, then fix it by fetching the association eagerly for that use case with a JOIN FETCH query or an `@EntityGraph`, or by enabling batch fetching so Hibernate loads children in IN-clauses. When I only need a few fields I map straight to a DTO projection."

---

# 22. JPA

## What & JPA vs Hibernate
- **JPA (Jakarta/Java Persistence API)** is a **specification** — a set of interfaces and annotations (`EntityManager`, `@Entity`, JPQL) for ORM.
- **Hibernate** is the most common **implementation** (provider) of that spec. Others: EclipseLink, OpenJPA.
- You code to the JPA API for portability; Hibernate runs underneath. Vendor-specific features (e.g., `@BatchSize`) are Hibernate extensions.

## Core annotations
```java
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) // IDENTITY | SEQUENCE | TABLE | AUTO
    private Long id;

    @Column(name = "full_name", nullable = false, length = 100)
    private String name;

    @Enumerated(EnumType.STRING)   // store enum as text, NOT ORDINAL (ordinal breaks on reorder)
    private Status status;

    @Embedded private Address address;

    @ManyToOne(fetch = FetchType.LAZY)   // override default EAGER for *ToOne
    @JoinColumn(name = "dept_id")
    private Department department;
}

@Embeddable
public class Address { private String street; private String city; }
```
- `@GeneratedValue`: IDENTITY (DB auto-increment, no batching), SEQUENCE (DB sequence, batch-friendly, preferred on Postgres/Oracle), TABLE (portable but slow).

## Relationships
```java
// One department has many employees; Employee owns the FK.
@Entity class Department {
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Employee> employees = new ArrayList<>();
}
@Entity class Employee {
    @ManyToOne @JoinColumn(name = "dept_id") private Department department; // OWNING side (has FK)
}
```
- **Owning side** holds the foreign key and controls persistence of the relationship.
- **`mappedBy`** marks the **inverse** side (no FK column); it points to the field on the owning side. Without it you get an extra join table / duplicate FK.
- Default fetch: `@ManyToOne`/`@OneToOne` = EAGER; `@OneToMany`/`@ManyToMany` = LAZY. Best practice: make everything LAZY and fetch explicitly.

## JPQL vs native vs Criteria
```java
// JPQL — operates on entities/fields, DB-independent
@Query("SELECT e FROM Employee e WHERE e.salary > :min")
List<Employee> highEarners(@Param("min") double min);

// Native SQL
@Query(value = "SELECT * FROM employees WHERE salary > ?1", nativeQuery = true)
List<Employee> highEarnersNative(double min);
```
- **JPQL** — object-oriented query language over entities.
- **Criteria API** — programmatic, type-safe dynamic queries (verbose).
- **Specifications** (Spring Data) — composable Criteria predicates for dynamic filtering.
- **Projections** — return interfaces/DTOs instead of full entities for efficiency.

## Interview Q&A
#### Q1. JPA vs Hibernate?
**Answer:** JPA is the specification — the standard annotations and `EntityManager` API. Hibernate is a concrete implementation of that spec (plus extra features). Coding to JPA keeps you portable across providers; Hibernate is what actually executes.

**Counter Q:** What is mappedBy and the owning side?
**Answer:** The owning side is the entity that holds the foreign key and controls the relationship's persistence. `mappedBy` marks the inverse side and names the field on the owning side, telling JPA not to create a separate FK/join table. Updates must be made on the owning side to persist.

**Counter Q:** Why `@Enumerated(STRING)` over ORDINAL?
**Answer:** ORDINAL stores the enum's position (0,1,2). If you reorder or insert enum constants, existing rows silently map to the wrong value. STRING stores the name, which is stable and readable.

**Counter Q:** IDENTITY vs SEQUENCE generation?
**Answer:** IDENTITY relies on the DB auto-increment and requires an immediate insert, disabling JDBC batch inserts. SEQUENCE pre-allocates ids from a DB sequence, enabling batching and better performance — preferred where supported.

### Quick Revision
- JPA = spec; Hibernate = implementation.
- Owning side has the FK; inverse side uses `mappedBy`.
- *ToOne EAGER, *ToMany LAZY by default → prefer LAZY + explicit fetch.
- Enum as STRING; SEQUENCE for batching.

---

# 23. Spring Core (IoC / DI)

## Inversion of Control (IoC)
Instead of your code creating and wiring its dependencies (`new`), the **container** creates, configures, and injects them. You declare *what* you need; Spring supplies it. This inverts the control of object creation from your code to the framework.

## Why IoC/DI?
- **Loose coupling** — depend on interfaces, not concrete `new` calls; swap implementations without touching consumers.
- **Testability** — inject mocks/stubs in tests.
- **Centralized configuration** & lifecycle management.
- **Cross-cutting concerns** — the container can wrap beans (AOP, transactions) transparently.

## IoC container: BeanFactory vs ApplicationContext
- **BeanFactory** — basic container, lazy bean instantiation.
- **ApplicationContext** — superset: eager singleton init, event publishing, i18n, `Environment`, `@Autowired`/annotation support, AOP integration. **Use ApplicationContext** in real apps.

## Dependency Injection types
```java
// Constructor injection (PREFERRED)
@Service
public class OrderService {
    private final PaymentGateway gateway;
    public OrderService(PaymentGateway gateway) { this.gateway = gateway; } // @Autowired optional if single ctor
}

// Setter injection (optional/reconfigurable deps)
@Autowired public void setGateway(PaymentGateway g) { this.gateway = g; }

// Field injection (discouraged)
@Autowired private PaymentGateway gateway;
```

### Which to prefer and why? (very common)
**Constructor injection**, because:
- Dependencies are **`final`** → immutable, guaranteed set → no half-built beans.
- **Mandatory dependencies are explicit** — you can't construct the object without them.
- **Testable without Spring** — just call `new OrderService(mock)`.
- **Exposes circular dependencies at startup** (fails fast) instead of hiding them.
Field injection hides dependencies, can't be `final`, needs reflection, and makes unit testing harder.

## Circular dependencies
A → B and B → A.
- With **setter/field** injection, Spring can resolve simple singleton cycles by injecting a partially-initialized bean (via early reference exposure).
- With **constructor** injection on both sides, Spring **can't** build either first → `BeanCurrentlyInCreationException` at startup. This is a *feature* — it surfaces a design smell early.
- Fixes: redesign to remove the cycle, use `@Lazy` on one dependency, or `ApplicationEventPublisher`/setter injection as a last resort.

## Interview Q&A
#### Q1. What is IoC and DI?
**Answer:** IoC means the framework, not your code, controls object creation and wiring. DI is the mechanism — the container injects a bean's dependencies (via constructor/setter/field) rather than the bean creating them. The result is loose coupling, easy testing, and centralized lifecycle management.

**Counter Q:** Why is constructor injection preferred?
**Answer:** It lets dependencies be `final` and guarantees they're set at construction, so the object is always fully initialized and immutable. It makes required dependencies explicit, allows testing without Spring, and fails fast on circular dependencies instead of hiding them.

**Counter Q:** What happens with two implementations of the same interface?
**Answer:** Spring can't decide which to inject and throws `NoUniqueBeanDefinitionException`. You resolve it with `@Primary` (default winner) or `@Qualifier("beanName")` at the injection point to select explicitly.

**Counter Q:** How does @Qualifier work?
**Answer:** It names the specific bean to inject, matching by bean name/qualifier value, overriding type-only matching. It's how you disambiguate multiple candidates.

**Counter Q:** How does Spring actually inject the dependency, and when is the bean created?
**Answer:** At startup Spring scans for bean definitions, then instantiates singletons eagerly (by default), resolving each bean's dependencies from the container and injecting them via the chosen mechanism. Prototype beans are created on each request. The container maintains the singleton registry and manages lifecycle callbacks.

**Counter Q:** BeanFactory vs ApplicationContext?
**Answer:** BeanFactory is the minimal, lazy container. ApplicationContext extends it with eager singleton init, events, i18n, environment/property resolution, and annotation/AOP support — it's what you use in practice.

### Quick Revision
- IoC = container controls creation; DI = injects dependencies.
- Prefer constructor injection (final, explicit, testable, fail-fast).
- Multiple beans → @Primary / @Qualifier.
- ApplicationContext ⊃ BeanFactory.
- Constructor cycles fail fast; break with redesign/@Lazy.

### Interview Answer (speak this)
> "IoC means Spring controls object creation and wiring instead of my code calling `new`; dependency injection is how it supplies collaborators. I use constructor injection because the fields can be `final`, required dependencies are explicit, the bean is never half-initialized, I can unit test with plain `new` and mocks, and circular dependencies fail fast at startup. When multiple implementations exist I disambiguate with `@Primary` or `@Qualifier`."

---

# 24. Spring Beans

## What is a bean?
An object instantiated, assembled, and managed by the Spring IoC container.

## Declaring beans
- **Stereotypes** (component scanning): `@Component`, and specializations `@Service` (business logic), `@Repository` (persistence + exception translation), `@Controller`/`@RestController` (web).
- **`@Bean`** methods inside a **`@Configuration`** class — for third-party classes you can't annotate, or when you need construction logic.
- `@ComponentScan` tells Spring where to look; Spring Boot auto-scans the main class's package downward.

### @Component vs @Bean
- `@Component` — Spring instantiates the class it's on via scanning (your own classes).
- `@Bean` — you write the factory method returning the instance (any class, incl. libraries), giving full control over construction.

## Wiring annotations
- `@Autowired` — inject by type (then by name/qualifier).
- `@Qualifier` — select among multiple candidates.
- `@Primary` — default candidate when ambiguous.
- `@Value("${prop}")` — inject a property/SpEL value.

## Bean scopes
| Scope | Meaning |
|---|---|
| **singleton** (default) | One shared instance per container |
| **prototype** | New instance every injection/lookup |
| request | One per HTTP request (web) |
| session | One per HTTP session (web) |
| application | One per ServletContext |

**Gotcha:** injecting a **prototype** into a **singleton** — the prototype is resolved once at singleton creation, so you get the same instance. Use `ObjectProvider`, `@Lookup`, or scoped proxies to get fresh instances.

## Bean lifecycle
```text
Instantiate -> populate dependencies (DI) -> BeanNameAware/etc. ->
BeanPostProcessor.before -> @PostConstruct / InitializingBean.afterPropertiesSet() / init-method ->
BeanPostProcessor.after -> [BEAN READY] ->
... on shutdown ... @PreDestroy / DisposableBean.destroy() / destroy-method
```
- **`@PostConstruct`** — run init logic after DI (preferred over `InitializingBean`).
- **`@PreDestroy`** — cleanup before destruction (singletons on context close).
- **`BeanPostProcessor`** — hooks to modify/wrap beans (this is how AOP proxies and `@Autowired` resolution are applied).

## Interview Q&A
#### Q1. @Component vs @Bean?
**Answer:** `@Component` (and its stereotypes) marks a class for component scanning so Spring instantiates it automatically. `@Bean` is a method in a `@Configuration` class where you construct and return the instance yourself — used for library classes you can't annotate or when construction needs custom logic.

**Counter Q:** Default scope, and the prototype-in-singleton trap?
**Answer:** Singleton by default — one shared instance. If you inject a prototype bean into a singleton, it's injected once at singleton creation, so you keep reusing the same prototype instance. To get fresh ones use `ObjectProvider`, method injection (`@Lookup`), or a scoped proxy.

**Counter Q:** Bean lifecycle order?
**Answer:** Instantiate → inject dependencies → aware callbacks → `BeanPostProcessor` before → init (`@PostConstruct`/`afterPropertiesSet`/init-method) → `BeanPostProcessor` after → ready. On shutdown: `@PreDestroy`/`destroy`. BeanPostProcessors are where proxies (AOP/transactions) get applied.

**Counter Q:** @Repository vs @Component?
**Answer:** `@Repository` is a `@Component` plus persistence-exception translation — it converts vendor/JPA exceptions into Spring's `DataAccessException` hierarchy.

### Quick Revision
- Beans declared via stereotypes or `@Bean` in `@Configuration`.
- Singleton default; prototype = new each time (watch injection into singleton).
- Lifecycle: construct → inject → @PostConstruct → ready → @PreDestroy.
- BeanPostProcessor applies AOP/transaction proxies.

---

# 25. Spring AOP

## What & Why
AOP (Aspect-Oriented Programming) modularizes **cross-cutting concerns** — logging, security, transactions, metrics — that would otherwise be scattered across many methods. You write the concern once (an aspect) and declaratively apply it.

## Terminology
- **Aspect** — the module holding cross-cutting logic (`@Aspect`).
- **Advice** — the action, and *when* it runs: `@Before`, `@After` (finally), `@AfterReturning`, `@AfterThrowing`, `@Around` (wraps, most powerful).
- **JoinPoint** — a point in execution (in Spring AOP: a method execution).
- **Pointcut** — an expression selecting join points (`execution(* com.app.service.*.*(..))`).
- **Weaving** — linking aspects to target objects; Spring does it at **runtime via proxies**.

```java
@Aspect @Component
public class LoggingAspect {
    @Around("execution(* com.app.service..*(..))")
    public Object logTiming(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.nanoTime();
        try { return pjp.proceed(); }
        finally { log.info("{} took {}ns", pjp.getSignature(), System.nanoTime() - start); }
    }
}
```

## How Spring proxies work
Spring wraps the bean in a proxy that runs advice around the real method:
- **JDK dynamic proxy** — used when the bean implements an interface; proxy implements the same interface.
- **CGLIB** — subclass-based proxy when there's no interface (Spring Boot defaults to CGLIB for classes). Can't proxy `final` classes/methods.

## Self-invocation problem (critical)
Advice runs only when the call goes **through the proxy**. When a method calls another method on **`this`** (internal call), it bypasses the proxy, so `@Transactional`/`@Cacheable`/AOP advice on that inner method **does not apply**.
```java
@Service class BillingService {
    @Transactional public void outer() { inner(); }  // inner() called on `this` -> proxy bypassed
    @Transactional public void inner() { /* NEW transaction NOT started */ }
}
```
**Fixes:** call through an injected self-reference/`ApplicationContext.getBean`, split into two beans, or use AspectJ compile/load-time weaving.

## Private methods & proxying
Proxies intercept only externally visible (public, non-static) methods. **Private methods can't be advised** by Spring AOP (subclass/proxy can't override them).

## Interview Q&A
#### Q1. How does Spring AOP work internally?
**Answer:** Spring creates a proxy around the target bean (JDK dynamic proxy if it implements an interface, otherwise CGLIB subclass). Calls to the bean go through the proxy, which runs the matching advice before/around/after invoking the real method. Weaving happens at runtime via a BeanPostProcessor.

**Counter Q:** Why doesn't @Transactional work on self-invocation?
**Answer:** Because the transactional behavior lives in the proxy. An internal call on `this` goes straight to the real method, skipping the proxy, so no new transaction/advice is applied. You fix it by invoking through the proxy (self-injection, separate bean, or AspectJ weaving).

**Counter Q:** JDK proxy vs CGLIB?
**Answer:** JDK dynamic proxies require the target to implement an interface and proxy that interface. CGLIB generates a runtime subclass, so it works without interfaces but can't proxy `final` classes/methods. Spring Boot defaults to CGLIB.

**Counter Q:** Can you advise private methods?
**Answer:** Not with Spring's proxy-based AOP — only externally visible methods are intercepted. Use AspectJ weaving if you truly need it (rare).

### Tricky
- *"Why does calling `@Cacheable` method internally skip the cache?"* → Same self-invocation/proxy reason.

### Quick Revision
- AOP = modularized cross-cutting concerns via proxies.
- Advice: before/after/afterReturning/afterThrowing/around.
- JDK proxy (interface) vs CGLIB (subclass, default in Boot).
- Self-invocation & private methods bypass the proxy → advice skipped.

### Interview Answer (speak this)
> "Spring AOP applies cross-cutting concerns by wrapping beans in proxies — JDK dynamic proxies when there's an interface, otherwise CGLIB subclasses. When you call the bean, the proxy runs the advice around the real method. The classic gotcha is self-invocation: if a method calls another annotated method on `this`, the call bypasses the proxy, so `@Transactional` or `@Cacheable` on the inner method silently does nothing. I fix it by going through the proxy or splitting the logic into another bean."

---

# 26. Spring MVC

## Request lifecycle
```text
Client -> DispatcherServlet (front controller)
       -> HandlerMapping (find controller for URL)
       -> HandlerAdapter (invoke it)
       -> Controller -> Service -> Repository -> DB
       -> return value -> HttpMessageConverter (Jackson) serializes to JSON
       -> HTTP Response
```
- **DispatcherServlet** — the front controller; routes every request.
- **HandlerMapping** — maps URL+method to a handler.
- **HandlerAdapter** — invokes the handler, binds args, handles the return.
- **HttpMessageConverter** (Jackson) — converts `@RequestBody`/`@ResponseBody` ↔ JSON.

## Controller essentials
```java
@RestController                     // = @Controller + @ResponseBody
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public ResponseEntity<UserDto> get(@PathVariable Long id) {
        return ResponseEntity.ok(service.find(id));
    }

    @PostMapping
    public ResponseEntity<UserDto> create(@Valid @RequestBody CreateUserRequest req) {
        UserDto created = service.create(req);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }

    @GetMapping
    public List<UserDto> list(@RequestParam(defaultValue = "0") int page) { ... }
}
```
- `@PathVariable` (path segment), `@RequestParam` (query/form), `@RequestBody` (deserialize JSON), `@ResponseBody` (serialize return).
- `@RestController` implies `@ResponseBody` on all methods.
- `ResponseEntity` gives full control over status/headers/body.

## Validation
```java
public record CreateUserRequest(@NotBlank String name, @Email String email, @Min(18) int age) {}
// @Valid triggers Bean Validation; on failure -> MethodArgumentNotValidException
```
- `@Valid` / `@Validated` (the latter supports validation groups).
- `BindingResult` lets you handle errors inline (else the framework throws).

## Exception handling
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<ApiError> notFound(NotFoundException e) {
        return ResponseEntity.status(404).body(new ApiError("NOT_FOUND", e.getMessage()));
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> invalid(MethodArgumentNotValidException e) { ... }
}
```
- `@ExceptionHandler` — per-controller.
- `@ControllerAdvice`/`@RestControllerAdvice` — global, centralizes error mapping so controllers stay clean.

## Filters vs Interceptors
- **Filter** (Servlet API) — runs before `DispatcherServlet`, at the servlet-container level; sees raw request/response (auth, CORS, logging, compression).
- **Interceptor** (`HandlerInterceptor`) — Spring MVC level, around handler execution; has access to the handler/model (`preHandle`/`postHandle`/`afterCompletion`).
- Order: Filter → DispatcherServlet → Interceptor → Controller.

## CORS
Browser same-origin policy blocks cross-origin calls; configure allowed origins/methods via `@CrossOrigin` or a global `CorsConfigurationSource`.

## Interview Q&A
#### Q1. Walk through the request flow.
**Answer:** The DispatcherServlet is the front controller receiving all requests. It asks HandlerMapping which controller handles the URL and method, then a HandlerAdapter invokes it, binding path/query/body parameters. The controller delegates to services/repositories, returns an object, and an HttpMessageConverter (Jackson) serializes it to JSON in the response. Exceptions are routed to `@ControllerAdvice` handlers.

**Counter Q:** Filter vs Interceptor?
**Answer:** Filters are Servlet-level, run before Spring's DispatcherServlet, and work on raw request/response — good for auth, CORS, logging. Interceptors are Spring MVC-level, wrap handler execution with access to the handler and model — good for concerns tied to controllers. Filters run first.

**Counter Q:** @Controller vs @RestController?
**Answer:** `@Controller` returns view names (server-side rendering) unless methods add `@ResponseBody`. `@RestController` is `@Controller` + `@ResponseBody` on every method, so return values are serialized to the response body (JSON) — the standard for REST APIs.

**Counter Q:** How does validation error become a 400?
**Answer:** `@Valid` on `@RequestBody` triggers Bean Validation; failures raise `MethodArgumentNotValidException`, which Spring (or your `@RestControllerAdvice`) maps to a 400 with field errors.

### Quick Revision
- DispatcherServlet → HandlerMapping → HandlerAdapter → Controller → converters.
- @PathVariable/@RequestParam/@RequestBody; @RestController = +@ResponseBody.
- Centralize errors with @RestControllerAdvice + @ExceptionHandler.
- Filter (servlet) before Interceptor (MVC).

---

# 27. Spring Boot

## What & Spring vs Spring Boot
- **Spring Framework** — the core (IoC, AOP, MVC, data). Powerful but needs lots of manual configuration (XML/Java config, dependency versions, server setup).
- **Spring Boot** — an opinionated layer *on top of* Spring that removes boilerplate via **auto-configuration**, **starter dependencies**, an **embedded server**, and production features (Actuator). It's not a replacement — it's Spring made convention-over-configuration.

**Advantages:** fast startup of new projects, sensible defaults, no XML, embedded Tomcat (runnable JAR), dependency management, easy externalized config and monitoring.

## @SpringBootApplication
A meta-annotation combining:
- **`@Configuration`** — this class defines beans.
- **`@EnableAutoConfiguration`** — turn on auto-configuration.
- **`@ComponentScan`** — scan this package and subpackages for beans.

## Starters
Curated dependency bundles: `spring-boot-starter-web` (MVC + Tomcat + Jackson), `-data-jpa`, `-security`, `-test`, `-actuator`. They pull a consistent, version-aligned set of libs (via the `spring-boot-dependencies` BOM), so you don't manage versions manually.

## Auto-configuration — how it works internally (key question)
1. `@EnableAutoConfiguration` loads auto-configuration classes listed in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (was `spring.factories` pre-2.7).
2. Each auto-config class is guarded by **`@Conditional`** annotations:
   - `@ConditionalOnClass` — apply only if a class is on the classpath (e.g., configure a `DataSource` only if a JDBC driver is present).
   - `@ConditionalOnMissingBean` — back off if you already defined the bean (your bean wins).
   - `@ConditionalOnProperty` — apply based on a property value.
3. So Boot inspects the classpath + your beans + properties and wires only what makes sense, while always letting your explicit beans override the defaults.

**"How does Spring Boot know which beans to create automatically?"** → It evaluates the conditions on each auto-configuration class against your classpath, existing beans, and configuration, and applies those that match, backing off wherever you've already provided a bean.

## Configuration
- `application.properties` / `application.yml` — externalized config.
- **Profiles** — `@Profile("dev")` beans + `spring.profiles.active=prod`; per-profile files `application-prod.yml`.
- **`@Value("${x}")`** — inject a single property.
- **`@ConfigurationProperties(prefix="app")`** — bind a group of properties to a typed POJO (preferred for structured config; supports relaxed binding and validation).
- Config precedence: command-line args > env vars > profile-specific files > application file > defaults.

## Actuator & Ops
- `/actuator/health`, `/info`, `/metrics`, `/beans`, `/mappings`, `/env`.
- Micrometer exports metrics to Prometheus/Grafana. Custom health indicators and metrics are easy to add.
- Embedded Tomcat + executable fat JAR (`java -jar app.jar`); Maven/Gradle plugins build it.

## Interview Q&A
#### Q1. Spring vs Spring Boot?
**Answer:** Spring is the core framework providing IoC, AOP, MVC, and data access but requiring substantial manual configuration. Spring Boot sits on top and applies convention-over-configuration: auto-configuration wires beans based on the classpath, starters manage dependencies, and an embedded server plus Actuator make apps runnable and observable out of the box. Boot uses Spring; it doesn't replace it.

**Counter Q:** How does auto-configuration actually work?
**Answer:** `@EnableAutoConfiguration` loads a list of auto-config classes from `AutoConfiguration.imports`. Each is conditional — `@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty` — so Boot only applies a configuration when its trigger classes are present, its properties match, and you haven't already defined the bean. That's why adding a starter "just works" and why your own beans override the defaults.

**Counter Q:** What is @ConditionalOnMissingBean for?
**Answer:** It makes an auto-configured bean back off if you've defined your own of that type — the mechanism that lets you override Boot's defaults simply by declaring a bean.

**Counter Q:** How do you disable an auto-configuration?
**Answer:** `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)` or `spring.autoconfigure.exclude` in properties.

**Counter Q:** What is a starter?
**Answer:** A dependency aggregator that brings in a coherent, version-aligned set of libraries for a capability (web, data-jpa, security), so you add one dependency instead of many and avoid version conflicts.

### Quick Revision
- Boot = opinionated Spring: auto-config + starters + embedded server + Actuator.
- @SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan.
- Auto-config = conditional classes (@ConditionalOnClass/MissingBean/Property).
- @ConfigurationProperties for typed config; profiles for env-specific config.

### Interview Answer (speak this)
> "Spring Boot is opinionated Spring. Its core trick is auto-configuration: `@EnableAutoConfiguration` loads a list of configuration classes, each guarded by conditions like `@ConditionalOnClass` and `@ConditionalOnMissingBean`. Boot checks the classpath, your existing beans, and properties, then wires only what fits — and backs off whenever you've defined your own bean. Combined with starters that align dependency versions and an embedded Tomcat, I get a runnable, production-ready app with almost no boilerplate."

---

# 28. Spring Data JPA

## What
A Spring module that removes DAO boilerplate: you declare a repository **interface**, and Spring generates the implementation at runtime (a proxy).

## Repository hierarchy
```text
Repository (marker)
└── CrudRepository<T,ID>              save, findById, findAll, delete, count
    └── PagingAndSortingRepository    + findAll(Pageable), findAll(Sort)
        └── JpaRepository<T,ID>       + flush, saveAll, batch, findAll(Example), JPA-specific
```

## Query mechanisms
```java
public interface UserRepository extends JpaRepository<User, Long> {

    // 1) Derived query — method name parsed into a query
    List<User> findByStatusAndAgeGreaterThan(Status status, int age);
    Optional<User> findByEmail(String email);

    // 2) @Query JPQL
    @Query("SELECT u FROM User u WHERE u.dept.id = :deptId")
    List<User> byDept(@Param("deptId") Long deptId);

    // 3) Native
    @Query(value = "SELECT * FROM users WHERE created_at > ?1", nativeQuery = true)
    List<User> createdAfter(Instant t);

    // 4) Pagination + sorting
    Page<User> findByStatus(Status status, Pageable pageable);
    // repo.findByStatus(ACTIVE, PageRequest.of(0, 20, Sort.by("name")));

    // 5) Projection (interface) — fetch subset of columns
    interface NameOnly { String getName(); }
    List<NameOnly> findByStatus(Status status);
}
```
- **Specifications** — `JpaSpecificationExecutor` for dynamic Criteria-based filtering.
- **`@EntityGraph`** on a method to control fetch (fix N+1).
- **Auditing** — `@CreatedDate`/`@LastModifiedDate`/`@CreatedBy` with `@EnableJpaAuditing`.
- Repository methods are **transactional** — reads are read-only transactions; writes need a write transaction.

## Interview Q&A
#### Q1. How does Spring Data JPA implement a repository with no code?
**Answer:** At startup Spring Data creates a dynamic proxy for each repository interface. For derived queries it parses the method name into a JPQL query; for `@Query` it uses the given JPQL/SQL; and standard CRUD comes from a base `SimpleJpaRepository`. So you get a full implementation from just an interface.

**Counter Q:** Derived query vs @Query — when to use which?
**Answer:** Derived queries are great for simple, readable finders. Once the method name gets long or the query is complex (joins, aggregations), switch to `@Query` for clarity and control, or Specifications for dynamic criteria.

**Counter Q:** Page vs Slice?
**Answer:** `Page` runs an extra count query to know the total; `Slice` only knows if there's a next page (no count) — cheaper for infinite-scroll UIs where you don't need totals.

### Quick Revision
- Declare interface → Spring generates proxy impl.
- Derived queries, @Query (JPQL/native), Specifications, projections, EntityGraph.
- Pageable/Sort for pagination; Page (count) vs Slice (no count).

---

# 29. Transactions

## ACID
- **Atomicity** — all-or-nothing.
- **Consistency** — DB moves between valid states (constraints hold).
- **Isolation** — concurrent transactions don't corrupt each other.
- **Durability** — committed data survives crashes.

## @Transactional
Spring wraps the method in a proxy that begins a transaction before and commits after (or rolls back on error). Default: rollback on **unchecked** (`RuntimeException`/`Error`), **commit** on checked exceptions (configurable via `rollbackFor`).

```java
@Transactional
public void transfer(Long from, Long to, BigDecimal amt) {
    debit(from, amt);
    credit(to, amt);   // if this throws a RuntimeException, the debit is rolled back too
}
```

## Propagation
How a method participates in an existing transaction:
| Propagation | Behavior |
|---|---|
| **REQUIRED** (default) | Join existing tx, or start one |
| **REQUIRES_NEW** | Suspend current, start a new independent tx (commits/rolls back separately) |
| **SUPPORTS** | Join if one exists, else run non-transactionally |
| **NOT_SUPPORTED** | Suspend any tx, run non-transactionally |
| **MANDATORY** | Must have an existing tx, else throw |
| **NEVER** | Must NOT have a tx, else throw |
| **NESTED** | Nested savepoint within the current tx (partial rollback) |

**REQUIRES_NEW** use case: writing an audit log/notification that must persist even if the main business tx rolls back.

## Isolation levels & read phenomena
| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ_UNCOMMITTED | ✅ possible | ✅ | ✅ |
| READ_COMMITTED | ❌ | ✅ | ✅ |
| REPEATABLE_READ | ❌ | ❌ | ✅ (mostly) |
| SERIALIZABLE | ❌ | ❌ | ❌ |

- **Dirty read** — read uncommitted data that may roll back.
- **Non-repeatable read** — same row read twice returns different values (another tx committed an update between).
- **Phantom read** — same query returns different *rows* (another tx inserted/deleted).
Higher isolation = more correctness, less concurrency. Default is usually READ_COMMITTED (Postgres/Oracle) or REPEATABLE_READ (MySQL InnoDB).

## Self-invocation problem
`@Transactional` is proxy-based (like AOP). An internal `this.method()` call bypasses the proxy → **no transaction starts**. Same fix as AOP: call through the proxy, split beans, or use AspectJ. Also, `@Transactional` on a **private** method does nothing.

## Read-only transactions
`@Transactional(readOnly = true)` hints the provider (Hibernate skips dirty checking/flush) and can route to read replicas — an optimization for pure reads.

## Interview Q&A
#### Q1. Explain @Transactional propagation with an example.
**Answer:** Propagation controls how a method joins transactions. REQUIRED (default) joins the caller's transaction or starts one, so nested service calls share a single commit/rollback. REQUIRES_NEW suspends the caller's transaction and runs in an independent one — I use it for audit logs that must survive even if the business transaction rolls back. NESTED uses a savepoint so I can roll back part of the work without aborting the whole transaction.

**Counter Q:** Which exceptions trigger rollback by default?
**Answer:** Unchecked exceptions (`RuntimeException`, `Error`) roll back; checked exceptions commit by default. Override with `@Transactional(rollbackFor = Exception.class)`.

**Counter Q:** Why doesn't @Transactional work on a self-invoked or private method?
**Answer:** It's implemented with a proxy. A call on `this` skips the proxy so no transaction is started, and private methods can't be intercepted at all. Fix by invoking through the injected proxy, moving the method to another bean, or using AspectJ weaving.

**Counter Q:** Dirty vs non-repeatable vs phantom read?
**Answer:** Dirty read sees uncommitted changes; non-repeatable read sees a changed value for the same row on re-read; phantom read sees new/removed rows for the same query. Each is prevented by progressively higher isolation levels up to SERIALIZABLE.

### Quick Revision
- ACID; rollback on unchecked by default (rollbackFor to change).
- REQUIRED joins, REQUIRES_NEW independent, NESTED savepoint, MANDATORY/NEVER assert.
- Isolation trades correctness vs concurrency; know the 3 read phenomena.
- Proxy-based → self-invocation/private methods bypass it.

### Interview Answer (speak this — propagation + self-invocation)
> "`@Transactional` wraps a bean method in a proxy that opens a transaction and commits on success or rolls back on a runtime exception. Propagation decides how nested calls behave — REQUIRED joins one shared transaction, REQUIRES_NEW runs an independent one for things like audit logs that must persist regardless. The classic trap is self-invocation: calling another `@Transactional` method on `this` bypasses the proxy so no new transaction starts, and it never works on private methods. I fix it by going through the proxy or extracting the method into a separate bean."

---

# 30. REST API

## Principles
REST = Representational State Transfer. Key constraints:
- **Client-server**, **stateless** (each request carries all needed context; no server session).
- **Uniform interface** — resources identified by URIs, manipulated via standard HTTP methods.
- **Resource-oriented** — nouns not verbs (`/users/123`, not `/getUser?id=123`).
- **Cacheable**, layered.

## HTTP methods & idempotency
| Method | Purpose | Idempotent | Safe |
|---|---|---|---|
| GET | read | ✅ | ✅ |
| POST | create / non-idempotent action | ❌ | ❌ |
| PUT | full replace/update | ✅ | ❌ |
| PATCH | partial update | ❌ (usually) | ❌ |
| DELETE | remove | ✅ | ❌ |

**Idempotent** = same request repeated has the same effect. GET/PUT/DELETE are idempotent; POST is not (repeating creates duplicates). This matters for **retries** — safe to retry idempotent calls.

## Status codes
| Code | Meaning |
|---|---|
| 200 OK | success with body |
| 201 Created | resource created (return `Location`) |
| 204 No Content | success, no body (e.g., DELETE) |
| 400 Bad Request | validation/malformed |
| 401 Unauthorized | not authenticated |
| 403 Forbidden | authenticated but not allowed |
| 404 Not Found | resource missing |
| 409 Conflict | state conflict (duplicate, version) |
| 422 Unprocessable Entity | semantic validation failure |
| 500 Internal Server Error | unhandled server error |

## PUT vs PATCH
PUT replaces the whole resource (send all fields; idempotent). PATCH applies a partial change (send changed fields only; not necessarily idempotent).

## Good API design
- **Versioning** — URI (`/api/v1/...`), header, or media type. URI versioning is most common/visible.
- **DTOs + validation** (`@Valid`), consistent **error response structure**.
- **Pagination/sorting/filtering** via query params (`?page=0&size=20&sort=name&status=ACTIVE`).
- **OpenAPI/Swagger** (springdoc) for docs.
- **CORS** for browser clients; **rate limiting** to protect resources.

## Interview Q&A
#### Q1. What makes an API RESTful, and what is idempotency?
**Answer:** RESTful means stateless, resource-oriented URIs manipulated with standard HTTP methods and appropriate status codes, with a uniform interface. Idempotency means repeating the same request yields the same server state — GET, PUT, and DELETE are idempotent, POST isn't. It matters for safe retries: I can retry a PUT after a timeout without creating duplicates, but retrying a POST might, so I'd add an idempotency key.

**Counter Q:** PUT vs PATCH?
**Answer:** PUT replaces the entire resource and is idempotent; PATCH sends a partial update and generally isn't idempotent. Use PUT for full updates, PATCH for changing a few fields.

**Counter Q:** 401 vs 403?
**Answer:** 401 means not authenticated (no/invalid credentials); 403 means authenticated but lacking permission for that resource.

**Counter Q:** How do you prevent duplicate POSTs?
**Answer:** Use an **idempotency key** header — the client sends a unique key, the server stores it and returns the original result on retry — plus DB unique constraints on natural keys.

### Quick Revision
- Stateless, resource URIs, standard methods + status codes.
- Idempotent: GET/PUT/DELETE; not POST/PATCH → retry safety.
- 201+Location on create, 204 on delete, 409 on conflict.
- DTO+validation, pagination params, versioning, OpenAPI.

---

# 31. Spring Security

## Core model
- **Authentication** — who are you? (verify credentials → `Authentication` in `SecurityContext`).
- **Authorization** — what can you do? (roles/authorities checked at URL or method level).
- **SecurityFilterChain** — an ordered chain of servlet filters (`FilterChainProxy`) that intercept every request: authentication filters, authorization, CSRF, CORS, exception translation.

## Key pieces
- **`UserDetailsService`** — loads user + credentials + authorities by username.
- **`UserDetails`** — the user principal (username, hashed password, authorities).
- **`PasswordEncoder`** — **BCrypt** (adaptive, salted) is standard; never store plaintext.
- **Roles vs authorities** — role is a coarse group (`ROLE_ADMIN`); authority is a fine-grained permission (`user:read`).

## Method security
```java
@PreAuthorize("hasRole('ADMIN')")          // checked BEFORE method
public void deleteUser(Long id) { ... }

@PostAuthorize("returnObject.owner == authentication.name") // checked AFTER, on result
public Document get(Long id) { ... }
```

## Session vs stateless (JWT)
- **Session-based** — server stores session, client holds a session cookie. Stateful; needs sticky sessions or shared session store when scaled.
- **Stateless (JWT)** — server issues a signed token; client sends it on each request; server validates the signature without storing state. Scales horizontally easily. For REST APIs, JWT/stateless is the norm.

## JWT
- Structure: `header.payload.signature` (base64url). Header = alg/type; payload = claims (sub, exp, roles); signature = HMAC/RSA over header+payload with a secret/key.
- **Not encrypted** — payload is readable (base64), only *signed* (tamper-proof). Never put secrets in it.
- **Access token** — short-lived (minutes), sent on each request. **Refresh token** — long-lived, stored securely, used to mint new access tokens without re-login.
- **Validation** — verify signature, `exp`, issuer/audience; reject if tampered/expired.

### Typical JWT login flow
```text
1. POST /login {username, password}
2. Server authenticates via UserDetailsService + PasswordEncoder
3. Server signs a JWT (access, short TTL) + refresh token; returns them
4. Client sends "Authorization: Bearer <access>" on each request
5. A JWT filter validates signature+exp, builds Authentication, sets SecurityContext
6. On access-token expiry, client calls /refresh with the refresh token to get a new access token
```

## CSRF & CORS
- **CSRF** — attacker tricks a logged-in browser into submitting a forged request using its cookie. Protection = anti-CSRF token. Relevant for **cookie/session** auth; typically **disabled for stateless JWT** APIs (no ambient cookie).
- **CORS** — controls which origins may call your API from a browser.

## OAuth2 / OpenID Connect (basics)
- **OAuth2** — delegated **authorization**; grants:
  - **Authorization Code (+ PKCE)** — for web/mobile apps; user logs in at the provider, app exchanges a code for tokens. PKCE protects public clients (no client secret).
  - **Client Credentials** — machine-to-machine (no user).
- **OpenID Connect** — an identity layer on top of OAuth2 adding an **ID token** for authentication (who the user is).

## Interview Q&A
#### Q1. Authentication vs authorization, and how does Spring Security enforce them?
**Answer:** Authentication verifies identity (credentials → an `Authentication` in the `SecurityContext`); authorization decides what that identity may access. Spring Security runs a filter chain per request: authentication filters establish the principal, then authorization checks roles/authorities at the URL level or via method annotations like `@PreAuthorize`. Passwords are stored hashed with BCrypt.

**Counter Q:** JWT vs session — which and why?
**Answer:** Sessions are stateful — the server stores session data, which complicates horizontal scaling (needs sticky sessions or a shared store). JWT is stateless — the signed token carries claims, so any instance can validate it without shared state, which scales cleanly for REST APIs. The tradeoff is you can't easily revoke a JWT before expiry, so I keep access tokens short-lived with refresh tokens.

**Counter Q:** Is a JWT encrypted? Can you trust its contents?
**Answer:** By default it's signed, not encrypted — anyone can base64-decode the payload, so never put secrets in it. You trust it because the signature proves it wasn't tampered with and was issued by you; you validate signature and expiry on every request.

**Counter Q:** Why disable CSRF for JWT APIs?
**Answer:** CSRF exploits ambient credentials like cookies sent automatically by the browser. A JWT sent in the `Authorization` header isn't sent automatically, so the CSRF vector doesn't apply — hence it's commonly disabled for stateless token APIs (but keep it for cookie-based auth).

**Counter Q:** How do you revoke a JWT?
**Answer:** You can't invalidate a self-contained JWT directly. Options: short TTL + refresh tokens, a server-side denylist of revoked token ids, or rotating the signing key. That's the main downside of stateless tokens.

### Quick Revision
- AuthN (identity) vs AuthZ (permissions); filter chain enforces both.
- BCrypt for passwords; UserDetailsService loads users.
- JWT = header.payload.signature, signed not encrypted; access(short)+refresh.
- Stateless JWT scales; disable CSRF for token APIs, keep for cookies.
- OAuth2 grants: Auth Code+PKCE (users), Client Credentials (M2M); OIDC adds ID token.

### Interview Answer (speak this — JWT flow)
> "For a REST API I use stateless JWT auth. On login I authenticate the user via `UserDetailsService` and a BCrypt `PasswordEncoder`, then issue a short-lived signed access token plus a longer-lived refresh token. The client sends the access token as a Bearer header; a filter validates the signature and expiry, builds an `Authentication`, and puts it in the `SecurityContext` so authorization rules and `@PreAuthorize` can run. When the access token expires the client uses the refresh token to get a new one. It's signed not encrypted, so no secrets go in the payload, and because there's no server session it scales horizontally without sticky sessions."

---

# 32. Microservices

## Monolith vs Microservices
- **Monolith** — one deployable; simple to build/test/deploy initially, but scales as a whole and couples teams as it grows.
- **Microservices** — independently deployable services around business capabilities, each with its own DB. Benefits: independent scaling/deployment, tech diversity, team autonomy, fault isolation. Costs: distributed-system complexity, network failures, eventual consistency, operational overhead.
Don't start microservices prematurely — a well-modularized monolith is often the right first step.

## Building blocks (Spring Cloud)
- **Service discovery** — services register with **Eureka**/Consul; callers look up instances by name instead of hardcoded hosts.
- **API Gateway** (**Spring Cloud Gateway**) — single entry point: routing, auth, rate limiting, aggregation.
- **Load balancing** — Spring Cloud LoadBalancer distributes calls across instances.
- **Inter-service calls**:
  - **RestTemplate** — legacy, synchronous, blocking (maintenance mode).
  - **WebClient** — reactive, non-blocking; the modern choice even for blocking-style use.
  - **Feign** — declarative HTTP client (interface + annotations); clean and integrates with discovery/resilience.
- **Config Server** — centralized externalized config across services.

## Resilience (Resilience4j)
- **Circuit breaker** — after too many failures to a dependency, "open" the circuit to fail fast and stop hammering a sick service; periodically "half-open" to test recovery. Prevents cascading failures.
- **Retry** — re-attempt transient failures (with backoff). Only retry **idempotent** operations.
- **Timeout** — bound how long you wait on a dependency.
- **Fallback** — graceful degraded response when a call fails.
- **Bulkhead** — isolate resource pools so one slow dependency can't exhaust all threads.
- **Retry storms** — naive retries multiply load on a failing service. Mitigate with exponential backoff + jitter, capped retries, and circuit breakers.

## Observability
- **Correlation/trace ID** — propagate an ID across services (Micrometer Tracing / Sleuth → Zipkin/Jaeger) so one request's path is traceable end to end. Put it in MDC for logs.

## Communication & data
- **Synchronous** (REST/gRPC) — simple, but couples availability.
- **Asynchronous / event-driven** (**Kafka**, **RabbitMQ**) — services publish events; consumers react. Decouples services, improves resilience and scalability.
  - **Kafka** — distributed, partitioned, replayable log; high throughput, ordering per partition, consumer groups. Great for event streaming/event sourcing.
  - **RabbitMQ** — traditional message broker (queues, exchanges, routing); great for task queues/RPC.
- **Distributed transactions**: avoid 2PC across services. Use the **Saga pattern** — a sequence of local transactions with compensating actions on failure (choreography via events, or orchestration via a coordinator).
- **Outbox pattern** — to publish events reliably, write the event to an "outbox" table in the same DB transaction as the business change, then a relay publishes it — avoids the dual-write problem (DB commit succeeds but event publish fails).

## Interview Q&A
#### Q1. How do you handle a downstream service being down?
**Answer:** Wrap the call with a timeout so I don't block indefinitely, a circuit breaker that opens after repeated failures to fail fast and let the dependency recover, retries with exponential backoff and jitter only for idempotent calls, and a fallback for a graceful degraded response. That combination stops one failing service from cascading across the system.

**Counter Q:** How do you avoid retry storms?
**Answer:** Cap retry counts, use exponential backoff with jitter so clients don't retry in sync, and pair retries with a circuit breaker so I stop retrying once the dependency is clearly down.

**Counter Q:** Sync vs async communication — when?
**Answer:** Synchronous REST/Feign when the caller needs an immediate answer and the coupling is acceptable. Asynchronous messaging (Kafka/RabbitMQ) when I want to decouple services, absorb load spikes, or drive event-driven workflows — at the cost of eventual consistency.

**Counter Q:** How do you keep data consistent across services without distributed transactions?
**Answer:** With the Saga pattern — each service does its local transaction and emits an event; if a later step fails, compensating transactions undo prior steps. To publish events reliably I use the outbox pattern so the event and the DB change commit atomically.

**Counter Q:** RestTemplate vs WebClient vs Feign?
**Answer:** RestTemplate is the legacy blocking client (maintenance mode). WebClient is the modern non-blocking client. Feign is a declarative client — you define an interface and it generates the HTTP calls, integrating cleanly with discovery and Resilience4j. I prefer Feign for readability or WebClient for reactive/non-blocking needs.

### Quick Revision
- Microservices = independent deploy + own DB; complexity is the cost.
- Discovery (Eureka) + Gateway + LoadBalancer + Feign/WebClient.
- Resilience4j: circuit breaker, retry+backoff, timeout, fallback, bulkhead.
- Async via Kafka/RabbitMQ; Saga + Outbox for distributed consistency.
- Propagate correlation IDs for tracing.

---

# 33. Redis & Caching

## Why caching?
To reduce latency and load by serving frequent/expensive reads from fast storage instead of recomputing or re-querying the DB. Trades some freshness/consistency for speed.

- **Cache hit** — data found in cache (fast). **Cache miss** — not found; fetch from source and populate.
- **Local cache** (Caffeine/EhCache) — in-process, fastest, but per-instance (not shared, can be stale across nodes).
- **Distributed cache** (**Redis**) — shared across instances, survives restarts, consistent across the cluster; adds a network hop.

## Spring Cache abstraction
```java
@Cacheable(value = "users", key = "#id")           // check cache; if miss, run method + store
public User getUser(Long id) { return repo.findById(id).orElseThrow(); }

@CachePut(value = "users", key = "#user.id")        // always run + update cache
public User update(User user) { return repo.save(user); }

@CacheEvict(value = "users", key = "#id")           // remove from cache (e.g., on delete)
public void delete(Long id) { repo.deleteById(id); }
```
Backed by a `CacheManager` (Caffeine, Redis, etc.). **TTL** and eviction (LRU/LFU) are configured on the cache provider.

## Caching strategies
- **Cache-aside (lazy loading)** — app checks cache; on miss, loads from DB and populates. Most common (`@Cacheable` is this). App controls consistency.
- **Read-through** — cache library loads from DB on miss transparently.
- **Write-through** — writes go to cache and DB synchronously (consistent, slower writes).
- **Write-behind** — write to cache, async flush to DB (fast, risk of loss).

## Cache invalidation (the hard problem)
Stale data is the main risk. Strategies: TTL expiry, explicit eviction on writes (`@CacheEvict`), versioned keys, or event-driven invalidation. *"There are only two hard things in CS: cache invalidation and naming things."*

## Redis use cases
Caching, session store, rate limiting (counters), distributed locks, leaderboards (sorted sets), pub/sub, queues. It's single-threaded per instance (atomic commands) and in-memory with optional persistence.

## Interview Q&A
#### Q1. What caching strategy do you use and how do you handle staleness?
**Answer:** Cache-aside by default — on a read I check Redis, and on a miss I load from the DB and populate the cache with a TTL. On writes I evict or update the key so the next read refreshes. I pick TTLs based on how stale the data can safely be, and for strong consistency I invalidate on write rather than relying on TTL alone.

**Counter Q:** Local vs distributed cache — when?
**Answer:** Local (Caffeine) for small, hot, read-mostly data where per-instance staleness is acceptable and you want zero network cost. Distributed (Redis) when multiple instances must share a consistent cache or the cache must survive restarts — at the cost of a network hop. Sometimes both in a two-tier setup.

**Counter Q:** Why does @Cacheable not work when called internally?
**Answer:** Same self-invocation/proxy reason as `@Transactional` — the caching advice lives in the proxy, so an internal `this` call bypasses it.

**Counter Q:** Cache stampede — what is it and how to prevent?
**Answer:** When a popular key expires, many requests miss simultaneously and all hit the DB. Prevent with locking/single-flight (only one loader per key), staggered TTLs with jitter, or refreshing slightly before expiry.

### Quick Revision
- Cache to cut latency/load; hit vs miss; local vs distributed (Redis).
- @Cacheable/@CachePut/@CacheEvict over a CacheManager; set TTL + eviction.
- Cache-aside is default; write-through for consistency.
- Invalidation is the hard part: TTL + evict-on-write; beware stampede.

### Interview Answer (speak this)
> "I cache expensive or frequent reads to cut latency and DB load. My default is cache-aside with Redis as a distributed cache: check the cache, on a miss load from the DB and store with a TTL, and evict or update the key on writes so reads stay fresh. I use a local Caffeine cache for tiny hot data where per-node staleness is fine. The hard part is invalidation, so I set TTLs to a tolerable staleness window, invalidate on writes, and guard against cache stampede with single-flight loading and jittered TTLs."

---

# 34. SQL

## Keys
- **Primary key** — uniquely identifies a row; not null, unique, one per table.
- **Foreign key** — references a PK in another table; enforces referential integrity.
- **Unique key** — enforces uniqueness; allows one null (DB-dependent), can be multiple per table.

## Indexes
- Speed up reads by avoiding full table scans (typically a B-tree). Cost: slower writes and extra storage.
- **Composite index** `(a, b)` — usable for filters on `a` or `a,b` (leftmost-prefix rule), not `b` alone.
- **Clustered index** — table rows physically stored in index order (one per table; usually the PK in MySQL InnoDB).
- **Non-clustered index** — separate structure pointing to rows (many allowed).

## Normalization vs denormalization
- **Normalization** (1NF/2NF/3NF) — remove redundancy, avoid update anomalies via separate related tables. Better write integrity, more joins.
- **Denormalization** — deliberately duplicate data to reduce joins and speed reads. Better read performance, risk of inconsistency. Common in reporting/read-heavy systems.

## Joins
- **INNER JOIN** — only matching rows in both.
- **LEFT JOIN** — all left rows + matches (nulls where none).
- **RIGHT JOIN** — all right rows + matches.
- **FULL OUTER** — all rows from both.

## GROUP BY / HAVING
`WHERE` filters rows **before** grouping; `HAVING` filters groups **after** aggregation.
```sql
SELECT dept_id, COUNT(*) AS cnt, AVG(salary) AS avg_sal
FROM employees
WHERE active = true          -- row filter
GROUP BY dept_id
HAVING COUNT(*) > 5;         -- group filter
```

## Subqueries & EXISTS
```sql
-- EXISTS is often faster than IN for correlated existence checks
SELECT * FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
```

## Query optimization
- Use **EXPLAIN / EXPLAIN ANALYZE** to see the plan (index usage, scan type, row estimates).
- Add indexes on columns in WHERE/JOIN/ORDER BY; avoid functions on indexed columns in WHERE (breaks index use).
- Select only needed columns; avoid `SELECT *`.
- Watch for full table scans, missing indexes, and N+1 from the app layer.

## Pagination: offset vs cursor
- **Offset** (`LIMIT 20 OFFSET 10000`) — simple but slow on deep pages (DB skips all prior rows).
- **Cursor/keyset** (`WHERE id > :lastId ORDER BY id LIMIT 20`) — fast and stable for deep/infinite scroll; requires a stable sort key.

## SQL coding questions
```sql
-- 2nd highest salary
SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);
-- or with window function:
SELECT DISTINCT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) rnk FROM employees
) t WHERE rnk = 2;

-- Nth highest per department (window function)
SELECT * FROM (
  SELECT e.*, DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) rnk
  FROM employees e
) t WHERE rnk = 2;

-- Find duplicates
SELECT email, COUNT(*) FROM users GROUP BY email HAVING COUNT(*) > 1;

-- Delete duplicates keeping lowest id
DELETE u1 FROM users u1 JOIN users u2 ON u1.email = u2.email AND u1.id > u2.id;
```

## Interview Q&A
#### Q1. When does an index NOT help / hurt?
**Answer:** Indexes hurt write-heavy tables (every insert/update maintains them) and low-cardinality columns (e.g., boolean). They're not used if you apply a function to the column in WHERE, use a leading wildcard `LIKE '%x'`, or the optimizer estimates a full scan is cheaper. Composite indexes only help when the query uses the leftmost columns.

**Counter Q:** WHERE vs HAVING?
**Answer:** WHERE filters individual rows before grouping and can't use aggregates; HAVING filters groups after aggregation and can use aggregate functions.

**Counter Q:** Offset vs cursor pagination?
**Answer:** Offset skips N rows so it degrades on deep pages and can skip/repeat rows if data changes. Keyset/cursor pagination uses a `WHERE key > lastSeen` predicate on an indexed sort column — fast and stable, ideal for infinite scroll.

### Quick Revision
- PK unique+not null; FK integrity; index = faster reads/slower writes.
- Composite index = leftmost-prefix rule.
- WHERE before grouping, HAVING after.
- EXPLAIN to diagnose; avoid functions on indexed cols.
- Cursor pagination beats offset on deep pages.

---

# 35. Testing

## Levels
- **Unit test** — one class in isolation; collaborators mocked; fast, no Spring context.
- **Integration test** — multiple components/real infra (DB, web layer) wired together; slower.

## JUnit 5 + Mockito + AssertJ
```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock PaymentGateway gateway;          // dummy collaborator
    @InjectMocks OrderService service;     // gateway injected into it

    @Test void placesOrder() {
        when(gateway.charge(any())).thenReturn(Receipt.ok());
        Order o = service.place(cart);
        assertThat(o.status()).isEqualTo(PLACED);
        verify(gateway).charge(any());
    }
}
```
- **`@Mock`** — a fake whose behavior you stub (`when/thenReturn`).
- **`@Spy`** — wraps a **real** object; real methods run unless stubbed.
- **`@InjectMocks`** — creates the subject and injects the mocks/spies.

## Spring test slices
- **`@SpringBootTest`** — full application context (integration; slowest). For end-to-end wiring.
- **`@WebMvcTest`** — only the web layer (controllers, filters, converters); services `@MockBean`ed. Use `MockMvc` to test endpoints.
- **`@DataJpaTest`** — only the JPA layer with an in-memory/Testcontainers DB; tests repositories/queries; rolls back per test.
- **`@MockBean`** — replaces a bean in the Spring context with a mock.
- **Testcontainers** — spin up real DBs/brokers in Docker for high-fidelity integration tests.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired MockMvc mvc;
    @MockBean UserService service;
    @Test void returns404() throws Exception {
        when(service.find(9L)).thenThrow(new NotFoundException(9L));
        mvc.perform(get("/api/users/9")).andExpect(status().isNotFound());
    }
}
```

## Interview Q&A
#### Q1. Mock vs Spy?
**Answer:** A mock is a fully fake object — all methods return defaults until you stub them, so you control every interaction. A spy wraps a real instance — real methods execute unless you explicitly stub them, useful for partially overriding behavior. Prefer mocks for pure unit isolation; spies when you need most of the real behavior.

**Counter Q:** @SpringBootTest vs @WebMvcTest?
**Answer:** `@SpringBootTest` loads the entire context for full integration tests — accurate but slow. `@WebMvcTest` loads only the MVC layer for a controller and mocks the services, so it's fast and focused on request/response, validation, and status codes.

**Counter Q:** When should you mock a repository, and what shouldn't you mock?
**Answer:** Mock the repository in **service** unit tests to isolate business logic from the DB. Don't mock it in a `@DataJpaTest`, whose whole point is to verify the real queries against a real DB. Generally don't mock the class under test or value objects/DTOs — mock only external collaborators you don't own or that are slow/nondeterministic.

**Counter Q:** Unit vs integration test — the tradeoff?
**Answer:** Unit tests are fast and pinpoint failures but don't prove components work together. Integration tests catch wiring/config/query bugs but are slower and more brittle. A healthy suite is mostly unit tests with targeted integration tests (the test pyramid).

### Quick Revision
- Unit (isolated, mocked) vs integration (wired, real infra).
- @Mock (fake) vs @Spy (real, partial) vs @InjectMocks (subject).
- Slices: @SpringBootTest (full), @WebMvcTest (web), @DataJpaTest (persistence).
- Testcontainers for real-DB fidelity; test pyramid = many unit, few integration.

---

# 36. Design Patterns

For each: problem → solution → Java → Spring usage → interview Q.

## Creational

### Singleton
- **Problem:** exactly one shared instance (config, connection pool).
- **Thread-safe options:** eager static final, or **enum** (best — serialization/reflection-safe), or **holder idiom**.
```java
public enum Config { INSTANCE; private final Map<String,String> props = load(); }
// Holder idiom (lazy + thread-safe without locking):
class Singleton {
    private Singleton() {}
    private static class Holder { static final Singleton I = new Singleton(); }
    public static Singleton get() { return Holder.I; }
}
```
- **Spring:** every singleton-scoped bean (default) — Spring manages one instance per container.
- **Q:** *Double-checked locking pitfall?* → The field must be `volatile`, else a partially constructed object can be published (reordering). Enum/holder avoid the issue.

### Factory / Abstract Factory
- **Problem:** create objects without exposing instantiation logic; decide the concrete type at runtime.
```java
interface Notifier { void send(String msg); }
class NotifierFactory {
    static Notifier create(Channel c) {
        return switch (c) { case SMS -> new SmsNotifier(); case EMAIL -> new EmailNotifier(); };
    }
}
```
- **Spring:** `BeanFactory`/`ApplicationContext` is a giant factory; `FactoryBean`.
- **Q:** *Factory vs Abstract Factory?* → Factory creates one product; Abstract Factory creates families of related products behind an interface.

### Builder
- **Problem:** construct complex objects with many optional params without telescoping constructors.
```java
User u = User.builder().name("Ana").email("a@x.com").age(30).build();
```
- **Spring/libs:** Lombok `@Builder`, `UriComponentsBuilder`, `Stream.Builder`.
- **Q:** *Builder vs many constructors?* → Builder is readable, avoids param-order bugs, supports immutability and validation in `build()`.

## Structural

### Adapter
- **Problem:** make an incompatible interface usable by wrapping it.
- **Spring:** `HandlerAdapter` in MVC; `HandlerInterceptorAdapter`.
- **Q:** *Adapter vs Decorator?* → Adapter changes the interface; Decorator keeps the interface but adds behavior.

### Decorator
- **Problem:** add responsibilities dynamically without subclassing.
```java
Reader r = new BufferedReader(new FileReader("f")); // decorators wrapping streams
```
- **Spring:** `BeanPostProcessor` wrapping; `TransactionAwareCacheDecorator`.

### Proxy
- **Problem:** a stand-in controlling access to another object (lazy load, security, remoting, AOP).
- **Spring:** AOP proxies (JDK/CGLIB), `@Transactional`, lazy-loading Hibernate proxies.
- **Q:** *Proxy vs Decorator?* → Both wrap; proxy controls access/lifecycle, decorator adds behavior.

## Behavioral

### Strategy
- **Problem:** select an algorithm at runtime; swap behavior.
```java
interface DiscountStrategy { BigDecimal apply(BigDecimal price); }
class Checkout { DiscountStrategy strategy; BigDecimal total(BigDecimal p){ return strategy.apply(p);} }
```
- **Spring:** inject a `Map<String, Strategy>` of beans and pick by key; `PasswordEncoder` implementations.
- **Q:** *Strategy vs inheritance?* → Strategy uses composition — swap at runtime, avoid class explosion.

### Observer
- **Problem:** notify many dependents when state changes (pub/sub).
- **Spring:** `ApplicationEventPublisher` + `@EventListener`; `@TransactionalEventListener`.

### Template Method
- **Problem:** fix the skeleton of an algorithm, let subclasses fill steps.
- **Spring:** `JdbcTemplate`, `RestTemplate`, `AbstractController` — framework controls the flow, you supply callbacks.
- **Q:** *Template Method vs Strategy?* → Template uses inheritance (compile-time steps); Strategy uses composition (runtime swap).

### Chain of Responsibility
- **Problem:** pass a request along a chain until one handles it.
- **Spring:** the **Security filter chain**, servlet filters, MVC interceptors.

## Interview Q&A
#### Q1. Which design patterns does Spring use?
**Answer:** Many. Singleton (bean scope), Factory (the container/`FactoryBean`), Proxy (AOP, `@Transactional`, lazy Hibernate proxies), Template Method (`JdbcTemplate`/`RestTemplate`), Strategy (pluggable beans like `PasswordEncoder`), Observer (application events), Chain of Responsibility (security/servlet filters), Decorator (bean post-processing). Knowing these shows I understand the framework's internals, not just its annotations.

### Quick Revision
- Creational: Singleton (enum/holder), Factory/Abstract Factory, Builder.
- Structural: Adapter (change interface), Decorator (add behavior), Proxy (control access).
- Behavioral: Strategy (swap algo), Observer (events), Template (skeleton), CoR (filters).
- Spring is a live catalog of these patterns.

---

# 37. SOLID Principles

## S — Single Responsibility
A class should have one reason to change (one responsibility).
```java
// BAD: does persistence, email, and reporting
class UserService {
    void save(User u) {}
    void sendWelcomeEmail(User u) {}
    byte[] generateReport() {}
}
// GOOD: split responsibilities
class UserRepository { void save(User u) {} }
class EmailService { void sendWelcome(User u) {} }
class ReportService { byte[] generate() {} }
```
**Why:** changes to email logic shouldn't risk breaking persistence; smaller classes are easier to test and reuse.

## O — Open/Closed
Open for extension, closed for modification — add behavior without editing existing code.
```java
// BAD: adding a shape means editing this method
double area(Object shape) { if (shape instanceof Circle c) ...; else if (...) ...; }
// GOOD: extend via new implementations
interface Shape { double area(); }
class Circle implements Shape { public double area() { return Math.PI*r*r; } }
class Square implements Shape { public double area() { return s*s; } }
double total(List<Shape> shapes) { return shapes.stream().mapToDouble(Shape::area).sum(); }
```
**Why:** new requirements = new classes, not risky edits to tested code. (Strategy/polymorphism enable this.)

## L — Liskov Substitution
Subtypes must be usable anywhere their base type is expected without breaking behavior.
```java
// VIOLATION: Square breaks Rectangle's contract
class Rectangle { void setW(int w){} void setH(int h){} }
class Square extends Rectangle { /* setW also sets H -> breaks callers expecting independent w/h */ }
```
**Why:** if a subtype violates the parent's contract (e.g., throws on a valid operation, changes invariants), polymorphism becomes unsafe. Fix by modeling correctly (don't force Square to be a Rectangle).

## I — Interface Segregation
Prefer many small, focused interfaces over one fat interface; clients shouldn't depend on methods they don't use.
```java
// BAD: forces printers that can't fax to implement fax()
interface Machine { void print(); void scan(); void fax(); }
// GOOD:
interface Printer { void print(); }
interface Scanner { void scan(); }
```

## D — Dependency Inversion
Depend on abstractions, not concretions. High-level modules and low-level modules both depend on interfaces.
```java
// BAD: service hard-wired to a concrete DB class
class OrderService { private final MySqlOrderDao dao = new MySqlOrderDao(); }
// GOOD: depend on an interface, inject the implementation
class OrderService {
    private final OrderRepository repo;      // abstraction
    OrderService(OrderRepository repo) { this.repo = repo; } // injected
}
```
**Why:** this is exactly what Spring DI enables — swap implementations, mock in tests, decouple layers.

## Interview Q&A
#### Q1. How does SOLID show up in a Spring app?
**Answer:** SRP maps to layered classes (controller/service/repository each with one job). OCP shows up as adding new strategy beans instead of editing `if/else` chains. LSP means my interface implementations honor the same contract so injection is safe. ISP is why Spring Data splits `CrudRepository`/`PagingAndSortingRepository`. DIP is the whole point of dependency injection — services depend on repository interfaces, and Spring injects the concrete bean, which also makes them trivially mockable.

**Counter Q:** Give a concrete Open/Closed example.
**Answer:** A payment processor: instead of a `switch` over payment types, I define a `PaymentStrategy` interface and one bean per method (`CardPayment`, `UpiPayment`). Adding a new method means adding a class, not modifying the dispatcher — the existing tested code stays untouched.

**Counter Q:** How is DIP different from DI?
**Answer:** DIP is the principle — depend on abstractions. DI is a technique/pattern to achieve it by injecting those abstractions. Spring's IoC container is the mechanism.

### Quick Revision
- S: one reason to change.
- O: extend via new classes, don't modify existing.
- L: subtypes honor base contract (safe substitution).
- I: small focused interfaces.
- D: depend on abstractions (DI enables it).

---

# 38. Coding Questions

Format per problem: Problem → Approach → Code → Complexity → Alternative → Follow-ups.

## Strings

### 38.1 Reverse a string
- **Approach:** two-pointer swap on a char array (in place).
```java
static String reverse(String s) {
    char[] a = s.toCharArray();
    for (int i = 0, j = a.length - 1; i < j; i++, j--) { char t = a[i]; a[i] = a[j]; a[j] = t; }
    return new String(a);
}
```
- **Complexity:** O(n) time, O(n) space (String immutable). **Alt:** `new StringBuilder(s).reverse()`.
- **Follow-up:** *Reverse words, not chars?* Split on spaces, reverse the array, join. *Unicode/surrogate pairs?* Use code points, not chars.

### 38.2 First non-repeating character
```java
static Character firstUnique(String s) {
    Map<Character,Integer> c = new LinkedHashMap<>();
    for (char ch : s.toCharArray()) c.merge(ch, 1, Integer::sum);
    return c.entrySet().stream().filter(e -> e.getValue() == 1).map(Map.Entry::getKey).findFirst().orElse(null);
}
```
- **Complexity:** O(n). **LinkedHashMap** preserves first-seen order. **Alt:** int[128] for ASCII.
- **Follow-up:** *Stream of chars?* Maintain counts + a queue of candidates.

### 38.3 Anagram check
```java
static boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    int[] cnt = new int[26];
    for (int i = 0; i < a.length(); i++) { cnt[a.charAt(i)-'a']++; cnt[b.charAt(i)-'a']--; }
    for (int x : cnt) if (x != 0) return false;
    return true;
}
```
- **Complexity:** O(n). **Alt:** sort both O(n log n). **Follow-up:** *Unicode?* Use a `HashMap<Integer,Integer>` on code points.

### 38.4 Palindrome
```java
static boolean isPalindrome(String s) {
    for (int i = 0, j = s.length()-1; i < j; i++, j--) if (s.charAt(i) != s.charAt(j)) return false;
    return true;
}
```
- **Follow-up:** *Ignore case/non-alphanumeric?* Filter/normalize first.

### 38.5 Longest substring without repeating characters
- **Approach:** sliding window + last-seen index map; move left pointer past duplicates.
```java
static int longestUnique(String s) {
    Map<Character,Integer> last = new HashMap<>();
    int left = 0, best = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (last.containsKey(c) && last.get(c) >= left) left = last.get(c) + 1;
        last.put(c, right);
        best = Math.max(best, right - left + 1);
    }
    return best;
}
```
- **Complexity:** O(n) time, O(min(n, charset)) space.
- **Follow-up:** *Return the substring?* Track best start index.

## Arrays

### 38.6 Two Sum
```java
static int[] twoSum(int[] nums, int target) {
    Map<Integer,Integer> seen = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int need = target - nums[i];
        if (seen.containsKey(need)) return new int[]{seen.get(need), i};
        seen.put(nums[i], i);
    }
    return new int[0];
}
```
- **Complexity:** O(n) time/space. **Alt:** sort + two pointers O(n log n) if indices not needed.
- **Follow-up:** *Sorted input?* Two pointers, O(1) space. *All pairs / duplicates?*

### 38.7 Missing number (0..n)
```java
static int missing(int[] a) {          // XOR avoids overflow
    int x = a.length;
    for (int i = 0; i < a.length; i++) x ^= i ^ a[i];
    return x;
}
```
- **Alt:** sum formula `n(n+1)/2 - actual` (watch overflow). **Follow-up:** *Two missing?*

### 38.8 Find the duplicate number
```java
// Floyd's cycle detection (values in 1..n), O(n) time O(1) space, no mutation
static int findDup(int[] a) {
    int slow = a[0], fast = a[0];
    do { slow = a[slow]; fast = a[a[fast]]; } while (slow != fast);
    slow = a[0];
    while (slow != fast) { slow = a[slow]; fast = a[fast]; }
    return slow;
}
```
- **Follow-up:** *Why Floyd's?* O(1) space without modifying the array.

### 38.9 Maximum subarray (Kadane)
```java
static int maxSubArray(int[] a) {
    int cur = a[0], best = a[0];
    for (int i = 1; i < a.length; i++) { cur = Math.max(a[i], cur + a[i]); best = Math.max(best, cur); }
    return best;
}
```
- **Complexity:** O(n). **Follow-up:** *Return indices? Circular array? 2D version?*

### 38.10 Merge intervals
```java
static int[][] merge(int[][] iv) {
    Arrays.sort(iv, Comparator.comparingInt(a -> a[0]));
    List<int[]> out = new ArrayList<>();
    for (int[] cur : iv) {
        if (!out.isEmpty() && cur[0] <= out.get(out.size()-1)[1])
            out.get(out.size()-1)[1] = Math.max(out.get(out.size()-1)[1], cur[1]);
        else out.add(cur);
    }
    return out.toArray(new int[0][]);
}
```
- **Complexity:** O(n log n) (sort dominates). **Follow-up:** *Insert one interval into a sorted list?*

### 38.11 Rotate array by k
```java
static void rotate(int[] a, int k) {   // reverse-based, O(1) extra space
    k %= a.length; reverse(a,0,a.length-1); reverse(a,0,k-1); reverse(a,k,a.length-1);
}
static void reverse(int[] a,int i,int j){ while(i<j){int t=a[i];a[i++]=a[j];a[j--]=t;} }
```

## Collections & Streams

### 38.12 Frequency map / group / top-K
```java
// Frequency
Map<String,Long> freq = list.stream().collect(Collectors.groupingBy(x->x, Collectors.counting()));

// Group objects
Map<Dept, List<Employee>> byDept = emps.stream().collect(Collectors.groupingBy(Employee::dept));

// Top K frequent (heap)
static List<Integer> topK(int[] nums, int k) {
    Map<Integer,Long> f = Arrays.stream(nums).boxed().collect(Collectors.groupingBy(x->x, Collectors.counting()));
    PriorityQueue<Map.Entry<Integer,Long>> pq = new PriorityQueue<>(Comparator.comparingLong(Map.Entry::getValue));
    for (var e : f.entrySet()) { pq.offer(e); if (pq.size() > k) pq.poll(); }
    List<Integer> res = new ArrayList<>();
    while (!pq.isEmpty()) res.add(pq.poll().getKey());
    Collections.reverse(res);
    return res;
}
```
- **Top-K complexity:** O(n log k) with a min-heap of size k.

## Java-specific classics

### 38.13 Immutable class
```java
public final class ImmutablePoint {
    private final int x, y;
    private final int[] data;
    public ImmutablePoint(int x, int y, int[] data) {
        this.x = x; this.y = y; this.data = data.clone(); // defensive copy in
    }
    public int getX(){ return x; }
    public int[] getData(){ return data.clone(); }        // defensive copy out
}
```

### 38.14 Thread-safe Singleton
```java
// Enum (best): serialization- and reflection-safe, lazy-ish, simplest
public enum Singleton { INSTANCE; public void doWork(){} }

// Double-checked locking (if you need lazy + a class)
public class Config {
    private static volatile Config instance;   // volatile is REQUIRED
    private Config() {}
    public static Config get() {
        if (instance == null) {
            synchronized (Config.class) {
                if (instance == null) instance = new Config();
            }
        }
        return instance;
    }
}
```

### 38.15 Custom Comparator / custom exception
```java
emps.sort(Comparator.comparingDouble(Employee::salary).reversed().thenComparing(Employee::name));

class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String msg) { super(msg); }
}
```

### 38.16 Producer-Consumer (BlockingQueue)
```java
BlockingQueue<Integer> q = new LinkedBlockingQueue<>(100);
Runnable producer = () -> { try { for (int i=0;;i++) q.put(i); } catch (InterruptedException e){ Thread.currentThread().interrupt(); } };
Runnable consumer = () -> { try { while (true) process(q.take()); } catch (InterruptedException e){ Thread.currentThread().interrupt(); } };
```
- `put`/`take` block on full/empty — the queue handles all synchronization. **Follow-up:** *Without BlockingQueue?* Use `wait/notify` on a shared buffer.

### 38.17 LRU Cache
```java
// Simple: LinkedHashMap with accessOrder + removeEldestEntry
class LRUCache<K,V> extends LinkedHashMap<K,V> {
    private final int cap;
    LRUCache(int cap){ super(cap, 0.75f, true); this.cap = cap; } // true = access-order
    @Override protected boolean removeEldestEntry(Map.Entry<K,V> e){ return size() > cap; }
}
```
- **From scratch:** `HashMap` + doubly-linked list → O(1) get/put (the classic LeetCode 146). **Follow-up:** *Thread-safe LRU?* Wrap with locks or use Caffeine.

### 38.18 Simple multithreading — print odd/even alternately
```java
class OddEven {
    private final Object lock = new Object();
    private int n = 1; private final int max;
    OddEven(int max){ this.max = max; }
    void printOdd() { print(true); }
    void printEven(){ print(false); }
    private void print(boolean odd) {
        synchronized (lock) {
            while (n <= max) {
                if ((n % 2 == 1) == odd) { System.out.println(n++); lock.notifyAll(); }
                else { try { lock.wait(); } catch (InterruptedException e){ Thread.currentThread().interrupt(); return; } }
            }
            lock.notifyAll();
        }
    }
}
```

### Follow-up questions bank (for any coding problem)
- *What's the time/space complexity? Can you do better?*
- *What if the input doesn't fit in memory?* (streaming / external sort)
- *How would you test edge cases?* (empty, single element, duplicates, overflow, nulls)
- *Make it thread-safe?* *Handle Unicode?* *Return the actual result, not just the count?*

---

# 39. Project-Based Questions

These are the make-or-break questions for a 3-year dev. Have a concrete project in mind and answer with specifics. Below are strong template answers — adapt to *your* project.

### Explain your project architecture.
> "It's a Spring Boot REST backend in a layered architecture: controllers handle HTTP and validation, services hold business logic and transactions, repositories (Spring Data JPA) handle persistence against PostgreSQL. DTOs decouple the API from entities, mapped with MapStruct. We secure it with JWT, cache hot reads in Redis, and expose metrics via Actuator/Micrometer to Prometheus. It's deployed as a Docker container on Kubernetes."

### Why Spring Boot? Why REST?
> "Spring Boot gives us auto-configuration, embedded Tomcat, and a huge ecosystem, so we ship features instead of wiring plumbing. REST because our clients are web/mobile needing stateless, cacheable, standard HTTP semantics, and it's simple to version and document with OpenAPI."

### How does a request flow through your application?
> "DispatcherServlet → the mapped controller → validation of the request DTO → service layer where the `@Transactional` business logic runs → repository queries the DB → the result is mapped to a response DTO → Jackson serializes JSON. Cross-cutting concerns like auth (filter), logging (interceptor/AOP), and error mapping (`@RestControllerAdvice`) wrap this."

### How do you handle exceptions?
> "A global `@RestControllerAdvice` maps domain exceptions to consistent error responses — e.g., `NotFoundException` → 404, validation errors → 400 with field details, and a catch-all → 500 with a correlation id but no internal leakage. Services throw meaningful domain exceptions rather than returning nulls."

### How do you validate requests?
> "Bean Validation on request DTOs (`@NotNull`, `@Email`, `@Size`) triggered by `@Valid`; validation failures are turned into structured 400s in the advice. For cross-field or business rules I add custom validators or check in the service."

### How does authentication work / JWT in your app?
> "Stateless JWT. On login I verify credentials via `UserDetailsService` + BCrypt, issue a short-lived access token and a refresh token. A `OncePerRequestFilter` validates the Bearer token's signature and expiry, builds the `Authentication`, and sets the `SecurityContext`; `@PreAuthorize` enforces roles on endpoints."

### How do you handle database transactions?
> "`@Transactional` at the service layer. Multi-step operations run in one transaction so they're atomic; I use REQUIRES_NEW for audit writes that must persist regardless, and readOnly for pure reads. I'm careful about self-invocation bypassing the proxy."

### How do you prevent duplicate requests / handle concurrency?
> "For duplicate submissions I use an idempotency key stored in Redis plus DB unique constraints. For concurrent updates to the same row I use optimistic locking with `@Version` so conflicting updates fail fast with a 409 instead of silently overwriting."

### How do you optimize slow APIs / find a slow query?
> "I start with metrics and traces to find the slow endpoint, then enable SQL logging / use the DB's EXPLAIN to find missing indexes or full scans. Common wins: add/adjust indexes, fix N+1 with JOIN FETCH or EntityGraph, project only needed columns, paginate, and cache hot reads in Redis."

### How do you handle N+1?
> "Detect it in SQL logs (a burst of similar queries), then fetch the association in one query with JOIN FETCH or an `@EntityGraph`, enable batch fetching, or map to a DTO projection."

### Where would you use Redis? How would you handle large traffic?
> "Redis for caching hot reads, session/rate-limit counters, and distributed locks. For large traffic: horizontal scaling behind a load balancer (stateless services), caching, DB read replicas and connection-pool tuning, async processing via Kafka for non-critical work, and autoscaling on Kubernetes."

### How do you debug production issues / monitor?
> "Structured logs with a correlation id in MDC, distributed tracing (Micrometer Tracing → Zipkin/Jaeger), and metrics/dashboards in Grafana with alerts. For a live incident I check dashboards and traces, pull logs by correlation id, and if needed capture a heap/thread dump."

### How do you handle failures between microservices / another service is down?
> "Timeouts on every call, a circuit breaker to fail fast and protect the failing service, retries with exponential backoff + jitter for idempotent calls only, and a fallback for graceful degradation. For data consistency across services I use sagas with compensating actions and the outbox pattern for reliable events."

### How do you avoid retry storms / connection pool exhaustion?
> "Cap retries, add backoff with jitter, and gate retries behind a circuit breaker. For the pool, I size HikariCP to the DB's capacity, set connection timeouts so requests fail fast instead of piling up, always close connections (framework-managed), and keep transactions short so connections return quickly."

### How do you deploy your Spring Boot application?
> "Build a fat JAR, containerize it with a slim JDK base image, push to a registry, and deploy to Kubernetes with liveness/readiness probes, config via ConfigMaps/Secrets, and rolling updates. CI/CD runs tests and builds on every merge."

**Interviewer tip:** For each, be ready for the "why" and "what did YOU do" follow-ups. Have one concrete story: a problem you diagnosed, the fix, and the measurable result (e.g., "cut p95 latency from 800ms to 120ms by fixing an N+1 and adding a Redis cache").

---

# 40. Tricky Questions

Rapid answers to the classic traps.

1. **Is Java pass-by-reference?** No — always pass-by-value (the reference's value is copied).
2. **Can we override static methods?** No — they're *hidden*, resolved by reference type.
3. **Can we override private methods?** No — not visible to subclasses; a new independent method.
4. **Can a constructor be inherited?** No.
5. **Can a constructor be overridden?** No (not polymorphic).
6. **Can an abstract class have a constructor?** Yes — invoked via subclass `super()`.
7. **Can an interface have a constructor?** No.
8. **Can an interface have variables?** Yes — implicitly `public static final` constants.
9. **Can an interface have static/default methods?** Yes (Java 8+); private methods too (Java 9+).
10. **Can a final reference be changed?** No — can't reassign the reference.
11. **Can a final object be modified?** Yes — `final` locks the reference, not the object's mutable state.
12. **Why is String immutable?** Pooling/security/thread-safety/hash caching.
13. **Why is StringBuilder faster?** Mutable buffer — no new object per modification.
14. **Why does `Integer ==` differ by value?** Cache -128..127 → same object; outside → new objects.
15. **equals overridden but not hashCode?** Breaks hash collections — equal objects may land in different buckets → lookups fail.
16. **Can HashMap have a null key?** Yes, one.
17. **Can ConcurrentHashMap have a null key?** No (ambiguity in concurrent get).
18. **ArrayList vs LinkedList?** Array (O(1) access) vs doubly-linked (O(1) ends, poor locality); ArrayList usually wins.
19. **HashMap vs Hashtable?** Unsynchronized+null key vs fully synchronized legacy, no null.
20. **HashMap vs ConcurrentHashMap?** Not thread-safe vs fine-grained thread-safe, no null keys/values.
21. **`==` vs equals?** Identity vs logical equality.
22. **throw vs throws?** Raise an exception vs declare it in a signature.
23. **final vs finally vs finalize?** Keyword modifier vs try-block always runs vs deprecated GC hook.
24. **checked vs unchecked?** Compiler-enforced recoverable vs runtime programming errors.
25. **sleep vs wait?** sleep keeps the lock (Thread); wait releases it (Object, needs synchronized).
26. **synchronized vs volatile?** Mutual exclusion+visibility vs visibility only (no atomicity).
27. **Runnable vs Callable?** void/no-throw vs returns value/throws, gives a Future.
28. **Future vs CompletableFuture?** Blocking get only vs composable non-blocking pipeline.
29. **JPA vs Hibernate?** Spec vs implementation.
30. **Spring vs Spring Boot?** Framework vs opinionated auto-configured layer on top.
31. **@Component vs @Bean?** Class scanned vs method-produced instance.
32. **@Controller vs @RestController?** View names vs `@ResponseBody` (JSON) on all methods.
33. **@Autowired vs constructor injection?** Field/reflection vs explicit, final, testable (preferred).
34. **@Transactional self-invocation?** Internal `this` call bypasses the proxy → no transaction.
35. **LAZY vs EAGER?** Load on access (proxy) vs load immediately with parent.
36. **save vs persist vs merge?** returns id / makes managed / copies detached state into managed copy.
37. **get vs load?** DB hit + null vs proxy + exception if missing.
38. **first-level vs second-level cache?** Per-session always-on vs shared opt-in across sessions.

---

# 41. Why Questions

The interviewer's favorite drill. Short, reasoned answers.

- **Why is String immutable?** Safe literal pooling/sharing, security (paths/credentials can't change after validation), inherent thread safety, and cached hash codes for fast map keys.
- **Why is String final?** So no subclass can break immutability by overriding behavior or adding mutable state.
- **Why override hashCode when overriding equals?** Hash collections locate by hashCode then match by equals; equal objects with different hashes land in different buckets → the map can't find/dedupe them.
- **Why does HashMap use hashCode?** To compute a bucket index in O(1), avoiding scanning every entry.
- **Why does HashMap resize?** To keep buckets short as it fills — beyond the load-factor threshold, chains lengthen and lookups degrade; doubling capacity restores O(1) average.
- **Why does Java use wrapper classes?** Generics/collections need objects, not primitives; plus nullability and utility methods.
- **Why is Java pass-by-value?** The language copies argument values (for objects, the reference value) — reassigning a parameter doesn't affect the caller, which proves it.
- **Why can't Java support multiple class inheritance?** To avoid the diamond ambiguity of conflicting state/methods; interfaces allow multiple type inheritance because you resolve default-method conflicts explicitly.
- **Why do we need interfaces?** To program to contracts/capabilities, enable polymorphism and multiple inheritance of type, and decouple callers from implementations (DIP).
- **Why is composition often preferred over inheritance?** Looser coupling, no fragile-base-class problem, expose only the API you want, and swap the delegate at runtime.
- **Why is constructor injection preferred in Spring?** Final/immutable dependencies, explicit required deps, testable without Spring, and circular dependencies fail fast.
- **Why does Spring use proxies?** To apply cross-cutting behavior (transactions, security, caching, logging) transparently without touching business code.
- **Why doesn't @Transactional work during self-invocation?** The transactional logic is in the proxy; an internal `this` call bypasses the proxy so no transaction starts.
- **Why does Hibernate use lazy loading?** To avoid loading entire object graphs eagerly — fetch associations only when actually needed, saving queries/memory.
- **Why does N+1 happen?** Loading N parents then triggering one lazy query per parent's association → 1 + N queries.
- **Why use DTOs?** Decouple the API from the schema, hide sensitive fields, avoid lazy-loading serialization issues, and version request/response independently.
- **Why use PreparedStatement?** Precompiled + parameters bound separately from SQL → prevents injection, enables caching/batching.
- **Why use connection pooling?** Opening connections is expensive; reuse warm ones to cut latency and bound total connections.
- **Why use indexes?** Turn full table scans into fast lookups (B-tree), at the cost of slower writes/storage.
- **Why use caching?** Serve frequent/expensive reads fast, reducing latency and backend load.
- **Why use Redis?** A fast, shared, in-memory store for distributed caching, counters, locks, and sessions across instances.
- **Why use microservices?** Independent deployment/scaling, team autonomy, fault isolation, tech diversity — accepting distributed-system complexity in return.
- **Why use Kafka?** Durable, partitioned, replayable event streaming with high throughput for decoupled, event-driven systems.

---

# 42. Counter-Question Training

How interviewers drill deeper. Practice speaking each full chain — the goal is to survive 5-7 "why?" levels without collapsing.

## Chain 1: Dependency Injection
- **Q: What is dependency injection?** → The container supplies a bean's dependencies instead of the bean creating them.
- **Why do we need it?** → Loose coupling and testability — depend on abstractions, not `new`.
- **What problems does it solve?** → Hard-wired dependencies, no easy mocking, scattered object construction, lifecycle management.
- **Why is constructor injection preferred?** → Final/immutable deps, explicit requirements, testable with plain `new`, fail-fast on cycles.
- **What if there are two implementations?** → `NoUniqueBeanDefinitionException`; resolve with `@Primary` or `@Qualifier`.
- **How does @Qualifier solve it?** → Names the specific bean to inject, overriding type-only matching.
- **How does Spring actually inject?** → Scans bean definitions, resolves each bean's dependencies from the context, injects via constructor/setter/field through a BeanPostProcessor.
- **When does Spring create the bean?** → Singletons eagerly at startup by default; prototypes on each request.

## Chain 2: HashMap
- **Q: What is a HashMap?** → Key-value store backed by a bucket array, O(1) average.
- **How does it work internally?** → hashCode → spread bits → `(n-1)&hash` index → bucket holds list/tree.
- **What happens on collision?** → Entries share a bucket as a linked list, then a red-black tree past threshold.
- **Why a tree in Java 8?** → O(log n) worst case instead of O(n) for pathological collisions.
- **What data structure is the tree?** → Red-black (self-balancing BST).
- **What's the load factor?** → 0.75; resize when size > capacity×0.75.
- **When does resizing happen and what does it do?** → Past threshold; capacity doubles and entries rehash (bit-split, order preserved).
- **Why must equals & hashCode be consistent?** → get finds bucket by hash then matches by equals; inconsistency breaks lookups.

## Chain 3: @Transactional
- **Q: What does @Transactional do?** → Wraps a method in a transaction via a proxy; commit on success, rollback on runtime exception.
- **Which exceptions roll back?** → Unchecked by default; checked commit unless `rollbackFor`.
- **How is it implemented?** → A proxy (JDK/CGLIB) around the bean applies the transaction advice.
- **Why doesn't it work on self-invocation?** → Internal `this` call skips the proxy.
- **How do you fix it?** → Self-inject the proxy, split into another bean, or AspectJ weaving.
- **What about a private method?** → Can't be proxied — no transaction at all.
- **What's REQUIRES_NEW for?** → Independent transaction (e.g., audit) that commits regardless of the outer one.

## Chain 4: JWT / Security
- **Q: How does JWT auth work?** → Signed token with claims; client sends it, server validates signature+expiry statelessly.
- **Is it encrypted?** → No, signed — payload is readable; no secrets inside.
- **How do you validate it?** → Verify signature with the key, check exp/issuer/audience.
- **Why stateless over sessions?** → Scales horizontally, no shared session store.
- **How do you revoke a token?** → Short TTL + refresh, denylist, or key rotation.
- **Why disable CSRF?** → No ambient cookie; the Bearer header isn't auto-sent.
- **What's the refresh token for?** → Get new short-lived access tokens without re-login.

## Chain 5: N+1 / Hibernate
- **Q: What's the N+1 problem?** → 1 query for parents + N queries for each parent's lazy association.
- **Why does it happen?** → Lazy associations accessed in a loop, one query each.
- **Why is lazy loading the default for collections?** → To avoid loading huge graphs eagerly.
- **How do you detect it?** → SQL logs showing repeated similar queries.
- **How do you fix it?** → JOIN FETCH, @EntityGraph, batch fetching, or DTO projections.
- **Why not just make it EAGER?** → EAGER over-fetches everywhere and can cause its own performance/cartesian-product problems; fetch per use case instead.

**Practice tip:** record yourself answering a chain end to end. If you stall at level 3+, that's the gap to study.

---

# 43. Rapid Fire

Cover the answer, quiz yourself, uncover.

1. **JDK vs JRE?** JDK = JRE + dev tools (compiler); JRE = JVM + libs to run.
2. **JVM platform independent?** No; bytecode is.
3. **Bytecode = machine code?** No; JIT converts hot bytecode to native.
4. **What is JIT?** Runtime compiler turning hot bytecode into optimized native code.
5. **Heap vs Stack?** Objects (shared) vs stack frames (per thread).
6. **Young gen parts?** Eden + 2 Survivor spaces.
7. **Default GC (Java 9+)?** G1.
8. **Metaspace vs PermGen?** Native auto-sizing vs fixed heap region (removed in Java 8).
9. **StackOverflow vs OOM?** Stack exhaustion vs heap/metaspace exhaustion.
10. **Java memory leak?** Reachable-but-unused objects GC can't reclaim.
11. **== vs equals?** Reference identity vs logical equality.
12. **Overriding vs overloading?** Runtime (actual type) vs compile-time (arg list).
13. **Can static be overridden?** No, hidden.
14. **Covariant return?** Overriding method may return a subtype.
15. **Abstract class vs interface?** State+partial impl+single vs contract+multiple.
16. **Default methods purpose?** Evolve interfaces without breaking implementers.
17. **equals/hashCode contract?** Equal objects → equal hash codes.
18. **String immutable why?** Pool/security/thread-safety/hash caching.
19. **new String("x") objects?** Up to 2 (pool literal + heap object).
20. **intern()?** Returns pooled reference for a string's content.
21. **StringBuilder vs StringBuffer?** Unsynchronized (fast) vs synchronized.
22. **Integer cache range?** -128..127.
23. **Autoboxing null → int?** NullPointerException.
24. **Widening vs narrowing?** Implicit safe vs explicit lossy.
25. **Upcast vs downcast?** Implicit safe vs explicit runtime-checked.
26. **Java pass-by?** Value (reference value for objects).
27. **var (Java 10)?** Local type inference; still static typing.
28. **record?** Immutable data carrier with auto methods.
29. **sealed class?** Restricts which types can extend/implement.
30. **Checked vs unchecked?** Compiler-enforced vs runtime.
31. **try-with-resources?** Auto-closes AutoCloseable, suppresses secondary exceptions.
32. **return in finally?** Overrides try's return/exception (anti-pattern).
33. **multi-catch types?** Must be disjoint (no parent+child).
34. **ArrayList vs LinkedList?** Array O(1) access vs linked O(1) ends.
35. **HashMap vs Hashtable?** Unsync + null key vs synced legacy.
36. **HashMap vs ConcurrentHashMap?** Unsafe vs fine-grained safe, no nulls.
37. **HashSet backing?** HashMap.
38. **TreeMap ordering?** Sorted by key (red-black tree).
39. **LinkedHashMap use?** Insertion/access order; LRU cache.
40. **Fail-fast vs fail-safe?** CME on modification vs snapshot iteration.
41. **HashMap treeify threshold?** 8 (with capacity ≥ 64).
42. **HashMap default cap / LF?** 16 / 0.75.
43. **HashMap index formula?** `(n-1) & (h ^ h>>>16)`.
44. **Why power-of-two capacity?** `&` indexing + easy bit-split resize.
45. **Comparable vs Comparator?** Natural (compareTo) vs external (compare).
46. **TreeSet uniqueness by?** compareTo, not equals.
47. **Type erasure?** Generics removed at runtime.
48. **PECS?** Producer extends, consumer super.
49. **Generics variance?** Invariant.
50. **Functional interface?** One abstract method.
51. **Predicate/Function/Consumer/Supplier?** test/apply/accept/get.
52. **Intermediate vs terminal op?** Lazy vs eager (triggers pipeline).
53. **Reuse a stream?** No (IllegalStateException).
54. **map vs flatMap?** Transform vs flatten nested streams.
55. **Parallel stream pool?** Common ForkJoinPool (shared).
56. **orElse vs orElseGet?** Eager value vs lazy supplier.
57. **Optional as field?** No — return type only.
58. **Runnable vs Callable?** void/no-throw vs value/throws.
59. **Future vs CompletableFuture?** Blocking vs composable.
60. **thenApply vs thenCompose?** map vs flatMap for CF.
61. **synchronized vs Lock?** Simple implicit vs flexible (tryLock/timeout/fair).
62. **volatile guarantees?** Visibility/ordering, not atomicity.
63. **CAS?** Compare-and-swap lock-free update.
64. **sleep vs wait?** Keeps lock vs releases (needs synchronized).
65. **notify vs notifyAll?** One vs all waiters.
66. **Deadlock avoidance?** Consistent lock ordering + timeouts.
67. **Daemon thread?** Background; JVM exits when only daemons remain.
68. **ThreadPoolExecutor key params?** core/max/queue/keep-alive/rejection.
69. **serialVersionUID?** Version stamp; mismatch → InvalidClassException.
70. **transient?** Field skipped in serialization.
71. **IO vs NIO?** Blocking stream vs buffer/channel non-blocking.
72. **Statement vs PreparedStatement?** Raw SQL vs precompiled parameterized.
73. **Why PreparedStatement safe?** SQL compiled separately from data.
74. **Connection pool?** Reuse warm connections (HikariCP).
75. **JDBC vs JPA vs Hibernate?** API vs spec vs implementation.
76. **POJO vs JavaBean?** Plain vs convention (no-arg ctor + accessors).
77. **Why DTO?** Decouple API from entity; hide fields; avoid lazy issues.
78. **Hibernate entity states?** Transient/persistent/detached/removed.
79. **save vs persist vs merge?** id / managed / detached→managed copy.
80. **get vs load?** DB now+null vs proxy+exception.
81. **L1 vs L2 cache?** Per-session vs shared opt-in.
82. **Dirty checking?** Auto UPDATE of changed managed entities on flush.
83. **N+1?** 1 parent query + N association queries.
84. **N+1 fix?** JOIN FETCH / EntityGraph / batch / DTO.
85. **Optimistic vs pessimistic lock?** @Version check vs DB row lock.
86. **JPA owning side?** Holds the FK; inverse uses mappedBy.
87. **@Enumerated best?** STRING (stable).
88. **IoC vs DI?** Container controls creation vs injects deps.
89. **BeanFactory vs ApplicationContext?** Basic lazy vs full eager+events.
90. **Preferred injection?** Constructor.
91. **@Component vs @Bean?** Scanned class vs factory method.
92. **Default bean scope?** Singleton.
93. **Bean lifecycle init?** @PostConstruct.
94. **AOP proxy types?** JDK (interface) / CGLIB (subclass).
95. **Self-invocation problem?** Internal call bypasses proxy → no advice/tx.
96. **DispatcherServlet?** Front controller routing all requests.
97. **@Controller vs @RestController?** View vs @ResponseBody JSON.
98. **Filter vs Interceptor?** Servlet-level vs MVC-level.
99. **@ControllerAdvice?** Global exception handling.
100. **Spring vs Spring Boot?** Framework vs opinionated auto-config layer.
101. **@SpringBootApplication?** @Configuration + @EnableAutoConfiguration + @ComponentScan.
102. **How auto-config works?** Conditional classes (@ConditionalOnClass/MissingBean).
103. **Disable auto-config?** exclude in @SpringBootApplication / properties.
104. **Starter?** Version-aligned dependency bundle.
105. **@ConfigurationProperties?** Bind property group to typed POJO.
106. **@Transactional rollback default?** Unchecked exceptions.
107. **REQUIRES_NEW?** Independent suspended transaction.
108. **Isolation phenomena?** Dirty/non-repeatable/phantom read.
109. **REST idempotent methods?** GET/PUT/DELETE.
110. **201 vs 204?** Created (+Location) vs no content.
111. **401 vs 403?** Not authenticated vs not authorized.
112. **PUT vs PATCH?** Full replace vs partial update.
113. **JWT structure?** header.payload.signature; signed not encrypted.
114. **Access vs refresh token?** Short-lived vs long-lived renewal.
115. **AuthN vs AuthZ?** Identity vs permissions.
116. **BCrypt?** Adaptive salted password hash.
117. **Circuit breaker?** Fail fast after repeated failures.
118. **Retry storm fix?** Backoff + jitter + circuit breaker + caps.
119. **Kafka vs RabbitMQ?** Log/stream vs broker/queue.
120. **Saga?** Local transactions + compensations for distributed consistency.
121. **Outbox pattern?** Atomic DB write + reliable event publish.
122. **Cache-aside?** App loads on miss and populates.
123. **orElse cache stampede fix?** Single-flight + jittered TTL.
124. **Index tradeoff?** Faster reads, slower writes.
125. **WHERE vs HAVING?** Pre-group rows vs post-group aggregates.
126. **Offset vs cursor pagination?** Slow deep pages vs fast keyset.
127. **Mock vs Spy?** Full fake vs real with partial stubbing.
128. **@WebMvcTest vs @SpringBootTest?** Web slice vs full context.
129. **Singleton best impl?** Enum.
130. **Strategy pattern in Spring?** Inject Map of beans / PasswordEncoder.

---

# 44. Mock Interviews

Try to answer out loud before reading the expected answers.

## Mock Interview 1 — Java Core (Questions)
1. Explain JDK, JRE, JVM and how a program runs.
2. Is the JVM platform independent?
3. What is JIT and what runs before it?
4. Explain the heap generations and minor vs full GC.
5. What causes a memory leak despite GC?
6. Why is String immutable and final?
7. How many objects does `new String("abc")` create?
8. Explain the Integer cache and `==` behavior.
9. Is Java pass-by-value or reference? Prove it.
10. `final` variable vs `final` object.
11. Widening vs narrowing casting.
12. Upcasting vs downcasting; when does ClassCastException happen?
13. Checked vs unchecked exceptions.
14. What does try-with-resources do?
15. What happens with `return` in `finally`?
16. What are records and sealed classes?
17. StringBuilder vs StringBuffer.
18. What is `var` and is it dynamic typing?
19. What is the difference between `==` and `equals`?
20. Why override hashCode with equals?

### Mock 1 — Expected Answers
1. JVM executes bytecode; JRE = JVM + libs; JDK = JRE + tools. `javac` → bytecode → JVM interprets + JIT-compiles → native.
2. No; bytecode is portable, the JVM is platform-specific.
3. JIT compiles hot bytecode to native at runtime; the interpreter runs it beforehand (mixed mode).
4. Young (Eden+2 survivors) → Old; minor GC collects young (frequent/cheap), full GC collects the whole heap (rare/expensive).
5. Reachable but unused objects (static caches, unremoved listeners, ThreadLocals).
6. Immutable for pooling/security/thread-safety/hash caching; final so subclasses can't break it.
7. Up to two (pooled literal + heap object).
8. `valueOf` caches -128..127 → `==` true in range, false outside; use `equals`.
9. Pass-by-value; mutating a passed object is visible but reassigning the param isn't → proves value semantics.
10. `final` var can't be reassigned; a final reference's object can still mutate.
11. Widening implicit/safe; narrowing explicit/lossy.
12. Upcast implicit/safe; downcast explicit, runtime-checked; CCE when the object isn't actually that subtype.
13. Checked = compiler-enforced recoverable; unchecked = runtime programming errors.
14. Auto-closes AutoCloseable resources in reverse order, suppressing secondary exceptions.
15. It overrides any try/catch return or exception — swallows them (anti-pattern).
16. Records = immutable data carriers with generated methods; sealed = restrict permitted subtypes.
17. Both mutable; StringBuilder unsynchronized (faster), StringBuffer synchronized.
18. Compile-time local type inference; still static typing.
19. Reference identity vs logical equality.
20. Hash collections locate by hash then equals; equal objects need equal hashes or lookups fail.

## Mock Interview 2 — OOP + Collections (Questions)
1. Four OOP pillars in one line each.
2. Overriding rules (return type, exceptions, access).
3. Can you override static/private/final methods?
4. Composition vs inheritance — when each?
5. Abstract class vs interface.
6. Diamond problem with default methods.
7. equals/hashCode contract.
8. ArrayList vs LinkedList internals.
9. HashMap internal working end to end.
10. What triggers treeification and resizing?
11. Why must keys be immutable?
12. HashMap vs ConcurrentHashMap.
13. Comparable vs Comparator.
14. What if a Comparator is inconsistent with equals?
15. Fail-fast vs fail-safe iterators.
16. Set implementations and their ordering.
17. Type erasure consequences.
18. Explain PECS.
19. When is LinkedList actually better?
20. How does TreeSet decide duplicates?

### Mock 2 — Expected Answers
1. Encapsulation (hide state), Inheritance (reuse/specialize), Polymorphism (one interface many impls), Abstraction (what not how).
2. Same/covariant return, no broader checked exceptions, no reduced visibility.
3. No to all — static hidden, private invisible, final locked.
4. Composition for reuse (loose coupling); inheritance for real IS-A + polymorphism.
5. Abstract = state+partial impl+single; interface = contract+multiple inheritance.
6. Implementing class must override and can call `Interface.super.method()`.
7. Equal objects → equal hashCodes; equal hashCodes don't require equals.
8. Dynamic array (O(1) access) vs doubly-linked (O(1) ends, poor locality).
9. hashCode → spread → `(n-1)&hash` → bucket list/tree; on collision walk + equals; treeify/resize as needed.
10. Bucket ≥ 8 with capacity ≥ 64 → treeify; size > cap×0.75 → resize (double + rehash).
11. Mutating hash-affecting fields makes the key unfindable.
12. Not thread-safe vs fine-grained safe, no null keys/values.
13. Natural (compareTo, inside class) vs external (compare, many).
14. Sorting works but TreeSet/TreeMap may drop/duplicate "unequal" elements.
15. CME on structural modification vs snapshot iteration.
16. HashSet (none), LinkedHashSet (insertion), TreeSet (sorted).
17. No `new T()`, no `T[]`, no reified generic instanceof; enables legacy compat.
18. Producer extends (read), consumer super (write).
19. Rarely — deque/queue with heavy end operations (ArrayDeque usually better).
20. By compareTo/compare returning 0, not equals.

## Mock Interview 3 — Multithreading (Questions)
1. Process vs thread.
2. Runnable vs Callable vs Future.
3. Why use thread pools; risks of unbounded pools?
4. synchronized vs ReentrantLock.
5. volatile vs Atomic; is `count++` safe if volatile?
6. What is CAS and its downsides?
7. sleep vs wait; notify vs notifyAll.
8. How to prevent deadlock?
9. ThreadPoolExecutor parameters.
10. Future vs CompletableFuture.
11. thenApply vs thenCompose.
12. Why pass a custom Executor to CompletableFuture?
13. exceptionally vs handle vs whenComplete.
14. allOf vs anyOf.
15. What is a daemon thread?
16. How does interrupt work?
17. Race condition example and fix.
18. ForkJoinPool and work stealing.
19. Producer-consumer with BlockingQueue.
20. Livelock vs starvation.

### Mock 3 — Expected Answers
1. Process = own memory; thread = shares heap, own stack.
2. void/no-throw vs value/throws; Future = handle to async result.
3. Reuse threads, bound resources; unbounded → OOM/thread exhaustion.
4. Implicit simple vs explicit flexible (tryLock/timeout/fair).
5. Visibility vs lock-free atomicity; `count++` NOT safe (read-modify-write).
6. Atomic set-if-expected; downsides: spin under contention, ABA.
7. sleep keeps lock; wait releases (needs synchronized); notify one vs notifyAll all.
8. Consistent lock ordering, tryLock timeouts, minimal lock scope.
9. core/max pool, keep-alive, work queue, rejection policy.
10. Blocking get only vs composable non-blocking pipeline.
11. map vs flatMap (mapper returns a CF).
12. Common pool is CPU-sized/shared; blocking IO starves it.
13. recover-on-error vs handle both vs observe side-effect.
14. wait for all vs complete on first.
15. Background thread; JVM exits when only daemons remain.
16. Cooperative signal; code checks isInterrupted/handles InterruptedException.
17. Two threads incrementing shared counter; fix with Atomic/synchronized.
18. Divide-and-conquer with idle threads stealing tasks.
19. put/take block on full/empty; queue handles synchronization.
20. Threads keep reacting without progress vs never getting resources.

## Mock Interview 4 — Spring Core (Questions)
1. IoC and DI.
2. BeanFactory vs ApplicationContext.
3. Injection types; which is preferred and why?
4. Circular dependency handling.
5. Two implementations of an interface — how to resolve?
6. @Component vs @Bean.
7. Bean scopes and the prototype-in-singleton trap.
8. Bean lifecycle order.
9. What is a BeanPostProcessor?
10. @PostConstruct vs InitializingBean.
11. How does Spring AOP work?
12. JDK proxy vs CGLIB.
13. Self-invocation problem.
14. Can you advise private methods?
15. What is a pointcut?
16. Types of advice.
17. @Primary vs @Qualifier.
18. @Value vs @ConfigurationProperties.
19. When are singleton beans created?
20. What does @Repository add over @Component?

### Mock 4 — Expected Answers
1. Container controls creation; injects dependencies.
2. Basic lazy container vs full-featured (eager, events, env, AOP).
3. Constructor/setter/field; constructor (final, explicit, testable, fail-fast).
4. Setter/field cycles resolved via early reference; constructor cycles fail fast.
5. @Primary default or @Qualifier by name.
6. Scanned class vs factory method for any/library class.
7. singleton/prototype/request/session; prototype injected once into singleton (use ObjectProvider).
8. Instantiate → inject → aware → BPP-before → init → BPP-after → ready → destroy.
9. Hook to modify/wrap beans (applies AOP/tx proxies).
10. Annotation-based init preferred over the interface.
11. Proxy wraps bean; advice runs around real method.
12. Interface-based vs subclass-based (default in Boot); CGLIB can't proxy final.
13. Internal `this` call bypasses proxy → advice/tx skipped.
14. No — only externally visible methods.
15. Expression selecting join points.
16. before/after/afterReturning/afterThrowing/around.
17. Default winner vs explicit name selection.
18. Single property vs typed group binding.
19. Eagerly at startup by default.
20. Persistence exception translation to DataAccessException.

## Mock Interview 5 — Spring Boot + REST (Questions)
1. Spring vs Spring Boot.
2. How does auto-configuration work internally?
3. @ConditionalOnMissingBean purpose.
4. What is a starter?
5. How to disable an auto-configuration?
6. Profiles and config precedence.
7. Request flow through Spring MVC.
8. @Controller vs @RestController.
9. How is validation triggered and errors mapped?
10. Global exception handling.
11. Filter vs Interceptor.
12. REST principles and statelessness.
13. Idempotency and why it matters.
14. Status codes: 201/204/400/401/403/409.
15. PUT vs PATCH.
16. API versioning strategies.
17. How to prevent duplicate POSTs.
18. Pagination in REST.
19. What does Actuator provide?
20. How is the app packaged/deployed?

### Mock 5 — Expected Answers
1. Framework vs opinionated auto-configured layer with embedded server.
2. Conditional auto-config classes from AutoConfiguration.imports evaluated against classpath/beans/properties.
3. Backs off if you defined your own bean → lets you override defaults.
4. Version-aligned dependency bundle for a capability.
5. exclude attribute or spring.autoconfigure.exclude.
6. @Profile beans + active profile; CLI > env > profile file > app file > defaults.
7. DispatcherServlet → HandlerMapping → HandlerAdapter → controller → converters.
8. View names vs @ResponseBody JSON.
9. @Valid on DTO → MethodArgumentNotValidException → 400 in advice.
10. @RestControllerAdvice + @ExceptionHandler.
11. Servlet-level (raw) vs MVC-level (handler/model).
12. Stateless, resource URIs, standard methods/status.
13. Repeat = same effect; enables safe retries.
14. Created(+Location)/no content/bad request/unauth/forbidden/conflict.
15. Full replace (idempotent) vs partial update.
16. URI/header/param/media-type.
17. Idempotency key + unique constraints.
18. page/size/sort params; Page vs Slice.
19. Health/metrics/info/beans endpoints + Micrometer.
20. Fat JAR → Docker → Kubernetes with probes.

## Mock Interview 6 — Hibernate/JPA/SQL (Questions)
1. JPA vs Hibernate.
2. Entity lifecycle states.
3. save vs persist vs merge.
4. get vs load.
5. First vs second-level cache.
6. Dirty checking.
7. Lazy vs eager; LazyInitializationException.
8. N+1 problem and fixes.
9. Optimistic vs pessimistic locking.
10. Owning side and mappedBy.
11. @GeneratedValue strategies.
12. @Enumerated STRING vs ORDINAL.
13. JPQL vs native vs Criteria.
14. Spring Data derived queries vs @Query.
15. Page vs Slice.
16. Index tradeoffs.
17. WHERE vs HAVING.
18. INNER vs LEFT JOIN.
19. Offset vs cursor pagination.
20. How to find a slow query.

### Mock 6 — Expected Answers
1. Spec vs implementation.
2. Transient/persistent/detached/removed.
3. id / managed / detached→managed copy returned.
4. DB now+null vs proxy+exception.
5. Per-session always-on vs shared opt-in.
6. Auto UPDATE of changed managed entities on flush.
7. Load on access vs immediate; LIE when accessing lazy after session close.
8. 1+N queries; fix via JOIN FETCH/EntityGraph/batch/DTO.
9. @Version conflict check vs DB row lock.
10. Owning holds FK; inverse uses mappedBy.
11. IDENTITY/SEQUENCE/TABLE/AUTO; SEQUENCE batches.
12. STRING stable vs ORDINAL position-fragile.
13. Entity query language vs raw SQL vs type-safe programmatic.
14. Method-name parsing vs explicit query.
15. count query vs next-page-only.
16. Faster reads, slower writes.
17. Pre-group rows vs post-group aggregates.
18. Only matches vs all-left + matches.
19. Slow deep pages vs fast keyset.
20. Metrics/traces → EXPLAIN → indexes/N+1/projection.

## Mock Interview 7 — Full 3-Year Backend (40 Questions)
1. Walk me through your project architecture. 2. Why Spring Boot? 3. Request flow end to end. 4. How do you structure layers? 5. Constructor vs field injection — why? 6. How does auto-configuration work? 7. How do you handle exceptions globally? 8. How do you validate input? 9. Explain @Transactional propagation. 10. What's the self-invocation problem? 11. How do you prevent N+1? 12. Optimistic vs pessimistic locking in your app? 13. How do you cache and invalidate? 14. Where do you use Redis? 15. JWT auth flow. 16. Access vs refresh tokens. 17. Why stateless auth? 18. How do you secure endpoints by role? 19. How do you handle a downstream service being down? 20. Circuit breaker vs retry. 21. How do you avoid retry storms? 22. Sync vs async communication. 23. How do you ensure distributed consistency? 24. Explain the outbox pattern. 25. How do you find and fix a slow API? 26. How do you diagnose an OOM in prod? 27. How do you monitor the app? 28. What's in your logging/tracing setup? 29. HashMap internals. 30. equals/hashCode contract. 31. volatile vs Atomic. 32. Deadlock prevention. 33. CompletableFuture for parallel calls. 34. Stream vs loop tradeoffs. 35. Immutable class design. 36. How do you test a service and a controller? 37. Mock vs Spy. 38. SOLID example from your code. 39. A design pattern you used and why. 40. A hard bug you solved and the impact.

### Mock 7 — Expected Answers (guidance)
- **1-8 (architecture/web):** Use the Part 39 template answers — layered Boot app, DTOs, global `@RestControllerAdvice`, `@Valid`, auto-config via conditionals. Always add *your* specifics.
- **9-14 (data/tx/cache):** REQUIRED default / REQUIRES_NEW for audit; self-invocation bypasses proxy; N+1 → JOIN FETCH/EntityGraph; optimistic `@Version` → 409; cache-aside + evict on write + TTL; Redis for cache/locks/counters.
- **15-18 (security):** BCrypt + UserDetailsService, short access + refresh token, stateless scales, `@PreAuthorize` for roles, JWT signed not encrypted.
- **19-24 (microservices):** timeout + circuit breaker + backoff retry (idempotent only) + fallback; cap retries + jitter; async for decoupling; saga + outbox for consistency.
- **25-28 (ops):** metrics/traces → EXPLAIN/logs → fix; heap dump in MAT for OOM; correlation ids + dashboards + alerts.
- **29-35 (core Java):** use the earlier deep sections verbatim.
- **36-40 (testing/design/story):** unit-mock services, `@WebMvcTest`+MockMvc for controllers; mock=fake, spy=real; give a concrete SOLID/pattern example and one bug story *with a measurable result*.

---

# 45. Cheat Sheets

### 1. Java Cheat Sheet
- JDK ⊃ JRE ⊃ JVM; `javac` → bytecode → interpret + JIT.
- Bytecode portable; JVM platform-specific.
- Pass-by-value always; primitives 8; wrappers immutable.
- `var` = compile-time inference; records = immutable data; sealed = restricted hierarchy.
- Prefer immutability; `final` locks reference, not object state.

### 2. OOP Cheat Sheet
- Encapsulation/Inheritance/Polymorphism/Abstraction.
- Overload = compile-time (args); override = runtime (actual type).
- Static hidden, private/final not overridable; covariant returns OK.
- Composition > inheritance for reuse.
- Abstract class (state+partial) vs interface (contract+multiple).
- equals ⇒ hashCode consistency mandatory.

### 3. String Cheat Sheet
- Immutable + final → pool, security, thread-safe, hash-cached.
- Literals pooled; `new String` = heap object; `intern()` → pooled ref.
- `==` identity, `equals` content.
- StringBuilder (fast) vs StringBuffer (synced); never `+` in loops.

### 4. Collections Cheat Sheet
- List: ArrayList (array) / LinkedList (linked).
- Set: HashSet / LinkedHashSet (insertion) / TreeSet (sorted).
- Map: HashMap / LinkedHashMap / TreeMap / ConcurrentHashMap.
- Queue: PriorityQueue / ArrayDeque / BlockingQueue.
- Fail-fast (CME) vs fail-safe (snapshot).

### 5. HashMap Cheat Sheet
- Bucket array; list → red-black tree at 8 (cap ≥ 64).
- index = `(n-1) & (h ^ h>>>16)`; cap 16, LF 0.75, threshold 12.
- Resize doubles + rehashes (bit-split); collisions via equals.
- Immutable keys; consistent equals/hashCode.

### 6. Multithreading Cheat Sheet
- Pools not raw threads; bound them.
- synchronized (simple) vs ReentrantLock (tryLock/timeout/fair).
- volatile = visibility; Atomic/CAS = lock-free atomicity.
- wait releases lock (synchronized); sleep keeps it.
- Deadlock: consistent ordering + timeouts.

### 7. Stream API Cheat Sheet
- Lazy intermediates + eager terminal; single-use.
- map/filter/flatMap/reduce/collect; groupingBy/partitioningBy/counting/joining.
- Parallel = large CPU-bound stateless only (shared common pool).
- findFirst (ordered) vs findAny (parallel-cheap).

### 8. Exception Cheat Sheet
- Throwable → Error / Exception(→RuntimeException unchecked).
- try-with-resources auto-closes + suppresses.
- Never return/throw in finally; don't blanket-catch Exception.
- Rollback in Spring on unchecked by default.

### 9. JDBC Cheat Sheet
- Driver implements Connection/PreparedStatement/ResultSet.
- PreparedStatement: precompiled + parameterized → no injection + batching.
- autoCommit off → commit/rollback for atomicity.
- Pooled DataSource (HikariCP); always close (try-with-resources).

### 10. Hibernate Cheat Sheet
- SessionFactory (shared) → Session/EntityManager → persistence context (L1 + dirty checking).
- States: transient/persistent/detached/removed.
- merge → managed copy; get=DB, load=proxy.
- N+1 → JOIN FETCH/EntityGraph/batch/DTO.
- Optimistic (@Version) vs pessimistic (FOR UPDATE).

### 11. JPA Cheat Sheet
- Spec; Hibernate implements it.
- @Entity/@Id/@GeneratedValue(SEQUENCE for batching)/@Column.
- @Enumerated(STRING); *ToOne EAGER, *ToMany LAZY (prefer LAZY).
- Owning side has FK; inverse uses mappedBy.

### 12. Spring Core Cheat Sheet
- IoC = container creates; DI = injects.
- Constructor injection (final, explicit, testable, fail-fast).
- @Primary/@Qualifier for multiple beans.
- Singleton default; lifecycle @PostConstruct/@PreDestroy.
- ApplicationContext ⊃ BeanFactory.

### 13. Spring Boot Cheat Sheet
- @SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan.
- Auto-config = conditional classes (@ConditionalOnClass/MissingBean/Property).
- Starters = version-aligned bundles; @ConfigurationProperties for typed config.
- Actuator + embedded Tomcat + fat JAR.

### 14. REST Cheat Sheet
- Stateless; resource URIs; standard methods + status codes.
- Idempotent: GET/PUT/DELETE; not POST/PATCH.
- 201+Location, 204 delete, 400 validation, 401/403, 409 conflict.
- DTO + @Valid; versioning; pagination params; idempotency key.

### 15. Spring Security Cheat Sheet
- Filter chain; AuthN (identity) vs AuthZ (permissions).
- BCrypt + UserDetailsService; @PreAuthorize for roles.
- JWT header.payload.signature — signed not encrypted; access(short)+refresh.
- Stateless scales; CSRF off for token APIs, on for cookies.

### 16. SQL Cheat Sheet
- PK unique+not null; FK integrity; index = fast reads/slow writes.
- Composite index leftmost-prefix; WHERE before HAVING.
- EXPLAIN to diagnose; avoid functions on indexed columns.
- Cursor pagination beats offset on deep pages.

### 17. Microservices Cheat Sheet
- Independent deploy + own DB; distributed complexity is the cost.
- Discovery (Eureka) + Gateway + Feign/WebClient.
- Resilience4j: circuit breaker + retry(backoff+jitter) + timeout + fallback + bulkhead.
- Async: Kafka/RabbitMQ; Saga + Outbox for consistency; correlation IDs for tracing.

---

# 46. Important Comparisons

| Comparison | Key difference |
|---|---|
| **JDK vs JRE vs JVM** | Dev kit (tools+compiler) ⊃ runtime (libs) ⊃ execution engine |
| **Heap vs Stack** | Objects, shared vs stack frames, per-thread |
| **String vs StringBuilder vs StringBuffer** | Immutable vs mutable-unsynced vs mutable-synced |
| **== vs equals()** | Reference identity vs logical equality |
| **Comparable vs Comparator** | Natural order (compareTo, in class) vs external orders (compare) |
| **ArrayList vs LinkedList** | Array O(1) access vs linked O(1) ends, poor locality |
| **HashMap vs Hashtable** | Unsynced + 1 null key vs fully synced legacy, no null |
| **HashMap vs ConcurrentHashMap** | Not thread-safe vs fine-grained safe, no null keys/values |
| **HashSet vs TreeSet** | O(1) unordered vs O(log n) sorted |
| **Collection vs Collections** | Root interface vs utility class (static helpers) |
| **Iterator vs ListIterator** | Forward-only vs bidirectional + index + set/add |
| **Fail-fast vs fail-safe** | CME on modification vs snapshot iteration |
| **Checked vs unchecked exception** | Compiler-enforced recoverable vs runtime programming error |
| **throw vs throws** | Raise an exception vs declare it |
| **final vs finally vs finalize** | Modifier vs always-run block vs deprecated GC hook |
| **abstract class vs interface** | State + partial impl + single vs contract + multiple |
| **overloading vs overriding** | Compile-time (args) vs runtime (actual type) |
| **composition vs inheritance** | HAS-A, loose coupling vs IS-A, tight coupling |
| **primitive vs wrapper** | Value on stack vs object (nullable, for collections) |
| **upcasting vs downcasting** | Implicit safe vs explicit runtime-checked |
| **Runnable vs Callable** | void/no-throw vs value/throws (Future) |
| **synchronized vs Lock** | Implicit simple vs explicit flexible (tryLock/timeout/fair) |
| **volatile vs Atomic** | Visibility only vs lock-free atomicity (CAS) |
| **wait vs sleep** | Releases lock (Object, synchronized) vs keeps lock (Thread) |
| **Future vs CompletableFuture** | Blocking get vs composable non-blocking |
| **JDBC vs JPA** | Manual SQL/mapping vs ORM abstraction |
| **JPA vs Hibernate** | Specification vs implementation |
| **save vs persist vs merge** | Returns id / makes managed / detached→managed copy |
| **lazy vs eager** | Load on access (proxy) vs load immediately |
| **first-level vs second-level cache** | Per-session always-on vs shared opt-in |
| **Spring vs Spring Boot** | Framework vs opinionated auto-config layer |
| **@Component vs @Bean** | Scanned class vs factory method instance |
| **@Controller vs @RestController** | View names vs @ResponseBody (JSON) |
| **@Autowired vs constructor injection** | Field/reflection vs explicit final testable (preferred) |
| **Filter vs Interceptor** | Servlet-level (raw) vs MVC-level (handler/model) |
| **Authentication vs Authorization** | Who you are vs what you can do |
| **JWT vs Session** | Stateless signed token vs server-side stateful session |
| **PUT vs PATCH** | Full replace (idempotent) vs partial update |
| **Monolith vs Microservices** | Single deploy, simple vs independent services, complex |

---

# Last 24 Hours Before Interview

**Read these first — highest ROI, most-asked.**

### Core Java (must be automatic)
- [ ] JDK/JRE/JVM + bytecode + JIT + platform independence.
- [ ] Heap vs stack, GC generations, memory leak = reachable-but-unused.
- [ ] String immutability (4 reasons) + pool + `new String` object count.
- [ ] `==` vs equals; equals/hashCode contract.
- [ ] Overloading vs overriding + rules (covariant, exceptions, access, static hiding).
- [ ] Integer cache -128..127; pass-by-value proof.
- [ ] Checked vs unchecked; try-with-resources; return-in-finally.

### Collections (guaranteed)
- [ ] **HashMap internals end-to-end** (hash spread, index, collision, treeify 8/cap64, LF 0.75, resize) — rehearse the full chain out loud.
- [ ] ArrayList vs LinkedList; HashMap vs ConcurrentHashMap; fail-fast vs fail-safe.
- [ ] Comparable vs Comparator + `comparing/thenComparing`.

### Concurrency
- [ ] synchronized vs Lock; volatile vs Atomic (count++ not atomic); CAS.
- [ ] wait vs sleep; deadlock prevention; Future vs CompletableFuture.

### Streams / Java 8
- [ ] Lazy vs terminal; map/filter/reduce/groupingBy; orElse vs orElseGet.

### Spring / Boot (heavily asked)
- [ ] IoC/DI + why constructor injection; @Primary/@Qualifier.
- [ ] Bean lifecycle + scopes; @Component vs @Bean.
- [ ] **AOP proxies + self-invocation problem** (also for @Transactional/@Cacheable).
- [ ] MVC request flow; @RestController; @RestControllerAdvice; Filter vs Interceptor.
- [ ] **Auto-configuration internals** (@ConditionalOnClass/MissingBean).
- [ ] **@Transactional propagation + rollback rules + self-invocation.**

### Data
- [ ] JPA vs Hibernate; entity lifecycle; save/persist/merge; get vs load.
- [ ] **N+1 problem + fixes** (JOIN FETCH/EntityGraph/batch/DTO) — rehearse.
- [ ] L1 vs L2 cache; optimistic vs pessimistic; lazy vs eager + LazyInitializationException.
- [ ] SQL: joins, WHERE vs HAVING, indexes, EXPLAIN, offset vs cursor pagination.

### REST / Security / Microservices
- [ ] REST principles, idempotency, status codes, PUT vs PATCH.
- [ ] **JWT login flow**; AuthN vs AuthZ; stateless vs session; CSRF off for tokens.
- [ ] Circuit breaker + retry(backoff+jitter) + timeout + fallback; retry storms.
- [ ] Caching: cache-aside + invalidation + stampede; where you use Redis.

### Project story (make-or-break)
- [ ] Rehearse your **architecture** answer (Part 39).
- [ ] Have ONE bug/optimization story with a **measurable result** (e.g., p95 latency, N+1 fix).
- [ ] Be ready for "why?" x5 on every choice — practice a counter-question chain (Part 42).

### Behavioral / logistics
- [ ] "Why are you leaving?", strengths/weaknesses, STAR stories.
- [ ] Research the company + role; prepare 2-3 questions to ask them.

### Mindset
- [ ] It's fine to say "I'd verify that" — reasoning beats memorization.
- [ ] Think out loud in coding rounds; state complexity and edge cases.
- [ ] Sleep well. A fresh mind recalls more than one more hour of cramming.

**Good luck — you've prepared for the depth *and* the follow-ups. Go show it.**
