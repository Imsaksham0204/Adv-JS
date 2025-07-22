<!-- # Advanced JavaScript Engine & Runtime Notes

## Table of Contents

- [1. JavaScript Engine](#1-javascript-engine)
- [2. Interpreter and Compilers](#2-interpreter-and-compilers)
  - [2.1 How They Work](#21-how-they-work)
  - [2.2 Performance Comparison](#22-performance-comparison)
  - [2.3 Writing Optimized Code](#23-writing-optimized-code)
- [3. Call Stack and Heap Memory](#3-call-stack-and-heap-memory)
- [4. Stack Overflow](#4-stack-overflow)
- [5. Garbage Collection](#5-garbage-collection)
- [6. Memory Leaks](#6-memory-leaks)
  - [6.1 Common Scenarios](#61-common-scenarios)
  - [6.2 Prevention Tips](#62-prevention-tips)
- [7. Single Threaded Nature](#7-single-threaded-nature)
- [8. JavaScript Runtime](#8-javascript-runtime)

---

## 1. JavaScript Engine

The **JavaScript engine** is responsible for compiling JavaScript code (written in human-readable format) into machine code that computers can understand (binary format).

### Popular JavaScript Engines:

- **V8** - Google Chrome and Node.js
- **Chakra** - Microsoft Edge (legacy)
- **SpiderMonkey** - Mozilla Firefox
- **JavaScriptCore (Nitro)** - Apple Safari

### Code Processing Pipeline:

JavaScript code goes through several processes to become machine code:

```
Parser → AST → Interpreter → Profiler → Compiler → Optimized Code
```

1. **Parser**: Breaks code into tokens and creates syntax tree
2. **AST (Abstract Syntax Tree)**: Structured representation of code
3. **Interpreter**: Executes code line by line initially
4. **Profiler**: Monitors code execution patterns
5. **Compiler**: Optimizes frequently used code sections
6. **Optimized Code**: Final highly-efficient machine code

### ECMAScript Standard

While anyone can create their own JavaScript engine, this could create chaos in the JavaScript community. **ECMAScript** was created as a standardized specification that all JavaScript engines must follow to ensure consistency and compatibility.

---

## 2. Interpreter and Compilers

### 2.1 How They Work

**Interpreter:**

- Runs code **line by line**
- Translates and executes simultaneously
- Faster to start execution
- Examples: Python, early JavaScript engines

**Compiler:**

- Compiles the **entire code** into machine code first
- Then executes the compiled code
- Examples: Babel, TypeScript compiler

### 2.2 Performance Comparison

| Aspect               | Interpreter             | Compiler            |
| -------------------- | ----------------------- | ------------------- |
| **Startup Time**     | Fast                    | Slower              |
| **Execution Speed**  | Slower overall          | Faster overall      |
| **Repetitive Tasks** | Becomes slow with loops | Better optimization |
| **Memory Usage**     | Lower                   | Higher initially    |

**Key Point**: Interpreters become slow when performing repetitive tasks (like loops) because they re-interpret the same code multiple times. Compilers excel here by pre-optimizing these patterns.

### 2.3 Writing Optimized Code

Modern JavaScript engines use **JIT (Just-In-Time) compilation** - combining both approaches. To help the compiler optimize your code:

**Avoid these patterns:**

- `eval()` - Prevents optimization
- `arguments` object - Use rest parameters instead
- `for...in` loops - Use `for...of` or array methods
- `delete` operator - Restructure objects instead
- `with` statements - Deprecated and slow

**Optimize for:**

- **Hidden Classes**: Keep object structures consistent
- **Inline Caching**: Use consistent property access patterns

---

## 3. Call Stack and Heap Memory

JavaScript uses two main memory areas:

### Call Stack

- Stores **function calls** and **primitive variables**
- Operates in **LIFO (Last In, First Out)** mode
- Also called "Stack Memory"
- Fast access due to ordered structure

### Heap Memory

- Stores **objects**, **arrays**, and **functions**
- Stores actual variable data and complex data types
- Unordered memory allocation
- References to heap objects are stored in the call stack

**Memory Allocation Example:**

```javascript
let name = "John"; // Primitive - stored in call stack
let person = {
	// Object reference in call stack
	name: "John", // Object data in heap memory
	age: 25,
};
```

---

## 4. Stack Overflow

**Stack Overflow** occurs when too many function calls are stacked in the call stack, exceeding its memory limit.

### Common Cause: Infinite Recursion

```javascript
function inception() {
	inception(); // Calls itself infinitely
}
inception(); // Error: Maximum call stack size exceeded
```

### Prevention:

- Add base cases to recursive functions
- Use iteration instead of deep recursion when possible
- Be mindful of function call depth

---

## 5. Garbage Collection

JavaScript automatically handles memory management through **Garbage Collection**, but developers should understand how it works.

### Mark and Sweep Algorithm

JavaScript uses the **Mark and Sweep** algorithm:

1. **Mark Phase**:

   - Starts from "root" objects (global variables, current execution context)
   - Marks all reachable objects as "in use"
   - Follows all references to mark connected objects

2. **Sweep Phase**:
   - Scans through heap memory
   - Removes all unmarked (unreachable) objects
   - Frees up memory for new allocations

This process happens automatically, but developers can write code that helps garbage collection work efficiently.

### Garbage Collection Visualization

![Garbage Collection Visualization](mem-alocation.gif)

*The above GIF illustrates how the garbage collector marks reachable objects and sweeps away the unreachable ones, freeing up memory.*

---

## 6. Memory Leaks

**Memory Leaks** occur when memory usage increases over time and occupied memory is not properly released, even though it's no longer needed by the program.

### Example of Memory Leak:

```javascript
let array = [];
for (let i = 5; i > 1; i++) {
	// This loop runs infinitely!
	array.push(i + 1);
}
```

### 6.1 Common Scenarios

#### 1. Global Variables

Global variables stay in memory for the entire application lifetime.

```javascript
// Problem: Accidental global
function createLeak() {
	leakyVar = "I'm global!"; // Missing 'let/const/var'
}

// Solution: Always declare variables
function noLeak() {
	let properVar = "I'm scoped!";
}
```

#### 2. Event Listeners

Event listeners hold references to objects and prevent garbage collection.

```javascript
// Problem: Listener not removed
const button = document.getElementById("myBtn");
button.addEventListener("click", handleClick);

// Solution: Remove when no longer needed
button.removeEventListener("click", handleClick);
```

#### 3. SetInterval/SetTimeout

Timers keep running and hold references to objects.

```javascript
// Problem: Interval not cleared
const interval = setInterval(() => {
	console.log("Still running...");
}, 1000);

// Solution: Clear when done
clearInterval(interval);
```

### 6.2 Prevention Tips

- Always use `let`, `const`, or `var` to declare variables
- Remove event listeners when components unmount
- Clear intervals and timeouts when no longer needed
- Avoid keeping references to DOM elements after removal
- Be careful with closures that capture large objects

### Helpful Resource

For real-world examples, check out how [SoundCloud solved memory issues](https://developers.soundcloud.com/blog/garbage-collection-in-redux-applications).

---

## 7. Single Threaded Nature

JavaScript is a **single-threaded language** - it can only perform one task at a time and has only one call stack.

### Problems with Synchronous Code:

When there's a long-running task, other tasks must wait, making the application feel "frozen" or unresponsive.

**Real-world Example**: On Instagram, when you like a post with many existing likes, complex algorithms run in the background. If JavaScript waited for all these processes to complete before allowing other interactions (scrolling, commenting, viewing stories), the app would appear frozen.

**Solution**: Asynchronous programming with JavaScript Runtime environments.

---

## 8. JavaScript Runtime

Since JavaScript is single-threaded and synchronous by nature, browsers provide **Web APIs** to enable asynchronous behavior.

### How It Works:

- **JavaScript Engine**: Executes synchronous code
- **Web APIs**: Handle asynchronous operations in the background

  - HTTP requests
  - DOM event listeners
  - Timers (setTimeout, setInterval)
  - File operations

- **Web APIs** use lower-level languages like **C++** to work asynchronously
- The **window object** is part of the Web API provided by browsers

### Example of Asynchronous Execution:

```javascript
console.log("1"); // Executes immediately
setTimeout(() => {
	// Handed off to Web API
	console.log("2");
}, 1000);
console.log("3"); // Executes immediately

// Output: 1, 3, 2
```

**Execution Flow:**

1. `console.log("1")` - Executes synchronously
2. `setTimeout` - Passed to Web API, timer starts in background
3. `console.log("3")` - Executes synchronously
4. After 1 second, Web API sends callback back to JavaScript
5. `console.log("2")` - Executes when call stack is empty

This mechanism allows JavaScript to remain responsive while handling time-consuming operations in the background.

---

## Key Takeaways

1. **JavaScript engines** convert human-readable code to optimized machine code
2. **Modern engines** use JIT compilation combining interpreter and compiler benefits
3. **Memory management** involves call stack (primitives/references) and heap (objects)
4. **Garbage collection** automatically frees unused memory via mark and sweep
5. **Memory leaks** can be prevented by proper variable scoping and cleanup
6. **Asynchronous behavior** is achieved through Web APIs, not JavaScript itself
7. **Writing optimized code** helps the engine perform better optimizations

Understanding these concepts helps you write more efficient JavaScript and debug performance issues effectively. -->

# JavaScript Foundation - 1

## JavaScript Engine & Runtime

## Table of Contents

- [1. JavaScript Engine](#1-javascript-engine)
  - [1.1 Overview](#11-overview)
  - [1.2 Popular JavaScript Engines](#12-popular-javascript-engines)
  - [1.3 Code Compilation Pipeline](#13-code-compilation-pipeline)
  - [1.4 ECMAScript Standardization](#14-ecmascript-standardization)
- [2. Interpreters vs Compilers](#2-interpreters-vs-compilers)
  - [2.1 Core Differences](#21-core-differences)
  - [2.2 Performance Comparison](#22-performance-comparison)
  - [2.3 Popular JavaScript Tools](#23-popular-javascript-tools)
- [3. Code Optimization](#3-code-optimization)
  - [3.1 Developer's Goal](#31-developers-goal)
  - [3.2 Keywords & Patterns to Avoid](#32-keywords--patterns-to-avoid)
  - [3.3 Advanced Optimization Concepts](#33-advanced-optimization-concepts)
- [4. Memory Architecture](#4-memory-architecture)
  - [4.1 Call Stack](#41-call-stack)
  - [4.2 Heap Memory](#42-heap-memory)
  - [4.3 Stack Overflow](#43-stack-overflow)
- [5. Garbage Collection](#5-garbage-collection)
  - [5.1 Automatic Memory Management](#51-automatic-memory-management)
  - [5.2 Mark and Sweep Algorithm](#52-mark-and-sweep-algorithm)
  - [5.3 Developer Responsibilities](#53-developer-responsibilities)
- [6. Memory Leaks](#6-memory-leaks)
  - [6.1 Definition & Example](#61-definition--example)
  - [6.2 Common Memory Leak Scenarios](#62-common-memory-leak-scenarios)
  - [6.3 Prevention Strategies](#63-prevention-strategies)
- [7. Single-Threaded Nature](#7-single-threaded-nature)
  - [7.1 Understanding Single Threading](#71-understanding-single-threading)
  - [7.2 Problems with Synchronous Code](#72-problems-with-synchronous-code)
  - [7.3 Real-World Example](#73-real-world-example)
- [8. JavaScript Runtime Environment](#8-javascript-runtime-environment)
  - [8.1 Browser Runtime Architecture](#81-browser-runtime-architecture)
  - [8.2 Web APIs](#82-web-apis)
  - [8.3 Asynchronous Code Example](#83-asynchronous-code-example)
  - [8.4 Event Loop & Callback Queue](#84-event-loop--callback-queue)
- [9. Node.js Runtime](#9-nodejs-runtime)
  - [9.1 Architecture Overview](#91-architecture-overview)
  - [9.2 Server-Side Capabilities](#92-server-side-capabilities)

---

## 1. JavaScript Engine

### 1.1 Overview

The **JavaScript engine** is responsible for converting human-readable JavaScript code (written in English-like syntax) into computer language (binary format) that machines can execute directly.

### 1.2 Popular JavaScript Engines

| Engine             | Vendor/Environment      |
| ------------------ | ----------------------- |
| **V8**             | Google Chrome, Node.js  |
| **Chakra**         | Microsoft Edge (Legacy) |
| **SpiderMonkey**   | Mozilla Firefox         |
| **JavaScriptCore** | Apple Safari            |

### 1.3 Code Compilation Pipeline

JavaScript code goes through several stages before becoming executable machine code:

```
Parser → AST → Interpreter → Profiler → Compiler → Optimized Code
```

1. **Parser** - Analyzes syntax and structure
2. **AST (Abstract Syntax Tree)** - Creates a tree representation of code
3. **Interpreter** - Executes code line by line
4. **Profiler** - Identifies optimization opportunities
5. **Compiler** - Optimizes frequently used code paths
6. **Optimized Code** - Final machine code output

### 1.4 ECMAScript Standardization

While anyone can theoretically create their own JavaScript engine, this would create chaos in the JavaScript community. **ECMAScript** was created as a standardization specification to ensure consistency across all JavaScript engines and prevent fragmentation.

## 2. Interpreters vs Compilers

### 2.1 Core Differences

| Aspect               | Interpreter  | Compiler                        |
| -------------------- | ------------ | ------------------------------- |
| **Execution**        | Line by line | Entire code first, then execute |
| **Speed to start**   | Faster       | Slower (compilation overhead)   |
| **Repetitive tasks** | Slower       | Faster (optimized code)         |

### 2.2 Performance Comparison

- **Interpreter** is faster for initial execution but becomes slow with repetitive operations (like loops)
- **Compiler** takes time upfront but handles repetitive tasks more efficiently by creating optimized code
- Modern JavaScript engines use **JIT (Just-In-Time) compilation** combining both approaches

### 2.3 Popular JavaScript Tools

- **Babel** - Transpiler/compiler for modern JavaScript features
- **TypeScript (TS)** - Compiler that adds static typing to JavaScript

## 3. Code Optimization

### 3.1 Developer's Goal

Writing **optimized code** that compilers can understand easily should be every JavaScript developer's goal. Developers should work _with_ the compiler, not _against_ it, to achieve better performance.

### 3.2 Keywords & Patterns to Avoid

To write optimized code, avoid these performance-hindering keywords:

- `eval()`
- `arguments` object
- `for...in` loops (on large objects)
- `delete` operator on object properties
- `with` statements

### 3.3 Advanced Optimization Concepts

Two critical concepts for writing optimized JavaScript:

1. **Hidden Classes** - V8 creates internal class structures for objects with similar property layouts
2. **Inline Caching** - Optimizes property access by caching property locations

_Changing object shapes dynamically breaks these optimizations_

## 4. Memory Architecture

### 4.1 Call Stack

- **Purpose**: Stores function calls and execution contexts
- **Operation**: LIFO (Last In, First Out) / FILO (First In, Last Out)
- **Contents**: Function parameters, local variables, return addresses

### 4.2 Heap Memory

- **Purpose**: Stores actual data, variables, objects, and complex data structures
- **Structure**: Large memory pool for dynamic allocation
- **Management**: Handled by garbage collector

### 4.3 Stack Overflow

Occurs when too many function calls are stacked in the call stack, commonly caused by **infinite recursion**:

```javascript
function inception() {
	inception(); // Calls itself infinitely
}
inception(); // Error: Maximum call stack size exceeded
```

**Prevention**: Always ensure recursive functions have proper base cases and exit conditions.

## 5. Garbage Collection

### 5.1 Automatic Memory Management

JavaScript automatically handles garbage collection by freeing memory occupied by unused variables and functions from both heap and call stack.

### 5.2 Mark and Sweep Algorithm

JavaScript uses the **Mark and Sweep algorithm**:

1. **Mark Phase**: Identifies all objects reachable from root references
2. **Sweep Phase**: Removes all unmarked (unreachable) objects from memory
3. **Memory Reclamation**: Frees up space for new allocations

### Garbage Collection Visualization

![Garbage Collection Visualization](mem-alocation.gif)

_The above GIF illustrates how the garbage collector marks reachable objects and sweeps away the unreachable ones, freeing up memory._

### 5.3 Developer Responsibilities

While garbage collection is automatic, developers should:

- Nullify references when objects are no longer needed
- Avoid creating unnecessary global variables
- Remove event listeners when components are destroyed

## 6. Memory Leaks

### 6.1 Definition & Example

**Memory leaks** occur when memory usage increases over time and objects remain in memory despite no longer being needed by the program.

**Example of problematic code**:

```javascript
let array = [];
for (let i = 5; i > 1; i++) {
	// Note: This creates an infinite loop
	array.push(i + 1);
}
```

### 6.2 Common Memory Leak Scenarios

#### 1. Global Variables

- Added to global `window` object
- Persist for the entire application lifecycle
- "Scope of global window is invincible just like Thanos" 💀

#### 2. Event Listeners

- Attached to DOM elements
- Remain in memory even after elements are removed
- Should be manually removed when no longer needed

#### 3. SetInterval Functions

- Continue running indefinitely if not cleared
- Especially problematic when referencing objects inside the callback
- Always use `clearInterval()` when done

### 6.3 Prevention Strategies

- Use local scope whenever possible
- Remove event listeners: `element.removeEventListener()`
- Clear intervals: `clearInterval(intervalId)`
- Set object references to `null` when done

**Reference**: [SoundCloud Memory Management](https://developers.soundcloud.com/blog/garbage-collection-in-redux-applications)

## 7. Single-Threaded Nature

### 7.1 Understanding Single Threading

JavaScript is a **single-threaded language**:

- Can perform only **one task at a time**
- Must complete current task before moving to the next
- Has only **one call stack**

### 7.2 Problems with Synchronous Code

When there's a long-running task, all other operations must wait, causing the webpage/application to appear "hung" or unresponsive.

### 7.3 Real-World Example

**Instagram Like Button Scenario**:

- User clicks like on a post with many existing likes
- Complex algorithms run in the background to process the like
- If JavaScript waits for completion, other features (scrolling, commenting, viewing stories) become unresponsive
- This creates a poor user experience

**Solution**: Asynchronous programming using JavaScript runtime capabilities.

## 8. JavaScript Runtime Environment

### 8.1 Browser Runtime Architecture

Since JavaScript is single-threaded, browsers provide additional capabilities:

- **JavaScript Engine** (V8, SpiderMonkey, etc.)
- **Web APIs** (provided by browser)
- **Callback Queue**
- **Event Loop**

### 8.2 Web APIs

Browsers provide Web APIs that work in the background:

- **HTTP requests** (fetch, XMLHttpRequest)
- **DOM event listening**
- **Timer functions** (setTimeout, setInterval)
- **File system access** (limited)
- Built using low-level languages like **C++** for performance

**Note**: The `window` object is a Web API provided by the browser.

### 8.3 Asynchronous Code Example

```javascript
console.log("1");
setTimeout(() => {
	console.log("2");
}, 1000);
console.log("3");

// Output: 1 3 2
```

**Execution Flow**:

1. `console.log("1")` executes immediately
2. `setTimeout` is handed off to Web API
3. `console.log("3")` executes immediately
4. After 1000ms, Web API sends callback to callback queue
5. Event loop pushes callback to call stack when stack is empty
6. `console.log("2")` finally executes

### 8.4 Event Loop & Callback Queue

The **Event Loop** is the mechanism that enables asynchronous behavior:

1. **Web API execution** - Async functions run in Web API environment
2. **Callback Queue** - Completed async operations wait here
3. **Event Loop monitoring** - Continuously checks if call stack is empty
4. **Stack execution** - Only when call stack is completely empty does the event loop push callbacks from queue to stack

This system allows JavaScript to perform asynchronously despite being single-threaded.

## 9. Node.js Runtime

### 9.1 Architecture Overview

**Node.js** is an advanced JavaScript runtime environment featuring:

- **V8 Engine** (Chrome's JavaScript engine)
- **libuv** (C++ library for async I/O operations)
- Enhanced capabilities beyond browser limitations

### 9.2 Server-Side Capabilities

Node.js enables:

- **Server-side JavaScript execution**
- **File system access** (reading/writing files)
- **Network operations** (HTTP servers, TCP connections)
- **Operating system interactions**
- **Database connections**
- **Command-line tool development**

The combination of V8's performance and libuv's async capabilities makes Node.js suitable for building scalable server applications, APIs, and full-stack JavaScript development.

---

_These notes cover the fundamental concepts of JavaScript engine architecture, compilation processes, memory management, and runtime environments. Understanding these concepts is crucial for writing efficient JavaScript code and building performant applications._
