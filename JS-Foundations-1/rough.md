// JS Enigne
The JS engine will compile the JS code written in english format to the computer langauage that is binary format.
There are many JS engines available. For ex:- V8, chakra, Spider Monkey
The JS code behind the scenes go through many processes to give the machine code.
Parser -> AST -> Interpreter -> Profiler -> Compiler -> Optimized Code

anyone can create their own JS engine. But it will create chaos in JS commmunity. So that's why ECMAScript was created

# Interpreter and Compilers

The interpreter will run the code line by line and the compiler will compile the whole code into machine code at first.

Some famous compilers are babel and TS

The interpreter is faster than compiler when it comes to the execution.
But interpreter becomes slow when it comes to doing repetetive thing more times. Ex running a loop. Here compilers work better as they will try to simplify the things

Writing Optimized code which compiler can understand should be the goal of a JS developer. They have to work in a way so that it is easy for Compiler to understand the code quickly and developers have to work for them not against them.

How we can write a optimized code?
Avoid these keywords -> eval()
arguments
for in
delete
with
hidden classes |
inline caching | These 2 concepts should be kept in mind for writing the optimized JS code.

// call stack and heap memory
Call stacks are actually used to store the functions call. It operates in first in last out mode.
And the heap will store the actual variables and data and objects and many more in the heap memory

// stack overflow
When too many functions calls are stacked in the call stack, then this stack overflow can happen and will eventually throw the error. Common scenarios are infinite times of calling functions using recursions
EG:- function inception() {
inception();
}
inception(); // This will throw error:- max call stack size exceeded

// Gargbage Collection
Although JS automatically handles the garbage collection by freeing the memory occupied by the variables or functions from the heap and call stack. But its the developer job also to make sure that garbage collection is happening properly
How does grabage collection works in JS :- It uses mark and sweep algo. It checks in heap memory which values or objects are being used by the program. If any of the object ref is not being used, JS will delete that from memory

// Memory Leaks
If the memory over the period of time keeps on increasing and is also being used in the program, then in this scenario memory leak will happen.
Eg:-
let array = []
for(let i = 5; i> 1; i++) {
array.push(i+1);
}

Common scenarios in which JS memory leaks can happen are:-

1. Global variables :- these variables are prone to memory leaks as they will added in the global window and will be there forever as scope of global window is invincible just like thanos

2. Event listeners:- Same for event listeners also as they will added in document. And when it's not required it should be removed from the document or else memory leaks can happen

3. SetIntervals:- The setIntervals should be cleared after some time specially if any object is being referenced inside the interval. If not cleared manually, then it will keep on running the callback fn

Ref this page to know more about memory leaks and how soundcloud solve the memory issue :- [SoundCloud Mem issue](https://developers.soundcloud.com/blog/garbage-collection-in-redux-applications)

// Single Threaded
JS is a single threaded languange. It can perform one task at one time and can only go ahead after completing the first task. It has only one call stack memory.

What is the problem with synchronous code? :- If there is a long running task, then other task have to wait for that task to be completed so that other tasks can be performed. So the webpage or the SW can feel like hanged. Eg:- Instagram if we like any post which has many likes already. Then on clicking the like button, there are functions and complex algo which are performed in the background or Backend. So if the JS waits for all those functions and algo to get completed and record your like, then other things like scrolling, commenting, veiwing stories wont work for that particular period of time. This behaviour can make the app look like hanged or stucked.
For solving this, we need asynchronous code and JS engine is a single threaded which means it will perform as synchronous only. This is where, JS runtime comes into the picture

// Javascript Runtime
As we know that JS in a single threaded and it has to wait for the functions to complete to execute the next functions or programs.
So the browsers comes with the web API. The web browsers work in the background while the synchronous JS code is running. The web API works in the background and comes back with the data which it gives back to JS engine.
The web API comes with many things like HTTP, listening to DOM events, delay execution, etc.
window object is the web API which browser provides
These web API's uses low level languages like C++, which helps the code to work asynchronously

console.log("1")
setTimeout(() => {
console.log("2");
},1000)
console.log("3")

O/P:- 1 3 2

The JS will execute it line by line. And when it sees the setTimeout fn, it handles that to the webAPI and the after executing it, the webAPI gives the data back to JS.

The callback queue and event loop also comes into the picture. When any webAPI fn is called, it is executed in the webAPI window, then it is sent to the callback queue. The event loop checks if the JS file has been fully executed and the call stack is empty. If the call stack is empty and full JS file has been read, then only the event loop will push the functions which are in the callback ques into the call stack.

That's how the JS performs asynchronously even though JS is single threaded language.

// NodeJS
Node JS is another JS runtime with more advanced capabilities. It basically uses the chrome V8 engine along with the LIBUV which is written in C++, which helps to achieve async code. Along with that they give the file system access also. This nodejs also helps in writing the server side code.
