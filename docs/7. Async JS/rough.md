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
Job queue has higher priority than callback queue. So first the job queue will be executed, then callback queue will be executed

// Parallel, Sequentioal, Race

parallel -> multiple promises run all at the same time
sequential -> call the promises in sequence, one after the other
race -> whichever promise fulfill first, execute it and ignore the rest

```js
const promisify = (item, delay) =>
	new Promise((resolve) => setTimeout(() => resolve(item), delay));

const a = () => promisify("a", 100);
const b = () => promisify("b", 5000);
const c = () => promisify("c", 3000);

async function parallel() {
	const promises = [a(), b(), c()];
	const [output1, output2, output3] = await Promise.all(promises);
	return `prallel is done: ${output1} ${output2} ${output3}`;
}

async function race() {
	const promises = [a(), b(), c()];
	const output1 = await Promise.race(promises);
	return `race is done: ${output1}`;
}

async function sequence() {
	const output1 = await a();
	const output2 = await b();
	const output3 = await c();
	return `sequence is done ${output1} ${output2} ${output3}`;
}

sequence().then(console.log);
parallel().then(console.log);
race().then(console.log);
```

// New feature - allSettled()
The Promise.allSettled() static method takes an iterable of promises as input and returns a single Promise. This returned promise fulfills when all of the input's promises settle (including when an empty iterable is passed), with an array of objects that describe the outcome of each promise.

```js
const promise1 = Promise.resolve(3);
const promise2 = new Promise((resolve, reject) =>
	setTimeout(reject, 100, "foo")
);
const promises = [promise1, promise2];

Promise.allSettled(promises).then((results) =>
	results.forEach((result) => console.log(result.status))
);

// Expected output:
// "fulfilled"
// "rejected"
```

// Threads, Concurrency and Parallelism

As we have learnt earlier, JS is a single threaded language. When we hit a new tab in chrome or someother browser, we only get one thread to work on.
Then the question is, how webAPI works and how this asyncronous behaviour is achieved

The thing is, that when any website is loaded, the JS of that website is on main thread that executes it line by line. Along with that browsers also give web workers.

The v8 JS enigne is limited to one thread only, but the browsers can pass some operations required to something like LIBUV which is written in C++

If we want, we can also create a worker to do the heavy operations separately from main thread in the background

const worker = new Worker("fileName.js)

Perplexity:- Add some more information about this topic.

some ref:- [more on Multithreading](https://www.internalpointers.com/post/gentle-introduction-multithreading)
[Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)
[Nodejs and workers](https://www.freecodecamp.org/news/scaling-node-js-applications-8492bd8afadc/)
