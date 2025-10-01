# Performance Optimization

Techniques and best practices for optimizing JavaScript performance.

## Table of Contents
- [Code Optimization](#code-optimization)
- [Memory Management](#memory-management)
- [Rendering Performance](#rendering-performance)
- [Lazy Loading & Code Splitting](#lazy-loading--code-splitting)
- [Measurement & Profiling](#measurement--profiling)
- [Best Practices](#best-practices)

---

## Code Optimization

Writing efficient JavaScript code.

### Debouncing

Limit how often a function executes.

```javascript
function debounce(func, delay) {
  let timeoutId;
  
  return function(...args) {
    clearTimeout(timeoutId);
    
    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

const searchInput = document.querySelector('#search');

function handleSearch(event) {
  console.log('Searching for:', event.target.value);
}

const debouncedSearch = debounce(handleSearch, 300);
searchInput.addEventListener('input', debouncedSearch);
```

### Throttling

Ensure a function executes at most once per time period.

```javascript
function throttle(func, limit) {
  let inThrottle;
  
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

function handleScroll() {
  console.log('Scroll position:', window.scrollY);
}

const throttledScroll = throttle(handleScroll, 100);
window.addEventListener('scroll', throttledScroll);
```

### Memoization

Cache function results for repeated calls.

```javascript
function memoize(fn) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache.has(key)) {
      return cache.get(key);
    }
    
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

const memoizedFib = memoize(fibonacci);

console.time('First call');
console.log(memoizedFib(40));
console.timeEnd('First call');

console.time('Second call');
console.log(memoizedFib(40));
console.timeEnd('Second call');
```

### Loop Optimization

```javascript
const items = Array(10000).fill(0);

console.time('For loop');
for (let i = 0; i < items.length; i++) {
}
console.timeEnd('For loop');

console.time('For...of');
for (const item of items) {
}
console.timeEnd('For...of');

console.time('forEach');
items.forEach(item => {
});
console.timeEnd('forEach');
```

### Avoid Unnecessary Work

```javascript
function processData(items) {
  const result = [];
  
  for (let i = 0; i < items.length; i++) {
    if (items[i].active) {
      const processed = expensiveOperation(items[i]);
      result.push(processed);
    }
  }
  
  return result;
}

function expensiveOperation(item) {
}
```

### Use Appropriate Data Structures

```javascript
const array = [1, 2, 3, 4, 5];
console.time('Array lookup');
array.includes(3);
console.timeEnd('Array lookup');

const set = new Set([1, 2, 3, 4, 5]);
console.time('Set lookup');
set.has(3);
console.timeEnd('Set lookup');

const map = new Map([
  ['key1', 'value1'],
  ['key2', 'value2']
]);
console.time('Map lookup');
map.get('key1');
console.timeEnd('Map lookup');
```

---

## Memory Management

Managing memory efficiently to avoid leaks.

### Identifying Memory Leaks

**Common Causes:**
1. Global variables
2. Forgotten timers/callbacks
3. Event listeners not removed
4. Closures retaining references

### Removing Event Listeners

```javascript
function setupEventListener() {
  const button = document.querySelector('#btn');
  
  function handleClick() {
    console.log('Clicked');
  }
  
  button.addEventListener('click', handleClick);
  
  return function cleanup() {
    button.removeEventListener('click', handleClick);
  };
}

const cleanup = setupEventListener();

cleanup();
```

### Clearing Timers

```javascript
function startPolling() {
  const intervalId = setInterval(() => {
    console.log('Polling...');
  }, 1000);
  
  return function stopPolling() {
    clearInterval(intervalId);
  };
}

const stop = startPolling();

setTimeout(() => {
  stop();
}, 5000);
```

### WeakMap and WeakSet

Automatically garbage collect unused references.

```javascript
const cache = new WeakMap();

function processUser(user) {
  if (cache.has(user)) {
    return cache.get(user);
  }
  
  const result = expensiveComputation(user);
  cache.set(user, result);
  return result;
}

function expensiveComputation(user) {
  return { ...user, processed: true };
}

let user = { id: 1, name: "Alice" };
processUser(user);

user = null;
```

### Avoiding Memory Leaks in Closures

```javascript
function createHandler() {
  const largeData = new Array(1000000).fill('data');
  
  return function() {
    console.log('Handler');
  };
}

function createHandlerOptimized() {
  const largeData = new Array(1000000).fill('data');
  const neededData = largeData[0];
  
  return function() {
    console.log('Handler', neededData);
  };
}
```

---

## Rendering Performance

Optimizing DOM operations and rendering.

### Minimize DOM Access

```javascript
const list = document.querySelector('#list');
const items = ['Item 1', 'Item 2', 'Item 3'];

items.forEach(item => {
  const li = document.createElement('li');
  li.textContent = item;
  list.appendChild(li);
});

const fragment = document.createDocumentFragment();

items.forEach(item => {
  const li = document.createElement('li');
  li.textContent = item;
  fragment.appendChild(li);
});

list.appendChild(fragment);
```

### Batch DOM Updates

```javascript
const element = document.querySelector('#box');

element.style.width = '100px';
element.style.height = '100px';
element.style.backgroundColor = 'blue';

element.style.cssText = 'width: 100px; height: 100px; background-color: blue;';

element.classList.add('box-styles');
```

### Virtual Scrolling

```javascript
class VirtualScroller {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleItems = Math.ceil(container.clientHeight / itemHeight);
    this.startIndex = 0;
    
    this.render();
    this.container.addEventListener('scroll', this.handleScroll.bind(this));
  }
  
  handleScroll() {
    const scrollTop = this.container.scrollTop;
    const newStartIndex = Math.floor(scrollTop / this.itemHeight);
    
    if (newStartIndex !== this.startIndex) {
      this.startIndex = newStartIndex;
      this.render();
    }
  }
  
  render() {
    const endIndex = Math.min(
      this.startIndex + this.visibleItems + 1,
      this.items.length
    );
    
    const visibleItems = this.items.slice(this.startIndex, endIndex);
    
    this.container.innerHTML = '';
    this.container.style.height = `${this.items.length * this.itemHeight}px`;
    
    visibleItems.forEach((item, index) => {
      const div = document.createElement('div');
      div.textContent = item;
      div.style.position = 'absolute';
      div.style.top = `${(this.startIndex + index) * this.itemHeight}px`;
      div.style.height = `${this.itemHeight}px`;
      this.container.appendChild(div);
    });
  }
}
```

### RequestAnimationFrame

```javascript
function smoothScroll(targetY, duration) {
  const startY = window.scrollY;
  const distance = targetY - startY;
  const startTime = performance.now();
  
  function animate(currentTime) {
    const elapsed = currentTime - startTime;
    const progress = Math.min(elapsed / duration, 1);
    
    const easeInOutQuad = progress < 0.5
      ? 2 * progress * progress
      : 1 - Math.pow(-2 * progress + 2, 2) / 2;
    
    window.scrollTo(0, startY + distance * easeInOutQuad);
    
    if (progress < 1) {
      requestAnimationFrame(animate);
    }
  }
  
  requestAnimationFrame(animate);
}

smoothScroll(1000, 1000);
```

### Intersection Observer

```javascript
const images = document.querySelectorAll('img[data-src]');

const imageObserver = new IntersectionObserver((entries, observer) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.removeAttribute('data-src');
      observer.unobserve(img);
    }
  });
});

images.forEach(img => imageObserver.observe(img));
```

---

## Lazy Loading & Code Splitting

Load code and resources only when needed.

### Dynamic Imports

```javascript
async function loadModule() {
  const button = document.querySelector('#load-chart');
  
  button.addEventListener('click', async () => {
    const { Chart } = await import('./chart.js');
    const chart = new Chart();
    chart.render();
  });
}
```

### Route-Based Code Splitting

```javascript
const routes = {
  '/home': () => import('./pages/Home.js'),
  '/about': () => import('./pages/About.js'),
  '/contact': () => import('./pages/Contact.js')
};

async function navigate(path) {
  const loadPage = routes[path];
  
  if (loadPage) {
    const module = await loadPage();
    module.default.render();
  }
}
```

### Lazy Loading Images

```javascript
function lazyLoadImages() {
  const images = document.querySelectorAll('img[data-src]');
  
  const config = {
    rootMargin: '50px 0px',
    threshold: 0.01
  };
  
  const imageObserver = new IntersectionObserver((entries, self) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const img = entry.target;
        img.src = img.dataset.src;
        img.removeAttribute('data-src');
        self.unobserve(img);
      }
    });
  }, config);
  
  images.forEach(image => {
    imageObserver.observe(image);
  });
}

lazyLoadImages();
```

---

## Measurement & Profiling

Measure performance to identify bottlenecks.

### Performance.now()

```javascript
const start = performance.now();

for (let i = 0; i < 1000000; i++) {
}

const end = performance.now();
console.log(`Operation took ${end - start}ms`);
```

### Console.time()

```javascript
console.time('Database Query');
await fetchDataFromDatabase();
console.timeEnd('Database Query');
```

### Performance Observer

```javascript
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log(`${entry.name}: ${entry.duration}ms`);
  }
});

observer.observe({ entryTypes: ['measure'] });

performance.mark('start-operation');

await performOperation();

performance.mark('end-operation');
performance.measure('operation', 'start-operation', 'end-operation');
```

### Chrome DevTools

```javascript
console.profile('My Operation');

expensiveOperation();

console.profileEnd('My Operation');
```

---

## Best Practices

### 1. Avoid Premature Optimization

```javascript
function processItems(items) {
  return items.filter(item => item.active);
}
```

### 2. Use Web Workers for Heavy Computation

```javascript
const worker = new Worker('worker.js');

worker.postMessage({ data: largeDataset });

worker.onmessage = (event) => {
  console.log('Result:', event.data);
};
```

**worker.js:**
```javascript
self.onmessage = (event) => {
  const result = performHeavyComputation(event.data);
  self.postMessage(result);
};

function performHeavyComputation(data) {
  return data;
}
```

### 3. Minimize Bundle Size

```javascript
import { debounce } from 'lodash-es';

import debounce from 'lodash/debounce';
```

### 4. Use CDN for Libraries

```html
<script src="https://cdn.jsdelivr.net/npm/react@18/umd/react.production.min.js"></script>
```

### 5. Enable Compression

Server configuration for gzip/brotli compression.

### 6. Cache API Responses

```javascript
const cache = new Map();

async function fetchData(url) {
  if (cache.has(url)) {
    return cache.get(url);
  }
  
  const response = await fetch(url);
  const data = await response.json();
  
  cache.set(url, data);
  return data;
}
```

### 7. Use Service Workers

```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js')
    .then(reg => console.log('Service Worker registered'))
    .catch(err => console.log('Service Worker registration failed'));
}
```

---

## Key Takeaways

- **Measure first**: Profile before optimizing to find real bottlenecks
- **Debounce/Throttle**: Limit expensive operations (search, scroll)
- **Memoization**: Cache expensive function results
- **Minimize DOM access**: Batch updates, use DocumentFragment
- **Lazy loading**: Load code and resources on demand
- **Code splitting**: Break large bundles into smaller chunks
- **Memory management**: Remove event listeners, clear timers
- **Web Workers**: Offload heavy computation from main thread
- **Virtual scrolling**: Render only visible items in large lists
- **Use appropriate data structures**: Set/Map for lookups 