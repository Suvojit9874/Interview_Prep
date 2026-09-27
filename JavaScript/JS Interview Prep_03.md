# ⏳ JS Interview Prep 03 — Asynchronous JavaScript

> Timers, callbacks, promises, async/await, the event loop, and HTTP (AJAX/XHR/Fetch/Axios).
> ⬅️ Back to the [Master Index](./README.md)

---

## 📑 Index

1. [`setTimeout` & `setInterval`](#1-settimeout--setinterval)
2. [Callbacks](#2-callbacks)
3. [Callback Hell / Pyramid of Doom](#3-callback-hell--pyramid-of-doom)
4. [Promises](#4-promises)
5. [Promise Combinators](#5-promise-combinators)
6. [Combinators with Real API Calls](#6-combinators-with-real-api-calls)
7. [`async` / `await`](#7-async--await)
8. [Error Handling in Async Code](#8-error-handling-in-async-code)
9. [Microtask vs Macrotask Queue](#9-microtask-vs-macrotask-queue)
10. [AJAX, XHR, Fetch, Axios](#10-ajax-xhr-fetch-axios)
11. [Generators for Async Patterns](#11-generators-for-async-patterns)
12. [Interview Q&A](#12-interview-qa)
13. [MCQs — Guess the Output (with answers)](#13-mcqs--guess-the-output-with-answers)

---

## 1. `setTimeout` & `setInterval`

```javascript
setTimeout(() => console.log("Runs once after 2s"), 2000);

const id = setInterval(() => console.log("Every 1s"), 1000);
clearInterval(id); // stop it
```

- `setTimeout` runs a callback **once** after a delay.
- `setInterval` runs it **repeatedly**.
- The delay is a **minimum**, not a guarantee — the callback waits for the stack to clear.

**↪️ Counter-question:** Does `setTimeout(fn, 0)` run instantly? → No. It waits for the current synchronous code and all microtasks to finish.

---

## 2. Callbacks

A **callback** is a function passed to another function, to be called later (often after an async task).

```javascript
function greet(name, callback) {
  console.log("Hi " + name);
  callback();
}
greet("Suvojit", () => console.log("Bye!"));
```

---

## 3. Callback Hell / Pyramid of Doom

Deeply nested callbacks are hard to read and maintain — the "pyramid of doom."

```javascript
setTimeout(() => {
  console.log("Step 1: Login");
  setTimeout(() => {
    console.log("Step 2: Get User Data");
    setTimeout(() => {
      console.log("Step 3: Get Orders");
    }, 1000);
  }, 1000);
}, 1000);
```

**Fix with promises (flat chain):**
```javascript
login()
  .then(getUserData)
  .then(getOrders)
  .then(showSummary);
```

**Or async/await (reads top-to-bottom):**
```javascript
async function mainFlow() {
  await login();
  await getUserData();
  await getOrders();
  await showSummary();
}
```

> **One-line revision:** Promises flatten nesting; async/await makes async code look synchronous.

---

## 4. Promises

A **Promise** represents the eventual result of an async operation. Three states:

- **Pending** — still running.
- **Fulfilled** — succeeded (`resolve`).
- **Rejected** — failed (`reject`).

Once settled (fulfilled/rejected), a promise never changes state again.

```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("I worked hard and succeeded!"), 2000);
  // or reject("I failed.")
});

myPromise
  .then(result => console.log("Success:", result))
  .catch(error => console.log("Error:", error))
  .finally(() => console.log("Done either way"));
```

**Chaining** — each `.then` returns a new promise; return a value (or promise) to pass it along.

```javascript
Promise.resolve(2)
  .then(n => n * 2)   // 4
  .then(n => n + 1)   // 5
  .then(n => console.log(n)); // 5
```

> **One-line revision:** A promise is a placeholder for a future value; chain with `.then`, handle errors with `.catch`.

**↪️ Counter-questions:**
- Can a promise resolve twice? → No, the first settle wins; later calls are ignored.
- What does a `.then` callback return become? → the resolved value of the next promise in the chain.
- Where does an error mid-chain go? → to the nearest `.catch` downstream.

---

## 5. Promise Combinators

| Method                  | Resolves when…                             | Rejects when…                     |
| :---------------------- | :----------------------------------------- | :-------------------------------- |
| `Promise.all`           | **all** fulfill (array of results)         | **any** rejects (first error)     |
| `Promise.allSettled`    | **all** settle (array of status objects)   | never rejects                     |
| `Promise.any`           | **first** fulfills                         | **all** reject (`AggregateError`) |
| `Promise.race`          | **first** settles (fulfill OR reject)      | first settles as a rejection      |
| `Promise.resolve(v)`    | immediately with `v`                       | —                                 |
| `Promise.reject(e)`     | —                                          | immediately with `e`              |
| `Promise.withResolvers()` | returns `{ promise, resolve, reject }` for external control | — |

```javascript
Promise.all([p1, p2, p3]).then(([r1, r2, r3]) => {/* all ok */});
Promise.allSettled([p1, p2]).then(results => {
  // [{status:"fulfilled", value}, {status:"rejected", reason}]
});
Promise.any([p1, p2]).then(first => {/* first success */});
Promise.race([p1, p2]).then(firstSettled => {/* fastest */});

const { promise, resolve, reject } = Promise.withResolvers();
resolve("external!"); // resolve from outside
```

> **One-line revision:** `all` = all-or-nothing; `allSettled` = report everything; `any` = first success; `race` = first to finish.

**↪️ Counter-questions:**
- `all` vs `allSettled`? → `all` rejects on the first failure; `allSettled` waits for everyone and never rejects.
- `any` vs `race`? → `any` ignores rejections until all fail; `race` settles on the very first result, even a rejection.

---

## 6. Combinators with Real API Calls

```javascript
const api1 = fetch("https://api.example.com/data1");
const api2 = fetch("https://api.example.com/data2");

// race — fastest wins (fulfilled or rejected)
Promise.race([api1, api2])
  .then(r => r.json())
  .then(data => console.log("First responded:", data));

// any — first successful response
Promise.any([api1, api2])
  .then(r => r.json())
  .then(data => console.log("First success:", data))
  .catch(err => console.error("All failed:", err));

// all — need every request to succeed
Promise.all([api1, api2])
  .then(responses => Promise.all(responses.map(r => r.json())))
  .then(all => console.log("All succeeded:", all))
  .catch(err => console.error("One failed:", err));

// allSettled — collect successes and failures
Promise.allSettled([api1, api2]).then(results => {
  results.forEach((res, i) => {
    if (res.status === "fulfilled") console.log(`API ${i + 1} ok`);
    else console.error(`API ${i + 1} failed:`, res.reason);
  });
});
```

---

## 7. `async` / `await`

`async` functions always return a promise. `await` pauses inside the function until a promise settles, giving synchronous-looking code.

```javascript
async function getUser() {
  const res = await fetch("/api/user");   // wait for the response
  const data = await res.json();           // wait for parsing
  return data;                             // wrapped in a resolved promise
}

getUser().then(user => console.log(user));
```

**Run in parallel** — don't `await` one at a time if they're independent:
```javascript
// ❌ sequential (slow)
const a = await fetch("/a");
const b = await fetch("/b");

// ✅ parallel (fast)
const [a2, b2] = await Promise.all([fetch("/a"), fetch("/b")]);
```

> **One-line revision:** `await` unwraps a promise; an `async` function always returns a promise.

**↪️ Counter-questions:**
- Can you use `await` at the top level? → Yes, in ES modules (top-level await); not in regular scripts.
- What does `await 5` do? → wraps `5` in a resolved promise and returns `5`.
- Is `await` blocking the whole thread? → No, only that async function pauses; other code keeps running.

---

## 8. Error Handling in Async Code

**With async/await → `try/catch`:**
```javascript
async function load() {
  try {
    const res = await fetch("/api");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return await res.json();
  } catch (err) {
    console.error("Failed:", err.message);
  } finally {
    console.log("cleanup (hide loader)");
  }
}
```

**With promises → `.catch`:**
```javascript
fetch("/api").then(r => r.json()).catch(err => console.error(err));
```

> ⚠️ `fetch` only rejects on **network failure**, not on HTTP errors like 404/500. Always check `res.ok`.

**↪️ Counter-questions:**
- Does `try/catch` catch errors thrown inside `setTimeout`? → No; the callback runs later, outside the try block.
- What is an unhandled promise rejection? → a rejected promise with no `.catch`; handle it or listen for `unhandledrejection`.

---

## 9. Microtask vs Macrotask Queue

- **Microtask queue** (higher priority): `Promise.then/catch/finally`, `queueMicrotask`, `MutationObserver`.
- **Macrotask / callback queue**: `setTimeout`, `setInterval`, I/O, `requestAnimationFrame`, `setImmediate` (Node).

**The event loop drains ALL microtasks before the next macrotask.**

```text
1. Run synchronous code (call stack)
2. Stack empty? → run ALL microtasks
3. Then run ONE macrotask
4. Repeat from step 2
```

```javascript
console.log("Start");
setTimeout(() => console.log("Timeout"), 0);   // macrotask
Promise.resolve().then(() => console.log("Promise")); // microtask
console.log("End");
// Start, End, Promise, Timeout
```

Nested example:
```javascript
setTimeout(() => console.log("setTimeout 1"), 0);
Promise.resolve()
  .then(() => { console.log("Promise 1"); return Promise.resolve(); })
  .then(() => console.log("Promise 2"));
setTimeout(() => console.log("setTimeout 2"), 0);
// Promise 1, Promise 2, setTimeout 1, setTimeout 2
```

`queueMicrotask` — manually schedule a microtask:
```javascript
console.log("Start");
queueMicrotask(() => console.log("Microtask"));
console.log("End");
// Start, End, Microtask
```

> **One-line revision:** Microtasks (promises) always beat macrotasks (timers).

**↪️ Counter-question:** Why the priority? → promise chains often carry critical logic, so JS resolves them before delayed/UI tasks.

---

## 10. AJAX, XHR, Fetch, Axios

| Term    | Type            | Modern? | Notes                                       |
| :------ | :-------------- | :------ | :------------------------------------------ |
| AJAX    | concept         | ✅      | technique for async HTTP without page reload |
| XHR     | Web API         | 🚫 legacy | `XMLHttpRequest`, callback-based, verbose  |
| Fetch   | Web API         | ✅      | promise-based, built-in, clean              |
| Axios   | library         | ✅      | promise-based, interceptors, timeouts, auto-JSON |

**XHR (legacy):**
```javascript
const xhr = new XMLHttpRequest();
xhr.open("GET", "/api/data");
xhr.onload = () => { if (xhr.status === 200) console.log(xhr.responseText); };
xhr.send();
```

**Fetch (preferred, native):**
```javascript
fetch("/api/data")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

**Axios (library):**
```javascript
axios.get("/api/data")
  .then(res => console.log(res.data))
  .catch(err => console.error(err));
```

**Fetch vs Axios:** Axios auto-parses JSON, supports interceptors/timeouts/cancellation, and treats 4xx/5xx as errors. Fetch needs manual `res.json()` and `res.ok` checks but requires no install.

**↪️ Counter-questions:**
- Does `fetch` reject on 404? → No, only on network failure. Check `res.ok`.
- Why choose Axios? → interceptors, timeouts, automatic JSON, and consistent error handling.

---

## 11. Generators for Async Patterns

Generators (`function*`) can pause with `yield`, which historically powered async flows (before async/await). Still useful for lazy sequences.

```javascript
function* taskRunner() {
  const a = yield fetch("/a");
  const b = yield fetch("/b");
  return [a, b];
}
```

> Today, prefer `async/await`. Generators shine for infinite/lazy iteration and custom iterators.

---

## 12. Interview Q&A

**Q: What's the difference between synchronous and asynchronous code?**
Synchronous runs line by line, blocking until each finishes. Asynchronous schedules work (timers, network) to complete later without blocking the main thread.

**Q: Callback vs Promise vs async/await — why did each appear?**
Callbacks came first but nest badly (callback hell). Promises flattened chains and improved error handling. async/await added synchronous-looking syntax on top of promises.

**Q: What is the event loop in one sentence?**
A loop that, when the call stack is empty, drains all microtasks and then runs one macrotask — repeatedly.

**↪️ Counter-questions:**
- Why is JavaScript "non-blocking" if it's single-threaded? → because long-running tasks are offloaded to the environment (Web APIs / libuv in Node).
- What happens if a promise never resolves? → its `.then` never runs; the awaiting function stays paused (a potential hang).

---

## 13. MCQs — Guess the Output (with answers)

**Q1.**
```javascript
console.log("Start");
setTimeout(() => console.log("Timeout"), 0);
console.log("End");
```
<details><summary>Answer</summary>

`Start`, `End`, `Timeout` — the timer callback is a macrotask, run after sync code.
</details>

**Q2.**
```javascript
setTimeout(() => console.log("First"), 300);
setTimeout(() => console.log("Second"), 100);
setTimeout(() => console.log("Third"), 200);
```
<details><summary>Answer</summary>

`Second`, `Third`, `First` — shorter delays fire earlier.
</details>

**Q3.**
```javascript
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
<details><summary>Answer</summary>

`3 3 3` — shared `var i` read after the loop.
</details>

**Q4.**
```javascript
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), 100);
```
<details><summary>Answer</summary>

`0 1 2` — `let` creates a new binding per iteration.
</details>

**Q5.**
```javascript
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```
<details><summary>Answer</summary>

`A`, `D`, `C`, `B` — sync first, then microtask (`C`), then macrotask (`B`).
</details>

**Q6.**
```javascript
setTimeout(() => {
  console.log("One");
  setTimeout(() => console.log("Two"), 0);
}, 0);
```
<details><summary>Answer</summary>

`One` then `Two` — the inner timer is scheduled only after the outer runs.
</details>

**Q7.**
```javascript
let count = 0;
const id = setInterval(() => {
  console.log(count++);
  if (count === 3) clearInterval(id);
}, 100);
```
<details><summary>Answer</summary>

`0 1 2` — logs then stops when `count` reaches 3.
</details>

**Q8.**
```javascript
console.log("Before");
setTimeout(() => console.log("Inside Timeout"), 0);
console.log("After");
```
<details><summary>Answer</summary>

`Before`, `After`, `Inside Timeout`.
</details>

**Q9.**
```javascript
setTimeout(() => console.log("Timeout"), 100);
const start = Date.now();
while (Date.now() - start < 200) {} // block 200ms
console.log("End");
```
<details><summary>Answer</summary>

`End` then `Timeout` — synchronous blocking delays the timer callback until the stack clears.
</details>

**Q10.**
```javascript
function test() {
  setTimeout(() => console.log("Timeout"), 0);
  console.log("Function End");
}
test();
```
<details><summary>Answer</summary>

`Function End` then `Timeout`.
</details>

**Q11.**
```javascript
setTimeout(() => {
  console.log("First");
  setTimeout(() => console.log("Second"), 0);
}, 0);
```
<details><summary>Answer</summary>

`First` then `Second`.
</details>

**Q12.**
```javascript
let i = 0;
const int = setInterval(() => {
  console.log(i++);
  if (i === 5) clearInterval(int);
}, 300);
```
<details><summary>Answer</summary>

`0 1 2 3 4` — one log every 300ms, then stops.
</details>

**Q13.**
```javascript
(() => {
  console.log("Start");
  setTimeout(() => console.log("Async"), 0);
  console.log("End");
})();
```
<details><summary>Answer</summary>

`Start`, `End`, `Async`.
</details>

**Q14.**
```javascript
setTimeout((msg) => console.log(msg), 100, "Hello with arg");
```
<details><summary>Answer</summary>

`Hello with arg` — extra args after the delay are passed to the callback.
</details>

**Q15.**
```javascript
setTimeout(() => console.log("This takes time"), 5000);
console.log("Done");
```
<details><summary>Answer</summary>

`Done` immediately, then `This takes time` after ~5s.
</details>

**Q16.**
```javascript
setTimeout(() => console.log("1"), 0);
setTimeout(() => console.log("2"), 0);
setTimeout(() => console.log("3"), 0);
```
<details><summary>Answer</summary>

`1 2 3` — same delay, queued in order.
</details>

**Q17.**
```javascript
setTimeout(() => console.log("Timeout"), 0);
Promise.resolve().then(() => console.log("Promise"));
console.log("Done");
```
<details><summary>Answer</summary>

`Done`, `Promise`, `Timeout` — microtask beats macrotask.
</details>

**Q18.**
```javascript
["a", "b", "c"].forEach((item) => setTimeout(() => console.log(item), 0));
```
<details><summary>Answer</summary>

`a b c` — three timers queued in iteration order.
</details>

**Q19.**
```javascript
try {
  setTimeout(null, 100);
  console.log("No error?");
} catch (e) {
  console.log("Error caught");
}
```
<details><summary>Answer</summary>

`No error?` — scheduling with a `null` callback doesn't throw synchronously here; the `try` block completes normally.
</details>

**Q20.**
```javascript
const id = setTimeout(() => console.log("Should not run"), 100);
clearTimeout(id);
```
<details><summary>Answer</summary>

(nothing) — the timer is cleared before it fires.
</details>

**Q21.**
```javascript
async function f() {
  console.log(1);
  await null;
  console.log(2);
}
console.log(0);
f();
console.log(3);
```
<details><summary>Answer</summary>

`0 1 3 2` — code before `await` runs synchronously; code after `await` resumes as a microtask.
</details>

**Q22.**
```javascript
Promise.resolve().then(() => console.log("A"));
queueMicrotask(() => console.log("B"));
setTimeout(() => console.log("C"), 0);
console.log("D");
```
<details><summary>Answer</summary>

`D`, `A`, `B`, `C` — sync first, microtasks in order, then the macrotask.
</details>

> More output drills across all topics: [MCQ Practice Bank](./mcqsOnHoisting.md).

---

⬅️ Prev: [02 — How JS Works + Scope & DOM](./JS%20Interview%20Prep_02.md) · [Master Index](./README.md)
