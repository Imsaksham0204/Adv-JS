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
