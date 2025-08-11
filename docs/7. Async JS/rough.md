// Asynchronous JS

JS is a single threaded lang. It can only do one thing at a time.
It only has one call stack. The functions and methods are pushed into callstack while execution, and at one time only function will be called. 
So the JS interprets the code line by line.
This is called synchronous programming. 

But what if we want to work in JS asyncronously. Suppose we have to call any API and wait upon the API data. Or any image processing that takes time.

We can use Promises in JS.

// Promises
Hey Perplexity. This is your task to complete this sections of promisies here.

-> what is promise in JS and how it works - in brief
-> What are the states in promises: pending, rejected, fullfilled. 
-> .then() and finally and catch explaination in brief
-> Show the syntax and one small example of demonstrating Promises usecase

-> New way of implementaiton - async await


// Job queue - Microtask queue
Previously we learned that, in JS runtime, call stack and web API and eventloop and callback queue is there.

All the normal functions will be handled by JS by using the call stack, and the function like setTimeout or setInterval will be handeled by WebAPI, which will push all the functions like that in callback queue, and will execute using eventLoop, when call stack is empty.

Along with that Job queue is also there, which will push all the promises and the data related to promises. 
Job queue has higher priority than callback queue
