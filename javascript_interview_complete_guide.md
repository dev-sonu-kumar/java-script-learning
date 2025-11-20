# JavaScript Interview Complete Guide

## 🎯 Core JavaScript Concepts (Must Know)

### 1. Event Loop & Call Stack
```js
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve().then(() => console.log('3'));
console.log('4');
// Output: 1, 4, 3, 2
```

**Explanation:** Call stack → Microtasks (Promises) → Macrotasks (setTimeout)

### 2. Prototypes & Inheritance
```js
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const john = new Person('John');
console.log(john.greet()); // "Hello, I'm John"
```

### 3. this Binding Rules
```js
// 1. Default binding
function foo() { console.log(this); } // window/global

// 2. Implicit binding
const obj = { foo() { console.log(this); } }; // obj

// 3. Explicit binding
foo.call(obj); // obj

// 4. new binding
new foo(); // new instance

// 5. Arrow functions (lexical this)
const arrow = () => console.log(this); // outer scope's this
```

### 4. Closures (Advanced)
```js
function createCounter() {
  let count = 0;
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.getCount()); // 1
```

### 5. Module Patterns
```js
// IIFE Module Pattern
const Module = (function() {
  let privateVar = 0;
  
  return {
    publicMethod() {
      return ++privateVar;
    }
  };
})();

// ES6 Modules
export const utils = {
  add: (a, b) => a + b,
  multiply: (a, b) => a * b
};
```

## 🔥 Advanced Interview Questions

### 1. Implement Promise.all
```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      reject(new TypeError('Expected an array'));
    }
    
    const results = [];
    let completed = 0;
    
    if (promises.length === 0) {
      resolve(results);
      return;
    }
    
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then(value => {
          results[index] = value;
          completed++;
          if (completed === promises.length) {
            resolve(results);
          }
        })
        .catch(reject);
    });
  });
}
```

### 2. Debounce Function
```js
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage
const debouncedSearch = debounce((query) => {
  console.log('Searching for:', query);
}, 300);
```

### 3. Throttle Function
```js
function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}
```

### 4. Deep Clone Object
```js
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Array) return obj.map(item => deepClone(item));
  if (typeof obj === 'object') {
    const cloned = {};
    Object.keys(obj).forEach(key => {
      cloned[key] = deepClone(obj[key]);
    });
    return cloned;
  }
}
```

### 5. Flatten Array (All Levels)
```js
function flattenArray(arr) {
  return arr.reduce((flat, item) => {
    return flat.concat(Array.isArray(item) ? flattenArray(item) : item);
  }, []);
}

// Modern approach
const flatten = arr => arr.flat(Infinity);
```

## 🚀 Tricky Interview Questions

### 1. Variable Hoisting
```js
console.log(a); // undefined (not error)
var a = 5;

console.log(b); // ReferenceError
let b = 10;

console.log(c); // ReferenceError  
const c = 15;
```

### 2. Function Hoisting
```js
console.log(foo()); // "Hello" - function declarations are hoisted

function foo() {
  return "Hello";
}

console.log(bar()); // TypeError - bar is undefined
var bar = function() {
  return "World";
};
```

### 3. Temporal Dead Zone
```js
console.log(typeof x); // "undefined"
console.log(typeof y); // ReferenceError

let y = 5;
```

### 4. Scope Chain
```js
var x = 1;
function outer() {
  var x = 2;
  function inner() {
    var x = 3;
    console.log(x); // 3
  }
  inner();
  console.log(x); // 2
}
outer();
console.log(x); // 1
```

## 💡 ES6+ Features

### 1. Destructuring
```js
// Array destructuring
const [a, b, ...rest] = [1, 2, 3, 4, 5];

// Object destructuring
const {name, age, ...others} = {name: 'John', age: 30, city: 'NYC'};

// Function parameters
function greet({name, age = 25}) {
  return `Hello ${name}, you are ${age}`;
}
```

### 2. Template Literals
```js
const name = 'John';
const age = 30;

// Multi-line strings
const message = `
  Hello ${name},
  You are ${age} years old.
  ${age >= 18 ? 'You are an adult' : 'You are a minor'}
`;
```

### 3. Async/Await Error Handling
```js
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Fetch failed:', error);
    throw error;
  }
}
```

## 🎪 Practical Coding Challenges

### 1. Implement Array.prototype.map
```js
Array.prototype.myMap = function(callback, thisArg) {
  const result = [];
  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      result[i] = callback.call(thisArg, this[i], i, this);
    }
  }
  return result;
};
```

### 2. Implement Array.prototype.reduce
```js
Array.prototype.myReduce = function(callback, initialValue) {
  let accumulator = initialValue;
  let startIndex = 0;
  
  if (accumulator === undefined) {
    accumulator = this[0];
    startIndex = 1;
  }
  
  for (let i = startIndex; i < this.length; i++) {
    accumulator = callback(accumulator, this[i], i, this);
  }
  
  return accumulator;
};
```

### 3. Memoization
```js
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

// Usage
const fibonacci = memoize(function(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});
```

### 4. Curry Function
```js
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, args.concat(nextArgs));
    };
  };
}

// Usage
const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);
console.log(curriedAdd(1)(2)(3)); // 6
```

## 🔧 Performance & Optimization

### 1. Event Delegation
```js
// Instead of adding listeners to each button
document.getElementById('container').addEventListener('click', function(e) {
  if (e.target.classList.contains('button')) {
    console.log('Button clicked:', e.target.textContent);
  }
});
```

### 2. Lazy Loading
```js
function lazyLoad(fn) {
  let loaded = false;
  let result;
  
  return function() {
    if (!loaded) {
      result = fn.apply(this, arguments);
      loaded = true;
    }
    return result;
  };
}
```

### 3. Memory Leak Prevention
```js
// Bad - creates memory leak
function createHandler() {
  const largeData = new Array(1000000).fill('data');
  
  return function() {
    // largeData is captured in closure
    console.log('Handler called');
  };
}

// Good - avoid unnecessary closures
function createHandler() {
  return function() {
    console.log('Handler called');
  };
}
```

## 🎯 Common Interview Scenarios

### 1. Fix the Loop Issue
```js
// Problem
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // Prints 3, 3, 3
}

// Solution 1: Use let
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // Prints 0, 1, 2
}

// Solution 2: Use closure
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(() => console.log(j), 100);
  })(i);
}

// Solution 3: Use bind
for (var i = 0; i < 3; i++) {
  setTimeout(console.log.bind(null, i), 100);
}
```

### 2. Object Property Access
```js
const obj = {
  a: {
    b: {
      c: 'value'
    }
  }
};

// Safe property access
function safeGet(obj, path) {
  return path.split('.').reduce((current, key) => {
    return current && current[key] !== undefined ? current[key] : undefined;
  }, obj);
}

console.log(safeGet(obj, 'a.b.c')); // 'value'
console.log(safeGet(obj, 'a.b.d')); // undefined
```

### 3. Array Manipulation
```js
// Remove duplicates
const removeDuplicates = arr => [...new Set(arr)];

// Group by property
const groupBy = (arr, key) => {
  return arr.reduce((groups, item) => {
    const group = item[key];
    groups[group] = groups[group] || [];
    groups[group].push(item);
    return groups;
  }, {});
};

// Chunk array
const chunk = (arr, size) => {
  return Array.from({length: Math.ceil(arr.length / size)}, (_, i) =>
    arr.slice(i * size, i * size + size)
  );
};
```

## 📝 Interview Tips

### 1. **Always Ask Clarifying Questions**
- What's the expected input/output?
- Are there any constraints?
- Should I handle edge cases?

### 2. **Think Out Loud**
- Explain your approach
- Discuss trade-offs
- Mention alternative solutions

### 3. **Start Simple, Then Optimize**
- Get a working solution first
- Then discuss optimizations
- Consider time/space complexity

### 4. **Common Gotchas to Mention**
- Type coercion: `'5' - 3 = 2` but `'5' + 3 = '53'`
- Falsy values: `false, 0, '', null, undefined, NaN`
- `==` vs `===`
- `var` vs `let` vs `const`

## 🏆 System Design Questions (Frontend)

### 1. Design a Frontend Cache
```js
class FrontendCache {
  constructor(maxSize = 100, ttl = 300000) { // 5 min default TTL
    this.cache = new Map();
    this.maxSize = maxSize;
    this.ttl = ttl;
  }
  
  set(key, value) {
    if (this.cache.size >= this.maxSize) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(key, {
      value,
      timestamp: Date.now()
    });
  }
  
  get(key) {
    const item = this.cache.get(key);
    if (!item) return null;
    
    if (Date.now() - item.timestamp > this.ttl) {
      this.cache.delete(key);
      return null;
    }
    
    return item.value;
  }
}
```

### 2. Implement Event Emitter
```js
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  emit(event, ...args) {
    if (this.events[event]) {
      this.events[event].forEach(callback => {
        callback.apply(this, args);
      });
    }
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
}
```

## 🎪 Bonus: Advanced Patterns

### 1. Observer Pattern
```js
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
```

### 2. Singleton Pattern
```js
class Singleton {
  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    Singleton.instance = this;
  }
  
  static getInstance() {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }
    return Singleton.instance;
  }
}
```

---

## 📚 Study Plan (2-3 weeks)

### Week 1: Fundamentals
- [ ] Hoisting, Closures, Scope
- [ ] this binding, Prototypes
- [ ] Event Loop, Call Stack
- [ ] Promises, async/await

### Week 2: Advanced Concepts
- [ ] Implement Array methods
- [ ] Debounce/Throttle
- [ ] Deep clone, Flatten
- [ ] Memoization, Currying

### Week 3: Practice & Polish
- [ ] Solve coding challenges
- [ ] Practice explaining concepts
- [ ] Mock interviews
- [ ] Review common gotchas

**Good luck with your interviews! 🚀**