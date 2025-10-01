# Array & Object Methods

Essential built-in methods for data manipulation in JavaScript.

## Table of Contents
- [Array Methods](#array-methods)
- [String Methods](#string-methods)
- [Object Methods](#object-methods)
- [Number Methods](#number-methods)
- [Chaining Methods](#chaining-methods)
- [Practical Examples](#practical-examples)

---

## Array Methods

Essential methods for working with arrays.

### Iteration Methods

**forEach()** - Execute a function for each element (no return value):
```javascript
const numbers = [1, 2, 3, 4, 5];

numbers.forEach((num, index, array) => {
  console.log(`Index ${index}: ${num}`);
});
```

**map()** - Transform each element and return new array:
```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

const users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 }
];
const names = users.map(user => user.name);
console.log(names); // ['Alice', 'Bob']
```

**filter()** - Return new array with elements that pass test:
```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4, 6]

const users = [
  { name: 'Alice', age: 25, active: true },
  { name: 'Bob', age: 30, active: false },
  { name: 'Charlie', age: 35, active: true }
];
const activeUsers = users.filter(user => user.active);
console.log(activeUsers); // [{ name: 'Alice', ... }, { name: 'Charlie', ... }]
```

**reduce()** - Reduce array to single value:
```javascript
const numbers = [1, 2, 3, 4, 5];

const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum); // 15

const max = numbers.reduce((max, num) => num > max ? num : max, numbers[0]);
console.log(max); // 5

const cart = [
  { name: 'Apple', price: 1.5, quantity: 2 },
  { name: 'Banana', price: 0.5, quantity: 3 },
  { name: 'Orange', price: 2, quantity: 1 }
];
const total = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
console.log(total); // 6.5
```

**reduceRight()** - Same as reduce but from right to left:
```javascript
const numbers = [1, 2, 3, 4, 5];
const result = numbers.reduceRight((acc, num) => acc + num, 0);
console.log(result); // 15
```

### Search Methods

**find()** - Return first element that passes test:
```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
];

const user = users.find(user => user.id === 2);
console.log(user); // { id: 2, name: 'Bob' }

const notFound = users.find(user => user.id === 99);
console.log(notFound); // undefined
```

**findIndex()** - Return index of first element that passes test:
```javascript
const numbers = [5, 12, 8, 130, 44];

const index = numbers.findIndex(num => num > 10);
console.log(index); // 1

const notFound = numbers.findIndex(num => num > 200);
console.log(notFound); // -1
```

**findLast()** - Return last element that passes test (ES2023):
```javascript
const numbers = [5, 12, 8, 130, 44];
const last = numbers.findLast(num => num > 10);
console.log(last); // 44
```

**findLastIndex()** - Return index of last element that passes test (ES2023):
```javascript
const numbers = [5, 12, 8, 130, 44];
const index = numbers.findLastIndex(num => num > 10);
console.log(index); // 4
```

**indexOf()** - Return first index of element:
```javascript
const fruits = ['apple', 'banana', 'orange', 'banana'];

console.log(fruits.indexOf('banana')); // 1
console.log(fruits.indexOf('grape')); // -1
console.log(fruits.indexOf('banana', 2)); // 3 (start from index 2)
```

**lastIndexOf()** - Return last index of element:
```javascript
const fruits = ['apple', 'banana', 'orange', 'banana'];
console.log(fruits.lastIndexOf('banana')); // 3
```

**includes()** - Check if array contains element:
```javascript
const fruits = ['apple', 'banana', 'orange'];

console.log(fruits.includes('banana')); // true
console.log(fruits.includes('grape')); // false
```

### Test Methods

**some()** - Test if at least one element passes test:
```javascript
const numbers = [1, 2, 3, 4, 5];

console.log(numbers.some(num => num > 3)); // true
console.log(numbers.some(num => num > 10)); // false

const users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 17 }
];
const hasMinor = users.some(user => user.age < 18);
console.log(hasMinor); // true
```

**every()** - Test if all elements pass test:
```javascript
const numbers = [1, 2, 3, 4, 5];

console.log(numbers.every(num => num > 0)); // true
console.log(numbers.every(num => num > 3)); // false

const users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 }
];
const allAdults = users.every(user => user.age >= 18);
console.log(allAdults); // true
```

### Transformation Methods

**flat()** - Flatten nested arrays:
```javascript
const nested = [1, [2, 3], [4, [5, 6]]];

console.log(nested.flat()); // [1, 2, 3, 4, [5, 6]]
console.log(nested.flat(2)); // [1, 2, 3, 4, 5, 6]
console.log(nested.flat(Infinity)); // [1, 2, 3, 4, 5, 6]
```

**flatMap()** - Map then flatten (one level):
```javascript
const sentences = ['Hello world', 'How are you'];
const words = sentences.flatMap(sentence => sentence.split(' '));
console.log(words); // ['Hello', 'world', 'How', 'are', 'you']

const numbers = [1, 2, 3];
const result = numbers.flatMap(num => [num, num * 2]);
console.log(result); // [1, 2, 2, 4, 3, 6]
```

**join()** - Join array elements into string:
```javascript
const words = ['Hello', 'World'];
console.log(words.join(' ')); // "Hello World"
console.log(words.join('-')); // "Hello-World"
console.log(words.join('')); // "HelloWorld"
```

**concat()** - Merge arrays:
```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const combined = arr1.concat(arr2, arr3);
console.log(combined); // [1, 2, 3, 4, 5, 6]

const modern = [...arr1, ...arr2, ...arr3];
console.log(modern); // [1, 2, 3, 4, 5, 6]
```

### Manipulation Methods

**slice()** - Extract portion of array (immutable):
```javascript
const fruits = ['apple', 'banana', 'orange', 'mango', 'grape'];

console.log(fruits.slice(1, 3)); // ['banana', 'orange']
console.log(fruits.slice(2)); // ['orange', 'mango', 'grape']
console.log(fruits.slice(-2)); // ['mango', 'grape']
console.log(fruits); // Original unchanged
```

**splice()** - Add/remove elements (mutable):
```javascript
const fruits = ['apple', 'banana', 'orange', 'mango'];

fruits.splice(2, 1);
console.log(fruits); // ['apple', 'banana', 'mango']

fruits.splice(1, 0, 'kiwi', 'lime');
console.log(fruits); // ['apple', 'kiwi', 'lime', 'banana', 'mango']

const removed = fruits.splice(1, 2, 'grape');
console.log(removed); // ['kiwi', 'lime']
console.log(fruits); // ['apple', 'grape', 'banana', 'mango']
```

**sort()** - Sort array (mutable):
```javascript
const numbers = [3, 1, 4, 1, 5, 9, 2, 6];

numbers.sort((a, b) => a - b);
console.log(numbers); // [1, 1, 2, 3, 4, 5, 6, 9]

numbers.sort((a, b) => b - a);
console.log(numbers); // [9, 6, 5, 4, 3, 2, 1, 1]

const users = [
  { name: 'Charlie', age: 35 },
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 }
];

users.sort((a, b) => a.age - b.age);
users.sort((a, b) => a.name.localeCompare(b.name));
```

**reverse()** - Reverse array (mutable):
```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.reverse();
console.log(numbers); // [5, 4, 3, 2, 1]

const immutable = [...numbers].reverse();
```

**fill()** - Fill array with value:
```javascript
const arr = new Array(5).fill(0);
console.log(arr); // [0, 0, 0, 0, 0]

const numbers = [1, 2, 3, 4, 5];
numbers.fill(0, 2, 4);
console.log(numbers); // [1, 2, 0, 0, 5]
```

**copyWithin()** - Copy part of array to another location:
```javascript
const arr = [1, 2, 3, 4, 5];
arr.copyWithin(0, 3);
console.log(arr); // [4, 5, 3, 4, 5]
```

### Stack/Queue Methods

**push()** - Add to end:
```javascript
const fruits = ['apple', 'banana'];
fruits.push('orange');
console.log(fruits); // ['apple', 'banana', 'orange']

fruits.push('mango', 'grape');
console.log(fruits); // ['apple', 'banana', 'orange', 'mango', 'grape']
```

**pop()** - Remove from end:
```javascript
const fruits = ['apple', 'banana', 'orange'];
const last = fruits.pop();
console.log(last); // 'orange'
console.log(fruits); // ['apple', 'banana']
```

**unshift()** - Add to beginning:
```javascript
const fruits = ['banana', 'orange'];
fruits.unshift('apple');
console.log(fruits); // ['apple', 'banana', 'orange']
```

**shift()** - Remove from beginning:
```javascript
const fruits = ['apple', 'banana', 'orange'];
const first = fruits.shift();
console.log(first); // 'apple'
console.log(fruits); // ['banana', 'orange']
```

### Static Methods

**Array.isArray()** - Check if value is array:
```javascript
console.log(Array.isArray([1, 2, 3])); // true
console.log(Array.isArray('hello')); // false
console.log(Array.isArray({ length: 0 })); // false
```

**Array.from()** - Create array from iterable:
```javascript
const str = 'hello';
const chars = Array.from(str);
console.log(chars); // ['h', 'e', 'l', 'l', 'o']

const range = Array.from({ length: 5 }, (_, i) => i + 1);
console.log(range); // [1, 2, 3, 4, 5]

const set = new Set([1, 2, 2, 3]);
const arr = Array.from(set);
console.log(arr); // [1, 2, 3]
```

**Array.of()** - Create array from arguments:
```javascript
const arr1 = Array.of(7);
console.log(arr1); // [7]

const arr2 = Array.of(1, 2, 3);
console.log(arr2); // [1, 2, 3]
```

---

## String Methods

Essential methods for working with strings.

### Search Methods

```javascript
const text = "Hello World, Welcome to JavaScript";

text.includes('World');
text.startsWith('Hello');
text.endsWith('Script');
text.indexOf('World');
text.lastIndexOf('o');
text.search(/world/i);
```

### Extraction Methods

```javascript
const text = "Hello World";

text.slice(0, 5);
text.substring(6, 11);
text.substr(6, 5);
text.charAt(0);
text.charCodeAt(0);
text[0];
```

### Transformation Methods

```javascript
const text = "  Hello World  ";

text.toLowerCase();
text.toUpperCase();
text.trim();
text.trimStart();
text.trimEnd();

text.replace('World', 'JavaScript');
text.replaceAll('o', '0');

text.split(' ');
text.repeat(3);
text.padStart(20, '*');
text.padEnd(20, '*');
```

### Template Literals

```javascript
const name = 'Alice';
const age = 25;

const greeting = `Hello, ${name}! You are ${age} years old.`;

const multiline = `
  Line 1
  Line 2
  Line 3
`;

const expression = `1 + 1 = ${1 + 1}`;
```

---

## Object Methods

Essential methods for working with objects.

### Object.keys()

Get array of object's keys:
```javascript
const user = { name: 'Alice', age: 25, city: 'NYC' };

const keys = Object.keys(user);
console.log(keys); // ['name', 'age', 'city']

keys.forEach(key => {
  console.log(`${key}: ${user[key]}`);
});
```

### Object.values()

Get array of object's values:
```javascript
const user = { name: 'Alice', age: 25, city: 'NYC' };

const values = Object.values(user);
console.log(values); // ['Alice', 25, 'NYC']
```

### Object.entries()

Get array of [key, value] pairs:
```javascript
const user = { name: 'Alice', age: 25, city: 'NYC' };

const entries = Object.entries(user);
console.log(entries);
// [['name', 'Alice'], ['age', 25], ['city', 'NYC']]

entries.forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});

const obj = Object.fromEntries(entries);
```

### Object.assign()

Copy properties to target object:
```javascript
const target = { a: 1, b: 2 };
const source = { b: 3, c: 4 };

Object.assign(target, source);
console.log(target); // { a: 1, b: 3, c: 4 }

const clone = Object.assign({}, target);

const merged = Object.assign({}, obj1, obj2, obj3);
```

### Object.freeze()

Prevent modifications:
```javascript
const obj = { name: 'Alice' };
Object.freeze(obj);

obj.name = 'Bob';
console.log(obj.name); // 'Alice' (unchanged)

obj.age = 25;
console.log(obj.age); // undefined (not added)
```

### Object.seal()

Prevent adding/removing properties:
```javascript
const obj = { name: 'Alice' };
Object.seal(obj);

obj.name = 'Bob';
console.log(obj.name); // 'Bob' (can modify)

obj.age = 25;
console.log(obj.age); // undefined (cannot add)
```

### Object.hasOwnProperty()

Check if object has property:
```javascript
const user = { name: 'Alice', age: 25 };

console.log(user.hasOwnProperty('name')); // true
console.log(user.hasOwnProperty('email')); // false
console.log('name' in user); // true
```

---

## Number Methods

### Parsing

```javascript
parseInt('42');
parseInt('42.5');
parseFloat('42.5');

Number('42');
Number('42.5');
Number('hello');
```

### Checking

```javascript
Number.isNaN(NaN);
Number.isFinite(100);
Number.isInteger(42);
Number.isSafeInteger(42);
```

### Formatting

```javascript
const num = 1234.5678;

num.toFixed(2);
num.toPrecision(4);
num.toExponential(2);
num.toLocaleString('en-US');
```

---

## Chaining Methods

Combine multiple methods for complex operations.

### Array Chaining

```javascript
const users = [
  { name: 'Alice', age: 25, active: true },
  { name: 'Bob', age: 30, active: false },
  { name: 'Charlie', age: 35, active: true },
  { name: 'Dave', age: 28, active: true }
];

const result = users
  .filter(user => user.active)
  .map(user => ({ ...user, age: user.age + 1 }))
  .sort((a, b) => a.age - b.age)
  .map(user => user.name);

console.log(result); // ['Alice', 'Dave', 'Charlie']
```

### String Chaining

```javascript
const text = "  Hello World  ";

const result = text
  .trim()
  .toLowerCase()
  .split(' ')
  .map(word => word.charAt(0).toUpperCase() + word.slice(1))
  .join(' ');

console.log(result); // "Hello World"
```

---

## Practical Examples

### Group By Property

```javascript
const items = [
  { category: 'fruit', name: 'apple' },
  { category: 'vegetable', name: 'carrot' },
  { category: 'fruit', name: 'banana' },
  { category: 'vegetable', name: 'broccoli' }
];

const grouped = items.reduce((acc, item) => {
  if (!acc[item.category]) {
    acc[item.category] = [];
  }
  acc[item.category].push(item.name);
  return acc;
}, {});

console.log(grouped);
// {
//   fruit: ['apple', 'banana'],
//   vegetable: ['carrot', 'broccoli']
// }
```

### Remove Duplicates

```javascript
const numbers = [1, 2, 2, 3, 4, 4, 5];

const unique1 = [...new Set(numbers)];

const unique2 = numbers.filter((num, index, arr) => arr.indexOf(num) === index);

console.log(unique1); // [1, 2, 3, 4, 5]
```

### Count Occurrences

```javascript
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];

const count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});

console.log(count);
// { apple: 3, banana: 2, orange: 1 }
```

### Array to Object

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
];

const usersById = users.reduce((acc, user) => {
  acc[user.id] = user;
  return acc;
}, {});

const usersById2 = Object.fromEntries(
  users.map(user => [user.id, user])
);
```

### Flatten Nested Object

```javascript
function flattenObject(obj, prefix = '') {
  return Object.keys(obj).reduce((acc, key) => {
    const fullKey = prefix ? `${prefix}.${key}` : key;
    
    if (typeof obj[key] === 'object' && obj[key] !== null) {
      Object.assign(acc, flattenObject(obj[key], fullKey));
    } else {
      acc[fullKey] = obj[key];
    }
    
    return acc;
  }, {});
}

const nested = {
  user: {
    name: 'Alice',
    address: {
      city: 'NYC',
      zip: '10001'
    }
  }
};

console.log(flattenObject(nested));
// {
//   'user.name': 'Alice',
//   'user.address.city': 'NYC',
//   'user.address.zip': '10001'
// }
```

### Pagination

```javascript
function paginate(array, pageSize, pageNumber) {
  const start = (pageNumber - 1) * pageSize;
  return array.slice(start, start + pageSize);
}

const items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
console.log(paginate(items, 3, 1)); // [1, 2, 3]
console.log(paginate(items, 3, 2)); // [4, 5, 6]
```

### Chunk Array

```javascript
function chunk(array, size) {
  return Array.from(
    { length: Math.ceil(array.length / size) },
    (_, i) => array.slice(i * size, i * size + size)
  );
}

const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(chunk(numbers, 3));
// [[1, 2, 3], [4, 5, 6], [7, 8]]
```

---

## Key Takeaways

- **map()**: Transform each element → new array
- **filter()**: Select elements that pass test → new array
- **reduce()**: Reduce to single value → any type
- **forEach()**: Execute function for each element → undefined
- **find()**: Find first matching element → element or undefined
- **some()**: Test if at least one passes → boolean
- **every()**: Test if all pass → boolean
- **Immutable**: map, filter, slice, concat don't modify original
- **Mutable**: push, pop, splice, sort, reverse modify original
- **Chain methods**: Combine for powerful transformations
- **Choose wisely**: Use the right method for the job 