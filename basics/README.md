# JavaScript Basics

Essential JavaScript concepts that form the foundation of the language.

## Table of Contents
- [Hoisting](#hoisting)
- [Scope](#scope)
- [Closures](#closures)
- [The `this` Keyword](#the-this-keyword)

---

## Hoisting

Hoisting is JavaScript's behavior of moving declarations to the top of their scope before code execution.

### Variable Hoisting

**`var` declarations are hoisted:**
```javascript
console.log(x); // undefined (not ReferenceError)
var x = 5;
console.log(x); // 5
```

**`let` and `const` are hoisted but not initialized (Temporal Dead Zone):**
```javascript
console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

console.log(z); // ReferenceError: Cannot access 'z' before initialization
const z = 15;
```

### Function Hoisting

**Function declarations are fully hoisted:**
```javascript
greet(); // "Hello!" - works fine

function greet() {
  console.log("Hello!");
}
```

**Function expressions are NOT hoisted:**
```javascript
sayHi(); // TypeError: sayHi is not a function

var sayHi = function() {
  console.log("Hi!");
};
```

---

## Scope

Scope determines the accessibility of variables, objects, and functions in different parts of your code.

### Global Scope

Variables declared outside any function or block have global scope.

```javascript
var globalVar = "I'm global";
let globalLet = "Also global";

function showGlobal() {
  console.log(globalVar); // Accessible
  console.log(globalLet); // Accessible
}
```

### Function Scope

Variables declared within a function are local to that function.

```javascript
function myFunction() {
  var functionScoped = "Only inside function";
  console.log(functionScoped); // Works
}

myFunction();
console.log(functionScoped); // ReferenceError
```

### Block Scope

`let` and `const` are block-scoped (within `{}`).

```javascript
if (true) {
  var varVariable = "var is function-scoped";
  let letVariable = "let is block-scoped";
  const constVariable = "const is block-scoped";
}

console.log(varVariable); // "var is function-scoped"
console.log(letVariable); // ReferenceError
console.log(constVariable); // ReferenceError
```

### Scope Chain

When looking for a variable, JavaScript searches the current scope, then outer scopes.

```javascript
const global = "global";

function outer() {
  const outerVar = "outer";
  
  function inner() {
    const innerVar = "inner";
    console.log(innerVar);  // "inner"
    console.log(outerVar);  // "outer"
    console.log(global);    // "global"
  }
  
  inner();
}

outer();
```

---

## Closures

A closure is a function that has access to variables in its outer (enclosing) lexical scope, even after the outer function has returned.

### Basic Closure

```javascript
function outer() {
  const message = "Hello";
  
  function inner() {
    console.log(message); // Has access to 'message'
  }
  
  return inner;
}

const myFunc = outer();
myFunc(); // "Hello" - closure maintains access to 'message'
```

### Practical Example: Counter

```javascript
function createCounter() {
  let count = 0;
  
  return {
    increment: function() {
      count++;
      return count;
    },
    decrement: function() {
      count--;
      return count;
    },
    getCount: function() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.decrement()); // 1
console.log(counter.getCount());  // 1
```

### Closures in Loops

**Problem with `var`:**
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Prints: 3, 3, 3
  }, 1000);
}
```

**Solution with `let`:**
```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Prints: 0, 1, 2
  }, 1000);
}
```

**Solution with closure (IIFE):**
```javascript
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // Prints: 0, 1, 2
    }, 1000);
  })(i);
}
```

---

## The `this` Keyword

`this` refers to the object that is executing the current function. Its value depends on how the function is called.

### Global Context

```javascript
console.log(this); // Window (browser) or global (Node.js)
```

### Object Method

```javascript
const person = {
  name: "Alice",
  greet: function() {
    console.log(this.name);
  }
};

person.greet(); // "Alice" - 'this' refers to 'person'
```

### Regular Function

```javascript
function showThis() {
  console.log(this);
}

showThis(); // Window (non-strict) or undefined (strict mode)
```

### Arrow Functions

Arrow functions don't have their own `this`. They inherit `this` from the enclosing scope.

```javascript
const obj = {
  name: "Bob",
  regularFunc: function() {
    console.log(this.name); // "Bob"
  },
  arrowFunc: () => {
    console.log(this.name); // undefined (inherits from global scope)
  }
};

obj.regularFunc();
obj.arrowFunc();
```

### Event Handlers

```javascript
const button = document.querySelector('button');

button.addEventListener('click', function() {
  console.log(this); // The button element
});

button.addEventListener('click', () => {
  console.log(this); // Window (arrow function inherits from outer scope)
});
```

### Explicit Binding

**`call()`:**
```javascript
function greet(greeting) {
  console.log(`${greeting}, ${this.name}`);
}

const user = { name: "Charlie" };
greet.call(user, "Hello"); // "Hello, Charlie"
```

**`apply()`:**
```javascript
function introduce(greeting, punctuation) {
  console.log(`${greeting}, I'm ${this.name}${punctuation}`);
}

const person = { name: "Dana" };
introduce.apply(person, ["Hi", "!"]); // "Hi, I'm Dana!"
```

**`bind()`:**
```javascript
function sayName() {
  console.log(this.name);
}

const obj1 = { name: "Eve" };
const boundFunc = sayName.bind(obj1);
boundFunc(); // "Eve"
```

### Constructor Functions

```javascript
function Person(name) {
  this.name = name;
  this.greet = function() {
    console.log(`Hello, I'm ${this.name}`);
  };
}

const person1 = new Person("Frank");
person1.greet(); // "Hello, I'm Frank"
```

---

## Key Takeaways

- **Hoisting**: Declarations are moved to the top; `var` is initialized with `undefined`, `let`/`const` remain in TDZ
- **Scope**: `var` is function-scoped, `let`/`const` are block-scoped
- **Closures**: Functions retain access to their lexical scope even after the outer function returns
- **`this`**: Context-dependent; determined by how the function is called, not where it's defined (except arrow functions) 