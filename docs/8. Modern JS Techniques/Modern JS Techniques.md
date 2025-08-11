# Modern JavaScript Cheat-Sheet – ES2020-ES2025 Features, Scopes & Advanced Patterns

## Table of Contents

- [1. Latest JavaScript Features](#1-latest-javascript-features)
  - [1.1 Optional Chaining (?.)](#11-optional-chaining-)
  - [1.2 Nullish Coalescing Operator (??)](#12-nullish-coalescing-operator-)
  - [1.3 Numeric Separators (\_)](#13-numeric-separators-_)
  - [1.4 `Array.prototype.at()`](#14-arrayprototypeat)
  - [1.5 `String.prototype.replaceAll()`](#15-stringprototypereplaceall)
  - [1.6 Logical Assignment Operators](#16-logical-assignment-operators)
  - [1.7 `Promise.any()`](#17-promiseany)
  - [1.8 Generator Functions](#18-generator-functions)
- [2. Debouncing & Throttling](#2-debouncing--throttling)
  - [2.1 Debouncing](#21-debouncing)
  - [2.2 Throttling](#22-throttling)
- [3. Best-Practice Takeaways](#3-bestpractice-takeaways)

---

## 1. Latest JavaScript Features

### 1.1 Optional Chaining (?.)

Safely access nested properties without manual checks.

```js
const user = { name: "Alice", profile: { age: 30 } };
console.log(user?.name); // 'Alice'
console.log(user?.profile?.age); // 30
console.log(user?.address?.city); // undefined (no error)
```

_Tip →_ Combine with nullish coalescing for defaults: `const city = user?.address?.city ?? 'Unknown';`

### 1.2 Nullish Coalescing Operator (??)

Returns right-hand value **only** when left-hand is `null` or `undefined`.

```js
const n = undefined;
console.log(n ?? "default"); // 'default'
const zero = 0;
console.log(zero ?? 10); // 0 (0 is not nullish)
```

### 1.3 Numeric Separators (\_)

Visual separators improve readability – no runtime impact.

```js
const budget = 10_000_000;
```

### 1.4 `Array.prototype.at()`

Positive or negative indices ( `-1` → last element ).

```js
const arr = [10, 20, 30, 40];
arr.at(0); // 10
arr.at(-1); // 40
```

### 1.5 `String.prototype.replaceAll()`

Replaces **all** occurrences (no regex flags needed).

```js
"foo bar foo".replaceAll("foo", "baz"); // 'baz bar baz'
```

### 1.6 Logical Assignment Operators

| Operator | Meaning               | Example                      |
| -------- | --------------------- | ---------------------------- | ------------------- | ------ | --- | ----- |
| `        |                       | =`                           | Assign if **falsy** | `obj.x |     | = 1;` |
| `&&=`    | Assign if **truthy**  | `settings.enabled &&= true;` |
| `??=`    | Assign if **nullish** | `config.port ??= 8080;`      |

```js
let retries = 0;
retries ||= 3; // 3 because 0 is falsy
```

### 1.7 `Promise.any()`

Resolves with _first fulfilled_ promise, otherwise rejects with `AggregateError`.

```js
Promise.any([fetch("/a"), fetch("/b"), fetch("/c")])
	.then(console.log)
	.catch(console.error);
```

### 1.8 Generator Functions

Paused-resumable functions that return iterators – great for custom iteration, lazy evaluation, or async flows.

```js
function* idGen() {
	let id = 1;
	while (true) yield id++;
}
const gen = idGen();
console.log(gen.next().value); // 1
```

---

## 2. Debouncing & Throttling

### 2.1 Debouncing

Execute a function **after** a pause in events – great for search boxes.

```js
const debounce = (fn, delay = 300) => {
	let id;
	return (...args) => {
		clearTimeout(id);
		id = setTimeout(() => fn(...args), delay);
	};
};

searchInput.addEventListener(
	"input",
	debounce((e) => fetchResults(e.target.value), 500)
);
```

### 2.2 Throttling

Guarantee a function runs at most **once per interval** – ideal for scroll/resize.

```js
const throttle = (fn, limit = 200) => {
	let inThrottle;
	return (...args) => {
		if (!inThrottle) {
			fn(...args);
			inThrottle = true;
			setTimeout(() => (inThrottle = false), limit);
		}
	};
};

window.addEventListener("scroll", throttle(handleScroll, 250));
```

Comparison:
| Pattern | When to Use | Behaviour |
|---------|-------------|-----------|
| Debounce | User stops typing/resizing | Executes _after_ events stop |
| Throttle | Continuous events (scroll) | Executes at regular intervals |

---

## 3. Best-Practice Takeaways

1. **Use modern syntax** (optional chaining, nullish coalescing) for safer code.
2. Prefer **block-scoped** `let`/`const`; avoid global `var` pollution.
3. Embrace **immutability & pure functions**; leverage closures for private state.
4. Optimize event handlers with **debounce** or **throttle** to improve performance.
5. Combine **logical assignment operators** with optional chaining for concise defaults.

```js
config.apiUrl ??= "https://api.default.dev";
user.settings ||= {};
```

_Stay curious – JavaScript evolves yearly. Master these patterns to write cleaner, safer, and more performant code._
