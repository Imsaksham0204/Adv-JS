// Types in JavaScript

There are multiple types in JS

1. String
2. Numbers
3. Boolean
4. undefined
5. null
6. Symbol // its newly introduced in ES6
7. Objects

Ohh Wait! Where is arrays or functions and why object is being printed when we do typeof null.

For the null type, it was a issue in JS when it was created and now we have to work with that. It is the wierd part of JS. It was not fixed because, lot of legacy code will break, as the null was typeof object.

And for the arrays and functions, they are of type object.
Even when we do typeof function(){}, it will give us 'function', but it is a object. And arrays also type of object. \* attach image here\

The types can be divided into 2 categories:-

1. Primitive - which holds a single value like strings, number, boolean, symbol
2. Non-premitive - Like objects which include functions, objects

In JS, we also have something called built-in objects. Take a ref here:- [Built-in Objects of JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)

If we see the webpage above, we see that built-in objects also have primitive data types also like Strings, Boolean, Numbers. But why? We just learned that these are not objects.

true.toString() // "true"

the above is a boolean, then how come it can have toString() method in it?
It because, JS does something like this in background
Boolean(true).toString();

So yes, in JS most of it is Object but not object also. JS is like how you want, you use like that

// Pass by value and pass by reference

const a = 5;
const b = a;

a = 6;

console.log(a); //6
console.log(b); //5

THe above example is for pass by value, where value will be assigned directly to the variables.

Pass by reference works for type objects.

const obj1 = {
a:'a',
b:'b'
}

const obj2 = obj1;

obj1.a = 5;

console.log(obj1); // {a:5,b:"b"}
console.log(obj2); // {a:5,b:"b"}

We changed only for obj1, but obj2 also changed as obj2 is taking ref from obj1.

To avoid this we should do a copy of the object.

const obj2 = {...obj1};

What if the obj1 is like this, and we do the copy of obj1 in obj2 using (...)spread only.

const obj1 = {
a: 'a',
b: {
deep: "Hi try to deep copy me"
}
};

const obj2 = {...obj1};

obj1.b.deep = "HA HA HA! I fooled you. You did shallow copy above"

console.log(obj1.b); // { deep: "HA HA HA! I fooled you. You did shallow copy above"}
console.log(obj2.b); // { deep: "HA HA HA! I fooled you. You did shallow copy above"}

What happened is, while copying the obj1, it did shallow copy. To do a deep copy, do like this

const obj2 = JSON.parse(JSON.stringify(obj1));

Take a quick look at this stackoverflow article https://stackoverflow.com/questions/1068834/object-comparison-in-javascript. It shows how to compare 2 objects. Its quite interesting

// Window: structuredClone() method

The structuredClone() method of the Window interface creates a deep clone of a given value using the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm).

[MDN link](https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone)
