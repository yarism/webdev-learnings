# Objects & Prototypes

Understanding JavaScript's object model and prototypal inheritance.

## Table of Contents
- [Objects](#objects)
- [Prototype Chain](#prototype-chain)
- [Prototypal Inheritance](#prototypal-inheritance)

---

## Objects

Objects are collections of key-value pairs and are fundamental to JavaScript.

### Creating Objects

**Object Literal:**
```javascript
const person = {
  name: "Alice",
  age: 30,
  greet: function() {
    console.log(`Hello, I'm ${this.name}`);
  }
};
```

**Constructor Function:**
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    console.log(`Hello, I'm ${this.name}`);
  };
}

const person1 = new Person("Bob", 25);
```

**`Object.create()`:**
```javascript
const personProto = {
  greet: function() {
    console.log(`Hello, I'm ${this.name}`);
  }
};

const person = Object.create(personProto);
person.name = "Charlie";
person.age = 28;
```

**ES6 Classes:**
```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
}

const person = new Person("Dana", 32);
```

### Accessing Properties

```javascript
const user = {
  name: "Eve",
  age: 27,
  "favorite-color": "blue"
};

console.log(user.name);              // Dot notation
console.log(user["age"]);            // Bracket notation
console.log(user["favorite-color"]); // Bracket notation for special characters
```

### Object Methods

**`Object.keys()`:**
```javascript
const obj = { a: 1, b: 2, c: 3 };
console.log(Object.keys(obj)); // ["a", "b", "c"]
```

**`Object.values()`:**
```javascript
console.log(Object.values(obj)); // [1, 2, 3]
```

**`Object.entries()`:**
```javascript
console.log(Object.entries(obj)); // [["a", 1], ["b", 2], ["c", 3]]
```

**`Object.assign()`:**
```javascript
const target = { a: 1 };
const source = { b: 2, c: 3 };
Object.assign(target, source);
console.log(target); // { a: 1, b: 2, c: 3 }
```

**Spread Operator:**
```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }
```

### Property Descriptors

```javascript
const person = {};

Object.defineProperty(person, 'name', {
  value: "Frank",
  writable: false,      // Cannot be changed
  enumerable: true,     // Shows up in loops
  configurable: false   // Cannot be deleted or reconfigured
});

person.name = "George"; // Silently fails (throws in strict mode)
console.log(person.name); // "Frank"
```

### Getters and Setters

```javascript
const person = {
  firstName: "Hannah",
  lastName: "Smith",
  
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  
  set fullName(name) {
    const parts = name.split(' ');
    this.firstName = parts[0];
    this.lastName = parts[1];
  }
};

console.log(person.fullName);  // "Hannah Smith"
person.fullName = "Ian Jones";
console.log(person.firstName); // "Ian"
console.log(person.lastName);  // "Jones"
```

---

## Prototype Chain

Every JavaScript object has an internal link to another object called its prototype. This creates a chain of objects.

### Understanding `__proto__` and `prototype`

**`prototype`** is a property of constructor functions:
```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  console.log(`${this.name} makes a sound`);
};

const dog = new Animal("Dog");
```

**`__proto__`** is the actual prototype object that the instance inherits from:
```javascript
console.log(dog.__proto__ === Animal.prototype); // true
```

### Prototype Chain Lookup

When accessing a property, JavaScript looks up the prototype chain:

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  console.log(`Hello, I'm ${this.name}`);
};

const alice = new Person("Alice");

console.log(alice.name);        // Found on instance
alice.greet();                  // Found on Person.prototype
console.log(alice.toString());  // Found on Object.prototype
```

**Lookup order:**
1. `alice` object itself
2. `Person.prototype`
3. `Object.prototype`
4. `null` (end of chain)

### Checking Prototypes

**`Object.getPrototypeOf()`:**
```javascript
console.log(Object.getPrototypeOf(alice) === Person.prototype); // true
```

**`instanceof`:**
```javascript
console.log(alice instanceof Person); // true
console.log(alice instanceof Object); // true
```

**`hasOwnProperty()`:**
```javascript
console.log(alice.hasOwnProperty('name'));  // true (own property)
console.log(alice.hasOwnProperty('greet')); // false (on prototype)
```

**`in` operator:**
```javascript
console.log('name' in alice);  // true
console.log('greet' in alice); // true (checks prototype chain)
```

### Modifying Prototypes

```javascript
function Car(model) {
  this.model = model;
}

Car.prototype.drive = function() {
  console.log(`${this.model} is driving`);
};

const car1 = new Car("Tesla");
const car2 = new Car("BMW");

car1.drive(); // "Tesla is driving"
car2.drive(); // "BMW is driving"

Car.prototype.honk = function() {
  console.log("Beep beep!");
};

car1.honk(); // "Beep beep!" - available to existing instances
car2.honk(); // "Beep beep!"
```

---

## Prototypal Inheritance

JavaScript uses prototypal inheritance, where objects inherit directly from other objects.

### Constructor Function Inheritance

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log(`${this.name} barks!`);
};

const myDog = new Dog("Buddy", "Golden Retriever");
myDog.eat();  // "Buddy is eating" (inherited from Animal)
myDog.bark(); // "Buddy barks!" (own method)
```

### ES6 Class Inheritance

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  eat() {
    console.log(`${this.name} is eating`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  
  bark() {
    console.log(`${this.name} barks!`);
  }
}

const myDog = new Dog("Max", "Labrador");
myDog.eat();  // "Max is eating"
myDog.bark(); // "Max barks!"
```

### Object.create() Pattern

```javascript
const animal = {
  eat: function() {
    console.log(`${this.name} is eating`);
  }
};

const dog = Object.create(animal);
dog.name = "Rex";
dog.bark = function() {
  console.log(`${this.name} barks!`);
};

dog.eat();  // "Rex is eating"
dog.bark(); // "Rex barks!"
```

### Composition Over Inheritance

Modern JavaScript often favors composition:

```javascript
const canEat = {
  eat: function() {
    console.log(`${this.name} is eating`);
  }
};

const canWalk = {
  walk: function() {
    console.log(`${this.name} is walking`);
  }
};

const canSwim = {
  swim: function() {
    console.log(`${this.name} is swimming`);
  }
};

function createDog(name) {
  return {
    name,
    ...canEat,
    ...canWalk
  };
}

function createFish(name) {
  return {
    name,
    ...canEat,
    ...canSwim
  };
}

const dog = createDog("Buddy");
dog.eat();  // "Buddy is eating"
dog.walk(); // "Buddy is walking"

const fish = createFish("Nemo");
fish.eat();  // "Nemo is eating"
fish.swim(); // "Nemo is swimming"
```

### Method Overriding

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} barks`);
  }
}

class Cat extends Animal {
  speak() {
    super.speak(); // Call parent method
    console.log(`${this.name} meows`);
  }
}

const dog = new Dog("Buddy");
dog.speak(); // "Buddy barks"

const cat = new Cat("Whiskers");
cat.speak();
// "Whiskers makes a sound"
// "Whiskers meows"
```

### Static Methods

```javascript
class MathHelper {
  static add(a, b) {
    return a + b;
  }
  
  static multiply(a, b) {
    return a * b;
  }
}

console.log(MathHelper.add(5, 3));      // 8
console.log(MathHelper.multiply(4, 2)); // 8
```

---

## Key Takeaways

- **Objects**: Collections of key-value pairs; fundamental building blocks in JavaScript
- **Prototype Chain**: Every object has a link to its prototype, forming a chain for property lookup
- **`prototype` vs `__proto__`**: `prototype` is on constructor functions, `__proto__` is the actual prototype object
- **Inheritance**: JavaScript uses prototypal inheritance; objects inherit from other objects
- **ES6 Classes**: Syntactic sugar over prototypal inheritance, not true classical inheritance
- **Composition**: Often preferred over inheritance for more flexible and maintainable code 