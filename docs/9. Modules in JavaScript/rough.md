// Modules in JS

In JavaScript, a module is just a file that contains code (variables, functions, classes, etc.) that can be exported and reused in other files using import/export.

Why it’s needed:
Encapsulation: Keeps related code together in one file.
Reusability: You can write a function once and use it anywhere by importing it.
Maintainability: Makes large codebases easier to manage by splitting into smaller files.
Avoids pollution: Prevents too many variables/functions in the global scope.

eg:-

```js
// math.js (module)
export function add(a, b) {
	return a + b;
}

// app.js
import { add } from "./math.js";
console.log(add(2, 3)); // 5
```

// Module pattern

Before ES6, module pattern was used to achieve the module functionality in JS
The Module Pattern in JavaScript is a design pattern that lets you create private and public members inside an object, mainly using closures.

It’s used to organize code, avoid polluting the global scope, and control access to variables/methods.

```js
const CounterModule = (function () {
	// private variable
	let count = 0;

	// public API
	return {
		increment: function () {
			count++;
			return count;
		},
		decrement: function () {
			count--;
			return count;
		},
		getCount: function () {
			return count;
		},
	};
})();

console.log(CounterModule.increment()); // 1
console.log(CounterModule.increment()); // 2
console.log(CounterModule.getCount()); // 2
console.log(CounterModule.count); // undefined (private)
```

Basically, the module pattern was a workaround before ES6 modules (import/export) existed, but it’s still a good concept for understanding encapsulation in JS.

// CommonJS, AMD, UMD

```js
const module1 = require("./module1");
const module2 = require("./module2");

module.exports = {
	...obj,
};
```

// ES6 Modules

The es6 module has been made as standard way to import and export the modules. React heavily uses this module.

-> import export
-> export function
-> export default
-> script type=module

give eg:-
