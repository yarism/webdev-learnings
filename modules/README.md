# JavaScript Modules

Understanding module systems in JavaScript for better code organization and reusability.

## Table of Contents
- [What are Modules?](#what-are-modules)
- [ES Modules (ESM)](#es-modules-esm)
- [CommonJS (CJS)](#commonjs-cjs)
- [ESM vs CommonJS](#esm-vs-commonjs)
- [Best Practices](#best-practices)

---

## What are Modules?

Modules allow you to break up your code into separate files, making it more maintainable and reusable.

**Benefits:**
- Code organization
- Reusability
- Namespace isolation
- Dependency management

---

## ES Modules (ESM)

ES Modules is the official standard for JavaScript modules (ES6+).

### Basic Export/Import

**Named Exports:**
```javascript
// math.js
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}
```

```javascript
// app.js
import { PI, add, subtract } from './math.js';

console.log(PI);           // 3.14159
console.log(add(5, 3));    // 8
console.log(subtract(5, 3)); // 2
```

**Default Export:**
```javascript
// calculator.js
export default class Calculator {
  add(a, b) {
    return a + b;
  }
  
  subtract(a, b) {
    return a - b;
  }
}
```

```javascript
// app.js
import Calculator from './calculator.js';

const calc = new Calculator();
console.log(calc.add(5, 3)); // 8
```

### Mixed Exports

```javascript
// utils.js
export const version = '1.0.0';

export function helper() {
  return 'Helper function';
}

export default function main() {
  return 'Main function';
}
```

```javascript
// app.js
import mainFunc, { version, helper } from './utils.js';

console.log(mainFunc());  // "Main function"
console.log(version);     // "1.0.0"
console.log(helper());    // "Helper function"
```

### Import Variations

**Import All:**
```javascript
import * as MathUtils from './math.js';

console.log(MathUtils.PI);
console.log(MathUtils.add(5, 3));
```

**Rename Imports:**
```javascript
import { add as sum, subtract as diff } from './math.js';

console.log(sum(5, 3));  // 8
console.log(diff(5, 3)); // 2
```

**Re-exporting:**
```javascript
// index.js
export { add, subtract } from './math.js';
export { default as Calculator } from './calculator.js';
export * from './utils.js';
```

**Dynamic Imports:**
```javascript
async function loadModule() {
  const module = await import('./math.js');
  console.log(module.add(5, 3));
}

loadModule();
```

```javascript
button.addEventListener('click', async () => {
  const { default: Calculator } = await import('./calculator.js');
  const calc = new Calculator();
  console.log(calc.add(5, 3));
});
```

### Using ESM in Browser

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>ESM Example</title>
</head>
<body>
  <script type="module" src="app.js"></script>
</body>
</html>
```

### Using ESM in Node.js

**Option 1: Use `.mjs` extension:**
```javascript
// math.mjs
export function add(a, b) {
  return a + b;
}
```

**Option 2: Add `"type": "module"` to package.json:**
```json
{
  "type": "module",
  "name": "my-app"
}
```

---

## CommonJS (CJS)

CommonJS is the module system used in Node.js (before ESM support).

### Basic Exports/Require

**Single Export:**
```javascript
// math.js
function add(a, b) {
  return a + b;
}

module.exports = add;
```

```javascript
// app.js
const add = require('./math');

console.log(add(5, 3)); // 8
```

**Multiple Exports:**
```javascript
// math.js
const PI = 3.14159;

function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = {
  PI,
  add,
  subtract
};
```

```javascript
// app.js
const math = require('./math');

console.log(math.PI);           // 3.14159
console.log(math.add(5, 3));    // 8
console.log(math.subtract(5, 3)); // 2
```

**Destructuring:**
```javascript
// app.js
const { PI, add, subtract } = require('./math');

console.log(PI);           // 3.14159
console.log(add(5, 3));    // 8
console.log(subtract(5, 3)); // 2
```

### exports vs module.exports

**Using `exports` (shorthand):**
```javascript
// math.js
exports.add = function(a, b) {
  return a + b;
};

exports.subtract = function(a, b) {
  return a - b;
};
```

**Important:** Don't reassign `exports`:
```javascript
// ❌ WRONG - This won't work
exports = {
  add: function(a, b) { return a + b; }
};

// ✅ CORRECT - Use module.exports
module.exports = {
  add: function(a, b) { return a + b; }
};
```

### Caching

CommonJS modules are cached after first load:

```javascript
// counter.js
let count = 0;

module.exports = {
  increment: () => ++count,
  getCount: () => count
};
```

```javascript
// app.js
const counter1 = require('./counter');
const counter2 = require('./counter');

counter1.increment();
console.log(counter1.getCount()); // 1
console.log(counter2.getCount()); // 1 (same instance)
```

---

## ESM vs CommonJS

### Key Differences

| Feature | ESM | CommonJS |
|---------|-----|----------|
| **Syntax** | `import`/`export` | `require()`/`module.exports` |
| **Loading** | Asynchronous | Synchronous |
| **Static** | Yes (analyzed at parse time) | No (executed at runtime) |
| **Tree Shaking** | Yes | No |
| **Top-level await** | Yes | No |
| **Browser** | Native support | Needs bundler |
| **Node.js** | Supported (>12.x) | Default |
| **Dynamic imports** | Yes | Yes |
| **File extension** | `.js`, `.mjs` | `.js`, `.cjs` |

### Loading Behavior

**ESM (Static):**
```javascript
// ✅ Works - imports are hoisted
console.log(add(5, 3));

import { add } from './math.js';
```

**CommonJS (Dynamic):**
```javascript
// ❌ Error - require is not hoisted
console.log(add(5, 3)); // ReferenceError

const { add } = require('./math');
```

### Conditional Loading

**ESM:**
```javascript
// Must use dynamic import
if (condition) {
  const module = await import('./module.js');
}
```

**CommonJS:**
```javascript
// Can use require anywhere
if (condition) {
  const module = require('./module');
}
```

### Tree Shaking

**ESM enables tree shaking:**
```javascript
// utils.js
export function usedFunction() { /* ... */ }
export function unusedFunction() { /* ... */ }
```

```javascript
// app.js
import { usedFunction } from './utils.js';
// Bundlers can remove unusedFunction
```

### Interoperability

**Using CommonJS in ESM:**
```javascript
// ESM file
import cjsModule from './commonjs-module.cjs';

const { something } = cjsModule;
```

**Using ESM in CommonJS:**
```javascript
// CommonJS file
(async () => {
  const esmModule = await import('./esm-module.mjs');
  console.log(esmModule.default);
})();
```

---

## Best Practices

### 1. Use ESM for New Projects

```javascript
// ✅ Recommended
export function helper() { /* ... */ }

// ❌ Avoid for new projects
module.exports = function helper() { /* ... */ };
```

### 2. Be Consistent

Don't mix ESM and CommonJS in the same file:

```javascript
// ❌ WRONG
import something from './module.js';
module.exports = something;
```

### 3. Use Named Exports When Possible

```javascript
// ✅ Better - more explicit
export function add(a, b) { return a + b; }
export function subtract(a, b) { return a - b; }

// ⚠️ Less clear
export default {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b
};
```

### 4. Default Exports for Classes/Main Functions

```javascript
// ✅ Good use of default export
export default class User {
  constructor(name) {
    this.name = name;
  }
}
```

### 5. Organize Exports in index.js

```javascript
// src/utils/index.js
export { add, subtract } from './math.js';
export { capitalize, lowercase } from './string.js';
export { default as User } from './User.js';
```

```javascript
// app.js
import { add, capitalize, User } from './utils';
```

### 6. Use Dynamic Imports for Code Splitting

```javascript
// Load heavy modules only when needed
async function handleAction() {
  const { heavyFunction } = await import('./heavy-module.js');
  heavyFunction();
}
```

---

## Key Takeaways

- **ESM** is the modern standard and preferred for new projects
- **CommonJS** is still widely used in Node.js legacy code
- ESM enables better optimization (tree shaking, static analysis)
- Use dynamic imports for code splitting and lazy loading
- Be aware of the differences when working with both systems
- Most modern tools support both, but ESM is the future 