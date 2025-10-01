# JavaScript Design Patterns

Common design patterns for writing maintainable and scalable JavaScript code.

## Table of Contents
- [What are Design Patterns?](#what-are-design-patterns)
- [Factory Pattern](#factory-pattern)
- [Singleton Pattern](#singleton-pattern)
- [Observer Pattern](#observer-pattern)
- [Module Pattern](#module-pattern)
- [Other Common Patterns](#other-common-patterns)

---

## What are Design Patterns?

Design patterns are reusable solutions to commonly occurring problems in software design. They provide tested, proven development paradigms.

**Benefits:**
- Code reusability
- Easier maintenance
- Common vocabulary for developers
- Tested solutions to common problems

---

## Factory Pattern

The Factory Pattern creates objects without specifying the exact class to create.

### Basic Factory

```javascript
function createUser(name, role) {
  return {
    name,
    role,
    permissions: role === 'admin' ? ['read', 'write', 'delete'] : ['read']
  };
}

const admin = createUser('Alice', 'admin');
const user = createUser('Bob', 'user');

console.log(admin.permissions); // ['read', 'write', 'delete']
console.log(user.permissions);  // ['read']
```

### Class-Based Factory

```javascript
class User {
  constructor(name, permissions) {
    this.name = name;
    this.permissions = permissions;
  }
}

class Admin extends User {
  constructor(name) {
    super(name, ['read', 'write', 'delete']);
    this.role = 'admin';
  }
}

class Moderator extends User {
  constructor(name) {
    super(name, ['read', 'write']);
    this.role = 'moderator';
  }
}

class RegularUser extends User {
  constructor(name) {
    super(name, ['read']);
    this.role = 'user';
  }
}

class UserFactory {
  static createUser(name, role) {
    switch (role) {
      case 'admin':
        return new Admin(name);
      case 'moderator':
        return new Moderator(name);
      default:
        return new RegularUser(name);
    }
  }
}

const alice = UserFactory.createUser('Alice', 'admin');
const bob = UserFactory.createUser('Bob', 'moderator');
const charlie = UserFactory.createUser('Charlie', 'user');
```

### Real-World Example: UI Components

```javascript
class Button {
  constructor(text, style) {
    this.text = text;
    this.style = style;
  }
  
  render() {
    return `<button class="${this.style}">${this.text}</button>`;
  }
}

class Link {
  constructor(text, url, style) {
    this.text = text;
    this.url = url;
    this.style = style;
  }
  
  render() {
    return `<a href="${this.url}" class="${this.style}">${this.text}</a>`;
  }
}

class ComponentFactory {
  static create(type, config) {
    switch (type) {
      case 'button':
        return new Button(config.text, config.style);
      case 'link':
        return new Link(config.text, config.url, config.style);
      default:
        throw new Error(`Unknown component type: ${type}`);
    }
  }
}

const saveButton = ComponentFactory.create('button', {
  text: 'Save',
  style: 'btn-primary'
});

const homeLink = ComponentFactory.create('link', {
  text: 'Home',
  url: '/',
  style: 'nav-link'
});
```

---

## Singleton Pattern

The Singleton Pattern ensures a class has only one instance and provides a global access point to it.

### Basic Singleton

```javascript
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }
    
    this.connection = null;
    Database.instance = this;
  }
  
  connect() {
    if (!this.connection) {
      this.connection = 'Connected to database';
      console.log(this.connection);
    }
    return this.connection;
  }
  
  query(sql) {
    return `Executing: ${sql}`;
  }
}

const db1 = new Database();
const db2 = new Database();

console.log(db1 === db2); // true (same instance)
```

### Module Singleton

```javascript
let instance = null;

class ConfigManager {
  constructor() {
    if (instance) {
      throw new Error('Use ConfigManager.getInstance()');
    }
    
    this.config = {
      apiUrl: 'https://api.example.com',
      timeout: 5000
    };
  }
  
  static getInstance() {
    if (!instance) {
      instance = new ConfigManager();
    }
    return instance;
  }
  
  get(key) {
    return this.config[key];
  }
  
  set(key, value) {
    this.config[key] = value;
  }
}

const config1 = ConfigManager.getInstance();
const config2 = ConfigManager.getInstance();

config1.set('theme', 'dark');
console.log(config2.get('theme')); // 'dark'
console.log(config1 === config2);  // true
```

### Object Literal Singleton

```javascript
const Logger = {
  logs: [],
  
  log(message) {
    const timestamp = new Date().toISOString();
    this.logs.push({ timestamp, message, level: 'info' });
    console.log(`[${timestamp}] INFO: ${message}`);
  },
  
  error(message) {
    const timestamp = new Date().toISOString();
    this.logs.push({ timestamp, message, level: 'error' });
    console.error(`[${timestamp}] ERROR: ${message}`);
  },
  
  getLogs() {
    return this.logs;
  },
  
  clearLogs() {
    this.logs = [];
  }
};

Logger.log('Application started');
Logger.error('Something went wrong');
console.log(Logger.getLogs());
```

---

## Observer Pattern

The Observer Pattern defines a one-to-many dependency between objects. When one object changes state, all its dependents are notified.

### Basic Observer

```javascript
class Subject {
  constructor() {
    this.observers = [];
  }
  
  subscribe(observer) {
    this.observers.push(observer);
  }
  
  unsubscribe(observer) {
    this.observers = this.observers.filter(obs => obs !== observer);
  }
  
  notify(data) {
    this.observers.forEach(observer => observer.update(data));
  }
}

class Observer {
  constructor(name) {
    this.name = name;
  }
  
  update(data) {
    console.log(`${this.name} received:`, data);
  }
}

const subject = new Subject();

const observer1 = new Observer('Observer 1');
const observer2 = new Observer('Observer 2');

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.notify('Hello Observers!');

subject.unsubscribe(observer1);
subject.notify('Only Observer 2 will receive this');
```

### Event Emitter Pattern

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
  }
  
  off(event, listenerToRemove) {
    if (!this.events[event]) return;
    
    this.events[event] = this.events[event].filter(
      listener => listener !== listenerToRemove
    );
  }
  
  emit(event, data) {
    if (!this.events[event]) return;
    
    this.events[event].forEach(listener => listener(data));
  }
  
  once(event, listener) {
    const onceWrapper = (data) => {
      listener(data);
      this.off(event, onceWrapper);
    };
    this.on(event, onceWrapper);
  }
}

const emitter = new EventEmitter();

function handleUserLogin(user) {
  console.log(`User logged in: ${user.name}`);
}

function handleUserLogout(user) {
  console.log(`User logged out: ${user.name}`);
}

emitter.on('login', handleUserLogin);
emitter.on('logout', handleUserLogout);

emitter.emit('login', { name: 'Alice' });
emitter.emit('logout', { name: 'Alice' });

emitter.once('welcome', (message) => {
  console.log(message);
});

emitter.emit('welcome', 'Welcome!');
emitter.emit('welcome', 'This will not be logged');
```

### Real-World Example: Store

```javascript
class Store {
  constructor(initialState = {}) {
    this.state = initialState;
    this.listeners = [];
  }
  
  getState() {
    return this.state;
  }
  
  setState(newState) {
    this.state = { ...this.state, ...newState };
    this.notify();
  }
  
  subscribe(listener) {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }
  
  notify() {
    this.listeners.forEach(listener => listener(this.state));
  }
}

const store = new Store({ count: 0, user: null });

const unsubscribe1 = store.subscribe((state) => {
  console.log('Listener 1 - Count:', state.count);
});

const unsubscribe2 = store.subscribe((state) => {
  console.log('Listener 2 - User:', state.user);
});

store.setState({ count: 1 });
store.setState({ user: { name: 'Alice' } });

unsubscribe1();
store.setState({ count: 2 });
```

---

## Module Pattern

The Module Pattern encapsulates private and public members.

### IIFE Module

```javascript
const Calculator = (function() {
  let result = 0;
  
  function add(x) {
    result += x;
    return this;
  }
  
  function subtract(x) {
    result -= x;
    return this;
  }
  
  function getResult() {
    return result;
  }
  
  function reset() {
    result = 0;
    return this;
  }
  
  return {
    add,
    subtract,
    getResult,
    reset
  };
})();

Calculator.add(5).add(3).subtract(2);
console.log(Calculator.getResult()); // 6
```

### ES6 Module Pattern

```javascript
let count = 0;

function increment() {
  count++;
}

function decrement() {
  count--;
}

function getCount() {
  return count;
}

export { increment, decrement, getCount };
```

---

## Other Common Patterns

### Revealing Module Pattern

```javascript
const UserManager = (function() {
  const users = [];
  
  function addUser(user) {
    users.push(user);
  }
  
  function removeUser(userId) {
    const index = users.findIndex(u => u.id === userId);
    if (index > -1) {
      users.splice(index, 1);
    }
  }
  
  function getUser(userId) {
    return users.find(u => u.id === userId);
  }
  
  function getAllUsers() {
    return [...users];
  }
  
  return {
    add: addUser,
    remove: removeUser,
    get: getUser,
    getAll: getAllUsers
  };
})();

UserManager.add({ id: 1, name: 'Alice' });
UserManager.add({ id: 2, name: 'Bob' });
console.log(UserManager.getAll());
```

### Prototype Pattern

```javascript
const carPrototype = {
  drive() {
    console.log(`${this.model} is driving`);
  },
  
  stop() {
    console.log(`${this.model} stopped`);
  }
};

function createCar(model, year) {
  const car = Object.create(carPrototype);
  car.model = model;
  car.year = year;
  return car;
}

const car1 = createCar('Tesla Model 3', 2023);
const car2 = createCar('BMW i4', 2023);

car1.drive();
car2.drive();
```

### Builder Pattern

```javascript
class QueryBuilder {
  constructor() {
    this.query = {
      select: '*',
      from: null,
      where: [],
      orderBy: null,
      limit: null
    };
  }
  
  select(fields) {
    this.query.select = fields;
    return this;
  }
  
  from(table) {
    this.query.from = table;
    return this;
  }
  
  where(condition) {
    this.query.where.push(condition);
    return this;
  }
  
  orderBy(field, direction = 'ASC') {
    this.query.orderBy = `${field} ${direction}`;
    return this;
  }
  
  limit(count) {
    this.query.limit = count;
    return this;
  }
  
  build() {
    let sql = `SELECT ${this.query.select} FROM ${this.query.from}`;
    
    if (this.query.where.length > 0) {
      sql += ` WHERE ${this.query.where.join(' AND ')}`;
    }
    
    if (this.query.orderBy) {
      sql += ` ORDER BY ${this.query.orderBy}`;
    }
    
    if (this.query.limit) {
      sql += ` LIMIT ${this.query.limit}`;
    }
    
    return sql;
  }
}

const query = new QueryBuilder()
  .select('id, name, email')
  .from('users')
  .where('age > 18')
  .where('active = true')
  .orderBy('name', 'ASC')
  .limit(10)
  .build();

console.log(query);
```

---

## Key Takeaways

- **Factory Pattern**: Create objects without specifying exact class; great for complex object creation
- **Singleton Pattern**: Ensure only one instance exists; useful for shared resources
- **Observer Pattern**: One-to-many dependency; perfect for event systems and reactive programming
- **Module Pattern**: Encapsulate private/public members; organize code into logical units
- **Choose the right pattern**: Not every problem needs a pattern; use them when they add value
- **Patterns are guidelines**: Adapt them to fit your specific needs 