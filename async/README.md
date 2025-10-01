# Asynchronous JavaScript

Understanding how JavaScript handles asynchronous operations.

## Table of Contents
- [The Event Loop](#the-event-loop)
- [Promises](#promises)
- [Async/Await](#asyncawait)

---

## The Event Loop

JavaScript is single-threaded, but can handle asynchronous operations through the event loop mechanism.

### Call Stack

The call stack is where JavaScript keeps track of function execution.

```javascript
function first() {
  console.log("First");
}

function second() {
  first();
  console.log("Second");
}

second();
// Output:
// "First"
// "Second"
```

### Web APIs & Task Queue

Asynchronous operations are handled by Web APIs (browser) or C++ APIs (Node.js), then callbacks are placed in the task queue.

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

console.log("End");

// Output:
// "Start"
// "End"
// "Timeout"
```

**Why?** Even with 0ms delay, `setTimeout` is asynchronous and its callback goes to the task queue.

### Microtasks vs Macrotasks

**Microtasks** (higher priority):
- Promise callbacks (`.then`, `.catch`, `.finally`)
- `queueMicrotask()`
- MutationObserver

**Macrotasks** (lower priority):
- `setTimeout`, `setInterval`
- `setImmediate` (Node.js)
- I/O operations

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("Promise");
});

console.log("End");

// Output:
// "Start"
// "End"
// "Promise"    <- Microtask runs first
// "Timeout"    <- Macrotask runs after
```

### Event Loop Flow

1. Execute synchronous code (call stack)
2. Check microtask queue and execute all microtasks
3. Render (if in browser)
4. Execute one macrotask
5. Repeat from step 2

---

## Promises

A Promise is an object representing the eventual completion or failure of an asynchronous operation.

### Promise States

- **Pending**: Initial state, neither fulfilled nor rejected
- **Fulfilled**: Operation completed successfully
- **Rejected**: Operation failed

### Creating a Promise

```javascript
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  
  if (success) {
    resolve("Operation successful!");
  } else {
    reject("Operation failed!");
  }
});
```

### Consuming Promises

```javascript
myPromise
  .then((result) => {
    console.log(result); // "Operation successful!"
  })
  .catch((error) => {
    console.error(error);
  })
  .finally(() => {
    console.log("Promise settled");
  });
```

### Chaining Promises

```javascript
function fetchUser(id) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ id, name: "Alice" });
    }, 1000);
  });
}

function fetchPosts(userId) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, title: "Post 1" },
        { id: 2, title: "Post 2" }
      ]);
    }, 1000);
  });
}

fetchUser(1)
  .then((user) => {
    console.log("User:", user);
    return fetchPosts(user.id);
  })
  .then((posts) => {
    console.log("Posts:", posts);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

### Promise Static Methods

**`Promise.all()`** - Waits for all promises to resolve (or any to reject):
```javascript
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3])
  .then((values) => {
    console.log(values); // [1, 2, 3]
  });
```

**`Promise.allSettled()`** - Waits for all promises to settle (resolve or reject):
```javascript
const promises = [
  Promise.resolve(1),
  Promise.reject("Error"),
  Promise.resolve(3)
];

Promise.allSettled(promises)
  .then((results) => {
    console.log(results);
    // [
    //   { status: "fulfilled", value: 1 },
    //   { status: "rejected", reason: "Error" },
    //   { status: "fulfilled", value: 3 }
    // ]
  });
```

**`Promise.race()`** - Returns the first promise that settles:
```javascript
const slow = new Promise((resolve) => setTimeout(() => resolve("Slow"), 2000));
const fast = new Promise((resolve) => setTimeout(() => resolve("Fast"), 1000));

Promise.race([slow, fast])
  .then((result) => {
    console.log(result); // "Fast"
  });
```

**`Promise.any()`** - Returns the first promise that fulfills:
```javascript
const promises = [
  Promise.reject("Error 1"),
  Promise.resolve("Success!"),
  Promise.reject("Error 2")
];

Promise.any(promises)
  .then((result) => {
    console.log(result); // "Success!"
  });
```

---

## Async/Await

`async`/`await` provides a cleaner syntax for working with promises, making asynchronous code look synchronous.

### Basic Syntax

```javascript
async function fetchData() {
  const response = await fetch('https://api.example.com/data');
  const data = await response.json();
  return data;
}
```

### Async Function Always Returns a Promise

```javascript
async function greet() {
  return "Hello";
}

greet().then((message) => {
  console.log(message); // "Hello"
});
```

### Error Handling with Try/Catch

```javascript
async function fetchUser(id) {
  try {
    const response = await fetch(`https://api.example.com/users/${id}`);
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const user = await response.json();
    return user;
  } catch (error) {
    console.error("Failed to fetch user:", error);
    throw error;
  }
}
```

### Sequential vs Parallel Execution

**Sequential (slower):**
```javascript
async function sequential() {
  const user = await fetchUser(1);      // Wait 1s
  const posts = await fetchPosts(1);    // Wait another 1s
  return { user, posts };
}
// Total: ~2 seconds
```

**Parallel (faster):**
```javascript
async function parallel() {
  const [user, posts] = await Promise.all([
    fetchUser(1),
    fetchPosts(1)
  ]);
  return { user, posts };
}
// Total: ~1 second (both run simultaneously)
```

### Practical Example

```javascript
async function getUserData(userId) {
  try {
    console.log("Fetching user...");
    const userResponse = await fetch(`/api/users/${userId}`);
    const user = await userResponse.json();
    
    console.log("Fetching posts...");
    const postsResponse = await fetch(`/api/users/${userId}/posts`);
    const posts = await postsResponse.json();
    
    console.log("Fetching comments...");
    const commentsResponse = await fetch(`/api/users/${userId}/comments`);
    const comments = await commentsResponse.json();
    
    return {
      user,
      posts,
      comments
    };
  } catch (error) {
    console.error("Error fetching user data:", error);
    throw error;
  }
}

getUserData(1)
  .then((data) => console.log(data))
  .catch((error) => console.error(error));
```

### Async/Await with Array Methods

**Wrong approach:**
```javascript
const urls = ['/api/1', '/api/2', '/api/3'];

urls.forEach(async (url) => {
  const response = await fetch(url);
  console.log(response);
});
```

**Correct approach:**
```javascript
async function fetchAll(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(response);
  }
}
```

**Parallel approach:**
```javascript
async function fetchAllParallel(urls) {
  const promises = urls.map((url) => fetch(url));
  const responses = await Promise.all(promises);
  return responses;
}
```

### Top-Level Await

In modern JavaScript modules, you can use `await` at the top level:

```javascript
// module.js
const data = await fetch('/api/data');
export default data;
```

---

## Key Takeaways

- **Event Loop**: Manages execution of code, events, and asynchronous callbacks
- **Microtasks** (Promises) have higher priority than **Macrotasks** (setTimeout)
- **Promises**: Represent eventual completion/failure of async operations
- **Async/Await**: Syntactic sugar over Promises, makes async code more readable
- Use `Promise.all()` for parallel execution when operations don't depend on each other
- Always handle errors with `.catch()` or `try/catch` 