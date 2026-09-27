# ⚙️ JS Interview Prep 02 — How JavaScript Works + Scope & DOM

> How the engine runs your code, execution contexts, hoisting, TDZ, scope chain, closures, and the DOM/events.
> ⬅️ Back to the [Master Index](./README.md)

---

## 📑 Index

1. [How the JS Engine Runs Code](#1-how-the-js-engine-runs-code)
2. [Synchronous vs Asynchronous JavaScript](#2-synchronous-vs-asynchronous-javascript)
3. [Lexical Environment](#3-lexical-environment)
4. [Execution Context (GEC & FEC)](#4-execution-context-gec--fec)
5. [`var`, `let`, `const`, Hoisting & TDZ](#5-var-let-const-hoisting--tdz)
6. [Scope Chain](#6-scope-chain)
7. [Block Scope vs Function Scope](#7-block-scope-vs-function-scope)
8. [Closures](#8-closures)
9. [Garbage Collection & Memory](#9-garbage-collection--memory)
10. [`async` vs `defer` in `<script>`](#10-async-vs-defer)
11. [The DOM](#11-the-dom)
12. [Events, Bubbling, Capturing, Delegation](#12-events-bubbling-capturing-delegation)
13. [Static vs Live Node Lists](#13-static-vs-live-node-lists)
14. [Call Stack, Callback Queue & Event Loop](#14-call-stack-callback-queue--event-loop)
15. [MCQs](#15-mcqs)

---

## 1. How the JS Engine Runs Code

JavaScript is often called "interpreted," but modern engines (V8, SpiderMonkey) use **Just-In-Time (JIT) compilation** — they parse, compile, optimize, and execute at runtime.

The phases:

```text
Source Code
   ↓  1. Tokenizing / Lexical Analysis   → break code into tokens
   ↓  2. Parsing                          → build the Abstract Syntax Tree (AST)
   ↓  3. AST transformations              → optional simplifications
   ↓  4. Scope & environment setup        → hoisting, execution contexts
   ↓  5. Interpreter (e.g. V8 Ignition)   → generate bytecode
   ↓  6. JIT compiler (e.g. V8 TurboFan)  → hot code → optimized machine code
   ↓  7. Execution                        → run, manage call stack & event loop
Result
```

| Phase | Input | Output |
| :---- | :---- | :----- |
| Tokenizing | raw code | tokens |
| Parsing | tokens | AST |
| Interpreter | AST | bytecode |
| JIT | bytecode | optimized machine code |

> **One-line revision:** Code → tokens → AST → bytecode → (hot paths) optimized machine code → run.

**↪️ Counter-questions:**
- Why JIT and not pure compilation? → start fast with the interpreter, then optimize only frequently-run ("hot") code.
- What is "deoptimization"? → if an assumption breaks (e.g., a variable's type changes), V8 discards the optimized code and falls back to bytecode.

---

## 2. Synchronous vs Asynchronous JavaScript

JavaScript is **single-threaded and synchronous** — one line at a time. Asynchronous work (`setTimeout`, `fetch`, DOM events) is **handled by the environment** (browser Web APIs or Node runtime), not by the JS engine itself.

```javascript
console.log("Start");
setTimeout(() => console.log("Timer"), 0);
console.log("End");
// Output: Start, End, Timer
```

Flow:
1. **Call stack** runs synchronous code.
2. **Web APIs** (browser) or Node APIs handle the async task (timer, network).
3. When done, the callback is placed in the **callback queue**.
4. The **event loop** moves it to the stack once the stack is empty.

> **One-line revision:** JS is single-threaded; the *environment* provides concurrency, not the language.

**↪️ Counter-questions:**
- Is `setTimeout` part of JavaScript? → No, it's a Web API (browser) / timer (Node).
- Does `setTimeout(fn, 0)` run immediately? → No — it waits for the stack to clear and for microtasks to finish first.

---

## 3. Lexical Environment

A **lexical environment** is a structure that stores variable/function declarations plus a reference to its **outer** environment.

Two parts:
1. **Environment Record** — where local variables/functions live.
2. **Outer reference** — link to the parent environment (used for the scope chain).

"Lexical" means **static** — scope is decided by *where you write* code, not where it runs.

```javascript
function outer() {
  let a = 10;
  function inner() {
    let b = 20;
    console.log(a + b); // inner can see outer's `a`
  }
  inner();
}
outer(); // 30
```

Each function call creates a **new** lexical environment.

**↪️ Counter-question:** What does "lexical" mean? → based on the physical location of code, resolved at author time.

---

## 4. Execution Context (GEC & FEC)

An **execution context** is the environment in which code runs.

**Global Execution Context (GEC)** — created once when the script starts:
- creates the global object (`window` in browsers),
- sets `this`,
- sets up the global lexical environment.

**Function Execution Context (FEC)** — created **each time a function is called**:
- has its own `arguments`, local variables, and outer reference.

Each context has two phases:
1. **Creation/Memory phase** — hoisting (declarations stored in memory).
2. **Execution phase** — code runs line by line.

```javascript
var name = "Suvojit";
function greet() {
  var greeting = "Hello";
  console.log(greeting + " " + name);
}
greet();
```

**Call stack:** GEC is pushed first → `greet()` FEC pushed on call → popped when it returns.

**↪️ Counter-questions:**
- How many GECs exist? → exactly one per program.
- What happens to an FEC when the function returns? → it's popped off the call stack (unless a closure keeps its variables alive).

---

## 5. `var`, `let`, `const`, Hoisting & TDZ

**Hoisting** = declarations are moved to the top of their scope during the memory phase. **Only declarations are hoisted, not initializations.**

```javascript
console.log(a); // undefined  (var is hoisted + initialized to undefined)
var a = 5;

console.log(b); // ❌ ReferenceError: Cannot access 'b' before initialization
let b = 10;
```

**Temporal Dead Zone (TDZ)** — the gap between the start of the block and the line where a `let`/`const` is declared. Accessing the variable in that gap throws `ReferenceError`.

**Function declarations** are fully hoisted (usable before their line). **Function expressions** follow their variable's rules.

```javascript
sayHi();               // "Hi" — declarations are fully hoisted
function sayHi() { console.log("Hi"); }

sayBye();              // ❌ TypeError: sayBye is not a function
var sayBye = function () { console.log("Bye"); };
```

| Feature          | `var`              | `let`      | `const`    |
| ---------------- | ------------------ | ---------- | ---------- |
| Hoisted          | ✅ Yes             | ✅ Yes     | ✅ Yes     |
| Initialized on hoist | ✅ `undefined` | ❌ (TDZ)   | ❌ (TDZ)   |
| TDZ applies      | ❌ No              | ✅ Yes     | ✅ Yes     |
| Redeclarable (same scope) | ✅ Yes    | ❌ No      | ❌ No      |
| Reassignable     | ✅ Yes             | ✅ Yes     | ❌ No      |
| Scope            | function           | block      | block      |
| Must initialize  | ❌ No              | ❌ No      | ✅ Yes     |

> **One-line revision:** `var` hoists to `undefined`; `let`/`const` hoist but stay in the TDZ until declared.

### 🎯 Interview Q&A

**Q: Are `let` and `const` hoisted?**
Yes — but they aren't initialized. They live in the TDZ until the declaration line, so accessing them early throws `ReferenceError`.

**↪️ Counter-questions:**
- Is the TDZ a "waste"? → No, it's intentional safety to prevent using variables before they're ready.
- Does `const` make objects immutable? → No, only the binding is constant; object contents can still change.

---

## 6. Scope Chain

When JS looks up a variable, it checks the **current scope**, then walks **outward** to parent scopes until it finds it or reaches global scope.

```javascript
let a = 10;
function outer() {
  let b = 20;
  function inner() {
    let c = 30;
    console.log(a + b + c); // 60 — reads across the chain
  }
  inner();
}
outer();
```

**Analogy:** searching for a file — desk (local) → room (outer) → house (global).

**↪️ Counter-question:** Can an outer scope read an inner scope's variable? → No; lookups only go outward, never inward.

---

## 7. Block Scope vs Function Scope

- **Function scope** — `var` is visible throughout the whole function.
- **Block scope** — `let`/`const` are visible only inside the nearest `{ }`.

```javascript
if (true) {
  let x = 1;   // block-scoped
  var z = 2;   // function/global-scoped (leaks out)
}
console.log(z); // 2
console.log(x); // ❌ ReferenceError
```

> **One-line revision:** `var` leaks out of blocks; `let`/`const` stay inside them.

---

## 8. Closures

A **closure** is a function bundled with the lexical environment it was created in. It "remembers" outer variables even after the outer function returns.

```javascript
function outer() {
  let name = "Suvojit";
  return function inner() { console.log("Hello, " + name); };
}
const greet = outer(); // outer() finished...
greet();               // "Hello, Suvojit" (still remembers `name`)
```

**Counter with closure — private state:**
```javascript
function createCounter() {
  let count = 0;
  return () => ++count;
}
const c = createCounter();
c(); // 1
c(); // 2
```

**Uses:** data privacy/encapsulation, keeping state in callbacks/timers, function factories, currying.

**Classic pitfall — `var` in a loop:**
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 3 3 3 — one shared `i`, read after the loop ends

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 0 1 2 — `let` creates a new binding each iteration
```

> **One-line revision:** A closure = function + remembered outer variables. `let` in loops fixes the shared-`var` bug.

### 🎯 Interview Q&A

**Q: Why does the `var` loop print `3 3 3`?**
All three callbacks close over the *same* `i`. By the time they run (after the loop), `i` is `3`.

**↪️ Counter-questions:**
- Fix without `let`? → wrap in an IIFE that captures `i` per iteration.
- Can closures cause memory leaks? → Yes, if they keep references to large objects or DOM nodes longer than needed.

---

## 9. Garbage Collection & Memory

JavaScript frees memory automatically using **mark-and-sweep**: objects still reachable from the root (global, call stack) are kept; unreachable ones are collected.

```javascript
let obj = { name: "X" };
obj = null; // no references left → eligible for GC
```

**Common leak sources:** forgotten timers/intervals, un-removed event listeners, closures holding big data, accidental globals.

**Best practices:** null out large data after use, `removeEventListener`, avoid polluting the global scope.

**↪️ Counter-question:** Why is reference counting alone insufficient? → it can't collect **circular references**; mark-and-sweep can.

---

## 10. `async` vs `defer`

Both let the browser download external scripts in parallel with HTML parsing (only work with `src`).

| Feature          | `async`                              | `defer`                             |
| ---------------- | ------------------------------------ | ----------------------------------- |
| Execution timing | as soon as downloaded (may pause HTML) | after HTML is fully parsed          |
| Preserves order? | ❌ No                                | ✅ Yes                              |
| Blocks DOM?      | possibly                             | No                                  |
| Best for         | independent scripts (analytics, ads) | scripts needing the DOM / each other |

```html
<script src="analytics.js" async></script>  <!-- independent -->
<script src="main.js" defer></script>        <!-- needs DOM -->
```

> **One-line revision:** `async` = run ASAP, any order. `defer` = run after DOM, in order.

**↪️ Counter-question:** No `async`/`defer` — what happens? → the browser stops HTML parsing to download and run the script (blocking).

---

## 11. The DOM

The **DOM (Document Object Model)** is a tree representation of the HTML page that JS can read and change (structure, style, content).

```html
<html>
  <body>
    <h1>Hello</h1>
    <p>World</p>
  </body>
</html>
```

- Built by the **browser** when the page loads.
- Each tag → a **node**.
- JS can traverse, create, update, and remove nodes.

Common queries: `getElementById`, `querySelector`, `querySelectorAll`, `getElementsByClassName`.

---

## 12. Events, Bubbling, Capturing, Delegation

**Event Bubbling (default):** the event fires on the target, then bubbles up through ancestors.

```html
<div id="parent"><button id="child">Click</button></div>
```
```javascript
parent.addEventListener("click", () => console.log("Parent"));
child.addEventListener("click", () => console.log("Child"));
// Click the button -> "Child" then "Parent"
```

**Event Capturing (trickling):** the event travels top-down first. Enable with the third argument.
```javascript
parent.addEventListener("click", () => console.log("Parent capturing"), true);
// -> "Parent capturing" then "Child"
```

**Event Delegation:** attach one listener on a parent and use bubbling to handle many children (great for dynamic lists — fewer listeners, less memory).
```javascript
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.tagName === "LI") console.log("Clicked:", e.target.textContent);
});
```

Stop propagation when needed:
```javascript
event.stopPropagation();  // stop bubbling/capturing further
event.preventDefault();   // stop the browser's default action (e.g., form submit)
```

| Concept    | Flow                | Use case                        |
| :--------- | :------------------ | :------------------------------ |
| Bubbling   | target → parent     | default, most common            |
| Capturing  | parent → target     | intercept early (rare)          |
| Delegation | uses bubbling       | many/dynamic children           |

> **One-line revision:** Bubbling goes up, capturing goes down, delegation puts one listener on the parent.

**↪️ Counter-questions:**
- Difference between `stopPropagation` and `preventDefault`? → one stops the event traveling; the other stops the browser's default behavior.
- Why delegation for dynamic lists? → new child elements are handled automatically without adding new listeners.

---

## 13. Static vs Live Node Lists

- **Static list** — a snapshot; doesn't update when the DOM changes. Returned by `querySelectorAll()` (a `NodeList`).
- **Live list** — auto-updates with the DOM. Returned by `getElementsByTagName()` / `getElementsByClassName()` (an `HTMLCollection`).

```javascript
const staticList = document.querySelectorAll("p");
const liveList = document.getElementsByTagName("p");
document.body.innerHTML += "<p>New</p>";
// staticList.length -> unchanged
// liveList.length   -> increased
```

| Feature        | Static (`querySelectorAll`) | Live (`getElementsByTagName`) |
| :------------- | :-------------------------- | :---------------------------- |
| Updates w/ DOM | ❌ No                       | ✅ Yes                        |
| Type           | NodeList                    | HTMLCollection                |

**↪️ Counter-question:** Which can you use `forEach` on directly? → `NodeList` (from `querySelectorAll`). For `HTMLCollection`, convert with `Array.from`.

---

## 14. Call Stack, Callback Queue & Event Loop

Think of JS as a **chef** who cooks one dish at a time.

- **Call Stack** — the chef's current to-do list (functions being executed, LIFO).
- **Callback Queue (macrotask queue)** — waiting room for callbacks from `setTimeout`, events, etc.
- **Microtask Queue** — VIP queue for promise callbacks (`.then`), `queueMicrotask` — **higher priority**.
- **Event Loop** — the assistant that, when the stack is empty, drains **all microtasks first**, then takes one macrotask.

```javascript
console.log("Start");
setTimeout(() => console.log("Timeout"), 0); // macrotask
Promise.resolve().then(() => console.log("Promise")); // microtask
console.log("End");
// Start, End, Promise, Timeout
```

Order rule:
```text
1. Run all synchronous code (call stack)
2. Stack empty? → run ALL microtasks
3. Then run ONE macrotask
4. Repeat from step 2
```

| Concept        | Role                            | Analogy                     |
| :------------- | :------------------------------ | :-------------------------- |
| Call Stack     | executes functions              | chef's to-do list           |
| Callback Queue | holds macrotasks                | waiting room                |
| Microtask Queue| holds promise callbacks (priority) | VIP/fire-alarm queue    |
| Event Loop     | moves tasks to the stack        | kitchen assistant           |

> **One-line revision:** Sync first → microtasks (promises) → one macrotask (timers) → repeat.

**↪️ Counter-questions:**
- Why do promises run before `setTimeout(…, 0)`? → microtasks have higher priority and drain fully before any macrotask.
- Can microtasks starve macrotasks? → Yes, if microtasks keep queuing more microtasks, timers can be delayed.

---

## 15. MCQs

**Q1.** Output?
```javascript
console.log(a);
var a = 5;
console.log(a);
```
<details><summary>Answer</summary>

`undefined` then `5` — `var a` is hoisted and initialized to `undefined`.
</details>

**Q2.** Output?
```javascript
console.log(b);
let b = 5;
```
<details><summary>Answer</summary>

`ReferenceError: Cannot access 'b' before initialization` — TDZ.
</details>

**Q3.** Output?
```javascript
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 0);
```
<details><summary>Answer</summary>

`3 3 3` — one shared `i` read after the loop.
</details>

**Q4.** Output?
```javascript
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), 0);
```
<details><summary>Answer</summary>

`0 1 2` — `let` gives each iteration its own binding.
</details>

**Q5.** Output?
```javascript
console.log("Start");
setTimeout(() => console.log("Timeout"), 0);
Promise.resolve().then(() => console.log("Promise"));
console.log("End");
```
<details><summary>Answer</summary>

`Start`, `End`, `Promise`, `Timeout` — microtask before macrotask.
</details>

**Q6.** Output?
```javascript
function outer() {
  let x = 10;
  function inner() { console.log(x); }
  x = 20;
  return inner;
}
outer()();
```
<details><summary>Answer</summary>

`20` — the closure reads the live variable, which was updated to `20` before `inner` ran.
</details>

**Q7.** Output?
```javascript
var a = 1;
function foo() {
  console.log(a);
  var a = 2;
}
foo();
```
<details><summary>Answer</summary>

`undefined` — the local `var a` is hoisted, shadowing the global before its assignment.
</details>

**Q8.** Output?
```javascript
sayHi();
function sayHi() { console.log("Hi"); }
sayBye();
var sayBye = function () { console.log("Bye"); };
```
<details><summary>Answer</summary>

`"Hi"`, then `TypeError: sayBye is not a function` — declaration hoisted; expression not yet assigned.
</details>

**Q9.** Which returns a live collection?
```javascript
document.querySelectorAll("p");
document.getElementsByClassName("item");
```
<details><summary>Answer</summary>

`getElementsByClassName` (live HTMLCollection). `querySelectorAll` is a static NodeList.
</details>

**Q10.** Output when clicking the button?
```javascript
parent.addEventListener("click", () => console.log("P"));
child.addEventListener("click", () => console.log("C"));
```
<details><summary>Answer</summary>

`C` then `P` — default bubbling from target upward.
</details>

**Q11.** Output?
```javascript
let count = 0;
(function immediate() {
  if (count === 0) {
    var count = 1;
    console.log(count);
  }
  console.log(count);
})();
```
<details><summary>Answer</summary>

`1` then `1` — the inner `var count` is hoisted; the outer `count` is shadowed inside the IIFE.
</details>

**Q12.** Output?
```javascript
console.log(typeof foo);
function foo() {}
var foo = 5;
console.log(typeof foo);
```
<details><summary>Answer</summary>

`"function"` then `"number"` — function declaration is hoisted; later reassigned to a number at runtime.
</details>

> More output-prediction drills: see the [MCQ Practice Bank](./mcqsOnHoisting.md).

---

⬅️ Prev: [01 — Core JavaScript](./JS%20Interview%20Prep_01.md) · [Master Index](./README.md) · Next: [03 — Asynchronous JavaScript](./JS%20Interview%20Prep_03.md)
