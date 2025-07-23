// Execution context
It is the environment in which JS is executed. Initially the global execution context is initiated, which tells hey this is the whole JS file, read it and start executing the functions.

function printMyName() {
return "Saksham Gupta";
}

function getMyName() {
return printMyName();
}

function sayMyName() {
return getMyName();
}

sayMyName();

Global Execution context(); -> sayMyName() -> getMyName() -> printMyName()

Above is the order of context execution. And simultaneously they will be added to call stack.

The global execution context hits first and it gives two things. One global object and second is this object.

// Lexical Env
It is the local environment of the function call. Means that each function has its own world. The first very lexical scope in the JS is global lexical scope

// Hoisting
Previously we learned about the global context execution giving two things global object and this object. Along with that, it also does the hoisting part.

Hoisting is nothing but move all the variables and functions declaration to top of the JS file. The varibales are hoisted on the top and are assigned with undefined. But the functions are fully hoisted means functions are as it is what user have defined.

Eg:-
console.log("1-----");
console.log(teddy) // undefined
sing() // la la la la
var teddy = "bear"
function sing() {
return "la la la la"
}

Above the teddy is printed as undefined bcoz of hoisting and sing fun executes as the function is also hoisted.

PS:- Only variable declaration with var will be hoisted, not with let or const.
In hoisting, the code is not moved to the top. Instead, one pass is made throught the whole JS code, and it assigns the variables in the call stack to reserve the memory.

Here is one complex example of hoisting

```js
var favouriteFood = "Grapes";

var foodThoughts = function () {
  console.log("Orginal favourite food is: " + favouriteFood);
  var favouriteFood = "apple";
  console.log("New favourite food is: " + favouriteFood);
};

foodThoughts();
```

O/P :-
Orginal favourite food is: undefined
New favourite food is: apple

Why it behaved like this? :- Bcoz, inside the function, again the favouriteFood is hoisted to that function execution context. As we learned previously, each function will have its own execution context, So this function will hoist the favouriteFood variable with undefined. After that it will assign the value to apple

# Functions and arguments

```js
// Function Expression
var canada = () => { // This function is defined at run time bcoz of hoisting but it will be undefined
  console.log("Cold");
};

// Function Declaration
function india() { // This function will be initialized at parse time and will be hoisted also
  console.log("warm");
}

// Function invocation/Call/Execution
canada();
india();
```
During the function invocation, it will create a execution context for each function. In that context, we will be provided with this keyword and arguments keyword.

```js
function marry(person1, person2) {
    console.log(arguments);
    console.log(`${person1} marries to ${person2}`);
}

marry("Tim","Teena");
```
{0: Tim, 1: Teena} // here the arguments will be provided in object form.
Tim marries to Teena

As we learnt in the previous section, it is not advisable for JS developers to use the arguments keyword directly as it is not optimized in JS engine.

The better way to use the arguments is like this:-

```js
function marry(...args) {
    console.log(args);
    console.log(`${args[0]} marries to ${args[1]}`);
}
marry("Tim","Teena");
```

["Tim", "Teena"]

OR 

```js
function marry(person1, person2) {
    console.log(Array.fron(arguments));
    console.log(`${person1} marries to ${person2}`);
}
marry("Tim","Teena");
```

In modern JS, avoid using the arguments keyword, bcoz it had some complications with previous versions of JS.

// Variable environment
each function execution context will also have variable enviroment. Basically each variable will have the lexical scope

// Scope chain
Each function execution context is also linked to the global execution context and the parent context also.
\**will attach a image here*\
Also if a function is defined in global scope, then that function will be available in the window object. if we expand that function object in window, we will see a [[scope]] object which will be of type global.
\**will attach a image here*\


// use strict

Consider this example:- 

function sayHello() {
    message = "Hello How are you!"
    console.log(message);
}

Even thought the message variable is not defined any where, it will print the message. This is because, message will be automatically defined in the global scope. This is wierd behaviour of JS. To avoid these wierd issues, use strict is used. On top of the JS file, give 'use strict' and it will avoid these issues.

```js
'use strict'
function sayHello() {
    message = "Hello How are you!"
    console.log(message); // error will come as message has to be declared first
}

```


