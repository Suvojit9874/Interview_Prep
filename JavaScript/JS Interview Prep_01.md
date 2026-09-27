# 📘 JS Interview Prep 01 — Core JavaScript

> Data types, operators, arrays, objects, functions, `this`, prototypes, classes, and functional concepts.
> Beginner-friendly explanations + code + MCQs + interview Q&A with counter-questions.
> ⬅️ Back to the [Master Index](./README.md)

---

## 📑 Index

1. [Data Types & `typeof`](#1-data-types--typeof)
2. [`null` vs `undefined`](#2-null-vs-undefined)
3. [Truthy & Falsy Values](#3-truthy--falsy-values)
4. [`==` vs `===` and Type Coercion](#4--vs--and-type-coercion)
5. [Modern Operators (`??`, `?.`, spread, rest, ternary)](#5-modern-operators)
6. [Template Literals & Tagged Templates](#6-template-literals--tagged-templates)
7. [Destructuring](#7-destructuring)
8. [Arrays — Core Methods](#8-arrays--core-methods)
9. [`map`, `filter`, `reduce`, `forEach`](#9-map-filter-reduce-foreach)
10. [Iterables vs Array-Like Objects](#10-iterables-vs-array-like-objects)
11. [Set, Map, WeakMap, WeakSet](#11-set-map-weakmap-weakset)
12. [Objects: Cloning, Shallow vs Deep Copy](#12-objects-cloning-shallow-vs-deep-copy)
13. [`Object.freeze` & Immutability](#13-objectfreeze--immutability)
14. [JSON Methods](#14-json-methods)
15. [Functions: Declarations, Expressions, Arrow, IIFE](#15-functions)
16. [Higher-Order Functions & Callbacks](#16-higher-order-functions--callbacks)
17. [The `this` Keyword](#17-the-this-keyword)
18. [`call`, `apply`, `bind`](#18-call-apply-bind)
19. [Prototypes, `__proto__` & `[[Prototype]]`](#19-prototypes)
20. [The `new` Keyword](#20-the-new-keyword)
21. [Classes, `extends`, `super`, `static`, getters/setters](#21-classes)
22. [Factory vs Constructor Functions](#22-factory-vs-constructor-functions)
23. [Currying, Composition, Memoization](#23-currying-composition-memoization)
24. [Pure vs Impure Functions](#24-pure-vs-impure-functions)
25. [Debounce vs Throttle](#25-debounce-vs-throttle)
26. [Symbols](#26-symbols)
27. [Generators & Iterators](#27-generators--iterators)
28. [Error Handling](#28-error-handling)
29. [Modules (ESM vs CommonJS)](#29-modules)
30. [Custom Events](#30-custom-events)
31. [MCQs](#31-mcqs)

---

## 1. Data Types & `typeof`

JavaScript has **8 data types**, split into two families.

**Primitives** (immutable, compared by value): `string`, `number`, `boolean`, `null`, `undefined`, `bigint`, `symbol`.
**Reference type** (compared by reference): `object` (includes arrays, functions, dates, etc.).

```javascript
typeof "hello";        // "string"
typeof 42;             // "number"
typeof true;           // "boolean"
typeof undefined;      // "undefined"
typeof 10n;            // "bigint"
typeof Symbol();       // "symbol"
typeof { a: 1 };       // "object"
typeof [1, 2];         // "object"  (arrays are objects)
typeof function () {}; // "function" (special case)
typeof null;           // "object"  ⚠️ historical bug
```

**Primitive vs Reference — the key difference:**

```javascript
let a = 10;
let b = a;   // copy of the value
b = 20;
console.log(a); // 10 (unchanged)

let obj1 = { x: 1 };
let obj2 = obj1; // copy of the reference (same object)
obj2.x = 99;
console.log(obj1.x); // 99 (both point to same object)
```

> **One-line revision:** Primitives copy the value; objects copy the reference (address).

### 🎯 Interview Q&A

**Q: Why does `typeof null` return `"object"`?**
It's a bug from the very first version of JavaScript that was never fixed for backward-compatibility. To check for `null`, use `value === null`.

**Q: How do you reliably check if something is an array?**
Use `Array.isArray(value)` — `typeof` returns `"object"` for arrays.

**↪️ Counter-questions an interviewer may ask:**
- How would you check the "real" type of any value? → `Object.prototype.toString.call(value)` returns things like `"[object Array]"`, `"[object Date]"`.
- Is `NaN` a number? → Yes, `typeof NaN === "number"`. Check with `Number.isNaN()`.
- What's the difference between `Number.isNaN()` and the global `isNaN()`? → global `isNaN()` coerces first (`isNaN("abc") === true`), `Number.isNaN()` does not coerce.

---

## 2. `null` vs `undefined`

- **`undefined`** — a variable has been declared but not assigned; a missing function return; a missing object property. Set *by the engine*.
- **`null`** — an intentional "empty" value that *you* assign to say "no value here."

```javascript
let x;
console.log(x); // undefined (engine gave it)

let y = null;   // you decided it's empty
console.log(y); // null

typeof undefined; // "undefined"
typeof null;      // "object"

null == undefined;  // true  (loose equality treats them as equal)
null === undefined; // false (different types)
```

> **One-line revision:** `undefined` = "not set yet" (by JS). `null` = "intentionally empty" (by you).

**↪️ Counter-questions:**
- What does a function with no `return` return? → `undefined`.
- What is `undefined + 1`? → `NaN`. What is `null + 1`? → `1` (null coerces to 0).

---

## 3. Truthy & Falsy Values

Every value is either "truthy" or "falsy" when used in a boolean context (`if`, `&&`, `||`).

**The 8 falsy values (memorize these):**
`false`, `0`, `-0`, `0n`, `""` (empty string), `null`, `undefined`, `NaN`.

Everything else is **truthy** — including `"0"`, `"false"`, `[]`, `{}`, and functions.

```javascript
if ([]) console.log("empty array is truthy"); // runs
if ("0") console.log("string zero is truthy"); // runs
if (0) console.log("won't run");
```

> **One-line revision:** Only 8 things are falsy; empty arrays/objects are truthy.

**↪️ Counter-questions:**
- Is `[]` truthy or falsy? → Truthy. But `[] == false` is `true` (coercion!). This is why `==` is dangerous.
- How do you check "value exists and isn't empty string"? → `if (value != null && value !== "")` or `if (value)` if you also want to reject 0.

---

## 4. `==` vs `===` and Type Coercion

- **`===` (strict equality):** compares value **and** type. No conversion.
- **`==` (loose equality):** converts operands to the same type first, then compares.

```javascript
5 === 5;      // true
5 === "5";    // false (different types)
5 == "5";     // true  (string coerced to number)

0 == false;   // true  (both become 0)
"" == false;  // true
null == undefined; // true
null == 0;    // false (special rule)
NaN == NaN;   // false (NaN is never equal to anything)
[] == "";     // true  (array -> "" )
[] == 0;      // true
```

**Type coercion** happens in three common ways:

```javascript
"3" + 2;   // "32"  (+ with a string means concatenation)
"3" - 2;   // 1     (- forces numeric conversion)
"5" * "2"; // 10
true + 1;  // 2     (true -> 1)
[] + {};   // "[object Object]"
```

> **One-line revision:** Always prefer `===`. Use `==` only when you deliberately want `null`/`undefined` to match.

### 🎯 Interview Q&A

**Q: Why is `[] == ![]` true?**
`![]` is `false` (array is truthy, negated → false). Then `[] == false` → both coerce to `0` → `0 == 0` → `true`.

**↪️ Counter-questions:**
- What does `+ "3"` produce? → the number `3` (unary plus converts to number).
- Why avoid `==`? → hidden coercions cause bugs; linters (ESLint `eqeqeq`) enforce `===`.

---

## 5. Modern Operators

### Nullish Coalescing `??`
Returns the right side only when the left is `null` or `undefined` (not for `0` or `""`).

```javascript
const name = null ?? "Anonymous"; // "Anonymous"
const count = 0 ?? 10;            // 0  (0 is NOT null/undefined)
const count2 = 0 || 10;           // 10 (|| falls back on any falsy value)
```

### Optional Chaining `?.`
Safely reads deep properties; returns `undefined` instead of throwing if a link is `null`/`undefined`.

```javascript
const user = { profile: { name: "Alice" } };
user?.profile?.name;    // "Alice"
user?.settings?.theme;  // undefined (no error)
user?.getName?.();      // safe method call
arr?.[0];               // safe index access
```

### Spread `...`
Expands an iterable into individual elements.

```javascript
const nums = [1, 2, 3];
const more = [...nums, 4, 5];        // [1,2,3,4,5]
const clone = { ...{ a: 1 }, b: 2 }; // { a: 1, b: 2 }
Math.max(...nums);                    // 3
```

### Rest `...`
Collects the remaining arguments/elements into an array. Must be **last**.

```javascript
function sum(...numbers) {
  return numbers.reduce((t, n) => t + n, 0);
}
sum(1, 2, 3, 4); // 10

const [first, ...others] = [10, 20, 30]; // first=10, others=[20,30]
```

> **One-line revision:** Spread *expands* out; rest *gathers* in. Same `...`, opposite jobs.

### Ternary
```javascript
const label = age >= 18 ? "adult" : "minor";
```

**↪️ Counter-questions:**
- Difference between `??` and `||`? → `||` falls back on any falsy value; `??` only on `null`/`undefined`.
- Is rest the same as the `arguments` object? → No. Rest is a real array; `arguments` is array-like and unavailable in arrow functions.

---

## 6. Template Literals & Tagged Templates

Backtick strings support interpolation and multi-line text.

```javascript
const name = "Sam";
const msg = `Hello ${name}, 2 + 3 = ${2 + 3}`; // "Hello Sam, 2 + 3 = 5"
const multi = `line 1
line 2`;
```

**Tagged templates** — a function processes the string parts and values.

```javascript
function highlight(strings, ...values) {
  return strings.reduce((out, str, i) =>
    `${out}${str}${values[i] ? `<b>${values[i]}</b>` : ""}`, "");
}
highlight`Hi ${name}!`; // "Hi <b>Sam</b>!"
```

> Used by libraries like `styled-components` and for safe HTML/SQL escaping.

---

## 7. Destructuring

Unpack values from arrays/objects into variables.

```javascript
// Array
const [a, b, ...rest] = [1, 2, 3, 4]; // a=1, b=2, rest=[3,4]
const [x = 10] = [];                  // default: x=10

// Object
const { name, age = 18 } = { name: "Kai" }; // name="Kai", age=18 (default)
const { name: userName } = { name: "Kai" }; // rename: userName="Kai"

// Nested
const { address: { city } } = { address: { city: "Pune" } }; // city="Pune"

// Swap without a temp variable
let p = 1, q = 2;
[p, q] = [q, p]; // p=2, q=1
```

**↪️ Counter-questions:**
- How do you set a default *and* rename? → `const { name: userName = "Guest" } = obj;`
- Can you destructure function parameters? → Yes: `function f({ id, name }) {}`.

---

## 8. Arrays — Core Methods

### `sort()`
Sorts **in place** (mutates). Default sorts as **strings**, so always pass a compare function for numbers.

```javascript
[100, 20, 5].sort();            // [100, 20, 5] -> [100,20,5] string order = wrong
[100, 20, 5].sort((a, b) => a - b); // [5, 20, 100] ascending
[100, 20, 5].sort((a, b) => b - a); // [100, 20, 5] descending
// Non-mutating modern version: toSorted()
```
Compare function: negative → `a` first, positive → `b` first, `0` → keep order.

### `find`, `every`, `some`

| Method    | Returns                       | Stops on    |
| :-------- | :---------------------------- | :---------- |
| `find()`  | first matching element or `undefined` | first match |
| `every()` | `true` if **all** pass        | first false |
| `some()`  | `true` if **any** passes      | first true  |

```javascript
[4, 9, 16].find(n => n > 10);   // 16
[2, 4, 6].every(n => n % 2 === 0); // true
[1, 3, 8].some(n => n % 2 === 0);  // true
```

### `fill()` and `splice()` (both mutate)

```javascript
[1, 2, 3, 4].fill(0, 1, 3);      // [1, 0, 0, 4]  fill value, start, end
const arr = [1, 2, 3, 4, 5];
arr.splice(2, 1);                 // removes index 2 -> [1,2,4,5]
arr.splice(1, 0, 9);              // inserts 9 at index 1
```

> **Mutating vs non-mutating:** `sort`, `splice`, `fill`, `reverse`, `push`, `pop`, `shift`, `unshift` mutate. `map`, `filter`, `slice`, `concat`, `toSorted` return new arrays.

### `Array.from` and `Array.of`
```javascript
Array.from("abc");             // ["a","b","c"]
Array.from({ length: 3 }, (_, i) => i); // [0,1,2]
Array.of(7);                   // [7]  (Array(7) makes empty length-7!)
```

**↪️ Counter-questions:**
- Why does `[10, 2, 1].sort()` give `[1, 10, 2]`? → default string comparison.
- Difference between `slice` and `splice`? → `slice` copies (non-mutating), `splice` edits in place.

---

## 9. `map`, `filter`, `reduce`, `forEach`

```javascript
const nums = [1, 2, 3, 4];

nums.map(n => n * 2);       // [2,4,6,8]  transform -> new array
nums.filter(n => n % 2);    // [1,3]      keep matching -> new array
nums.reduce((acc, n) => acc + n, 0); // 10  fold to single value
nums.forEach(n => console.log(n));   // undefined; just iterates (side effects)
```

**`reduce` is the powerhouse** — build sums, group data, flatten, etc.

```javascript
// Count occurrences
["a", "b", "a"].reduce((acc, x) => {
  acc[x] = (acc[x] || 0) + 1;
  return acc;
}, {}); // { a: 2, b: 1 }
```

> **One-line revision:** `map` transforms, `filter` selects, `reduce` accumulates, `forEach` just loops.

**↪️ Counter-questions:**
- Does `map` mutate the original array? → No, it returns a new array.
- When would you use `forEach` over `map`? → When you only need side effects (logging, DOM updates), not a returned array.
- Can you `break` out of `map`/`forEach`? → No. Use a `for`/`for...of` loop, or `some`/`every` to short-circuit.

---

## 10. Iterables vs Array-Like Objects

- **Iterable:** has a `[Symbol.iterator]` method. Works with `for...of`, spread, destructuring. Examples: Array, String, Set, Map.
- **Array-like:** has numeric indices and a `length`, but **no** array methods. Examples: `arguments`, `NodeList`.

```javascript
for (const ch of "hi") console.log(ch); // strings are iterable

function foo() {
  // arguments is array-like, NOT iterable-array
  const realArray = Array.from(arguments); // convert
}
```

**↪️ Counter-question:** How do you convert array-like → array? → `Array.from(x)` or `[...x]` (only if iterable).

---

## 11. Set, Map, WeakMap, WeakSet

### Set — collection of **unique** values
```javascript
const ids = new Set([1, 2, 2, 3]); // {1,2,3}
ids.add(4); ids.has(2); ids.delete(1); ids.size;
const unique = [...new Set([1, 1, 2, 3])]; // [1,2,3] dedupe trick
```

### Map — key/value store where **keys can be any type**
```javascript
const m = new Map();
m.set("name", "Sam");
m.set(1, "one");
const objKey = {};
m.set(objKey, "obj value"); // objects as keys!
m.get("name"); m.has(1); m.size;
for (const [k, v] of m) console.log(k, v);
```
> Map keeps insertion order and lets you use any key type — a plain object only allows string/symbol keys.

### WeakMap / WeakSet
Keys/values must be **objects**, held **weakly** (garbage-collectable). **Not iterable**, no `size`.

```javascript
const wm = new WeakMap();
let obj = {};
wm.set(obj, "private data"); // good for private per-object data
```

> **One-line revision:** Set = unique values. Map = any-key dictionary. Weak* = object-only, GC-friendly, private data.

**↪️ Counter-questions:**
- When use Map over a plain object? → non-string keys, frequent add/remove, need `size`, guaranteed order.
- Why can't you iterate a WeakMap? → entries can be collected at any time, so iteration order/contents aren't stable.

---

## 12. Objects: Cloning, Shallow vs Deep Copy

**Shallow copy** — top level copied, nested objects still shared.

```javascript
const user = { name: "Alice", address: { city: "Pune" } };

const c1 = Object.assign({}, user); // shallow
const c2 = { ...user };             // shallow (spread)

c2.address.city = "Delhi";
console.log(user.address.city); // "Delhi" ⚠️ nested object was shared
```

**Deep copy** — fully independent, including nested objects.

```javascript
const deep = structuredClone(user); // modern, built-in
deep.address.city = "Goa";
console.log(user.address.city); // unchanged

// Old trick (loses functions, dates, undefined):
const deep2 = JSON.parse(JSON.stringify(user));
```

> **One-line revision:** Spread/`Object.assign` = shallow. Use `structuredClone` for deep copies.

**↪️ Counter-questions:**
- What does `JSON.parse(JSON.stringify(obj))` lose? → functions, `undefined`, `Symbol`, converts `Date` to string, breaks on circular refs.
- Can `structuredClone` copy functions? → No, it throws on functions.

---

## 13. `Object.freeze` & Immutability

`Object.freeze` makes an object read-only (shallow — nested objects stay mutable).

```javascript
const config = Object.freeze({ mode: "dark", nested: { x: 1 } });
config.mode = "light";   // ignored (throws in strict mode)
config.nested.x = 99;    // ⚠️ still works (freeze is shallow)
Object.isFrozen(config); // true
```

- `Object.freeze` — no add/remove/change.
- `Object.seal` — can change existing values, but no add/remove.

**↪️ Counter-question:** How to deep-freeze? → recursively `Object.freeze` every nested object.

---

## 14. JSON Methods

```javascript
JSON.stringify({ a: 1, b: [2, 3] }); // '{"a":1,"b":[2,3]}'
JSON.parse('{"a":1}');               // { a: 1 }

// Pretty print + filter keys
JSON.stringify(obj, null, 2);        // 2-space indentation
JSON.stringify(obj, ["a"]);          // only include key "a"
```

> `JSON.stringify` skips `undefined`, functions, and symbols; converts `Date` to an ISO string.

**↪️ Counter-question:** What happens to a circular object in `JSON.stringify`? → throws `TypeError: Converting circular structure to JSON`.

---

## 15. Functions

```javascript
// Declaration — hoisted (callable before definition)
function add(a, b) { return a + b; }

// Expression — not hoisted as a value
const sub = function (a, b) { return a - b; };

// Arrow — shorter, no own `this`, no `arguments`
const mul = (a, b) => a * b;

// IIFE — runs immediately, creates a private scope
(function () {
  const secret = 42; // not leaked to outer scope
})();
```

**Arrow vs regular functions:**

| Feature        | Regular              | Arrow                          |
| :------------- | :------------------- | :----------------------------- |
| own `this`     | ✅ (by call site)    | ❌ (inherits from parent scope) |
| `arguments`    | ✅                   | ❌                             |
| usable as constructor (`new`) | ✅    | ❌                             |
| hoisted (declaration) | ✅            | ❌ (it's an expression)        |

**↪️ Counter-questions:**
- Why can't arrow functions be constructors? → they have no `[[Construct]]` internal method and no own `this`.
- When must you use a regular function? → object methods that rely on `this`, and constructors.

---

## 16. Higher-Order Functions & Callbacks

A **higher-order function (HOF)** takes a function as an argument and/or returns a function.

```javascript
// Takes a function
[1, 2, 3].map(n => n * 2);

// Returns a function
function multiplier(factor) {
  return n => n * factor;
}
const double = multiplier(2);
double(5); // 10
```

A **callback** is a function passed to be called later.

> `map`, `filter`, `reduce`, `setTimeout`, event listeners — all use callbacks.

---

## 17. The `this` Keyword

`this` refers to the object executing the current function. **Its value depends on how the function is called**, not where it's defined.

```javascript
// 1) Method call -> the object before the dot
const person = { name: "Alex", greet() { console.log(this.name); } };
person.greet(); // "Alex"

// 2) Standalone call -> global object (non-strict) or undefined (strict)
function show() { console.log(this); }
show(); // window / undefined (strict)

// 3) Arrow function -> inherits `this` from the enclosing scope
const obj = {
  val: 10,
  method() {
    const arrow = () => console.log(this.val);
    arrow(); // 10 (this = obj, inherited)
  },
};
obj.method();

// 4) Event handler -> the DOM element that fired the event
// button.addEventListener("click", function () { console.log(this); }); // the button
```

> **One-line revision:** `this` is decided at **call time**, except in arrow functions (decided at **definition** time).

### 🎯 Interview Q&A

**Q: What is `this` inside `setTimeout(function(){...})`?**
The global object (or `undefined` in strict/modules), because it's called as a plain function. Use an arrow function to keep the outer `this`.

**↪️ Counter-questions:**
- How do you fix a "lost `this`" in a callback? → arrow function, or `.bind(this)`.
- What is `this` at the top level of a module? → `undefined` (modules are strict).

---

## 18. `call`, `apply`, `bind`

All three set `this` explicitly.

| Method    | Executes?  | Arguments        |
| :-------- | :--------- | :--------------- |
| `call`    | immediately | individual (`a, b`) |
| `apply`   | immediately | array (`[a, b]`) |
| `bind`    | returns a new function | individual (can pre-fill) |

```javascript
function greet(msg) { console.log(`${msg}, ${this.name}`); }
const user = { name: "Alice" };

greet.call(user, "Hello");    // Hello, Alice
greet.apply(user, ["Hi"]);    // Hi, Alice
const bound = greet.bind(user);
bound("Hey");                 // Hey, Alice

// Method borrowing
const arrLike = { 0: "a", 1: "b", length: 2 };
Array.prototype.join.call(arrLike, "-"); // "a-b"
```

**↪️ Counter-questions:**
- `call` vs `apply`? → same, except how args are passed (list vs array).
- What does `bind` return? → a new function permanently bound to the given `this`; useful for event handlers and partial application.
- Can you re-bind a bound function? → No, the first `bind` wins.

---

## 19. Prototypes

Every object has a hidden internal link `[[Prototype]]`. When you access a property JS can't find on the object, it walks up this **prototype chain**.

- **`[[Prototype]]`** — the internal, hidden link (spec term).
- **`__proto__`** — a legacy getter/setter that exposes `[[Prototype]]`. Prefer `Object.getPrototypeOf` / `Object.setPrototypeOf`.
- **`function.prototype`** — the object that becomes the `[[Prototype]]` of instances created with `new`.

```javascript
const userMethods = {
  is18() { return this.age >= 18; },
};

function createUser(name, age) {
  const user = Object.create(userMethods); // user's prototype = userMethods
  user.name = name;
  user.age = age;
  return user;
}

const u = createUser("Sam", 22);
u.is18(); // true — found via prototype chain, `this` = u
```

**Constructor + prototype:**
```javascript
function Person(name) { this.name = name; }
Person.prototype.sayHi = function () { console.log("Hi " + this.name); };

const p = new Person("Alice");
p.sayHi();                                 // "Hi Alice"
Object.getPrototypeOf(p) === Person.prototype; // true
```

> **One-line revision:** Shared methods live on the prototype so every instance uses one copy (memory-efficient, DRY).

**↪️ Counter-questions:**
- Difference between `__proto__` and `prototype`? → `prototype` is a property **on constructor functions**; `__proto__` is the instance's link to it.
- Why put methods on the prototype instead of inside the constructor? → avoids duplicating the function per instance.

---

## 20. The `new` Keyword

`new Fn()` does four things:

1. Creates a fresh empty object.
2. Links its `[[Prototype]]` to `Fn.prototype`.
3. Calls `Fn` with `this` bound to that new object.
4. Returns the object (unless the constructor explicitly returns another object).

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}
const user = new Person("Alice", 25);
user.name; // "Alice"
```

**↪️ Counter-question:** What if a constructor returns a primitive? → it's ignored; the new object is returned. Returning an object overrides the default.

---

## 21. Classes

Classes are **syntactic sugar** over prototypes — cleaner syntax, same prototype machinery underneath.

```javascript
class Animal {
  constructor(name) { this.name = name; }
  speak() { console.log(`${this.name} makes a sound`); }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);          // call parent constructor (required before `this`)
    this.breed = breed;
  }
  speak() {
    super.speak();        // call parent method
    console.log(`${this.name} barks`);
  }
}

new Dog("Buddy", "Lab").speak(); // "Buddy makes a sound" / "Buddy barks"
```

**Getters / setters** — accessed like properties, run like methods.
```javascript
class Rectangle {
  constructor(w, h) { this._w = w; this._h = h; }
  get area() { return this._w * this._h; }
  set width(v) { if (v > 0) this._w = v; }
}
const r = new Rectangle(5, 10);
r.area;       // 50
r.width = 8;  // uses setter
```

**Static** members belong to the class, not instances.
```javascript
class MathHelper {
  static PI = 3.14159;
  static add(a, b) { return a + b; }
}
MathHelper.add(2, 3); // 5 (called on the class)
```

> **One-line revision:** `class` = a nicer way to write constructor functions + prototype methods.

**↪️ Counter-questions:**
- Why are classes called "fake"? → no classical classes exist; everything is objects linked by prototypes.
- What happens if you access `this` before `super()` in a subclass? → `ReferenceError`.
- Are class declarations hoisted? → Hoisted but **not initialized** (TDZ) — can't use before declaration.

---

## 22. Factory vs Constructor Functions

```javascript
// Factory: returns an object explicitly, no `new`, no `this`
function createUser(name) {
  return { name, greet() { return `Hello ${name}`; } };
}

// Constructor: uses `this`, called with `new`
function User(name) {
  this.name = name;
  this.greet = function () { return `Hello ${name}`; };
}
const u = new User("Sam");
```

| Feature        | Factory     | Constructor       |
| :------------- | :---------- | :---------------- |
| Returns object | explicit    | implicit (`this`) |
| Uses `new`     | ❌          | ✅                |
| `this` needed  | no          | yes               |

**↪️ Counter-question:** Advantage of factories? → no `new`/`this` confusion, easy to add private state via closures.

---

## 23. Currying, Composition, Memoization

**Currying** — turn `f(a, b)` into `f(a)(b)`.
```javascript
const add = a => b => a + b;
add(2)(3); // 5
```

**Composition** — combine small functions into one.
```javascript
const compose = (f, g) => x => f(g(x));
const shout = compose(s => s.toUpperCase(), s => s.trim());
shout("  hi "); // "HI"
```

**Memoization** — cache results of expensive calls.
```javascript
function memoize(fn) {
  const cache = {};
  return function (n) {
    if (n in cache) return cache[n];
    return (cache[n] = fn(n));
  };
}
```

**↪️ Counter-question:** Real use of currying? → partial application (pre-fill config), reusable specialized functions.

---

## 24. Pure vs Impure Functions

- **Pure:** same input → same output, no side effects.
- **Impure:** depends on / changes external state.

```javascript
const add = (a, b) => a + b; // pure

let count = 0;
const inc = () => count++;   // impure (mutates outside state)
```

> Prefer pure functions: easy to test, cache, and reason about.

---

## 25. Debounce vs Throttle

- **Debounce:** wait for a pause; run once after activity stops. (search inputs, resize)
- **Throttle:** run at most once per interval. (scroll, drag)

```javascript
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

function throttle(fn, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => (inThrottle = false), limit);
    }
  };
}
```

> **One-line revision:** Debounce = "wait until quiet." Throttle = "at most once per X ms."

**↪️ Counter-question:** Autocomplete search box — debounce or throttle? → debounce (wait until the user stops typing).

---

## 26. Symbols

A `Symbol` is a **unique, immutable** primitive — great for collision-free object keys.

```javascript
const id = Symbol("id");
const user = { [id]: 123 };
user[id]; // 123 (won't clash with any string key)

Symbol("x") === Symbol("x"); // false — always unique
```

> Well-known symbols like `Symbol.iterator` let you customize built-in behavior.

---

## 27. Generators & Iterators

A **generator** function (`function*`) can pause with `yield` and resume later.

```javascript
function* idMaker() {
  let id = 1;
  while (true) yield id++;
}
const gen = idMaker();
gen.next().value; // 1
gen.next().value; // 2
```

Make any object iterable with `[Symbol.iterator]`:
```javascript
const range = {
  from: 1, to: 3,
  *[Symbol.iterator]() { for (let i = this.from; i <= this.to; i++) yield i; },
};
[...range]; // [1, 2, 3]
```

**↪️ Counter-question:** Use case for generators? → lazy/infinite sequences, custom iteration, and older async flow control.

---

## 28. Error Handling

```javascript
try {
  throw new Error("Something broke");
} catch (err) {
  console.log(err.message); // "Something broke"
} finally {
  console.log("always runs"); // cleanup
}

// Custom error type
class ValidationError extends Error {
  constructor(msg) {
    super(msg);
    this.name = "ValidationError";
  }
}
```

> `finally` runs whether or not an error was thrown — use it for cleanup (closing files, hiding loaders).

**↪️ Counter-questions:**
- Does `try/catch` catch async errors in `setTimeout`? → No. The callback runs later, outside the try block. Handle inside the callback or use `async/await` + `try/catch`.
- How do you catch a rejected promise? → `.catch()` or `try/catch` around `await`.

---

## 29. Modules

**ES Modules (modern, browser + Node):**
```javascript
// math.js
export const add = (a, b) => a + b;
export default function () {}

// main.js
import defaultFn, { add } from "./math.js";
```

**CommonJS (older Node):**
```javascript
// math.js
module.exports = { add: (a, b) => a + b };
// main.js
const { add } = require("./math.js");
```

| Feature   | ES Modules            | CommonJS         |
| :-------- | :-------------------- | :--------------- |
| Syntax    | `import` / `export`   | `require` / `module.exports` |
| Loading   | static, async         | dynamic, synchronous |
| `this` at top | `undefined`       | `module.exports` |

**↪️ Counter-question:** Why are ESM imports hoisted/static? → enables tree-shaking (dead-code elimination) at build time.

---

## 30. Custom Events

Create and dispatch your own DOM events for decoupled communication.

```javascript
const evt = new CustomEvent("greet", { detail: { name: "Sam" } });
document.addEventListener("greet", (e) => console.log("Hello", e.detail.name));
document.dispatchEvent(evt); // "Hello Sam"
```

---

## 31. MCQs

Try each before revealing the answer.

**Q1.** What is the output?
```javascript
console.log(typeof null, typeof NaN, typeof []);
```
<details><summary>Answer</summary>

`object number object` — `null` is a historic `"object"`, `NaN` is a `number`, arrays are `object`.
</details>

**Q2.** What prints?
```javascript
console.log(0 == false, 0 === false, "" == false, null == undefined);
```
<details><summary>Answer</summary>

`true false true true` — `==` coerces; `===` does not.
</details>

**Q3.** What prints?
```javascript
console.log(1 + "2" + 3);
console.log(1 + 2 + "3");
```
<details><summary>Answer</summary>

`"123"` then `"33"` — left-to-right; `+` with a string concatenates.
</details>

**Q4.** Output?
```javascript
const a = { x: 1 };
const b = { ...a };
b.x = 5;
console.log(a.x, b.x);
```
<details><summary>Answer</summary>

`1 5` — spread makes a shallow copy of a top-level primitive, so `a` is unchanged.
</details>

**Q5.** Output?
```javascript
const obj = { x: 1, nested: { y: 2 } };
const copy = { ...obj };
copy.nested.y = 99;
console.log(obj.nested.y);
```
<details><summary>Answer</summary>

`99` — spread is shallow; `nested` is shared. Use `structuredClone` for a deep copy.
</details>

**Q6.** Output?
```javascript
console.log(0 ?? "a", 0 || "a", null ?? "b");
```
<details><summary>Answer</summary>

`0 a b` — `??` only falls back on `null`/`undefined`; `||` falls back on any falsy value.
</details>

**Q7.** Output?
```javascript
const arr = [10, 1, 2];
console.log(arr.sort());
```
<details><summary>Answer</summary>

`[1, 10, 2]` — default sort compares as strings. Use `sort((a,b)=>a-b)`.
</details>

**Q8.** Output?
```javascript
const nums = [1, 2, 3, 4];
console.log(nums.reduce((a, b) => a + b, 0));
console.log(nums.map(n => n * 2));
```
<details><summary>Answer</summary>

`10` then `[2, 4, 6, 8]`.
</details>

**Q9.** Output?
```javascript
const person = {
  name: "Sam",
  greet: () => console.log(this.name),
};
person.greet();
```
<details><summary>Answer</summary>

`undefined` — arrow functions don't get their own `this`; it's the enclosing (module/global) `this`, not `person`.
</details>

**Q10.** Output?
```javascript
function Foo() { this.x = 1; }
const f = new Foo();
console.log(Object.getPrototypeOf(f) === Foo.prototype);
```
<details><summary>Answer</summary>

`true` — `new` links the instance's prototype to `Foo.prototype`.
</details>

**Q11.** Output?
```javascript
console.log([1, 2, 3].includes(2));
console.log([1, 2, 3].indexOf(2));
console.log(Array.isArray([1]));
```
<details><summary>Answer</summary>

`true`, `1`, `true`.
</details>

**Q12.** Output?
```javascript
const s = new Set([1, 1, 2, 3, 3]);
console.log([...s], s.size);
```
<details><summary>Answer</summary>

`[1, 2, 3] 3` — Sets store unique values.
</details>

**Q13.** Output?
```javascript
console.log([] == false, [] == "", {} == {});
```
<details><summary>Answer</summary>

`true true false` — `[]`→`""`→`0`→`false`; two different object references are never `==`.
</details>

**Q14.** Output?
```javascript
const double = a => b => a + b;
console.log(double(3)(4));
```
<details><summary>Answer</summary>

`7` — curried addition.
</details>

**Q15.** Output?
```javascript
class A { greet() { return "A"; } }
class B extends A { greet() { return super.greet() + "B"; } }
console.log(new B().greet());
```
<details><summary>Answer</summary>

`"AB"` — `super.greet()` calls the parent method.
</details>

---

⬅️ [Master Index](./README.md) · Next: [02 — How JS Works + Scope & DOM](./JS%20Interview%20Prep_02.md)
