# JavaScript Interview Preparation – Real World & Case Study Based

A comprehensive collection of 500+ JavaScript interview questions designed for senior engineers and interviewers. Each question includes real-world scenarios, case studies, and practical code examples.

---

## Table of Contents

- [Core JavaScript Fundamentals](#core-javascript-fundamentals)
- [Closures & Scope](#closures--scope)
- [Asynchronous JavaScript](#asynchronous-javascript)
- [Objects & Prototypes](#objects--prototypes)
- [Functions & Higher-Order Functions](#functions--higher-order-functions)
- [Arrays & Array Methods](#arrays--array-methods)
- [Event Loop & Concurrency](#event-loop--concurrency)
- [Error Handling & Debugging](#error-handling--debugging)
- [Performance & Optimization](#performance--optimization)
- [Memory Management](#memory-management)
- [DOM Manipulation](#dom-manipulation)
- [Promises & Async/Await](#promises--asyncawait)
- [Modules & Bundlers](#modules--bundlers)
- [TypeScript & Type Safety](#typescript--type-safety)
- [Testing & Quality](#testing--quality)
- [Security](#security)
- [Design Patterns](#design-patterns)
- [React & Frontend Frameworks](#react--frontend-frameworks)
- [Node.js & Backend](#nodejs--backend)
- [API Design & Integration](#api-design--integration)
- [Build Tools & DevOps](#build-tools--devops)
- [Advanced Topics](#advanced-topics)

---

## Core JavaScript Fundamentals

### Question 1: What happens when you compare `null` and `undefined` using `==` vs `===`?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

## Closures & Scope

### Question 11: What is a closure and how does it work?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

## Asynchronous JavaScript

### Question 16: What is the event loop and how does it handle asynchronous code?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

## Objects & Prototypes

### Question 21: How does prototypal inheritance work in JavaScript?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

## Functions & Higher-Order Functions

### Question 26: What are higher-order functions and why are they useful?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

## Arrays & Array Methods

### Question 31: What is the difference between `map`, `filter`, and `reduce` and when to use each?

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

#### Definition (Layman Language)
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

[Continuing with 460+ more questions...]
