// Functional Programming

Pure functions
-> no side effects outsied of the function
-> should always return the same value irrespective of the parameters

```js
const arr = [1, 2, 3];

const mutateArr = (array) => {
	const copyArr = [...array];
	copyArr.pop();
	return copyArr;
};

const mutliplyBy2Arr = (array) => {
	return array.map((val) => {
		return val * 2;
	});
};
```

Can you guess is this a pure function or not?

```js
const a = () => {
	console.log("hi");
};
```

Yes it is as it is logging in the window and affecting the outside world, thus creating a side effect

Can every function in JS be a pure function? No, not everything in JavaScript—nor in most programming languages—can be purely functional or made into pure functions.

// Idempotent
A function which is expected to do what it is made to do. No matter what input we give to the function, it will always do what it has to do
Idempotent (or idempotence) in programming means that a function or operation can be applied multiple times and the result will be the same as if it were applied just once. Calling the function repeatedly with the same input will not change the outcome after the initial application.

Eg:-

```js
const a = (num) => {
	return Math.random();
};
a(5);
a(8);
a(9);

const b = (num) => {
	console.log(num);
};

a(5);
a(5);
a(5);
a(5);
```

or eg is deleting a user from a database.

// Imperative vs declarative
Hey Perplexity! Please fill up this topic for me in brief

// Immutability

```js
const obj = { name: "Andrie" };

const clone = (obj) => {
	return { ...obj };
};

const changeName = (obj) => {
	const newObj = clone(obj);
	newObj.name = "Nana";
	return newObj;
};

console.log(changeName(obj)); //Nana
console.log(obj.name); // Andrie
```

Even though the name in the obj is changed, but the original obj remains same. The idea is to not change the original data.

After seeing this, wonders if this will create a memory isssue, as we are cloning objects and in big projects, objects can be big. Yes and no also.

For this question, the answer is structural sharing.
Use immer

// Currying

```js
const multiply = (a, b) => a * b;
multiply(5, 3);
const curriedMultiply = (a) => (b) => a * b;
curriedMultiply(5)(3);

const multiplyBy5 = curriedMultiply(5);
multiplyBy5(3);

const multiplyBy10 = curriedMultiply(10);
multiplyBy10(2);
```

// Partial Application

```js
const multiply = (a, b, c) => {
	return a * b * c;
};

const partialMultiplyBy5 = multiply.bind(null, 5);
partialMultiplyBy5(2, 5); //50
```

// Memoization ~~~ Caching

```js
const memoization = () => {
	let cache = {};

	return (n) => {
		if (n in cache) {
			return cache[n];
		} else {
			console.log("Heavy operation simulation");
			cache[n] = n + 80;
			return cache[n];
		}
	};
};

const memoized = memoization();
memoized(5); // Heavy operation simulation 85
memoized(5); // 85
```

// Compose and Pipe
Hey Perplexity, Explain this topic and use the example below

```js
const compose = (f, g) => (data) => f(g(data));
const pipe = (f, g) => (data) => g(f(data));
const multiplyBy3 = (num) => 3 * num;
const makePositive = (num) => Math.abs(num);
const multiplyBy3AndAbsolute = compose(multiplyBy3, makePositive);
multiplyBy3AndAbsolute(-50);
```
