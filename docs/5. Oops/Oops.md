# Object-Oriented Programming (OOP) in JavaScript - Complete Guide

## Table of Contents
- [1. Classes and Objects](#1-classes-and-objects)
  - [1.1 What are Classes and Objects](#11-what-are-classes-and-objects)
  - [1.2 Methods and Properties](#12-methods-and-properties)
  - [1.3 Basic Class Example](#13-basic-class-example)
- [2. Constructors](#2-constructors)
  - [2.1 Understanding Constructors](#21-understanding-constructors)
  - [2.2 Constructor Parameters](#22-constructor-parameters)
  - [2.3 Constructor Best Practices](#23-constructor-best-practices)
- [3. Inheritance](#3-inheritance)
  - [3.1 Class Inheritance with extends](#31-class-inheritance-with-extends)
  - [3.2 The super Keyword](#32-the-super-keyword)
  - [3.3 Method Overriding](#33-method-overriding)
  - [3.4 Inheritance Examples](#34-inheritance-examples)
- [4. Static Properties and Methods](#4-static-properties-and-methods)
  - [4.1 Understanding Static Members](#41-understanding-static-members)
  - [4.2 Static Methods](#42-static-methods)
  - [4.3 Static Properties](#43-static-properties)
  - [4.4 Use Cases for Static Methods](#44-use-cases-for-static-methods)
- [5. Modern JavaScript OOP Features](#5-modern-javascript-oop-features)
  - [5.1 Getters and Setters](#51-getters-and-setters)
  - [5.2 Public and Private Fields](#52-public-and-private-fields)
  - [5.3 ES2022 Static Initialization Blocks](#53-es2022-static-initialization-blocks)
- [6. How Classes Work Under the Hood](#6-how-classes-work-under-the-hood)
  - [6.1 ES6 Classes as Syntactic Sugar](#61-es6-classes-as-syntactic-sugar)
  - [6.2 Constructor Functions vs Classes](#62-constructor-functions-vs-classes)
- [7. Prototype Chain in Classes](#7-prototype-chain-in-classes)
  - [7.1 Understanding the Prototype Chain](#71-understanding-the-prototype-chain)
  - [7.2 Prototypes in Functions](#72-prototypes-in-functions)
  - [7.3 __proto__ vs prototype](#73-__proto__-vs-prototype)
  - [7.4 Verification Examples](#74-verification-examples)

---

## 1. Classes and Objects

### 1.1 What are Classes and Objects

**Class**: A blueprint or template for creating objects. It defines the structure and behavior that the objects created from it will have.

**Object**: An instance of a class - a concrete entity created from the class blueprint that has actual values for the properties defined in the class.

**Real-world analogy**: 
- **Class** = House blueprint (defines rooms, layout, features)
- **Object** = Actual house built from that blueprint (has specific address, color, furniture)

### 1.2 Methods and Properties

**Properties**: Variables that store data/state in a class
- Example: `name`, `age`, `color`, `price`

**Methods**: Functions that define behavior/actions a class can perform
- Example: `walk()`, `speak()`, `calculateArea()`, `displayInfo()`

### 1.3 Basic Class Example

```js
class Person {
    // Properties (will be initialized in constructor)
    name;
    age;
    
    // Constructor
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    introduce() {
        return `Hi, I'm ${this.name} and I'm ${this.age} years old.`;
    }
    
    celebrateBirthday() {
        this.age++;
        return `Happy birthday! I'm now ${this.age} years old.`;
    }
}

// Creating objects (instances)
const john = new Person("John", 25);
const sarah = new Person("Sarah", 30);

console.log(john.introduce()); // "Hi, I'm John and I'm 25 years old."
console.log(sarah.celebrateBirthday()); // "Happy birthday! I'm now 31 years old."
```

## 2. Constructors

### 2.1 Understanding Constructors

The **constructor** is a special method that:
- Runs automatically when a new object is created
- Initializes the object's properties
- Sets up the initial state of the object
- Must be named exactly `constructor`

### 2.2 Constructor Parameters

```js
class Car {
    constructor(brand, model, year, color = "white") {
        this.brand = brand;
        this.model = model;
        this.year = year;
        this.color = color; // Default parameter
        this.isRunning = false; // Default property
    }
    
    startEngine() {
        this.isRunning = true;
        return `${this.brand} ${this.model} engine started!`;
    }
}

const myCar = new Car("Toyota", "Camry", 2023);
const friendsCar = new Car("Honda", "Civic", 2022, "blue");
```

### 2.3 Constructor Best Practices

- **Validate input parameters**
- **Set default values**
- **Initialize all necessary properties**
- **Keep constructor logic simple**

```js
class BankAccount {
    constructor(accountNumber, initialBalance = 0) {
        if (!accountNumber) {
            throw new Error("Account number is required");
        }
        if (initialBalance < 0) {
            throw new Error("Initial balance cannot be negative");
        }
        
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
        this.transactions = [];
    }
}
```

## 3. Inheritance

### 3.1 Class Inheritance with extends

**Inheritance** allows a class to inherit properties and methods from another class.

- **Parent/Super/Base Class**: The class being inherited from
- **Child/Sub/Derived Class**: The class that inherits

```js
// Parent class
class Animal {
    constructor(name, species) {
        this.name = name;
        this.species = species;
    }
    
    makeSound() {
        return `${this.name} makes a sound`;
    }
    
    sleep() {
        return `${this.name} is sleeping`;
    }
}

// Child class
class Dog extends Animal {
    constructor(name, breed) {
        super(name, "Canine"); // Call parent constructor
        this.breed = breed;
    }
    
    makeSound() {
        return `${this.name} barks: Woof! Woof!`;
    }
    
    wagTail() {
        return `${this.name} is wagging its tail`;
    }
}
```

### 3.2 The super Keyword

**`super`** is used to:
- **Call the parent constructor**: `super(parameters)`
- **Access parent methods**: `super.methodName()`
- **Access parent properties**: `super.propertyName`

```js
class Vehicle {
    constructor(make, model) {
        this.make = make;
        this.model = model;
        this.speed = 0;
    }
    
    accelerate(amount) {
        this.speed += amount;
        return `Accelerating to ${this.speed} mph`;
    }
}

class Motorcycle extends Vehicle {
    constructor(make, model, engineSize) {
        super(make, model); // Must call super() first
        this.engineSize = engineSize;
    }
    
    accelerate(amount) {
        // Call parent method and add custom behavior
        super.accelerate(amount * 1.5); // Motorcycles accelerate faster
        return `${this.make} ${this.model} is now going ${this.speed} mph`;
    }
    
    wheelie() {
        return `${this.make} ${this.model} is doing a wheelie!`;
    }
}

const bike = new Motorcycle("Yamaha", "R1", "1000cc");
console.log(bike.accelerate(20)); // "Yamaha R1 is now going 30 mph"
```

### 3.3 Method Overriding

Child classes can **override** parent methods to provide specialized behavior:

```js
class Shape {
    constructor(color) {
        this.color = color;
    }
    
    getArea() {
        return 0; // Default implementation
    }
    
    describe() {
        return `A ${this.color} shape with area ${this.getArea()}`;
    }
}

class Circle extends Shape {
    constructor(color, radius) {
        super(color);
        this.radius = radius;
    }
    
    // Override parent method
    getArea() {
        return Math.PI * this.radius * this.radius;
    }
}

class Rectangle extends Shape {
    constructor(color, width, height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    // Override parent method
    getArea() {
        return this.width * this.height;
    }
}

const circle = new Circle("red", 5);
const rectangle = new Rectangle("blue", 4, 6);

console.log(circle.describe()); // "A red shape with area 78.54"
console.log(rectangle.describe()); // "A blue shape with area 24"
```

### 3.4 Inheritance Examples

**Multi-level Inheritance:**

```js
class LivingThing {
    constructor(name) {
        this.name = name;
        this.isAlive = true;
    }
    
    breathe() {
        return `${this.name} is breathing`;
    }
}

class Animal extends LivingThing {
    constructor(name, species) {
        super(name);
        this.species = species;
    }
    
    move() {
        return `${this.name} is moving`;
    }
}

class Mammal extends Animal {
    constructor(name, furColor) {
        super(name, "Mammal");
        this.furColor = furColor;
        this.isWarmBlooded = true;
    }
    
    produceMilk() {
        return `${this.name} is producing milk`;
    }
}

const cat = new Mammal("Whiskers", "orange");
console.log(cat.breathe());     // Inherited from LivingThing
console.log(cat.move());        // Inherited from Animal
console.log(cat.produceMilk()); // Own method
```

## 4. Static Properties and Methods

### 4.1 Understanding Static Members

**Static members** belong to the class itself, not to instances:
- **Access**: `ClassName.staticMember` (not `instance.staticMember`)
- **Shared**: Same value across all instances
- **Memory**: Only one copy exists in memory

### 4.2 Static Methods

```js
class MathUtils {
    static PI = 3.14159;
    
    static getDiameter(radius) {
        return radius * 2;
    }
    
    static getCircumference(radius) {
        return 2 * this.PI * radius;
    }
    
    static getArea(radius) {
        return this.PI * radius * radius;
    }
}

// Usage - call on class, not instance
console.log(MathUtils.PI);                    // 3.14159
console.log(MathUtils.getDiameter(10));       // 20
console.log(MathUtils.getCircumference(10));  // 62.8318
console.log(MathUtils.getArea(10));           // 314.159

// ❌ This won't work:
// const math = new MathUtils();
// math.getArea(10); // Error!
```

### 4.3 Static Properties

```js
class User {
    static userCount = 0;
    static adminUsers = [];
    
    constructor(username, isAdmin = false) {
        this.username = username;
        this.isAdmin = isAdmin;
        
        // Increment static counter
        User.userCount++;
        
        if (isAdmin) {
            User.adminUsers.push(username);
        }
    }
    
    static getUserCount() {
        return `There are ${User.userCount} users registered`;
    }
    
    static getAdminList() {
        return User.adminUsers;
    }
}

const user1 = new User("alice");
const user2 = new User("bob", true);
const user3 = new User("charlie");

console.log(User.getUserCount());  // "There are 3 users registered"
console.log(User.getAdminList());  // ["bob"]
```

### 4.4 Use Cases for Static Methods

#### 1. **Factory Methods**
```js
class Article {
    constructor(title, content, date) {
        this.title = title;
        this.content = content;
        this.date = date;
    }
    
    // Factory method to create today's article
    static createToday(title, content) {
        return new Article(title, content, new Date());
    }
    
    // Factory method to create from JSON
    static fromJSON(jsonString) {
        const data = JSON.parse(jsonString);
        return new Article(data.title, data.content, new Date(data.date));
    }
}

const todayArticle = Article.createToday("Breaking News", "Something happened!");
```

#### 2. **Utility Functions**
```js
class DateHelper {
    static isWeekend(date) {
        const day = date.getDay();
        return day === 0 || day === 6; // Sunday or Saturday
    }
    
    static formatDate(date) {
        return date.toLocaleDateString('en-US');
    }
    
    static daysBetween(date1, date2) {
        const diffTime = Math.abs(date2 - date1);
        return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
    }
}
```

#### 3. **Comparison Functions**
```js
class Product {
    constructor(name, price, rating) {
        this.name = name;
        this.price = price;
        this.rating = rating;
    }
    
    static compareByPrice(product1, product2) {
        return product1.price - product2.price;
    }
    
    static compareByRating(product1, product2) {
        return product2.rating - product1.rating; // Higher rating first
    }
}

const products = [
    new Product("Laptop", 999, 4.5),
    new Product("Phone", 699, 4.2),
    new Product("Tablet", 399, 4.8)
];

products.sort(Product.compareByPrice);
console.log(products.map(p => p.name)); // ["Tablet", "Phone", "Laptop"]
```

## 5. Modern JavaScript OOP Features

### 5.1 Getters and Setters

**Getters** and **setters** provide controlled access to object properties:

```js
class Temperature {
    constructor(celsius = 0) {
        this._celsius = celsius;
    }
    
    // Getter - accessed like a property
    get celsius() {
        return this._celsius;
    }
    
    get fahrenheit() {
        return (this._celsius * 9/5) + 32;
    }
    
    get kelvin() {
        return this._celsius + 273.15;
    }
    
    // Setter - allows validation
    set celsius(value) {
        if (value < -273.15) {
            throw new Error("Temperature cannot be below absolute zero");
        }
        this._celsius = value;
    }
    
    set fahrenheit(value) {
        this.celsius = (value - 32) * 5/9;
    }
}

const temp = new Temperature(25);
console.log(temp.celsius);    // 25 (getter)
console.log(temp.fahrenheit); // 77 (calculated getter)

temp.fahrenheit = 100;        // setter
console.log(temp.celsius);    // 37.78 (converted automatically)
```

**Practical Example - User Class with Validation:**

```js
class User {
    constructor(email, age) {
        this.email = email;
        this.age = age;
    }
    
    get email() {
        return this._email;
    }
    
    set email(value) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(value)) {
            throw new Error("Invalid email format");
        }
        this._email = value;
    }
    
    get age() {
        return this._age;
    }
    
    set age(value) {
        if (value < 0 || value > 150) {
            throw new Error("Age must be between 0 and 150");
        }
        this._age = value;
    }
    
    get isAdult() {
        return this._age >= 18;
    }
}

const user = new User("john@example.com", 25);
console.log(user.isAdult); // true
// user.email = "invalid-email"; // Throws error
```

### 5.2 Public and Private Fields (ES2022)

**Public Fields**: Accessible from outside the class
**Private Fields**: Only accessible within the class (prefixed with `#`)

```js
class BankAccount {
    // Public fields
    accountHolder;
    accountType = "savings";
    
    // Private fields
    #balance = 0;
    #pin;
    #transactionHistory = [];
    
    constructor(accountHolder, initialBalance, pin) {
        this.accountHolder = accountHolder;
        this.#balance = initialBalance;
        this.#pin = pin;
    }
    
    // Public method
    getBalance(enteredPin) {
        if (this.#validatePin(enteredPin)) {
            return this.#balance;
        }
        throw new Error("Invalid PIN");
    }
    
    // Public method
    deposit(amount, pin) {
        if (this.#validatePin(pin)) {
            this.#balance += amount;
            this.#addTransaction("deposit", amount);
            return `Deposited $${amount}. New balance: $${this.#balance}`;
        }
        throw new Error("Invalid PIN");
    }
    
    // Private method
    #validatePin(enteredPin) {
        return enteredPin === this.#pin;
    }
    
    // Private method
    #addTransaction(type, amount) {
        this.#transactionHistory.push({
            type,
            amount,
            date: new Date(),
            balance: this.#balance
        });
    }
    
    // Getter for transaction history (controlled access)
    get transactionHistory() {
        return [...this.#transactionHistory]; // Return copy, not original
    }
}

const account = new BankAccount("John Doe", 1000, "1234");

console.log(account.accountHolder);        // "John Doe" (public)
console.log(account.getBalance("1234"));   // 1000 (authorized access)

// ❌ These won't work - private fields
// console.log(account.#balance);           // SyntaxError
// account.#validatePin("1234");            // SyntaxError
```

**Private Methods Example:**

```js
class Circle {
    // Private fields
    #radius;
    #color;
    
    constructor(radius, color) {
        this.#radius = radius;
        this.#color = color;
    }
    
    // Public getter
    get area() {
        return this.#calculateArea();
    }
    
    get circumference() {
        return this.#calculateCircumference();
    }
    
    // Private methods
    #calculateArea() {
        return Math.PI * this.#radius * this.#radius;
    }
    
    #calculateCircumference() {
        return 2 * Math.PI * this.#radius;
    }
    
    // Public method that uses private methods
    getInfo() {
        return {
            color: this.#color,
            radius: this.#radius,
            area: this.#calculateArea(),
            circumference: this.#calculateCircumference()
        };
    }
}

const circle = new Circle(5, "red");
console.log(circle.area);        // 78.54 (public access)
console.log(circle.getInfo());   // Full info object
// circle.#calculateArea();      // SyntaxError - private method
```

### 5.3 ES2022 Static Initialization Blocks

**Static initialization blocks** allow complex static setup:

```js
class DatabaseConnection {
    static connectionPool = [];
    static maxConnections = 10;
    static isInitialized = false;
    
    // Static initialization block
    static {
        console.log("Initializing database connection pool...");
        
        // Complex initialization logic
        for (let i = 0; i < this.maxConnections; i++) {
            this.connectionPool.push({
                id: i,
                isActive: false,
                createdAt: new Date()
            });
        }
        
        this.isInitialized = true;
        console.log(`Created ${this.maxConnections} database connections`);
    }
    
    static getConnection() {
        const available = this.connectionPool.find(conn => !conn.isActive);
        if (available) {
            available.isActive = true;
            return available;
        }
        throw new Error("No available connections");
    }
    
    static releaseConnection(connection) {
        connection.isActive = false;
    }
}

// Static block runs when class is first loaded
// Output: "Initializing database connection pool..."
//         "Created 10 database connections"

const conn = DatabaseConnection.getConnection();
console.log(conn.id); // 0
```

**Advanced Static Initialization Example:**

```js
class ConfigManager {
    static config = {};
    static environment;
    
    static {
        // Determine environment
        this.environment = process?.env?.NODE_ENV || 'development';
        
        // Load configuration based on environment
        try {
            if (this.environment === 'production') {
                this.config = {
                    apiUrl: 'https://api.production.com',
                    dbUrl: 'postgres://prod-db:5432/myapp',
                    logLevel: 'error'
                };
            } else {
                this.config = {
                    apiUrl: 'http://localhost:3000',
                    dbUrl: 'postgres://localhost:5432/myapp_dev',
                    logLevel: 'debug'
                };
            }
            
            console.log(`Configuration loaded for ${this.environment} environment`);
        } catch (error) {
            console.error('Failed to load configuration:', error);
            throw error;
        }
    }
    
    static get(key) {
        return this.config[key];
    }
}
```

## 6. How Classes Work Under the Hood

### 6.1 ES6 Classes as Syntactic Sugar

ES6 classes are **syntactic sugar** over JavaScript's prototype-based inheritance:

**ES6 Class:**
```js
class Person {
    constructor(name) {
        this.name = name;
    }
    
    sayHello() {
        console.log(`Hi, I'm ${this.name}`);
    }
}

const p1 = new Person('Alice');
p1.sayHello(); // Hi, I'm Alice
```

**Equivalent ES5 Constructor Function:**
```js
function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`Hi, I'm ${this.name}`);
};

const p1 = new Person('Alice');
p1.sayHello(); // Hi, I'm Alice
```

### 6.2 Constructor Functions vs Classes

| Aspect | Constructor Function | ES6 Class |
|--------|---------------------|-----------|
| **Syntax** | `function Person() {}` | `class Person {}` |
| **Hoisting** | Fully hoisted | Not hoisted |
| **Strict Mode** | Optional | Always strict |
| **Constructor Call** | Can be called without `new` | Must use `new` |
| **Methods** | Added to prototype manually | Defined in class body |

**Constructor Function Example:**
```js
function Car(make, model) {
    this.make = make;
    this.model = model;
}

Car.prototype.start = function() {
    return `${this.make} ${this.model} is starting`;
};

Car.prototype.stop = function() {
    return `${this.make} ${this.model} is stopping`;
};
```

**Equivalent Class:**
```js
class Car {
    constructor(make, model) {
        this.make = make;
        this.model = model;
    }
    
    start() {
        return `${this.make} ${this.model} is starting`;
    }
    
    stop() {
        return `${this.make} ${this.model} is stopping`;
    }
}
```

## 7. Prototype Chain in Classes

### 7.1 Understanding the Prototype Chain

Every object created from a class has a **prototype chain**:

```
Instance (p1)
    |
    | __proto__
    ↓
Person.prototype
    |
    | __proto__
    ↓
Object.prototype
    |
    | __proto__
    ↓
null
```

### 7.2 Prototypes in Functions

**Key Concepts:**
- Every function has a `prototype` property
- When used as constructor (with `new`), the `prototype` becomes the `__proto__` of created objects
- `prototype` is used to define shared methods and properties

```js
function Person(name) {
    this.name = name;
}

// Adding method to prototype
Person.prototype.sayHello = function() {
    console.log(`Hello, my name is ${this.name}`);
};

// Adding property to prototype
Person.prototype.species = "Homo sapiens";

const p1 = new Person('Alice');
p1.sayHello(); // "Hello, my name is Alice"
console.log(p1.species); // "Homo sapiens" (inherited from prototype)
```

### 7.3 __proto__ vs prototype

| Property | Where it exists | What it points to |
|----------|----------------|-------------------|
| `prototype` | On constructor functions | Object that becomes `__proto__` for instances |
| `__proto__` | On all objects | The object's prototype (parent in prototype chain) |

```js
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function() {
    return `${this.name} makes a sound`;
};

const dog = new Animal('Rex');

// Understanding the relationships:
console.log(dog.__proto__ === Animal.prototype);        // true
console.log(Animal.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null);      // true
```

### 7.4 Verification Examples

```js
class Person {
    constructor(name) {
        this.name = name;
    }
    
    sayHello() {
        console.log(`Hi, I'm ${this.name}`);
    }
}

const p1 = new Person('Alice');

// ✅ Prototype chain verification
console.log(p1.__proto__ === Person.prototype);            // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null);          // true

// ✅ Method inheritance verification
console.log(p1.hasOwnProperty('name'));        // true (own property)
console.log(p1.hasOwnProperty('sayHello'));    // false (inherited method)
console.log('sayHello' in p1);                 // true (available via prototype)

// ✅ Prototype chain lookup
console.log(p1.toString);                      // [Function: toString] (from Object.prototype)
console.log(p1.valueOf);                       // [Function: valueOf] (from Object.prototype)
```

**Advanced Prototype Chain Example:**

```js
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    breathe() {
        return `${this.name} is breathing`;
    }
}

class Mammal extends Animal {
    constructor(name, furColor) {
        super(name);
        this.furColor = furColor;
    }
    
    produceMilk() {
        return `${this.name} is producing milk`;
    }
}

class Dog extends Mammal {
    constructor(name, breed) {
        super(name, "brown");
        this.breed = breed;
    }
    
    bark() {
        return `${this.name} is barking: Woof!`;
    }
}

const myDog = new Dog('Buddy', 'Golden Retriever');

// Prototype chain: myDog -> Dog.prototype -> Mammal.prototype -> Animal.prototype -> Object.prototype -> null

console.log(myDog.__proto__ === Dog.prototype);                    // true
console.log(Dog.prototype.__proto__ === Mammal.prototype);         // true
console.log(Mammal.prototype.__proto__ === Animal.prototype);      // true
console.log(Animal.prototype.__proto__ === Object.prototype);      // true

// Method access through prototype chain
console.log(myDog.bark());        // Own method
console.log(myDog.produceMilk()); // From Mammal
console.log(myDog.breathe());     // From Animal
console.log(myDog.toString());    // From Object
```

---

## Key Takeaways

### Classes and Objects
- **Classes** are blueprints, **objects** are instances
- **Properties** store data, **methods** define behavior
- **Constructor** initializes object state

### Inheritance
- Use `extends` to create inheritance relationships
- Use `super()` to call parent constructor and methods
- Child classes can override parent methods

### Static Members
- Belong to the class, not instances
- Accessed via class name: `ClassName.staticMember`
- Useful for utility functions, factory methods, and shared data

### Modern Features
- **Getters/Setters** provide controlled property access
- **Private fields** (`#field`) ensure true encapsulation
- **Static initialization blocks** enable complex static setup

### Under the Hood
- ES6 classes are syntactic sugar over prototype-based inheritance
- Every object has a prototype chain leading to `Object.prototype`
- Understanding prototypes helps debug and optimize JavaScript code

*Object-Oriented Programming in JavaScript provides powerful tools for organizing code, promoting reusability, and building maintainable applications. Master these concepts to write cleaner, more structured JavaScript code.*
