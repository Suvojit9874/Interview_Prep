# 🧩 JS Interview Prep 04 — MCQ Practice Bank

> 100+ "guess the output" and concept questions with answers. Attempt each **before** revealing.
> Think in terms of the call stack, scope chain, hoisting, TDZ, closures, and the event loop.
> ⬅️ Back to the [Master Index](./README.md)

---

## 📑 Index

- [Section A — Closures, Lexical Scope & Hoisting (50 Qs)](#section-a--closures-lexical-scope--hoisting)
- [Section B — Types, Coercion & Operators (15 Qs)](#section-b--types-coercion--operators)
- [Section C — `this`, Prototypes & Classes (12 Qs)](#section-c--this-prototypes--classes)
- [Section D — Arrays & Objects (12 Qs)](#section-d--arrays--objects)
- [Section E — Async & Event Loop (10 Qs)](#section-e--async--event-loop)

> Related notes: [01 Core JS](./JS%20Interview%20Prep_01.md) · [02 Scope & DOM](./JS%20Interview%20Prep_02.md) · [03 Async](./JS%20Interview%20Prep_03.md)

---

## Section A — Closures, Lexical Scope & Hoisting

Medium-to-hard "guess the output" questions.

### 1.
```js
function outer() {
  let count = 0;
  return function inner() { return ++count; };
}
const fn = outer();
console.log(fn());
console.log(fn());
```
**Answer:** `1` then `2`

### 2.
```js
var a = 10;
function foo() {
  console.log(a);
  var a = 20;
}
foo();
```
**Answer:** `undefined` (local `a` is hoisted, shadowing the global)

### 3.
```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
**Answer:** `3 3 3`

### 4.
```js
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
**Answer:** `0 1 2`

### 5.
```js
function createCounter() {
  let count = 0;
  return {
    increment() { count++; },
    getValue() { return count; },
  };
}
const counter = createCounter();
counter.increment();
counter.increment();
console.log(counter.getValue());
```
**Answer:** `2`

### 6.
```js
var x = 21;
function fun() {
  console.log(x);
  var x = 20;
}
fun();
```
**Answer:** `undefined`

### 7.
```js
var a = 1;
(function () {
  console.log(a);
  var a = 2;
})();
```
**Answer:** `undefined`

### 8.
```js
var funcs = [];
for (var i = 0; i < 3; i++) funcs[i] = () => console.log(i);
funcs[0](); funcs[1](); funcs[2]();
```
**Answer:** `3 3 3`

### 9.
```js
var funcs = [];
for (let i = 0; i < 3; i++) funcs[i] = () => console.log(i);
funcs[0](); funcs[1](); funcs[2]();
```
**Answer:** `0 1 2`

### 10.
```js
function outer() {
  var a = 10;
  function inner() { console.log(a); }
  a = 20;
  return inner;
}
outer()();
```
**Answer:** `20` (closure reads the live variable)

### 11.
```js
var a = 1;
function test() {
  console.log(a);
  a = 2;
}
test();
console.log(a);
```
**Answer:** `1` then `2`

### 12.
```js
function test() {
  console.log(x);
  let x = 2;
}
test();
```
**Answer:** `ReferenceError: Cannot access 'x' before initialization` (TDZ)

### 13.
```js
console.log(typeof foo);
var foo = "Hello";
```
**Answer:** `undefined`

### 14.
```js
console.log(typeof bar);
let bar = "Hello";
```
**Answer:** `ReferenceError: Cannot access 'bar' before initialization`

### 15.
```js
function makeAdder(x) {
  return (y) => x + y;
}
console.log(makeAdder(5)(2));
```
**Answer:** `7`

### 16.
```js
var a = 1;
function foo() {
  a = 3;
  console.log(a);
  var a = 2;
}
foo();
console.log(a);
```
**Answer:** `3` then `1` (inside foo, local `a` is assigned 3 then logged; the global stays 1)

### 17.
```js
var a = 1;
function foo() {
  console.log(a);
  var a = 2;
}
foo();
console.log(a);
```
**Answer:** `undefined` then `1`

### 18.
```js
var a = 5;
(function () {
  console.log(a);
  var a = 10;
  console.log(a);
})();
```
**Answer:** `undefined` then `10`

### 19.
```js
var a = 5;
(function () {
  console.log(a);
  a = 10;
  console.log(a);
})();
console.log(a);
```
**Answer:** `5`, `10`, `10` (no local `var`, so it changes the global)

### 20.
```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
**Answer:** `3 3 3`

### 21.
```js
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
**Answer:** `0 1 2`

### 22.
```js
var funcs = [];
for (var i = 0; i < 3; i++) {
  funcs[i] = ((x) => () => console.log(x))(i);
}
funcs[0](); funcs[1](); funcs[2]();
```
**Answer:** `0 1 2` (IIFE captures each `i`)

### 23.
```js
function foo() {
  var a = (b = 0);
  a++; b++;
  console.log(a); console.log(b);
}
foo();
console.log(typeof a);
console.log(typeof b);
```
**Answer:** `1`, `1`, `undefined`, `number` (`b` leaked to global as an implicit global)

### 24.
```js
(function () {
  console.log(typeof foo);
  var foo = 123;
  console.log(typeof foo);
})();
```
**Answer:** `undefined` then `number`

### 25.
```js
var a = 1;
function test() {
  a = 2;
  function foo() {
    console.log(a);
    var a = 3;
  }
  foo();
}
test();
```
**Answer:** `undefined` (inner `foo` has its own hoisted `a`)

### 26.
```js
console.log(x);
let x = 7;
```
**Answer:** `ReferenceError: Cannot access 'x' before initialization`

### 27.
```js
function counter() {
  let count = 0;
  return () => count++;
}
const c = counter();
console.log(c()); console.log(c()); console.log(c());
```
**Answer:** `0 1 2` (post-increment returns the old value)

### 28.
```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), i * 1000);
```
**Answer:** `3 3 3`

### 29.
```js
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), i * 1000);
```
**Answer:** `0 1 2`

### 30.
```js
function outer() {
  var a = 10;
  function inner() {
    console.log(a);
    var a = 20;
  }
  return inner;
}
outer()();
```
**Answer:** `undefined` (inner's own `a` is hoisted, shadowing outer)

### 31.
```js
var a = 10;
(function () {
  console.log(a);
  a = 20;
  var a;
})();
```
**Answer:** `undefined`

### 32.
```js
let a = 10;
function foo() {
  console.log(a);
  let a = 20;
}
foo();
```
**Answer:** `ReferenceError: Cannot access 'a' before initialization`

### 33.
```js
var name = "Global";
function outer() {
  var name = "Outer";
  return function inner() { console.log(name); };
}
outer()();
```
**Answer:** `Outer`

### 34.
```js
console.log(foo);
var foo = "Hello";
```
**Answer:** `undefined`

### 35.
```js
var x = 1;
function foo() {
  x = 10;
  return;
  function x() {}
}
foo();
console.log(x);
```
**Answer:** `1` (the hoisted function declaration `x` is local, so `x = 10` sets the local, not the global)

### 36.
```js
function test() {
  console.log(a);
  console.log(foo());
  var a = 1;
  function foo() { return 2; }
}
test();
```
**Answer:** `undefined` then `2` (function declarations fully hoisted)

### 37.
```js
var length = 10;
function fn() { console.log(this.length); }
var obj = {
  length: 5,
  method(fn) { fn(); arguments[0](); },
};
obj.method(fn, 1);
```
**Answer:** `10` then `2` (first call: global; second call via `arguments`: `this` = arguments, whose length is 2)

### 38.
```js
function foo() {
  return
  { bar: "hello" };
}
console.log(foo());
```
**Answer:** `undefined` (automatic semicolon insertion after `return`)

### 39.
```js
var x = 1;
function bar() {
  console.log(x);
  var x = 2;
}
bar();
```
**Answer:** `undefined`

### 40.
```js
var y = 5;
(function () {
  console.log(y);
  var y = 10;
  console.log(y);
})();
```
**Answer:** `undefined` then `10`

### 41.
```js
var count = 0;
(function immediate() {
  if (count === 0) {
    var count = 1;
    console.log(count);
  }
  console.log(count);
})();
```
**Answer:** `1` then `1` (local `count` hoisted; the outer `count === 0` check reads the hoisted-undefined local... note: `undefined === 0` is false, so neither branch runs? Actually the hoisted local `count` is `undefined`, `undefined === 0` is `false`, so the `if` body is skipped and only the final `console.log(count)` runs → prints `undefined`.)

> ⚠️ Correction: because `var count` is hoisted **inside** the IIFE, the `if (count === 0)` compares `undefined === 0` → `false`. The block is skipped, so the output is a single `undefined`.

### 42.
```js
(function () {
  console.log(a);
  console.log(foo());
  var a = 1;
  function foo() { return 2; }
})();
```
**Answer:** `undefined` then `2`

### 43.
```js
var a = 10;
function example() {
  console.log(a);
  if (false) { var a = 20; }
}
example();
```
**Answer:** `undefined` (`var a` is hoisted regardless of the dead `if`)

### 44.
```js
var a = "Hello";
(function () {
  console.log(a);
  a = "World";
  console.log(a);
})();
console.log(a);
```
**Answer:** `Hello`, `World`, `World`

### 45.
```js
function f1() {
  let a = 1;
  function f2() { console.log(a); }
  return f2;
}
f1()();
```
**Answer:** `1`

### 46.
```js
var x = 10;
function test() {
  x = 20;
  return;
  function x() {}
}
test();
console.log(x);
```
**Answer:** `10` (hoisted local function `x` captures the assignment)

### 47.
```js
var x = 10;
function test() {
  console.log(x);
  var x = 20;
}
test();
```
**Answer:** `undefined`

### 48.
```js
var a = 1;
function foo() {
  a = 2;
  function inner() {
    console.log(a);
    var a = 3;
  }
  inner();
}
foo();
```
**Answer:** `undefined`

### 49.
```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), i * 1000);
```
**Answer:** `3 3 3`

### 50.
```js
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), i * 1000);
```
**Answer:** `0 1 2`

---

## Section B — Types, Coercion & Operators

### B1.
```js
console.log(typeof null, typeof undefined, typeof NaN);
```
**Answer:** `object undefined number`

### B2.
```js
console.log(1 + "2", "3" - 1, "5" * "2");
```
**Answer:** `"12"`, `2`, `10` (`+` concatenates with a string; `-`/`*` force numbers)

### B3.
```js
console.log(0 == false, 0 === false, "" == false);
```
**Answer:** `true false true`

### B4.
```js
console.log(null == undefined, null === undefined, null == 0);
```
**Answer:** `true false false`

### B5.
```js
console.log([] == ![]);
```
**Answer:** `true` (`![]` → `false`; then `[] == false` → `0 == 0`)

### B6.
```js
console.log(0.1 + 0.2 === 0.3);
```
**Answer:** `false` (floating-point rounding; the sum is `0.30000000000000004`)

### B7.
```js
console.log(NaN === NaN, Number.isNaN(NaN));
```
**Answer:** `false true`

### B8.
```js
console.log(0 ?? "a", 0 || "a", null ?? "b");
```
**Answer:** `0 a b`

### B9.
```js
const user = null;
console.log(user?.name ?? "Guest");
```
**Answer:** `Guest`

### B10.
```js
console.log(true + true + true);
```
**Answer:** `3` (each `true` coerces to `1`)

### B11.
```js
console.log("5" + 3 - 2);
```
**Answer:** `51` (`"5" + 3` = `"53"`, then `"53" - 2` = `51`)

### B12.
```js
console.log([1, 2, 3] + [4, 5, 6]);
```
**Answer:** `"1,2,34,5,6"` (arrays convert to strings and concatenate)

### B13.
```js
let x;
console.log(x, x + 1);
```
**Answer:** `undefined NaN`

### B14.
```js
console.log(null + 1, undefined + 1);
```
**Answer:** `1 NaN` (`null` → 0, `undefined` → NaN)

### B15.
```js
console.log(Boolean(""), Boolean("0"), Boolean([]), Boolean({}));
```
**Answer:** `false true true true`

---

## Section C — `this`, Prototypes & Classes

### C1.
```js
const obj = {
  name: "Sam",
  regular() { return this.name; },
  arrow: () => this.name,
};
console.log(obj.regular(), obj.arrow());
```
**Answer:** `"Sam"` and `undefined` (arrow has no own `this`)

### C2.
```js
function Person(name) { this.name = name; }
const p = new Person("Kai");
console.log(p.name, Object.getPrototypeOf(p) === Person.prototype);
```
**Answer:** `"Kai" true`

### C3.
```js
function greet() { return this.msg; }
console.log(greet.call({ msg: "hi" }));
```
**Answer:** `"hi"`

### C4.
```js
const o = { x: 1 };
function show() { return this.x; }
const bound = show.bind(o);
console.log(bound());
```
**Answer:** `1`

### C5.
```js
class A { hello() { return "A"; } }
class B extends A { hello() { return super.hello() + "B"; } }
console.log(new B().hello());
```
**Answer:** `"AB"`

### C6.
```js
class Counter {
  static count = 0;
  constructor() { Counter.count++; }
}
new Counter(); new Counter();
console.log(Counter.count);
```
**Answer:** `2`

### C7.
```js
const arr = { 0: "a", 1: "b", length: 2 };
console.log(Array.prototype.join.call(arr, "-"));
```
**Answer:** `"a-b"` (method borrowing)

### C8.
```js
function Foo() { return { custom: true }; }
console.log(new Foo());
```
**Answer:** `{ custom: true }` (constructor returning an object overrides `this`)

### C9.
```js
class Rect {
  constructor(w, h) { this._w = w; this._h = h; }
  get area() { return this._w * this._h; }
}
console.log(new Rect(4, 5).area);
```
**Answer:** `20`

### C10.
```js
const obj = {
  val: 10,
  outer() {
    const inner = () => this.val;
    return inner();
  },
};
console.log(obj.outer());
```
**Answer:** `10` (arrow inherits `this` from `outer`)

### C11.
```js
console.log(typeof class {});
```
**Answer:** `"function"` (classes are functions under the hood)

### C12.
```js
const a = new Number(5);
const b = 5;
console.log(a == b, a === b, typeof a);
```
**Answer:** `true false object` (`new Number` creates an object wrapper)

---

## Section D — Arrays & Objects

### D1.
```js
console.log([10, 1, 2].sort());
```
**Answer:** `[1, 10, 2]` (default string sort)

### D2.
```js
console.log([1, 2, 3, 4].reduce((a, b) => a + b));
```
**Answer:** `10`

### D3.
```js
console.log([1, 2, 3].map(n => n * 2).filter(n => n > 2));
```
**Answer:** `[4, 6]`

### D4.
```js
const a = [1, 2, 3];
const b = a;
b.push(4);
console.log(a.length);
```
**Answer:** `4` (arrays are shared by reference)

### D5.
```js
const obj = { a: 1, nested: { b: 2 } };
const copy = { ...obj };
copy.nested.b = 99;
console.log(obj.nested.b);
```
**Answer:** `99` (shallow copy shares nested objects)

### D6.
```js
console.log([...new Set([1, 1, 2, 3, 3])]);
```
**Answer:** `[1, 2, 3]`

### D7.
```js
const [a, , c] = [1, 2, 3];
console.log(a, c);
```
**Answer:** `1 3` (skipped middle element)

### D8.
```js
const { x = 5, y = 10 } = { x: 1 };
console.log(x, y);
```
**Answer:** `1 10`

### D9.
```js
console.log(Array.from({ length: 3 }, (_, i) => i * 2));
```
**Answer:** `[0, 2, 4]`

### D10.
```js
console.log([1, 2, 3].includes(2), [1, 2, 3].indexOf(5));
```
**Answer:** `true -1`

### D11.
```js
const arr = [1, 2, 3, 4];
arr.splice(1, 2, "a");
console.log(arr);
```
**Answer:** `[1, "a", 4]` (remove 2 from index 1, insert "a")

### D12.
```js
const frozen = Object.freeze({ n: 1 });
frozen.n = 5;
console.log(frozen.n);
```
**Answer:** `1` (frozen objects ignore writes; throws in strict mode)

---

## Section E — Async & Event Loop

### E1.
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```
**Answer:** `A D C B` (sync → microtask → macrotask)

### E2.
```js
async function f() { console.log(1); await null; console.log(2); }
console.log(0); f(); console.log(3);
```
**Answer:** `0 1 3 2`

### E3.
```js
Promise.resolve()
  .then(() => console.log("t1"))
  .then(() => console.log("t2"));
console.log("sync");
```
**Answer:** `sync t1 t2`

### E4.
```js
setTimeout(() => console.log("mac"), 0);
queueMicrotask(() => console.log("mic"));
console.log("sync");
```
**Answer:** `sync mic mac`

### E5.
```js
Promise.reject("err").catch(e => console.log("caught", e));
```
**Answer:** `caught err`

### E6.
```js
async function g() { return 5; }
g().then(v => console.log(v));
```
**Answer:** `5` (async functions return a promise)

### E7.
```js
console.log("1");
setTimeout(() => console.log("2"), 100);
setTimeout(() => console.log("3"), 0);
console.log("4");
```
**Answer:** `1 4 3 2`

### E8.
```js
Promise.all([Promise.resolve(1), Promise.resolve(2)]).then(v => console.log(v));
```
**Answer:** `[1, 2]`

### E9.
```js
Promise.race([
  new Promise(r => setTimeout(() => r("slow"), 100)),
  new Promise(r => setTimeout(() => r("fast"), 10)),
]).then(console.log);
```
**Answer:** `fast`

### E10.
```js
Promise.any([Promise.reject("a"), Promise.resolve("b")]).then(console.log);
```
**Answer:** `b` (first fulfilled; rejections ignored unless all fail)

---

⬅️ [Master Index](./README.md) · [01 Core JS](./JS%20Interview%20Prep_01.md) · [02 Scope & DOM](./JS%20Interview%20Prep_02.md) · [03 Async](./JS%20Interview%20Prep_03.md)
