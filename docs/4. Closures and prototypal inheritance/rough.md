The 2 pillars of JS - Closures and Prototypal inheritance

// Functions

Previously we learned that functions are objects in JS. When functions are called, one execution context is created and in that this and arguments keyword will be available along with a variable environment.

There are multiple ways to declare and invoke a function.

1. Directly declaring using the () => {} arrow notation
   const a = () => {
   return 'a';
   }
   a()
2. creating a method in one object.
   const obj = {
   b() {
   return 'b';
   }
   }
3. we can invoke the function using call or apply keyword

const c = () => {
return 'c';
}
c.call();

4. One more is to use a function constructor

const d = new Function('num','return num');
d(5);

// First class citizens
Functions are primarily refered as first class citizen in JS

// Higher Order functions

Simply takes function as argument and returns a function

```js
const multiplyBy = (num1) => {
	return (num2) => {
		return num1 * num2;
	};
};

const multiplyBy2 = multiplyBy(2);
const multiplyBy3 = multiplyBy(3);
const multiplyBy4 = multiplyBy(4);

console.log(multiplyBy2(10)); // 20
console.log(multiplyBy3(10)); //30
console.log(multiplyBy4(10)); //40
```

// Closures
A closure in JavaScript is a combination of a function and the lexical environment within which that function was declared. This means that an inner function, created inside an outer function, retains access to the variables and parameters of its outer function even after the outer function has finished executing.

Simple lang:- When a function is defined inside another function, it retains the varibales and parameters for the inside function, even if the outer function is executed and not there in call stack.

```js
const a = () => {
	const hour = 20;
	return function b() {
		const minutes = 45;
		return function c() {
			const seconds = 30;
			return `${hour}:${minutes}:${seconds}`;
		};
	};
};
a()()(); //20:45:30
```

If we see the example, first function a will be called and pushed to call stack and get executed and will be removed from call stack, while returning the function b. Same with function b and c will happen, and in last function c will return time.
THe questions is, when function a and b are called and removed from stack, then how come function c have the data for variables hour and minutes. Those should be collected by the garbage collecter and should not be there in memory, while those functions were removed from the stack.

Here comes the special feature of JS called closure, which gives a retains the memory in heap memory, until there references is there. In our case, the function c closes on those variables, so it was retained. When the functions are being removed from the call stack, the variables are retained memory if they have reference further, so that they can be referenced further to other clousured funcitons.

// Closure and memory effeciency
Closures actually help in memory effeciency.

```js
const heavyDuty = (ind) => {
	const bigArr = Array.from({ length: 1_000_000 }, (_, i) => i);
	return bigArr[ind];
};
heavyDuty(100);
heavyDuty(500);
heavyDuty(700);
// Here everytime, function will be called and bigArr will be created each time we call the function
const heavyDuty1 = () => {
	const bigArr = Array.from({ length: 1_000_000 }, (_, i) => i);
	return (ind) => bigArr[ind];
};
const getValAtInd = heavyDuty1();
getValAtInd(100);
getValAtInd()(100);
getValAtInd()(100);

// this is optimized, as the bigArr will be closured and reserved in the memory heap
```

one more example of closure where it can be usefule for calling the function only once

```js
let view = "";

const setView = () => {
	let called = 0;
	return () => {
		if (called >= 1) return;
		called++;
		view = "🗻";
		console.log("View is set");
	};
};

const setViewCall = setView();
setViewCall();
setViewCall();
setViewCall();
setViewCall();
console.log(view);
```

```js
const arr = [1, 2, 3, 4];

for (var i = 0; i < 4; i++) {
	(function (closureI) {
		setTimeout(() => {
			console.log(closureI);
		}, 1000);
	})(i);
}
```

// Prototypes
JS uses the prototypal inheritance. Remember we previously learned that JS uses the objects heavily. Even the arrays and functions are objects in JS.

const arr = [];
`arr.__proto__` // This will give all the methods which are predefined for arrays in JS. Eg concat, find, etc.

`arr.__proto__.__proto__` //This will refer the base object of JS.

similarly it will happen for functions also. If we go at last prototypal chain, it will refer to the base object of JS

Lets discuss one example:-

```js
let dragon = {
	name: "Tanya",
	fight: true,
	fire() {
		return 5;
	},
	sing() {
		return `${this.name} is singing`;
	},
};

let lizard = {
	name: "kiki",
	fire() {
		return 1;
	},
};

// Now we can use bind to call the sing property over lizard object.

dragon.sing.bind(lizard)(); // Kiki is singing

// But what if we change the sing method in dragon to something like this
dragon = {
	...dragon,
	sing() {
		if (this.fight) {
			return `${this.name} is singing`;
		}
	},
};

// Now if we again call that function over lizard object, it is returning undefined as fight is not a property in lizard
dragon.sing.bind(lizard)(); // undefined

// To solved this we can do something like this
lizard.__proto__ = dragon;

lizard.sing(); //Kiki is singing
lizard.fight(); //1
```

What will happen behind the scene is that lizard will create a prototype chain with dragon object.
when we do lizard.sing(), it will look for sing method in its own object, if not found, then it will go up the prototype chain, which is dragon object and there it will see that sing object is defined.
it will keep on going to the last prototypal chain until it reaches the base object and doesnot finds the required property or method.

Look at the image below for function prototypal chain.
![image](prototype-fn.png)

‼️Warning We should never use this `__proto__` key to modify or make a prototypal chain. As this can hinder the performance and natural working of the compiler

We can use Object.create function. It will also behave in the same way.

let lizard = Object.create(dragon);

See these exercises for practice

```js
// Exercise - extend the functionality of a built in object

// #1
// Date object => to have a new method .lastYear() which
// shows you last year 'YYYY' format.

Date.prototype.lastYear = function () {
	return this.getFullYear() - 1;
};
new Date("1900-10-10").lastYear();
//'1899'

// #Bonus
// Modify .map() to print'emoji' at the end of each iteration.
Array.prototype.map = function () {
	let arr = [];
	for (let i = 0; i < this.length; i++) {
		arr.push(this[i] + "🗺️");
	}
};
console.log([1, 2, 3].map());
```
