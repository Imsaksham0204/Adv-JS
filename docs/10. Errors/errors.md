# JavaScript Error Handling – Complete Mastery Guide

## Table of Contents

- [1. Error Fundamentals](#1-error-fundamentals)
  - [1.1 JavaScript's Built-in Error Constructor](#11-javascripts-built-in-error-constructor)
  - [1.2 Throwing Errors](#12-throwing-errors)
  - [1.3 Error Types in JavaScript](#13-error-types-in-javascript)
- [2. Try-Catch-Finally Blocks](#2-try-catch-finally-blocks)
  - [2.1 Basic Try-Catch](#21-basic-try-catch)
  - [2.2 The Finally Block](#22-the-finally-block)
  - [2.3 Nested Try-Catch](#23-nested-try-catch)
  - [2.4 Re-throwing Errors](#24-re-throwing-errors)
- [3. Asynchronous Error Handling](#3-asynchronous-error-handling)
  - [3.1 Promise Error Handling](#31-promise-error-handling)
  - [3.2 Async/Await Error Patterns](#32-asyncawait-error-patterns)
  - [3.3 Multiple Promise Error Handling](#33-multiple-promise-error-handling)
  - [3.4 Advanced Async Error Strategies](#34-advanced-async-error-strategies)
- [4. Custom Error Classes](#4-custom-error-classes)
  - [4.1 Extending the Error Class](#41-extending-the-error-class)
  - [4.2 Specialized Error Types](#42-specialized-error-types)
  - [4.3 Error Hierarchies](#43-error-hierarchies)
  - [4.4 Best Practices for Custom Errors](#44-best-practices-for-custom-errors)
- [5. Global Error Handling](#5-global-error-handling)
  - [5.1 Window.onerror](#51-windowonerror)
  - [5.2 Unhandled Promise Rejections](#52-unhandled-promise-rejections)
  - [5.3 Error Reporting Systems](#53-error-reporting-systems)
- [6. Error Handling Patterns](#6-error-handling-patterns)
  - [6.1 Retry Mechanisms](#61-retry-mechanisms)
  - [6.2 Circuit Breaker Pattern](#62-circuit-breaker-pattern)
  - [6.3 Graceful Degradation](#63-graceful-degradation)
  - [6.4 Error Recovery Strategies](#64-error-recovery-strategies)
- [7. Testing Error Scenarios](#7-testing-error-scenarios)
- [8. Performance & Best Practices](#8-performance--best-practices)

---

## 1. Error Fundamentals

### 1.1 JavaScript's Built-in Error Constructor

JavaScript provides a native **Error constructor** to create error objects:

```js
// Basic error creation
const basicError = new Error("Something went wrong!");
console.log(basicError.message); // "Something went wrong!"
console.log(basicError.name); // "Error"
console.log(basicError.stack); // Stack trace

// Error with additional context
const contextError = new Error("Database connection failed");
contextError.code = "DB_CONNECTION_ERROR";
contextError.statusCode = 500;
```

**Error Object Properties:**
| Property | Description | Example |
|----------|-------------|---------|
| `message` | Human-readable error description | "File not found" |
| `name` | Error type name | "TypeError" |
| `stack` | Stack trace (debugging info) | Call stack at error creation |
| Custom props | Additional context data | `statusCode`, `errorCode` |

### 1.2 Throwing Errors

**Throwing** interrupts normal execution and transfers control to error handlers:

```js
function validateAge(age) {
	if (typeof age !== "number") {
		throw new TypeError("Age must be a number");
	}

	if (age < 0) {
		throw new RangeError("Age cannot be negative");
	}

	if (age > 150) {
		throw new RangeError("Age seems unrealistic");
	}

	return age;
}

// Usage
try {
	validateAge("25"); // Throws TypeError
} catch (error) {
	console.log(`Validation failed: ${error.message}`);
}
```

### 1.3 Error Types in JavaScript

**Built-in Error Types:**

```js
// Generic Error
throw new Error("General error");

// Type-related errors
throw new TypeError("Expected string, got number");
throw new ReferenceError("Variable is not defined");

// Range/Value errors
throw new RangeError("Number out of range");
throw new SyntaxError("Invalid syntax");

// URI-related errors
throw new URIError("Invalid URI component");

// Evaluation errors (rare in modern JS)
throw new EvalError("eval() related error");
```

**Real-world Error Examples:**

```js
// API Response Handler
function handleApiResponse(response) {
	if (!response.ok) {
		switch (response.status) {
			case 400:
				throw new TypeError(`Bad Request: ${response.statusText}`);
			case 401:
				throw new Error("Unauthorized access");
			case 404:
				throw new ReferenceError("Resource not found");
			case 500:
				throw new Error("Internal server error");
			default:
				throw new Error(`HTTP Error: ${response.status}`);
		}
	}
	return response.json();
}
```

---

## 2. Try-Catch-Finally Blocks

### 2.1 Basic Try-Catch

**Try-catch** blocks provide controlled error handling:

```js
function parseJSON(jsonString) {
	try {
		const data = JSON.parse(jsonString);
		console.log("Parsing successful:", data);
		return data;
	} catch (error) {
		console.error("JSON parsing failed:", error.message);
		return null; // Fallback value
	}
}

// Multiple operations with specific error handling
function processUserData(rawData) {
	try {
		const userData = JSON.parse(rawData);

		// Validate required fields
		if (!userData.email) {
			throw new Error("Email is required");
		}

		// Process data
		const processedUser = {
			...userData,
			email: userData.email.toLowerCase(),
			createdAt: new Date().toISOString(),
		};

		return processedUser;
	} catch (error) {
		if (error instanceof SyntaxError) {
			console.error("Invalid JSON format");
		} else {
			console.error("User data processing failed:", error.message);
		}
		throw error; // Re-throw for caller to handle
	}
}
```

### 2.2 The Finally Block

**Finally** always executes, regardless of success or failure:

```js
function readFileData(filename) {
	let fileHandle = null;

	try {
		fileHandle = openFile(filename);
		const data = fileHandle.read();
		return processData(data);
	} catch (error) {
		console.error(`Failed to read ${filename}:`, error.message);
		return null;
	} finally {
		// Cleanup - always runs
		if (fileHandle) {
			fileHandle.close();
			console.log(`File ${filename} closed`);
		}
	}
}

// Database transaction example
async function updateUserProfile(userId, updates) {
	const transaction = await db.beginTransaction();

	try {
		const user = await db.users.findById(userId);
		if (!user) throw new Error("User not found");

		await db.users.update(userId, updates);
		await db.auditLog.create({ userId, action: "profile_update" });

		await transaction.commit();
		return { success: true };
	} catch (error) {
		await transaction.rollback();
		throw error;
	} finally {
		// Always release connection
		transaction.release();
	}
}
```

### 2.3 Nested Try-Catch

**Handle different error scenarios** at different levels:

```js
function complexDataProcessing(inputData) {
	try {
		// Outer try - handles overall operation
		console.log("Starting data processing...");

		const results = [];

		for (const item of inputData) {
			try {
				// Inner try - handles individual item errors
				const processed = processItem(item);
				results.push(processed);
			} catch (itemError) {
				console.warn(`Skipping invalid item:`, itemError.message);
				// Continue with next item instead of failing entirely
			}
		}

		if (results.length === 0) {
			throw new Error("No valid items could be processed");
		}

		return results;
	} catch (error) {
		console.error("Data processing failed:", error.message);
		return []; // Fallback empty array
	}
}
```

### 2.4 Re-throwing Errors

**Add context** before passing errors up the call stack:

```js
class UserService {
	async createUser(userData) {
		try {
			return await this.database.users.create(userData);
		} catch (error) {
			// Add context and re-throw
			const enhancedError = new Error(`User creation failed: ${error.message}`);
			enhancedError.originalError = error;
			enhancedError.userData = userData;
			enhancedError.timestamp = new Date().toISOString();
			throw enhancedError;
		}
	}
}

// Usage with context preservation
try {
	const userService = new UserService();
	await userService.createUser({ email: "invalid-email" });
} catch (error) {
	console.error("Operation failed:", error.message);
	console.error("Original cause:", error.originalError?.message);
	console.error("Failed at:", error.timestamp);
}
```

---

## 3. Asynchronous Error Handling

### 3.1 Promise Error Handling

**Promises** require special error handling patterns:

```js
// Basic Promise error handling
function fetchUserData(userId) {
	return fetch(`/api/users/${userId}`)
		.then((response) => {
			if (!response.ok) {
				throw new Error(`HTTP ${response.status}: ${response.statusText}`);
			}
			return response.json();
		})
		.then((userData) => {
			console.log("User data received:", userData);
			return userData;
		})
		.catch((error) => {
			console.error("Failed to fetch user data:", error.message);
			throw error; // Re-throw or return fallback
		});
}

// Chained operations with error propagation
function getUserProfile(userId) {
	return fetchUserData(userId)
		.then((user) => fetchUserPreferences(user.id))
		.then((preferences) => ({ user, preferences }))
		.catch((error) => {
			// Handle errors from any step in the chain
			if (error.message.includes("404")) {
				return { user: null, preferences: null }; // Graceful fallback
			}
			throw error; // Re-throw unexpected errors
		});
}
```

### 3.2 Async/Await Error Patterns

**Modern async/await** provides cleaner error handling:

```js
// Basic async/await error handling
async function fetchUserData(userId) {
	try {
		const response = await fetch(`/api/users/${userId}`);

		if (!response.ok) {
			throw new Error(`HTTP ${response.status}: ${response.statusText}`);
		}

		const userData = await response.json();
		return userData;
	} catch (error) {
		console.error("Failed to fetch user data:", error.message);

		// Different handling based on error type
		if (error.name === "TypeError") {
			throw new Error("Network error - please check your connection");
		}

		throw error;
	}
}

// Multiple async operations
async function getUserDashboard(userId) {
	try {
		// Run operations in parallel with proper error handling
		const [user, posts, notifications] = await Promise.allSettled([
			fetchUserData(userId),
			fetchUserPosts(userId),
			fetchNotifications(userId),
		]);

		// Handle partial failures gracefully
		return {
			user: user.status === "fulfilled" ? user.value : null,
			posts: posts.status === "fulfilled" ? posts.value : [],
			notifications:
				notifications.status === "fulfilled" ? notifications.value : [],
			errors: [user, posts, notifications]
				.filter((result) => result.status === "rejected")
				.map((result) => result.reason.message),
		};
	} catch (error) {
		console.error("Dashboard loading failed:", error.message);
		throw error;
	}
}
```

### 3.3 Multiple Promise Error Handling

**Different strategies** for handling multiple async operations:

```js
// Strategy 1: Fail-fast with Promise.all()
async function loadCriticalData() {
	try {
		const [users, products, settings] = await Promise.all([
			fetchUsers(),
			fetchProducts(),
			fetchSettings(),
		]);

		return { users, products, settings };
	} catch (error) {
		// If ANY promise fails, entire operation fails
		throw new Error(`Critical data loading failed: ${error.message}`);
	}
}

// Strategy 2: Best-effort with Promise.allSettled()
async function loadDashboardData() {
	const results = await Promise.allSettled([
		fetchUsers(),
		fetchProducts(),
		fetchSettings(),
	]);

	const success = [];
	const errors = [];

	results.forEach((result, index) => {
		const names = ["users", "products", "settings"];

		if (result.status === "fulfilled") {
			success.push({ [names[index]]: result.value });
		} else {
			errors.push({ [names[index]]: result.reason.message });
		}
	});

	return { success, errors };
}

// Strategy 3: Racing with timeout
async function fetchWithTimeout(url, timeoutMs = 5000) {
	const timeoutPromise = new Promise((_, reject) => {
		setTimeout(() => reject(new Error("Request timeout")), timeoutMs);
	});

	try {
		const response = await Promise.race([fetch(url), timeoutPromise]);

		return await response.json();
	} catch (error) {
		if (error.message === "Request timeout") {
			throw new Error(`Request to ${url} timed out after ${timeoutMs}ms`);
		}
		throw error;
	}
}
```

### 3.4 Advanced Async Error Strategies

**Sophisticated error handling** for production applications:

```js
// Retry mechanism with exponential backoff
async function retryOperation(operation, maxRetries = 3, baseDelay = 1000) {
	let lastError;

	for (let attempt = 1; attempt <= maxRetries; attempt++) {
		try {
			return await operation();
		} catch (error) {
			lastError = error;

			// Don't retry on certain error types
			if (error.status === 401 || error.status === 403) {
				throw error;
			}

			if (attempt === maxRetries) {
				break; // Don't delay on final attempt
			}

			// Exponential backoff with jitter
			const delay = baseDelay * Math.pow(2, attempt - 1);
			const jitter = Math.random() * 1000;

			console.warn(
				`Attempt ${attempt} failed, retrying in ${delay + jitter}ms...`
			);
			await new Promise((resolve) => setTimeout(resolve, delay + jitter));
		}
	}

	throw new Error(
		`Operation failed after ${maxRetries} attempts: ${lastError.message}`
	);
}

// Usage with retry
async function fetchCriticalData() {
	return retryOperation(async () => {
		const response = await fetch("/api/critical-data");
		if (!response.ok) {
			const error = new Error(`HTTP ${response.status}`);
			error.status = response.status;
			throw error;
		}
		return response.json();
	});
}
```

---

## 4. Custom Error Classes

### 4.1 Extending the Error Class

**Create specialized error types** for better error handling:

```js
// Basic custom error
class ValidationError extends Error {
	constructor(message, field) {
		super(message);
		this.name = "ValidationError";
		this.field = field;
	}
}

// HTTP-specific error
class HTTPError extends Error {
	constructor(message, status, response) {
		super(message);
		this.name = "HTTPError";
		this.status = status;
		this.response = response;
	}

	get isClientError() {
		return this.status >= 400 && this.status < 500;
	}

	get isServerError() {
		return this.status >= 500;
	}
}

// Database error
class DatabaseError extends Error {
	constructor(message, query, operation) {
		super(message);
		this.name = "DatabaseError";
		this.query = query;
		this.operation = operation;
		this.timestamp = new Date().toISOString();
	}
}
```

### 4.2 Specialized Error Types

**Domain-specific errors** for different application areas:

```js
// Authentication & Authorization
class AuthenticationError extends Error {
	constructor(message) {
		super(message);
		this.name = "AuthenticationError";
	}
}

class AuthorizationError extends Error {
	constructor(message, requiredPermissions) {
		super(message);
		this.name = "AuthorizationError";
		this.requiredPermissions = requiredPermissions;
	}
}

// Business Logic Errors
class BusinessRuleError extends Error {
	constructor(rule, message) {
		super(message);
		this.name = "BusinessRuleError";
		this.rule = rule;
	}
}

class InsufficientFundsError extends BusinessRuleError {
	constructor(available, requested) {
		super(
			"INSUFFICIENT_FUNDS",
			`Insufficient funds: ${available} available, ${requested} requested`
		);
		this.available = available;
		this.requested = requested;
	}
}

// Usage example
class BankAccount {
	constructor(balance = 0) {
		this.balance = balance;
	}

	withdraw(amount) {
		if (amount <= 0) {
			throw new ValidationError("Amount must be positive", "amount");
		}

		if (amount > this.balance) {
			throw new InsufficientFundsError(this.balance, amount);
		}

		this.balance -= amount;
		return this.balance;
	}
}

// Error handling with custom types
try {
	const account = new BankAccount(100);
	account.withdraw(150);
} catch (error) {
	if (error instanceof ValidationError) {
		console.error(`Validation error in ${error.field}: ${error.message}`);
	} else if (error instanceof InsufficientFundsError) {
		console.error(`Transaction failed: ${error.message}`);
		console.error(
			`Available: ${error.available}, Requested: ${error.requested}`
		);
	} else {
		console.error("Unexpected error:", error.message);
	}
}
```

### 4.3 Error Hierarchies

**Create error inheritance hierarchies** for sophisticated error handling:

```js
// Base application error
class AppError extends Error {
	constructor(message, code, statusCode = 500) {
		super(message);
		this.name = this.constructor.name;
		this.code = code;
		this.statusCode = statusCode;
		this.timestamp = new Date().toISOString();

		// Capture stack trace
		Error.captureStackTrace(this, this.constructor);
	}

	toJSON() {
		return {
			name: this.name,
			message: this.message,
			code: this.code,
			statusCode: this.statusCode,
			timestamp: this.timestamp,
		};
	}
}

// Client errors (4xx)
class ClientError extends AppError {
	constructor(message, code) {
		super(message, code, 400);
	}
}

class NotFoundError extends ClientError {
	constructor(resource) {
		super(`${resource} not found`, "RESOURCE_NOT_FOUND");
		this.statusCode = 404;
		this.resource = resource;
	}
}

class ValidationError extends ClientError {
	constructor(message, field, value) {
		super(message, "VALIDATION_ERROR");
		this.field = field;
		this.value = value;
	}
}

// Server errors (5xx)
class ServerError extends AppError {
	constructor(message, code) {
		super(message, code, 500);
	}
}

class DatabaseConnectionError extends ServerError {
	constructor(host, port) {
		super(
			`Failed to connect to database at ${host}:${port}`,
			"DB_CONNECTION_ERROR"
		);
		this.host = host;
		this.port = port;
	}
}

// Service layer with custom errors
class UserService {
	async getUser(id) {
		if (!id) {
			throw new ValidationError("User ID is required", "id", id);
		}

		try {
			const user = await this.database.findUser(id);
			if (!user) {
				throw new NotFoundError("User");
			}
			return user;
		} catch (error) {
			if (error instanceof AppError) {
				throw error; // Re-throw our custom errors
			}

			// Wrap unexpected errors
			throw new ServerError("Failed to retrieve user", "USER_RETRIEVAL_ERROR");
		}
	}
}
```

### 4.4 Best Practices for Custom Errors

**Guidelines for effective custom error design:**

```js
// 1. Always set error name
class MyCustomError extends Error {
	constructor(message) {
		super(message);
		this.name = this.constructor.name; // Automatically uses class name
	}
}

// 2. Include relevant context
class APIError extends Error {
	constructor(message, endpoint, method, status) {
		super(message);
		this.name = "APIError";
		this.endpoint = endpoint;
		this.method = method;
		this.status = status;
		this.timestamp = new Date().toISOString();
	}

	toString() {
		return `${this.name}: ${this.message} (${this.method} ${this.endpoint} - ${this.status})`;
	}
}

// 3. Provide helper methods
class NetworkError extends Error {
	constructor(message, type = "UNKNOWN") {
		super(message);
		this.name = "NetworkError";
		this.type = type;
	}

	static timeout(endpoint) {
		return new NetworkError(`Request to ${endpoint} timed out`, "TIMEOUT");
	}

	static offline() {
		return new NetworkError("Network is offline", "OFFLINE");
	}

	static dns(hostname) {
		return new NetworkError(
			`DNS resolution failed for ${hostname}`,
			"DNS_ERROR"
		);
	}

	isRetryable() {
		return ["TIMEOUT", "OFFLINE", "DNS_ERROR"].includes(this.type);
	}
}

// Usage
try {
	await fetchData();
} catch (error) {
	if (error instanceof NetworkError && error.isRetryable()) {
		// Retry logic
		console.log("Retrying due to network error...");
	}
}
```

---

## 5. Global Error Handling

### 5.1 Window.onerror

**Catch unhandled errors** globally in the browser:

```js
// Global error handler for uncaught exceptions
window.onerror = function (message, source, lineno, colno, error) {
	console.error("Global error caught:", {
		message,
		source,
		line: lineno,
		column: colno,
		error: error?.stack,
	});

	// Send error to monitoring service
	sendErrorToService({
		type: "javascript_error",
		message,
		source,
		line: lineno,
		column: colno,
		stack: error?.stack,
		userAgent: navigator.userAgent,
		url: window.location.href,
		timestamp: new Date().toISOString(),
	});

	// Return true to prevent default browser error handling
	return true;
};

// Modern alternative using addEventListener
window.addEventListener("error", (event) => {
	const { message, filename, lineno, colno, error } = event;

	// Handle different types of errors
	if (event.target !== window) {
		// Resource loading error (image, script, etc.)
		console.error("Resource loading failed:", {
			type: "resource_error",
			element: event.target.tagName,
			source: event.target.src || event.target.href,
			message: "Failed to load resource",
		});
	} else {
		// JavaScript runtime error
		console.error("JavaScript error:", {
			message,
			filename,
			lineno,
			colno,
			stack: error?.stack,
		});
	}
});
```

### 5.2 Unhandled Promise Rejections

**Catch unhandled promise rejections** before they cause issues:

```js
// Handle unhandled promise rejections
window.addEventListener("unhandledrejection", (event) => {
	console.error("Unhandled promise rejection:", {
		reason: event.reason,
		promise: event.promise,
	});

	// Send to error tracking
	sendErrorToService({
		type: "unhandled_rejection",
		message: event.reason?.message || String(event.reason),
		stack: event.reason?.stack,
		url: window.location.href,
		timestamp: new Date().toISOString(),
	});

	// Prevent default console error
	event.preventDefault();
});

// Handle rejections that get handled after being unhandled
window.addEventListener("rejectionhandled", (event) => {
	console.log("Previously unhandled rejection was handled:", event.reason);
});

// Node.js equivalent
process.on("unhandledRejection", (reason, promise) => {
	console.error("Unhandled Rejection at:", promise, "reason:", reason);

	// Send to monitoring service
	errorTracker.captureException(reason, {
		extra: { promise: promise.toString() },
	});
});

process.on("uncaughtException", (error) => {
	console.error("Uncaught Exception:", error);

	// Clean shutdown
	errorTracker.captureException(error, () => {
		process.exit(1);
	});
});
```

### 5.3 Error Reporting Systems

**Integrate with error monitoring services**:

```js
// Error reporting service integration
class ErrorReporter {
	constructor(config) {
		this.config = config;
		this.queue = [];
		this.maxQueueSize = 100;
	}

	captureError(error, context = {}) {
		const errorData = {
			message: error.message,
			stack: error.stack,
			name: error.name,
			timestamp: new Date().toISOString(),
			url: window.location.href,
			userAgent: navigator.userAgent,
			userId: this.getCurrentUserId(),
			sessionId: this.getSessionId(),
			buildVersion: this.config.buildVersion,
			environment: this.config.environment,
			context,
		};

		// Add to queue
		this.queue.push(errorData);

		// Keep queue size manageable
		if (this.queue.length > this.maxQueueSize) {
			this.queue.shift();
		}

		// Send immediately for critical errors
		if (this.isCriticalError(error)) {
			this.flush();
		}
	}

	async flush() {
		if (this.queue.length === 0) return;

		const errors = [...this.queue];
		this.queue = [];

		try {
			await fetch(this.config.endpoint, {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify({ errors }),
			});
		} catch (err) {
			// Re-add to queue if sending failed
			this.queue.unshift(...errors);
			console.error("Failed to send error reports:", err);
		}
	}

	isCriticalError(error) {
		const criticalTypes = ["SecurityError", "DatabaseError", "PaymentError"];
		return criticalTypes.includes(error.name);
	}

	getCurrentUserId() {
		// Implementation depends on your auth system
		return localStorage.getItem("userId");
	}

	getSessionId() {
		return sessionStorage.getItem("sessionId");
	}
}

// Initialize error reporter
const errorReporter = new ErrorReporter({
	endpoint: "https://api.yourapp.com/errors",
	buildVersion: "1.2.3",
	environment: "production",
});

// Global error setup
window.onerror = (message, source, lineno, colno, error) => {
	errorReporter.captureError(error || new Error(message), {
		source,
		lineno,
		colno,
	});
};

window.addEventListener("unhandledrejection", (event) => {
	errorReporter.captureError(
		event.reason instanceof Error
			? event.reason
			: new Error(String(event.reason)),
		{ type: "unhandled_rejection" }
	);
});

// Flush errors periodically
setInterval(() => errorReporter.flush(), 30000);

// Flush before page unload
window.addEventListener("beforeunload", () => errorReporter.flush());
```

---

## 6. Error Handling Patterns

### 6.1 Retry Mechanisms

**Implement smart retry logic** for transient failures:

```js
// Exponential backoff retry
class RetryHandler {
	static async withRetry(operation, options = {}) {
		const {
			maxRetries = 3,
			baseDelay = 1000,
			maxDelay = 10000,
			backoffFactor = 2,
			jitter = true,
			retryCondition = () => true,
		} = options;

		let lastError;

		for (let attempt = 0; attempt <= maxRetries; attempt++) {
			try {
				return await operation(attempt);
			} catch (error) {
				lastError = error;

				// Don't retry if condition not met
				if (!retryCondition(error, attempt)) {
					throw error;
				}

				// Don't wait on final attempt
				if (attempt === maxRetries) {
					break;
				}

				// Calculate delay with exponential backoff
				let delay = Math.min(
					baseDelay * Math.pow(backoffFactor, attempt),
					maxDelay
				);

				// Add jitter to prevent thundering herd
				if (jitter) {
					delay += Math.random() * 1000;
				}

				console.warn(
					`Attempt ${attempt + 1} failed, retrying in ${delay}ms...`,
					error.message
				);
				await this.sleep(delay);
			}
		}

		throw new Error(
			`Operation failed after ${maxRetries + 1} attempts: ${lastError.message}`
		);
	}

	static sleep(ms) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}
}

// Usage examples
async function fetchWithRetry(url) {
	return RetryHandler.withRetry(
		async (attempt) => {
			console.log(`Fetch attempt ${attempt + 1}`);
			const response = await fetch(url);

			if (!response.ok) {
				const error = new Error(`HTTP ${response.status}`);
				error.status = response.status;
				throw error;
			}

			return response.json();
		},
		{
			maxRetries: 3,
			retryCondition: (error) => {
				// Retry on network errors and 5xx responses
				return !error.status || error.status >= 500;
			},
		}
	);
}
```

### 6.2 Circuit Breaker Pattern

**Prevent cascading failures** in distributed systems:

```js
class CircuitBreaker {
	constructor(options = {}) {
		this.failureThreshold = options.failureThreshold || 5;
		this.timeout = options.timeout || 60000; // 1 minute
		this.monitor = options.monitor || this.defaultMonitor;

		this.state = "CLOSED"; // CLOSED, OPEN, HALF_OPEN
		this.failureCount = 0;
		this.lastFailureTime = null;
		this.successCount = 0;
	}

	async execute(operation) {
		if (this.state === "OPEN") {
			if (this.shouldAttemptReset()) {
				this.state = "HALF_OPEN";
				this.successCount = 0;
			} else {
				throw new Error("Circuit breaker is OPEN");
			}
		}

		try {
			const result = await operation();
			this.onSuccess();
			return result;
		} catch (error) {
			this.onFailure();
			throw error;
		}
	}

	onSuccess() {
		this.failureCount = 0;

		if (this.state === "HALF_OPEN") {
			this.successCount++;
			if (this.successCount >= 3) {
				this.state = "CLOSED";
				this.monitor("Circuit breaker reset to CLOSED");
			}
		}
	}

	onFailure() {
		this.failureCount++;
		this.lastFailureTime = Date.now();

		if (this.failureCount >= this.failureThreshold) {
			this.state = "OPEN";
			this.monitor(
				`Circuit breaker tripped to OPEN after ${this.failureCount} failures`
			);
		}
	}

	shouldAttemptReset() {
		return Date.now() - this.lastFailureTime >= this.timeout;
	}

	defaultMonitor(message) {
		console.log(`[CircuitBreaker] ${message}`);
	}

	getState() {
		return {
			state: this.state,
			failureCount: this.failureCount,
			lastFailureTime: this.lastFailureTime,
		};
	}
}

// Usage
const apiBreaker = new CircuitBreaker({
	failureThreshold: 3,
	timeout: 30000,
	monitor: (message) => console.log(`API Circuit Breaker: ${message}`),
});

async function callExternalAPI(data) {
	return apiBreaker.execute(async () => {
		const response = await fetch("/external-api", {
			method: "POST",
			body: JSON.stringify(data),
		});

		if (!response.ok) {
			throw new Error(`API call failed: ${response.status}`);
		}

		return response.json();
	});
}
```

### 6.3 Graceful Degradation

**Provide fallback functionality** when errors occur:

```js
class GracefulService {
	constructor() {
		this.cache = new Map();
		this.fallbackData = new Map();
	}

	async getUserData(userId) {
		try {
			// Try primary data source
			const userData = await this.fetchFromPrimaryAPI(userId);
			this.cache.set(userId, userData);
			return userData;
		} catch (primaryError) {
			console.warn(
				"Primary API failed, trying backup...",
				primaryError.message
			);

			try {
				// Try backup data source
				const userData = await this.fetchFromBackupAPI(userId);
				this.cache.set(userId, userData);
				return userData;
			} catch (backupError) {
				console.warn("Backup API failed, using cache...", backupError.message);

				// Use cached data if available
				if (this.cache.has(userId)) {
					const cachedData = this.cache.get(userId);
					return {
						...cachedData,
						_fromCache: true,
						_warning: "Data may be outdated",
					};
				}

				// Final fallback - minimal data
				return this.getFallbackUserData(userId);
			}
		}
	}

	getFallbackUserData(userId) {
		return {
			id: userId,
			name: "User",
			email: null,
			_fallback: true,
			_error: "Unable to load complete user data",
		};
	}

	async fetchFromPrimaryAPI(userId) {
		const response = await fetch(`/api/v1/users/${userId}`);
		if (!response.ok) throw new Error(`Primary API error: ${response.status}`);
		return response.json();
	}

	async fetchFromBackupAPI(userId) {
		const response = await fetch(`/api/v2/users/${userId}`);
		if (!response.ok) throw new Error(`Backup API error: ${response.status}`);
		return response.json();
	}
}
```

### 6.4 Error Recovery Strategies

**Implement self-healing mechanisms**:

```js
class ResilientDataFetcher {
	constructor() {
		this.errorCounts = new Map();
		this.backoffTimers = new Map();
	}

	async fetchData(endpoint, options = {}) {
		const {
			maxRetries = 3,
			fallbackValue = null,
			cacheKey = null,
			selfHeal = true,
		} = options;

		// Check if endpoint is in backoff period
		if (this.isInBackoff(endpoint)) {
			throw new Error(`Endpoint ${endpoint} is in backoff period`);
		}

		try {
			const data = await this.makeRequest(endpoint);

			// Reset error count on success
			if (selfHeal) {
				this.resetErrorCount(endpoint);
			}

			return data;
		} catch (error) {
			this.recordError(endpoint);

			// If too many errors, put endpoint in backoff
			if (this.getErrorCount(endpoint) >= maxRetries) {
				this.setBackoff(endpoint);
			}

			// Return fallback if available
			if (fallbackValue !== null) {
				console.warn(`Using fallback value for ${endpoint}:`, error.message);
				return fallbackValue;
			}

			throw error;
		}
	}

	recordError(endpoint) {
		const count = this.errorCounts.get(endpoint) || 0;
		this.errorCounts.set(endpoint, count + 1);
	}

	getErrorCount(endpoint) {
		return this.errorCounts.get(endpoint) || 0;
	}

	resetErrorCount(endpoint) {
		this.errorCounts.delete(endpoint);
		this.clearBackoff(endpoint);
	}

	setBackoff(endpoint, duration = 60000) {
		const timer = setTimeout(() => {
			this.resetErrorCount(endpoint);
		}, duration);

		this.backoffTimers.set(endpoint, timer);
		console.warn(`Endpoint ${endpoint} entering backoff for ${duration}ms`);
	}

	clearBackoff(endpoint) {
		const timer = this.backoffTimers.get(endpoint);
		if (timer) {
			clearTimeout(timer);
			this.backoffTimers.delete(endpoint);
		}
	}

	isInBackoff(endpoint) {
		return this.backoffTimers.has(endpoint);
	}

	async makeRequest(endpoint) {
		const response = await fetch(endpoint);
		if (!response.ok) {
			throw new Error(`HTTP ${response.status}: ${response.statusText}`);
		}
		return response.json();
	}
}

// Usage
const fetcher = new ResilientDataFetcher();

async function loadUserPreferences(userId) {
	try {
		return await fetcher.fetchData(`/api/users/${userId}/preferences`, {
			fallbackValue: { theme: "light", notifications: true },
			maxRetries: 2,
		});
	} catch (error) {
		console.error("Failed to load user preferences:", error.message);
		return { theme: "light", notifications: true }; // Safe defaults
	}
}
```

---

## 7. Testing Error Scenarios

**Test error handling thoroughly**:

```js
// Jest test examples for error scenarios
describe("Error Handling Tests", () => {
	test("should handle validation errors properly", async () => {
		const userService = new UserService();

		await expect(userService.createUser({})).rejects.toThrow(ValidationError);

		await expect(
			userService.createUser({ email: "invalid" })
		).rejects.toMatchObject({
			name: "ValidationError",
			field: "email",
		});
	});

	test("should retry on network failures", async () => {
		const mockFetch = jest
			.fn()
			.mockRejectedValueOnce(new Error("Network error"))
			.mockRejectedValueOnce(new Error("Network error"))
			.mockResolvedValueOnce({ ok: true, json: () => ({ data: "success" }) });

		global.fetch = mockFetch;

		const result = await fetchWithRetry("/api/data");

		expect(mockFetch).toHaveBeenCalledTimes(3);
		expect(result).toEqual({ data: "success" });
	});

	test("should handle circuit breaker states", async () => {
		const breaker = new CircuitBreaker({ failureThreshold: 2 });
		const failingOperation = jest
			.fn()
			.mockRejectedValue(new Error("Service down"));

		// First failure
		await expect(breaker.execute(failingOperation)).rejects.toThrow();
		expect(breaker.getState().state).toBe("CLOSED");

		// Second failure - should trip circuit
		await expect(breaker.execute(failingOperation)).rejects.toThrow();
		expect(breaker.getState().state).toBe("OPEN");

		// Should reject immediately when open
		await expect(breaker.execute(failingOperation)).rejects.toThrow(
			"Circuit breaker is OPEN"
		);
	});
});

// Error boundary testing for React components
const ErrorBoundary = ({ children }) => {
	const [hasError, setHasError] = useState(false);

	useEffect(() => {
		const handleError = (error, errorInfo) => {
			console.error("Error boundary caught error:", error, errorInfo);
			setHasError(true);
		};

		window.addEventListener("error", handleError);
		return () => window.removeEventListener("error", handleError);
	}, []);

	if (hasError) {
		return <div>Something went wrong. Please refresh the page.</div>;
	}

	return children;
};
```

---

## 8. Performance & Best Practices

### Error Handling Performance Tips

**Minimize performance impact** of error handling:

```js
// ✅ Good: Specific error types
try {
	processData(data);
} catch (error) {
	if (error instanceof ValidationError) {
		// Handle validation error
	} else if (error instanceof NetworkError) {
		// Handle network error
	} else {
		// Handle unexpected error
	}
}

// ❌ Bad: Catching and re-throwing everything
try {
	processData(data);
} catch (error) {
	throw new Error(`Processing failed: ${error.message}`);
}

// ✅ Good: Early validation
function processUser(userData) {
	if (!userData || typeof userData !== "object") {
		throw new ValidationError("Invalid user data");
	}

	if (!userData.email) {
		throw new ValidationError("Email is required");
	}

	// Process data...
}

// ✅ Good: Error object reuse
const COMMON_ERRORS = {
	INVALID_INPUT: new ValidationError("Invalid input provided"),
	NETWORK_TIMEOUT: new NetworkError("Request timed out"),
	UNAUTHORIZED: new AuthenticationError("Unauthorized access"),
};

// Throw pre-created error objects
throw COMMON_ERRORS.INVALID_INPUT;
```

### Best Practices Summary

1. **Be Specific**: Use custom error types for different scenarios
2. **Fail Fast**: Validate inputs early and throw meaningful errors
3. **Graceful Degradation**: Provide fallbacks when possible
4. **Log Contextually**: Include relevant information for debugging
5. **Test Error Paths**: Ensure error handling code is tested
6. **Monitor Globally**: Set up global error handlers and monitoring
7. **Document Error Types**: Document what errors your functions can throw
8. **Avoid Silent Failures**: Always handle or propagate errors appropriately

---

**Mastering JavaScript error handling transforms fragile code into robust, maintainable applications. These patterns and practices ensure your applications handle failures gracefully while providing excellent debugging capabilities and user experiences.**
