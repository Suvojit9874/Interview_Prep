# 🚀 JavaScript Interview Prep — Master Index

A structured, beginner-friendly set of notes to prepare for JavaScript interviews.
Each file is self-contained with its own index, clear explanations, code examples,
MCQs, and interview questions with **counter-questions** (the follow-ups an interviewer asks next).

> **How to use these notes**
>
> 1. Read a topic → run the code → read the "Interview Q&A" → try the "Counter-questions" out loud.
> 2. Attempt the MCQs **before** reading answers.
> 3. Revisit the "One-line revision" boxes the night before an interview.

---

## 📚 The Four Notebooks

| # | File | What's inside |
| :- | :--- | :--- |
| 01 | [Core JavaScript](./JS%20Interview%20Prep_01.md) | Data types, operators, coercion, arrays, objects, functions, `this`, prototypes, classes, functional concepts |
| 02 | [How JS Works + Scope & DOM](./JS%20Interview%20Prep_02.md) | Engine internals, execution context, hoisting, TDZ, scope chain, closures, DOM & events |
| 03 | [Asynchronous JavaScript](./JS%20Interview%20Prep_03.md) | Callbacks, promises, async/await, event loop, microtasks, HTTP (AJAX/XHR/Fetch/Axios), generators |
| 04 | [MCQ Practice Bank](./mcqsOnHoisting.md) | 100+ "guess the output" and concept MCQs across all topics, with answers & explanations |

---

## 🗺️ Full Topic Map

### 01 — Core JavaScript
- Data types (primitive vs reference), `typeof`
- `null` vs `undefined`, truthy/falsy values
- `==` vs `===`, type coercion
- Operators: spread, rest, nullish coalescing (`??`), optional chaining (`?.`), ternary
- Template literals & tagged templates
- Arrays: `sort`, `find`, `every`, `some`, `fill`, `splice`, `map`, `filter`, `reduce`, `forEach`, `Array.from`, `Array.of`
- Iterables vs array-like objects
- `Set`, `Map`, `WeakMap`, `WeakSet`
- Objects: cloning, shallow vs deep copy, `Object.assign`, `structuredClone`, `Object.freeze`, `JSON`
- Destructuring (array & object)
- Functions: declarations, expressions, arrow functions, IIFE, HOFs
- `this` keyword, `call` / `apply` / `bind`
- Prototypes, `__proto__` vs `[[Prototype]]`, `new`
- Classes, `extends`, `super`, `static`, getters/setters
- Factory vs constructor functions
- Currying, memoization, composition, pure vs impure functions
- Debounce vs throttle
- Symbols, generators & iterators
- Error handling (`try/catch/finally`, custom errors)
- Modules (ES Modules vs CommonJS)

### 02 — How JS Works + Scope & DOM
- JS engine phases (tokenize → parse → AST → bytecode → JIT)
- Synchronous vs asynchronous model
- Lexical environment, execution contexts (GEC/FEC)
- `var` / `let` / `const`, hoisting, Temporal Dead Zone
- Scope chain, block vs function scope
- Closures (uses + pitfalls)
- `async` vs `defer`
- DOM, events, bubbling/capturing/delegation
- Static vs live node lists
- Call stack, callback queue, event loop

### 03 — Asynchronous JavaScript
- `setTimeout` / `setInterval`
- Callbacks, callback hell / pyramid of doom
- Promises + states + combinators (`all`, `allSettled`, `any`, `race`, `resolve`, `reject`, `withResolvers`)
- `async` / `await` + error handling
- Microtask vs macrotask queue, event loop priority
- AJAX, XHR, Fetch, Axios
- Generators for async patterns

### 04 — MCQ Practice Bank
- Hoisting, closures, lexical scope (50 Qs)
- Async / event loop (20 Qs)
- Types, coercion & operators (new)
- `this`, prototypes & classes (new)
- Arrays & objects (new)

---

## ✅ Quick Revision Checklist

- [ ] I can explain the difference between `==` and `===` with examples.
- [ ] I can predict output of `var` vs `let` in a `setTimeout` loop.
- [ ] I can write a closure-based counter from memory.
- [ ] I can explain `this` in a method, standalone function, and arrow function.
- [ ] I can describe the event loop: call stack → microtasks → macrotasks.
- [ ] I can implement `debounce` and `throttle`.
- [ ] I can explain why "classes are syntactic sugar."
- [ ] I can use `Promise.all` vs `Promise.allSettled` correctly.

---

_Happy learning. Read → run → explain → repeat._
