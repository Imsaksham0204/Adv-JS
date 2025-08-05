// OOPS in JS

1. Explain class and object with example
   1.1 What are methods and properties
2. Constructors
3. Inheritance
   3.1 Super
   Show examples

4. Static properties and static methods
   4.1 Use cases of static methods

5. New features in JS of oops
   5.1 Getters and setters
   5.2 Public and private
   5.3 ES2022 Static Intialization Blocks

6. Explain how class works in ES6 under the hood
   Eg:- class Person {
   constructor(name) {
   this.name = name;
   }

sayHello() {
console.log(`Hi, I'm ${this.name}`);
}
}

const p1 = new Person('Alice');
p1.sayHello(); // Hi, I'm Alice

What’s happening internally?
function Person(name) {
this.name = name;
}
Person.prototype.sayHello = function () {
console.log(`Hi, I'm ${this.name}`);
};

So, class in JavaScript is just syntactic sugar for constructor functions + prototypes.

7. Prototype Chain
p1
 |
 | __proto__
 v
Person.prototype
 |
 | __proto__
 v
Object.prototype

✅ Verifications:
console.log(p1.__proto__ === Person.prototype);            // true
console.log(Person.prototype.__proto__ === Object.prototype); // true

7.1 Prototypes in JavaScript:
In JavaScript, functions are special types of objects that can be used as constructors (with the new keyword).

Every function in JavaScript automatically has a property called prototype.

The prototype is an object that will be assigned as the __proto__ of any object created using that function as a constructor.

It's used to define shared methods and properties for all instances created from that constructor.

function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function () {
  console.log(`Hello, my name is ${this.name}`);
};

const p1 = new Person('Alice');
p1.sayHello(); // "Hello, my name is Alice"
Here, sayHello is available to all instances of Person via the prototype.

7.2. __proto__:
Every object in JavaScript (except the root object) has a __proto__ property.

This property points to the object’s prototype, enabling prototypal inheritance.

The __proto__ property creates what's known as the prototype chain — if a property/method is not found on an object, JavaScript looks up the chain via __proto__.

console.log(p1.__proto__ === Person.prototype); // true
Note:

__proto__ is now considered legacy, and it's recommended to use Object.getPrototypeOf(obj) instead.

The prototype is on the constructor function.

The __proto__ is on the object created by that constructor.


