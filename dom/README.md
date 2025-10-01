# DOM Manipulation & Event Delegation

Understanding how to interact with the Document Object Model and handle events efficiently.

## Table of Contents
- [Selecting Elements](#selecting-elements)
- [Modifying Elements](#modifying-elements)
- [Creating and Removing Elements](#creating-and-removing-elements)
- [Event Handling](#event-handling)
- [Event Delegation](#event-delegation)
- [Best Practices](#best-practices)

---

## Selecting Elements

Different methods to select DOM elements.

### querySelector & querySelectorAll

```javascript
const header = document.querySelector('h1');
const firstButton = document.querySelector('.btn');
const submitBtn = document.querySelector('#submit-btn');

const allButtons = document.querySelectorAll('.btn');
allButtons.forEach(btn => {
  console.log(btn);
});
```

### getElementById, getElementsByClassName, getElementsByTagName

```javascript
const element = document.getElementById('main');
const buttons = document.getElementsByClassName('btn');
const paragraphs = document.getElementsByTagName('p');
```

### Traversing the DOM

```javascript
const element = document.querySelector('.item');

const parent = element.parentElement;
const children = element.children;
const firstChild = element.firstElementChild;
const lastChild = element.lastElementChild;

const nextSibling = element.nextElementSibling;
const prevSibling = element.previousElementSibling;
```

### Closest (Finding Parent Match)

```javascript
const button = document.querySelector('.delete-btn');

const card = button.closest('.card');
const container = button.closest('.container');
```

---

## Modifying Elements

Changing element content, attributes, and styles.

### Text Content

```javascript
const heading = document.querySelector('h1');

heading.textContent = 'New Heading';
heading.innerText = 'Another Heading';

heading.innerHTML = '<span>HTML</span> Heading';
```

### Attributes

```javascript
const link = document.querySelector('a');

link.getAttribute('href');
link.setAttribute('href', 'https://example.com');
link.removeAttribute('target');

link.href = 'https://example.com';
link.id = 'main-link';
```

### Data Attributes

```html
<div id="user" data-user-id="123" data-role="admin"></div>
```

```javascript
const user = document.querySelector('#user');

console.log(user.dataset.userId);  // "123"
console.log(user.dataset.role);    // "admin"

user.dataset.status = 'active';
```

### Classes

```javascript
const element = document.querySelector('.box');

element.classList.add('active');
element.classList.remove('hidden');
element.classList.toggle('highlight');
element.classList.contains('box');

element.classList.replace('old-class', 'new-class');

element.className = 'box active';
```

### Inline Styles

```javascript
const box = document.querySelector('.box');

box.style.backgroundColor = 'blue';
box.style.width = '200px';
box.style.padding = '20px';

box.style.cssText = 'background-color: blue; width: 200px; padding: 20px;';
```

### Computed Styles

```javascript
const element = document.querySelector('.box');
const styles = window.getComputedStyle(element);

console.log(styles.backgroundColor);
console.log(styles.width);
```

---

## Creating and Removing Elements

Adding and removing elements from the DOM.

### Creating Elements

```javascript
const div = document.createElement('div');
div.className = 'card';
div.id = 'user-card';
div.textContent = 'User Card';

const img = document.createElement('img');
img.src = 'image.jpg';
img.alt = 'Description';
```

### Adding Elements to DOM

```javascript
const container = document.querySelector('.container');
const newElement = document.createElement('div');

container.appendChild(newElement);
container.prepend(newElement);

container.insertBefore(newElement, container.firstChild);

const reference = document.querySelector('.reference');
reference.before(newElement);
reference.after(newElement);
```

### Removing Elements

```javascript
const element = document.querySelector('.to-remove');

element.remove();

element.parentElement.removeChild(element);
```

### Replacing Elements

```javascript
const oldElement = document.querySelector('.old');
const newElement = document.createElement('div');

oldElement.replaceWith(newElement);

parent.replaceChild(newElement, oldElement);
```

### Cloning Elements

```javascript
const original = document.querySelector('.template');

const clone = original.cloneNode(true);

document.body.appendChild(clone);
```

### innerHTML vs createElement

**Using innerHTML:**
```javascript
const container = document.querySelector('.container');

container.innerHTML = `
  <div class="card">
    <h2>Title</h2>
    <p>Description</p>
  </div>
`;
```

**Using createElement:**
```javascript
const container = document.querySelector('.container');

const card = document.createElement('div');
card.className = 'card';

const title = document.createElement('h2');
title.textContent = 'Title';

const description = document.createElement('p');
description.textContent = 'Description';

card.appendChild(title);
card.appendChild(description);

container.appendChild(card);
```

---

## Event Handling

Responding to user interactions.

### Adding Event Listeners

```javascript
const button = document.querySelector('#btn');

function handleClick(event) {
  console.log('Button clicked!');
  console.log(event.target);
}

button.addEventListener('click', handleClick);
```

### Common Events

```javascript
const input = document.querySelector('#input');
const form = document.querySelector('#form');

input.addEventListener('focus', (e) => {
  console.log('Input focused');
});

input.addEventListener('blur', (e) => {
  console.log('Input lost focus');
});

input.addEventListener('input', (e) => {
  console.log('Input value:', e.target.value);
});

input.addEventListener('change', (e) => {
  console.log('Input changed:', e.target.value);
});

form.addEventListener('submit', (e) => {
  e.preventDefault();
  console.log('Form submitted');
});
```

### Mouse Events

```javascript
const element = document.querySelector('.box');

element.addEventListener('click', (e) => {
  console.log('Clicked');
});

element.addEventListener('dblclick', (e) => {
  console.log('Double clicked');
});

element.addEventListener('mouseenter', (e) => {
  console.log('Mouse entered');
});

element.addEventListener('mouseleave', (e) => {
  console.log('Mouse left');
});

element.addEventListener('mousemove', (e) => {
  console.log(`Position: ${e.clientX}, ${e.clientY}`);
});
```

### Keyboard Events

```javascript
const input = document.querySelector('#input');

input.addEventListener('keydown', (e) => {
  console.log('Key down:', e.key);
  
  if (e.key === 'Enter') {
    console.log('Enter pressed');
  }
  
  if (e.ctrlKey && e.key === 's') {
    e.preventDefault();
    console.log('Ctrl+S pressed');
  }
});

input.addEventListener('keyup', (e) => {
  console.log('Key up:', e.key);
});
```

### Event Object

```javascript
element.addEventListener('click', (event) => {
  console.log(event.target);
  console.log(event.currentTarget);
  console.log(event.type);
  console.log(event.clientX, event.clientY);
  
  event.preventDefault();
  event.stopPropagation();
});
```

### Removing Event Listeners

```javascript
function handleClick() {
  console.log('Clicked');
}

button.addEventListener('click', handleClick);

button.removeEventListener('click', handleClick);
```

### Event Options

```javascript
element.addEventListener('click', handleClick, {
  once: true,
  capture: true,
  passive: true
});
```

---

## Event Delegation

Efficiently handling events on multiple elements.

### Problem: Multiple Event Listeners

```javascript
const buttons = document.querySelectorAll('.btn');

buttons.forEach(button => {
  button.addEventListener('click', (e) => {
    console.log('Button clicked');
  });
});
```

### Solution: Event Delegation

```javascript
const container = document.querySelector('.container');

container.addEventListener('click', (e) => {
  if (e.target.matches('.btn')) {
    console.log('Button clicked:', e.target);
  }
});
```

### Practical Example: Todo List

```javascript
const todoList = document.querySelector('#todo-list');

function handleTodoClick(event) {
  const target = event.target;
  
  if (target.matches('.delete-btn')) {
    const todoItem = target.closest('.todo-item');
    todoItem.remove();
  }
  
  if (target.matches('.complete-btn')) {
    const todoItem = target.closest('.todo-item');
    todoItem.classList.toggle('completed');
  }
  
  if (target.matches('.edit-btn')) {
    const todoItem = target.closest('.todo-item');
    editTodo(todoItem);
  }
}

todoList.addEventListener('click', handleTodoClick);
```

### Dynamic Elements

```javascript
const list = document.querySelector('#list');

list.addEventListener('click', (e) => {
  if (e.target.matches('.item')) {
    console.log('Item clicked:', e.target.textContent);
  }
});

function addItem(text) {
  const item = document.createElement('li');
  item.className = 'item';
  item.textContent = text;
  list.appendChild(item);
}

addItem('New item');
```

### Benefits of Event Delegation

1. **Performance**: Single listener instead of many
2. **Dynamic elements**: Works for elements added later
3. **Memory efficient**: Less memory usage
4. **Simpler cleanup**: One listener to remove

### Advanced Event Delegation

```javascript
const app = document.querySelector('#app');

function handleEvent(event) {
  const target = event.target;
  
  if (target.matches('[data-action="delete"]')) {
    handleDelete(target);
  } else if (target.matches('[data-action="edit"]')) {
    handleEdit(target);
  } else if (target.matches('[data-action="save"]')) {
    handleSave(target);
  }
}

function handleDelete(element) {
  const item = element.closest('.item');
  item.remove();
}

function handleEdit(element) {
  const item = element.closest('.item');
  item.classList.add('editing');
}

function handleSave(element) {
  const item = element.closest('.item');
  const input = item.querySelector('input');
  item.querySelector('.text').textContent = input.value;
  item.classList.remove('editing');
}

app.addEventListener('click', handleEvent);
```

---

## Best Practices

### 1. Cache DOM References

```javascript
const header = document.querySelector('#header');
const nav = document.querySelector('#nav');

function updateUI() {
  header.textContent = 'New Title';
  nav.classList.add('active');
}
```

### 2. Use Event Delegation for Lists

```javascript
document.querySelector('#list').addEventListener('click', (e) => {
  if (e.target.matches('.item')) {
  }
});
```

### 3. Prevent Memory Leaks

```javascript
const controller = new AbortController();

element.addEventListener('click', handler, {
  signal: controller.signal
});

controller.abort();
```

### 4. Batch DOM Updates

```javascript
const container = document.querySelector('.container');

const fragment = document.createDocumentFragment();

for (let i = 0; i < 100; i++) {
  const div = document.createElement('div');
  div.textContent = `Item ${i}`;
  fragment.appendChild(div);
}

container.appendChild(fragment);
```

### 5. Use Data Attributes for Configuration

```html
<button data-action="delete" data-id="123">Delete</button>
```

```javascript
function handleClick(event) {
  const target = event.target;
  const action = target.dataset.action;
  const id = target.dataset.id;
  
  if (action === 'delete') {
    deleteItem(id);
  }
}
```

### 6. Separate Logic from Event Handlers

```javascript
function handleButtonClick(event) {
  const data = getDataFromForm();
  processData(data);
}

function getDataFromForm() {
}

function processData(data) {
}

button.addEventListener('click', handleButtonClick);
```

---

## Key Takeaways

- **querySelector/querySelectorAll**: Modern and flexible element selection
- **Event delegation**: Attach listeners to parent elements for better performance
- **classList**: Use for class manipulation instead of className
- **dataset**: Clean way to work with data attributes
- **createDocumentFragment**: Batch DOM operations for better performance
- **preventDefault/stopPropagation**: Control event behavior
- **Cache DOM references**: Don't query the same element repeatedly 