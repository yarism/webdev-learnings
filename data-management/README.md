# Data Management: Mutable vs Immutable

Understanding how to work with mutable and immutable data in JavaScript.

## Table of Contents
- [Mutability Basics](#mutability-basics)
- [Mutable Data](#mutable-data)
- [Immutable Data](#immutable-data)
- [Deep vs Shallow Copy](#deep-vs-shallow-copy)
- [Immutability Techniques](#immutability-techniques)
- [Best Practices](#best-practices)

---

## Mutability Basics

**Mutable**: Can be changed after creation  
**Immutable**: Cannot be changed after creation

### Primitive Values (Immutable)

```javascript
let str = "hello";
let num = 42;
let bool = true;

str.toUpperCase(); // Returns "HELLO" but doesn't change str
console.log(str);  // "hello" - unchanged
```

### Objects and Arrays (Mutable)

```javascript
const arr = [1, 2, 3];
arr.push(4);
console.log(arr); // [1, 2, 3, 4] - changed

const obj = { name: "Alice" };
obj.age = 30;
console.log(obj); // { name: "Alice", age: 30 } - changed
```

---

## Mutable Data

Working with data that can be changed in place.

### Mutable Array Operations

```javascript
const numbers = [1, 2, 3];

numbers.push(4);
numbers.pop();
numbers.shift();
numbers.unshift(0);
numbers.splice(1, 1);
numbers.sort();
numbers.reverse();
```

### Mutable Object Operations

```javascript
const user = { name: "Alice", age: 25 };

user.email = "alice@example.com";
user.age = 26;
delete user.age;

Object.assign(user, { role: "admin" });
```

### Problems with Mutability

**Unexpected Side Effects:**
```javascript
function addItem(array, item) {
  array.push(item);
  return array;
}

const original = [1, 2, 3];
const result = addItem(original, 4);

console.log(original); // [1, 2, 3, 4] - mutated!
console.log(result);   // [1, 2, 3, 4]
```

**Reference Issues:**
```javascript
const user = { name: "Alice", age: 25 };
const admin = user;

admin.role = "admin";

console.log(user.role);  // "admin" - unexpected mutation
console.log(admin.role); // "admin"
```

---

## Immutable Data

Creating new data instead of modifying existing data.

### Immutable Array Operations

```javascript
const numbers = [1, 2, 3];

const added = [...numbers, 4];
const removed = numbers.filter(n => n !== 2);
const mapped = numbers.map(n => n * 2);
const sliced = numbers.slice(1);

console.log(numbers); // [1, 2, 3] - unchanged
```

### Immutable Object Operations

```javascript
const user = { name: "Alice", age: 25 };

const updated = { ...user, age: 26 };
const withEmail = { ...user, email: "alice@example.com" };
const { age, ...rest } = user;

console.log(user); // { name: "Alice", age: 25 } - unchanged
```

### Array Methods: Mutable vs Immutable

**Mutable Methods:**
- `push()`, `pop()`
- `shift()`, `unshift()`
- `splice()`
- `sort()`, `reverse()`

**Immutable Methods:**
- `map()`, `filter()`, `reduce()`
- `slice()`, `concat()`
- `spread operator [...]`

```javascript
const original = [3, 1, 2];

const mutable = original;
mutable.sort();
console.log(original); // [1, 2, 3] - changed!

const immutable = [...original].sort();
console.log(original); // [3, 1, 2] - unchanged
```

---

## Deep vs Shallow Copy

Understanding different levels of copying.

### Shallow Copy

Copies only the first level; nested objects are still referenced.

**Spread Operator:**
```javascript
const original = { name: "Alice", address: { city: "NYC" } };
const copy = { ...original };

copy.name = "Bob";
copy.address.city = "LA";

console.log(original.name);         // "Alice" - not changed
console.log(original.address.city); // "LA" - changed!
```

**Object.assign():**
```javascript
const original = { name: "Alice", hobbies: ["reading"] };
const copy = Object.assign({}, original);

copy.hobbies.push("gaming");
console.log(original.hobbies); // ["reading", "gaming"] - changed!
```

**Array Spread:**
```javascript
const original = [[1, 2], [3, 4]];
const copy = [...original];

copy[0].push(5);
console.log(original); // [[1, 2, 5], [3, 4]] - nested array changed!
```

### Deep Copy

Copies all levels; nested objects are also duplicated.

**JSON Method (limitations):**
```javascript
const original = {
  name: "Alice",
  address: { city: "NYC" },
  hobbies: ["reading"]
};

const copy = JSON.parse(JSON.stringify(original));

copy.address.city = "LA";
copy.hobbies.push("gaming");

console.log(original.address.city); // "NYC" - unchanged
console.log(original.hobbies);      // ["reading"] - unchanged
```

**Limitations of JSON method:**
```javascript
const obj = {
  date: new Date(),
  func: () => console.log("hi"),
  undef: undefined,
  symbol: Symbol("id")
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy);
```

**Recursive Deep Copy:**
```javascript
function deepCopy(obj) {
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  if (obj instanceof Date) {
    return new Date(obj);
  }
  
  if (obj instanceof Array) {
    return obj.map(item => deepCopy(item));
  }
  
  const copied = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copied[key] = deepCopy(obj[key]);
    }
  }
  
  return copied;
}

const original = {
  name: "Alice",
  date: new Date(),
  address: { city: "NYC" },
  hobbies: ["reading"]
};

const copy = deepCopy(original);
copy.address.city = "LA";
console.log(original.address.city); // "NYC"
```

**Modern structuredClone:**
```javascript
const original = {
  name: "Alice",
  date: new Date(),
  address: { city: "NYC" },
  hobbies: ["reading"]
};

const copy = structuredClone(original);
copy.address.city = "LA";
console.log(original.address.city); // "NYC"
```

---

## Immutability Techniques

Practical techniques for working with immutable data.

### Updating Nested Objects

```javascript
const user = {
  name: "Alice",
  address: {
    street: "123 Main St",
    city: "NYC"
  }
};

const updated = {
  ...user,
  address: {
    ...user.address,
    city: "LA"
  }
};
```

### Adding to Arrays

```javascript
const numbers = [1, 2, 3];

const addToEnd = [...numbers, 4];
const addToStart = [0, ...numbers];
const addToMiddle = [...numbers.slice(0, 1), 1.5, ...numbers.slice(1)];
```

### Removing from Arrays

```javascript
const numbers = [1, 2, 3, 4, 5];

const removeByValue = numbers.filter(n => n !== 3);
const removeByIndex = [...numbers.slice(0, 2), ...numbers.slice(3)];
const removeFirst = numbers.slice(1);
const removeLast = numbers.slice(0, -1);
```

### Updating Arrays

```javascript
const users = [
  { id: 1, name: "Alice", active: true },
  { id: 2, name: "Bob", active: false },
  { id: 3, name: "Charlie", active: true }
];

const updated = users.map(user =>
  user.id === 2 ? { ...user, active: true } : user
);

const toggled = users.map(user => ({
  ...user,
  active: !user.active
}));
```

### Combining Objects

```javascript
const defaults = { theme: "light", language: "en" };
const userPrefs = { theme: "dark" };

const settings = { ...defaults, ...userPrefs };
```

### Removing Object Properties

```javascript
const user = { id: 1, name: "Alice", password: "secret" };

const { password, ...safeUser } = user;
console.log(safeUser); // { id: 1, name: "Alice" }
```

### Array to Object Transformation

```javascript
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" }
];

const usersById = users.reduce((acc, user) => ({
  ...acc,
  [user.id]: user
}), {});
```

### Immutable State Updates

```javascript
function updateState(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, action.todo]
      };
    
    case 'REMOVE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.id)
      };
    
    case 'UPDATE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.id
            ? { ...todo, ...action.updates }
            : todo
        )
      };
    
    default:
      return state;
  }
}
```

---

## Best Practices

### 1. Prefer Immutability

```javascript
function addUser(users, newUser) {
  return [...users, newUser];
}

const original = [{ name: "Alice" }];
const updated = addUser(original, { name: "Bob" });

console.log(original.length); // 1 - unchanged
```

### 2. Use Const for References

```javascript
const user = { name: "Alice" };

user = { name: "Bob" };

user.name = "Bob";
```

### 3. Freeze Objects When Needed

```javascript
const config = Object.freeze({
  apiUrl: "https://api.example.com",
  timeout: 5000
});

config.apiUrl = "https://other.com";
console.log(config.apiUrl); // Still "https://api.example.com"
```

**Deep Freeze:**
```javascript
function deepFreeze(obj) {
  Object.freeze(obj);
  
  Object.values(obj).forEach(value => {
    if (typeof value === 'object' && value !== null) {
      deepFreeze(value);
    }
  });
  
  return obj;
}

const config = deepFreeze({
  database: {
    host: "localhost",
    port: 5432
  }
});
```

### 4. Use Libraries for Complex Immutability

**Immer.js:**
```javascript
import produce from 'immer';

const state = {
  todos: [
    { id: 1, text: "Learn JS", done: false }
  ]
};

const nextState = produce(state, draft => {
  draft.todos.push({ id: 2, text: "Learn React", done: false });
  draft.todos[0].done = true;
});
```

### 5. Document Mutation

```javascript
function sortArrayInPlace(arr) {
  arr.sort();
  return arr;
}

function sortArray(arr) {
  return [...arr].sort();
}
```

---

## Key Takeaways

- **Primitives are immutable**: Strings, numbers, booleans cannot be changed
- **Objects and arrays are mutable**: Can be changed in place
- **Immutability prevents bugs**: Reduces side effects and makes code predictable
- **Shallow copy**: Only copies first level (spread, Object.assign)
- **Deep copy**: Copies all levels (structuredClone, custom recursion)
- **Use immutable methods**: map, filter, reduce instead of push, splice, etc.
- **Prefer immutability**: Especially in React, Redux, and functional programming
- **Use libraries**: Immer, Immutable.js for complex state management 