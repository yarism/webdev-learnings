# Error Handling & Custom Errors

Understanding how to properly handle errors and create custom error types in JavaScript.

## Table of Contents
- [Error Basics](#error-basics)
- [Try/Catch/Finally](#trycatchfinally)
- [Error Types](#error-types)
- [Custom Errors](#custom-errors)
- [Error Propagation](#error-propagation)
- [Async Error Handling](#async-error-handling)
- [Best Practices](#best-practices)

---

## Error Basics

Errors are objects that represent problems in code execution.

### Throwing Errors

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error('Cannot divide by zero');
  }
  return a / b;
}

console.log(divide(10, 2)); // 5
console.log(divide(10, 0)); // Error: Cannot divide by zero
```

### Error Object Properties

```javascript
try {
  throw new Error('Something went wrong');
} catch (error) {
  console.log(error.name);    // "Error"
  console.log(error.message); // "Something went wrong"
  console.log(error.stack);   // Stack trace
}
```

---

## Try/Catch/Finally

The `try/catch/finally` statement handles exceptions gracefully.

### Basic Try/Catch

```javascript
try {
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  console.error('An error occurred:', error.message);
}
```

### Try/Catch/Finally

```javascript
function readFile(filename) {
  let file;
  
  try {
    file = openFile(filename);
    const data = file.read();
    return data;
  } catch (error) {
    console.error('Error reading file:', error.message);
    return null;
  } finally {
    if (file) {
      file.close();
    }
  }
}
```

### Multiple Catch Blocks (Pattern)

```javascript
try {
  const data = JSON.parse(jsonString);
  processData(data);
} catch (error) {
  if (error instanceof SyntaxError) {
    console.error('Invalid JSON:', error.message);
  } else if (error instanceof TypeError) {
    console.error('Type error:', error.message);
  } else {
    console.error('Unknown error:', error.message);
  }
}
```

---

## Error Types

JavaScript has several built-in error types.

### Built-in Error Types

**Error:**
```javascript
throw new Error('Generic error');
```

**SyntaxError:**
```javascript
try {
  eval('invalid code {');
} catch (error) {
  console.log(error instanceof SyntaxError); // true
}
```

**ReferenceError:**
```javascript
try {
  console.log(nonExistentVariable);
} catch (error) {
  console.log(error instanceof ReferenceError); // true
}
```

**TypeError:**
```javascript
try {
  null.toString();
} catch (error) {
  console.log(error instanceof TypeError); // true
}
```

**RangeError:**
```javascript
try {
  const arr = new Array(-1);
} catch (error) {
  console.log(error instanceof RangeError); // true
}
```

**URIError:**
```javascript
try {
  decodeURIComponent('%');
} catch (error) {
  console.log(error instanceof URIError); // true
}
```

---

## Custom Errors

Creating custom error classes for specific error types.

### Basic Custom Error

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

function validateAge(age) {
  if (age < 0) {
    throw new ValidationError('Age cannot be negative');
  }
  if (age > 150) {
    throw new ValidationError('Age seems unrealistic');
  }
  return true;
}

try {
  validateAge(-5);
} catch (error) {
  if (error instanceof ValidationError) {
    console.error('Validation failed:', error.message);
  }
}
```

### Custom Error with Additional Properties

```javascript
class HttpError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = 'HttpError';
    this.statusCode = statusCode;
  }
}

class NotFoundError extends HttpError {
  constructor(resource) {
    super(`${resource} not found`, 404);
    this.name = 'NotFoundError';
    this.resource = resource;
  }
}

class UnauthorizedError extends HttpError {
  constructor(message = 'Unauthorized access') {
    super(message, 401);
    this.name = 'UnauthorizedError';
  }
}

async function fetchUser(id) {
  const user = await database.findUser(id);
  
  if (!user) {
    throw new NotFoundError('User');
  }
  
  if (!user.hasAccess) {
    throw new UnauthorizedError();
  }
  
  return user;
}

try {
  const user = await fetchUser(123);
} catch (error) {
  if (error instanceof NotFoundError) {
    console.error(`Resource not found: ${error.resource}`);
  } else if (error instanceof UnauthorizedError) {
    console.error('Access denied');
  } else if (error instanceof HttpError) {
    console.error(`HTTP Error ${error.statusCode}: ${error.message}`);
  }
}
```

### Validation Error with Field Details

```javascript
class ValidationError extends Error {
  constructor(message, field = null) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

function validateUser(user) {
  if (!user.email) {
    throw new ValidationError('Email is required', 'email');
  }
  
  if (!user.email.includes('@')) {
    throw new ValidationError('Invalid email format', 'email');
  }
  
  if (!user.password || user.password.length < 8) {
    throw new ValidationError('Password must be at least 8 characters', 'password');
  }
}

try {
  validateUser({ email: 'invalid', password: '123' });
} catch (error) {
  if (error instanceof ValidationError) {
    console.error(`${error.field}: ${error.message}`);
  }
}
```

---

## Error Propagation

Errors can be caught, logged, and re-thrown.

### Catching and Re-throwing

```javascript
function processData(data) {
  try {
    return JSON.parse(data);
  } catch (error) {
    console.error('Failed to parse JSON');
    throw error;
  }
}

function handleRequest(request) {
  try {
    const data = processData(request.body);
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error.message };
  }
}
```

### Wrapping Errors

```javascript
class DatabaseError extends Error {
  constructor(message, originalError) {
    super(message);
    this.name = 'DatabaseError';
    this.originalError = originalError;
  }
}

async function getUser(id) {
  try {
    return await database.query('SELECT * FROM users WHERE id = ?', [id]);
  } catch (error) {
    throw new DatabaseError(`Failed to fetch user ${id}`, error);
  }
}

try {
  const user = await getUser(123);
} catch (error) {
  if (error instanceof DatabaseError) {
    console.error(error.message);
    console.error('Original error:', error.originalError);
  }
}
```

### Error Boundaries Pattern

```javascript
function errorBoundary(fn) {
  return async function(...args) {
    try {
      return await fn(...args);
    } catch (error) {
      console.error(`Error in ${fn.name}:`, error);
      throw error;
    }
  };
}

const safeProcessData = errorBoundary(async function processData(data) {
  return JSON.parse(data);
});

await safeProcessData('invalid json');
```

---

## Async Error Handling

Handling errors in asynchronous code.

### Async/Await with Try/Catch

```javascript
async function fetchUserData(userId) {
  try {
    const response = await fetch(`/api/users/${userId}`);
    
    if (!response.ok) {
      throw new HttpError('Failed to fetch user', response.status);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    if (error instanceof HttpError) {
      console.error('HTTP Error:', error.message);
    } else if (error instanceof SyntaxError) {
      console.error('Invalid JSON response');
    } else {
      console.error('Network error:', error.message);
    }
    throw error;
  }
}
```

### Promise Chain Error Handling

```javascript
fetchUser(123)
  .then(user => processUser(user))
  .then(result => saveResult(result))
  .catch(error => {
    if (error instanceof NotFoundError) {
      console.error('User not found');
    } else {
      console.error('Processing failed:', error.message);
    }
  })
  .finally(() => {
    console.log('Operation completed');
  });
```

### Multiple Async Operations

```javascript
async function loadUserProfile(userId) {
  try {
    const [user, posts, comments] = await Promise.all([
      fetchUser(userId),
      fetchPosts(userId),
      fetchComments(userId)
    ]);
    
    return { user, posts, comments };
  } catch (error) {
    console.error('Failed to load profile:', error.message);
    throw error;
  }
}
```

### Handling Individual Promise Failures

```javascript
async function loadUserData(userId) {
  const results = await Promise.allSettled([
    fetchUser(userId),
    fetchPosts(userId),
    fetchComments(userId)
  ]);
  
  const [userResult, postsResult, commentsResult] = results;
  
  return {
    user: userResult.status === 'fulfilled' ? userResult.value : null,
    posts: postsResult.status === 'fulfilled' ? postsResult.value : [],
    comments: commentsResult.status === 'fulfilled' ? commentsResult.value : [],
    errors: results
      .filter(r => r.status === 'rejected')
      .map(r => r.reason.message)
  };
}
```

---

## Best Practices

### 1. Always Provide Meaningful Error Messages

```javascript
// ❌ BAD
throw new Error('Error');

// ✅ GOOD
throw new Error('Failed to parse user data: invalid JSON format');
```

### 2. Use Custom Errors for Domain Logic

```javascript
// ✅ GOOD
class InsufficientFundsError extends Error {
  constructor(balance, amount) {
    super(`Insufficient funds: balance ${balance}, required ${amount}`);
    this.name = 'InsufficientFundsError';
    this.balance = balance;
    this.amount = amount;
  }
}

function withdraw(account, amount) {
  if (account.balance < amount) {
    throw new InsufficientFundsError(account.balance, amount);
  }
  account.balance -= amount;
}
```

### 3. Don't Swallow Errors

```javascript
// ❌ BAD
try {
  riskyOperation();
} catch (error) {
  // Silent failure
}

// ✅ GOOD
try {
  riskyOperation();
} catch (error) {
  console.error('Operation failed:', error);
  throw error;
}
```

### 4. Clean Up Resources in Finally

```javascript
async function processFile(filename) {
  const file = await openFile(filename);
  
  try {
    return await file.read();
  } catch (error) {
    console.error('Error reading file:', error);
    throw error;
  } finally {
    await file.close();
  }
}
```

### 5. Validate Early

```javascript
function calculateTotal(items) {
  if (!Array.isArray(items)) {
    throw new TypeError('Items must be an array');
  }
  
  if (items.length === 0) {
    throw new ValidationError('Items array cannot be empty');
  }
  
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

### 6. Create an Error Handler Utility

```javascript
class ErrorHandler {
  static handle(error) {
    if (error instanceof ValidationError) {
      return { status: 400, message: error.message };
    }
    
    if (error instanceof NotFoundError) {
      return { status: 404, message: error.message };
    }
    
    if (error instanceof UnauthorizedError) {
      return { status: 401, message: error.message };
    }
    
    console.error('Unexpected error:', error);
    return { status: 500, message: 'Internal server error' };
  }
}

try {
  await processRequest(request);
} catch (error) {
  const response = ErrorHandler.handle(error);
  res.status(response.status).json({ error: response.message });
}
```

---

## Key Takeaways

- **Always handle errors**: Use try/catch for synchronous code, .catch() or try/catch with async/await
- **Custom errors**: Create specific error types for different failure scenarios
- **Meaningful messages**: Provide context about what went wrong
- **Don't swallow errors**: Log or re-throw errors, don't ignore them
- **Finally blocks**: Use for cleanup operations that must run regardless of success/failure
- **Error propagation**: Let errors bubble up to appropriate handlers
- **Validate early**: Check inputs at function boundaries 