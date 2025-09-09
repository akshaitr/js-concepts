# Topics

1. [Scope](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#scope)
2. [Functions](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#functions)
3. [Closures](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#closures)
4. [Objects](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#objects)
5. [Binding](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#binding)
6. [Promises](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#promises)
7. [Event Propagation](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-propagation)
8. [Debouncing and throttling](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#debouncing-and-throttling)
9. [Compose and Pipe](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#compose-and-pipe)
10. [Prototypes](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#prototypes)
11. [Classes and constructors](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#class-and-constructors)
12. [Event Loop](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-loop)
13. [Async and await](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#async-and-await)


# Scope

A scope is a certain region of a program where a defined variable exists and can be recognized. Beyond that it cannot be recognized.
There are three types of scopes:
- Global scope
- Function scope
- Block scope


📢 NOTES: 

> `var` is function scoped

> `let` and `const` are block scoped

### Variable Shadowing

Variable shadowing occurs when a variable declared within a certain scope has the same name as a variable declared in an outer scope.

```javascript
let num = 10;

function printNum() {
  let num = 20;
  console.log(num);
}

console.log(num);
```

While shadowing a variable, it should not cross the boundary of the scope.

```javascript
let num = 10;

if(true) {
  var num = 20;
}
```
This is is known as illegal shadowing and it will give the error as ${\textsf{\color{orange}variable\ is\ already\ defined}}$.

### Declaration

- `var` can be declared as many times as we want
- `let` and `const` cannot be redeclared in the same scope
- `var` and `let` can be declared without initialization
- `const` cannot be declared without initialization
- `var` and `let` can be reinitialized
- `const` cannot be updated

### Hoisting

When a variable is declared in JavaScript, it gets hoisted to the top of its scope, meaning the declaration happens first regardless of where the actual code is.

📢 NOTES: 

> `var` variables are hoisted and initialized to `undefined`

>  `let` and `const` variables are hoisted but not initialized until the line they are declared. They are said to be hoisted to temporal dead zone. (i.e, they are in the scope but not yet declared)

# Functions

```javascript
function square(num) {
  return num * num;
}
```

This is known as a function declaration, a function definition or a function statement.

```javascript
const square = function(num) {
  return num * num;
}
```

When you store a function definition inside a variable, it is called a function expression.

In languages like JavaScript, functions can be treated as any other variables. Functions can be passed as arguments to other functions, can be returned by another function and can be assigned as values to a variable. Such functions are called first-class functions.


${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
(function (x) {
  return (function (y) {
    console.log(x);
  })(2);
})(1);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log(i);
  }, i * 1000);
}
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
for (let i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log(i);
  }, i * 1000);
}
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
var num = 21;
var func = function() {
  console.log(num);
  var num = 11;
}

func();
```


📢 NOTES: 

> Unlike variables, function definitions get hoisted completely

### Parameters vs Arguments

A parameter is a variable that is listed in the function definition. It represents a value that the function expects to receive when it is called. Parameters are placeholders that define the type and name of the value that will be passed into the function.

```javascript
function addNumbers(num1, num2) {
  return num1 + num2;
}
```
In the example above, num1 and num2 are parameters of the addNumbers function.

An argument, on the other hand, is the actual value that is passed into a function when it is called. It is the concrete value that is assigned to a parameter.

```javascript
let result = addNumbers(5, 10);
```
In the example above, 5 and 10 are arguments that are passed as values to the addNumbers function.

### Spread vs Rest operators

- The spread operator, denoted by three consecutive dots (...), is primarily used for expanding iterables like arrays into individual elements. This operator allows us to efficiently merge, copy, or pass array elements to functions without explicitly iterating through them
- Spread Operator Use Cases
  - Combining arrays
  - Passing arguments to functions
  - Copying arrays
- While the spread operator expands elements, the rest operator condenses them into a single entity within function parameters or array destructuring. It collects remaining elements into a designated variable, facilitating flexible function definitions and array manipulation
- Rest Operator Use Cases
  - Handling variable-length function arguments
  - Array destructuring
 
📢 NOTES: 

> A rest parameter must be the last parameter in a function definition. This is because the rest parameter collects all the remaining arguments passed to the function, so it doesn't make sense to have any parameters after it.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const printData = (a, ...numbers, x, y) => {
  console.log(x, y);
};

printData(5, 6, 7, 8);
```

### Callback functions

A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action.

There are two ways in which the callback may be called: synchronous and asynchronous. Synchronous callbacks are called immediately after the invocation of the outer function, with no intervening asynchronous tasks, while asynchronous callbacks are called at some point later, after an asynchronous operation has completed. Examples of synchronous callbacks include the callbacks passed to Array.prototype.map(), Array.prototype.forEach(), etc. Examples of asynchronous callbacks include the callbacks passed to setTimeout() and Promise.prototype.then().

### Arrow function vs Regular function

The ordinary way of declaring functions in JavaScript is to use the function keyword. 

```javascript
function sayHello(name) {
  return `Hello ${name}`;
}
```

Arrow functions were introduced with ECMAScript 6 (ES6). They give you a more concise way of defining functions in JavaScript.

```javascript
const sayHello = (name) => {
  return `Hello ${name}`;
};
```

- We can access all the arguments passed to a regular function using the `arguments` object. To access the arguments passed to an arrow function, we can use the rest parameter syntax (`...`).
```javascript
function regularFunc() {
  console.log(arguments);
}

regularFunc(1, 2, 3);
```
```javascript
const arrowFunc = () => {
  console.log(arguments); // ❌ ReferenceError
};

arrowFunc(1, 2, 3);
```  
- When a regular function has duplicate names in the parameters, the last parameter with the duplicate name will take precedence. But in "strict mode", using a duplicate named parameter will result in a syntax error. Arrow functions don't allow for the same parameter name to be used more than once in the parameter list. Doing so will result in a syntax error.

```javascript
function demo(a, b, a) {
  console.log(a); // Logs the second 'a'
}

demo(1, 2, 3);
```
```javascript
"use strict";

function demo(a, b, a) {
  console.log(a);
}
```
```javascript
const demo = (a, b, a) => {
  console.log(a);
};
```
Why?
Arrow functions are always in strict mode, implicitly.
Duplicate parameter names are not allowed.

- Regular functions are hoisted to the top. And you can access and call them even before they are declared. Arrow functions, on the other hand, cannot be accessed before they are initialised.
```javascript
function Timer() {
  this.seconds = 0;

  // Regular function — loses `this`
  setInterval(function () {
    this.seconds++; // ❌ `this` is now window (or undefined)
    console.log(this.seconds);
  }, 1000);
}

new Timer();

const person = {
  name: "Alice",
  regularFunc: function () {
    console.log("Regular:", this.name);
  },
  arrowFunc: () => {
    console.log("Arrow:", this.name);
  }
};

person.regularFunc(); // "Regular: Alice"
person.arrowFunc();   // "Arrow: undefined" (or global value)

```
- Regular functions have their own `this` context. And this is determined dynamically depending on how you call or execute the function. Arrow functions, on the other hand, do not have their own `this` context. Instead, they capture the `this` value from the surrounding lexical context in which the arrow function was created.
- For regular functions, you can create a new instance using the `new` keyword. And this sets the `this` value to the new instance you've created. For arrow functions, you cannot use them as constructors. This is because the value of `this` in arrow functions is lexically scoped – that is, determined by the surrounding execution context. This behaviour does not make them suitable to be used as constructors.

It's recommended to use regular function in any of the following cases:
1. when you need to use a constructor with the `new` keyword
2. when you need the `this` binding to be dynamically scoped
3. when you want to use the `arguments` object

And you can use arrow functions in any of the following cases:
1. when you want a more concise syntax for the function
2. when you need to maintain the lexical scope of `this`
3. for non-method functions (in most cases)

📢 NOTES: 

> In programming, a function is a block of reusable code that performs a certain task. Functions can take input arguments and return output values. On the other hand, a method is a function that is associated with an object in object-oriented programming. Methods are functions that are called on objects and can modify or access the object's properties.
```html
<!DOCTYPE html>
<html>
<head>
  <title>Function Prototype Chain Viewer</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }
    .box {
      margin: 10px 0;
      padding: 10px;
      border: 2px solid #333;
      border-radius: 8px;
      background-color: #f0f0f0;
    }
    .arrow {
      font-size: 24px;
      text-align: center;
    }
    code {
      background: #eee;
      padding: 2px 6px;
      border-radius: 4px;
    }
  </style>
</head>
<body>

  <h2>🔍 JavaScript Function Prototype Chain Viewer</h2>

  <div class="box">
    <strong>Step 1:</strong> A function is declared:
    <br>
    <code>function greet() { console.log("Hello"); }</code>
  </div>

  <div class="arrow">↓</div>

  <div class="box">
    <strong>Step 2:</strong> <code>greet.__proto__</code> points to:
    <br>
    <code>Function.prototype</code> → shared methods like <code>call()</code>, <code>bind()</code>
  </div>

  <div class="arrow">↓</div>

  <div class="box">
    <strong>Step 3:</strong> <code>Function.prototype.__proto__</code> is:
    <br>
    <code>Object.prototype</code> → generic object methods like <code>toString()</code>, <code>hasOwnProperty()</code>
  </div>

  <div class="arrow">↓</div>

  <div class="box">
    <strong>Step 4:</strong> <code>Object.prototype.__proto__</code> is:
    <br>
    <code>null</code> (End of the chain)
  </div>

  <h3>🧪 Live Console Output:</h3>
  <pre id="output"></pre>

  <script>
    function greet() {
      console.log("Hello");
    }

    const output = document.getElementById("output");

    output.innerText += `typeof greet: ${typeof greet}\n`;
    output.innerText += `greet instanceof Function: ${greet instanceof Function}\n`;
    output.innerText += `greet instanceof Object: ${greet instanceof Object}\n\n`;

    output.innerText += `Object.getPrototypeOf(greet) === Function.prototype: ${
      Object.getPrototypeOf(greet) === Function.prototype
    }\n`;

    output.innerText += `Function.prototype.__proto__ === Object.prototype: ${
      Function.prototype.__proto__ === Object.prototype
    }\n`;

    output.innerText += `Object.prototype.__proto__ === null: ${
      Object.prototype.__proto__ === null
    }\n`;
  </script>

</body>
</html>
```
# Closures(Function + its lexical environment = Closure)

A closure is a JavaScript feature that allows a function to remember and access its lexical scope even when the function is executed outside that scope.

```javascript
function init() {
  var name = "Mozilla"; // name is a local variable created by init
  function displayName() {
    // displayName() is the inner function, that forms the closure
    console.log(name); // use variable declared in the parent function
  }
  displayName();
}

init();
```

📢 NOTES: 

> In JavaScript, a closure is created every time a function is created, at the function creation time.

### Lexical Scope

Lexical scoping describes how a parser resolves variable names when functions are nested. The word lexical refers to the fact that lexical scoping uses the location where a variable is declared within the source code to determine where that variable is available. Nested functions have access to variables declared in their outer scope.
```javascript
function outer() {
  const secret = "12345";

  function inner() {
    const secret = "54321";
    console.log(secret); // Logs: 54321
  }

  inner();
}

outer();
```
```
┌──────────────────────────────┐
│    Global Lexical Env        │
│  ──────────────────────────  │
│  a = 10                      │
│  outer()                     │
│  Outer: null                 │
└────────────┬────────────────┘
             │
             ▼
┌──────────────────────────────┐
│    outer() Lexical Env       │
│  ──────────────────────────  │
│  b = 20                      │
│  inner()                     │
│  Outer: Global Env           │
└────────────┬────────────────┘
             │
             ▼
┌──────────────────────────────┐
│    inner() Lexical Env       │
│  ──────────────────────────  │
│  c = 30                      │
│  Outer: outer() Env          │
└──────────────────────────────┘
```
### Why do we need closures?

- Closures makes it possible for functions to have private variables
- JavaScript closures are used to control what is and what isn't in the scope of a particular function
- Control which variables are shared with sibling functions
- Closures can be used for optimizing the runtime of a function

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
for (var i = 0; i < 5; i++) {
  function print(index) {
    setTimeout(function log() {
      console.log(index);
    }, index * 1000);
  }
  print(i);
}
```

### Creating a private counter using closure

```javascript
function counter() {
  let counter = 0;

  function add(increment) {
    counter += increment;
  }

  function get() {
    return `Counter = ${counter}`;
  }

  return { add, get };
}
```

### Module pattern

The module pattern uses an IIFE to encapsulate private variables and functions, exposing only a public interface. The module pattern is a design pattern used for improving the maintainability and reusability of the code by creating public and private access levels. The module pattern keeps the privacy of the state and organizes using closures. It protects the pieces from the global scope, avoiding possible errors and conflicts.

```javascript
const createSupplier = (function () {
  const name = "General Motors";
  const field = "automobile";

  return {
    name,
    field,
  };
})();

createSupplier.name;
createSupplier.field;
```

### Closures vs Scopes

- Closure refers to the ability of a function to retain access to variables from its lexical scope even after the scope has been closed
- Scope refers to the visibility and accessbility of variables within a specified context, such as global scope, function scope or block scope

# Objects

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const result = (function (num) {
  delete num;
  return num;
})(5);

console.log(result);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const obj = {
  a: "one",
  b: "two",
  a: "three",
};

console.log(obj);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const a = {};

const b = {
  key: "b",
};

const c = {
  key: "c",
};

a[b] = 123;
a[c] = 456;

console.log(a[b]);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log([..."Akshai"]);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const user = {
  name: "Akshai",
  age: 28,
};

const admin = {
  admin: true,
  ...user,
};

console.log(admin);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const settings = {
  username: "Akshai",
  level: 19,
  health: 90,
};

const data = JSON.stringify(settings, ["level", "health"]);

console.log(data);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const shape = {
  radius: 10,
  diameter() {
    return this.radius * 2;
  },
  perimeter: () => {
    return 2 * Math.PI * this.radius;
  },
};

console.log(shape.diameter());
console.log(shape.perimeter());
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function getItems(fruitList, ...args, favouriteFruit) {
  return [...fruitList, ...args, favouriteFruit];
}

getItems(["banana", "apple"], "pear", "orange");
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
let welcome = {
  greeting: "Hello",
};

let temp;

temp = welcome;

welcome.greeting = "Hey you!";

console.log(temp.greeting);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
let person = {
  name: "Akshai",
};

const members = [person];

person = null;

console.log(members);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const values = {
  number: 10,
};

const multiply = (x = { ...values }) => {
  console.log(x.number *= 2);
};

multiply();
multiply();
multiply(values);
multiply(values);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function changeAgeAndReference(person) {
  person.age = 25;
  person = {
    name: "John",
    age: 50,
  };
  return person;
}

const person1 = {
  name: "Alex",
  age: 30,
};

const person2 = changeAgeAndReference(person1);

console.log(person1);
console.log(person2);
```

### How to clone/deep copy an object in JavaScript?

```javascript
let user = {
  name: "user",
  age: 28,
};
```

- Using Spread Operator
  ```javascript
  const clonedUser = { ...user, name: "Akshai" };
  ```
- Using Object.assign() method
  ```javascript
  const clonedUser = Object.assign({}, user);
  clonedUser.name = "Akshai";
  ```
- Using JSON.parse() and JSON.stringify()
  ```javascript
  const clonedUser = JSON.parse(JSON.stringify(user));
  clonedUser.name = "Akshai";
  ```
- Using structuredClone() global function
  ```javascript
  const clonedUser = structuredClone(user)
  ```

[Polyfill for deep clone](https://github.com/akshaitr/js-polyfills/blob/main/src/deepClone.js)

# Binding

### Implicit binding - `this` keyword

The this keyword refers to the context where a piece of code, such as a function's body, is supposed to run. Most typically, it is used in object methods, where this refers to the object that the method is attached to, thus allowing the same method to be reused on different objects.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const user = {
  firstName: "Akshai",
  getName() {
    const firstName = "TR";
    return this.firstName;
  },
};

console.log(user.getName());
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function createUser() {
  return {
    name: "John",
    ref: this,
  };
}

const user = createUser();
console.log(user.ref.name);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const user = {
  name: "Akshai",
  logName() {
    console.log(this.name);
  },
};

setTimeout(user.logName, 1000);

setTimeout(function () {
  user.logName();
}, 1000);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
var length = 4;

function callback() {
  console.log(this.length);
}

const obj = {
  length: 5,
  method(func) {
    func();
  },
};

obj.method(callback);

obj.method(callback.bind(obj));
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
var length = 4;

function callback() {
  console.log(this.length);
}

const obj = {
  length: 5,
  method() {
    console.log(arguments);
    arguments[0]();
  },
};

obj.method(callback, 2, 3);
```

### Explicit binding - Call, apply and bind

The `call` method in JavaScript is used to invoke a function with a specified `this` context and arguments individually. It accepts the context object as the first argument followed by individual arguments.

```javascript
var user = {
  name: "Akshai",
};

function greeting(greetingText) {
  return `${greetingText} ${this.name}!!`;
}

console.log(greeting.call(user, "Hello"));
```

The `apply` method in JavaScript is similar to call but accepts arguments as an array. It is used to invoke a function with a specified context and an array of arguments.

```javascript
var user = {
  name: "Akshai",
};

function greeting(greetingText) {
  return `${greetingText} ${this.name}!!`;
}

console.log(greeting.apply(user, ["Hello"]));
```

The bind method in JavaScript is used to create a new function with a specified `this` context. It doesn't immediately execute the function but return a new function that can be invoked later.

```javascript
var user = {
  name: "Akshai",
};

function greeting(greetingText) {
  return `${greetingText} ${this.name}!!`;
}

const greetUser = greeting.bind(user);

console.log(greetUser("Hello"));
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const age = 19;

var person = {
  name: "Akshai",
  age: 28,
  getAge: function () {
    console.log(this.age);
  },
};

var person2 = {
  age: 24,
};

person.getAge.call(person2);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const status = 1;

setTimeout(() => {
  const status = 2;
  const data = {
    status: 3,
    getStatus() {
      return this.status;
    },
  };

  console.log(data.getStatus());
  console.log(data.getStatus.call(this));
});
```
# Async and await

async Function

- Declaring a function with async makes it return a Promise, no matter what.
- If the function returns a value, JavaScript wraps it in a resolved Promise.
- If it throws an error, JavaScript wraps it in a rejected Promise.

```javascript
async function greet() {
  return "Hello World";
}

greet().then(console.log); // "Hello World"
```
await Expression

- Can only be used inside an async function.
- Pauses execution of the function until the Promise settles.
- Returns the resolved value if the Promise is fulfilled.
- Throws an error if the Promise is rejected.

```javascript
async function riskyOperation() {
  throw new Error("Something went wrong!");
}

async function run() {
  try {
    const res = await riskyOperation();
    console.log(res);
  } catch (err) {
    console.error("Caught error:", err.message);
  }
}

run();
// Output: Caught error: Something went wrong!
```

# Promises

The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.

A Promise is in one of these states:
- pending: initial state, neither fulfilled nor rejected.
- fulfilled: meaning that the operation was completed successfully
- rejected: meaning that the operation failed

```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo");
  }, 300);
});

myPromise.then((res) => {
  console.log(res);
});
```

[Polyfill for Promise](https://github.com/akshaitr/js-polyfills/edit/main/src/promise.js)
Reference: [Polyfill for Javascript Promise](https://medium.com/@manojsingh047/polyfill-for-javascript-promise-81053b284e37)

### Promise.all()

We can provide multiple promises to Promise.all(). It will run all the promises in parallel and returns an array with all the fullfilled promises. If any one of the promise fails, it will fail the complete Promise.all() operation.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 1000, "foo");
});

Promise.all([promise1, promise2, promise3]).then((values) => {
  console.log(values);
});
```

[Polyfill for Promise.all()](https://github.com/akshaitr/js-polyfills/blob/main/src/allPromise.js)

### Promise.race()

It returns the first promise that gets fullfilled.

### Promise.allSettled()

It takes multiple promises and returns a single promise, which when fullfilled, will return an array of objects that describe outcome of each promise.

### Promise.any()

It takes an array of promises as input and returns a single Promise. This returned promise fulfills when any of the input's promises fulfills, with this first fulfillment value. It rejects when all of the input's promises reject (including when an empty iterable is passed), with an AggregateError containing an array of rejection reasons.

### Async await

An async function declaration creates an AsyncFunction object. Each time when an async function is called, it returns a new Promise which will be resolved with the value returned by the async function, or rejected with an exception uncaught within the async function.

Await expressions make promise-returning functions behave as though they're synchronous by suspending execution until the returned promise is fulfilled or rejected. The resolved value of the promise is treated as the return value of the await expression.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log("start");

const promise1 = new Promise((resolve) => {
  console.log(1);
  resolve(2);
});

promise1.then((res) => {
  console.log(res);
});

console.log("end");
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log("start");

const promise1 = new Promise((resolve) => {
  console.log(1);
  console.log(3);
});

promise1.then((res) => {
  console.log("Result: ", res);
});

console.log("end");
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function job(state) {
  return new Promise(function (resolve, reject) {
    if (state) {
      resolve("Success");
    } else {
      reject("Error");
    }
  });
}

let promise = job(true);

promise
  .then(function (data) {
    console.log(data);
    return job(false);
  })
  .catch(function (error) {
    console.log(error);
    return "Error caught";
  })
  .then(function (data) {
    console.log(data);
    return job(true);
  })
  .catch(function (error) {
    console.log(error);
  });
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const firstPromise = new Promise((resolve, reject) => {
  resolve("First");
});

const secondPromise = new Promise((resolve, reject) => {
  resolve(firstPromise);
});

secondPromise
  .then((res) => {
    return res;
  })
  .then(console.log);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log('start');

const promise1 = Promise.resolve().then(() => {
  console.log('promise1');
  const timer2 = setTimeout(() => {
    console.log('timer2')
  }, 0)
});

const timer1 = setTimeout(() => {
  console.log('timer1')
  const promise2 = Promise.resolve().then(() => {
    console.log('promise2')
  })
}, 0)

console.log('end');
```

# Event Propagation

The complete process of deciding when and in which direction the event will be executed is called event propagation.

### Event bubbling

The propagation of an event from the innermost target element to it's outermost ancestor is called event bubbling. The events are executed from bottom up.

📢 NOTES: 

> There are few events that do not bubble, eg: focus(), blur() etc

### Target

`event.target` refers to an element that triggered the event.

`this.target` refers to the element to which the event listener is attached(i.e, the current context).

`event.currentTarget` refers to the element that is currently handling the event during it's bubbling/capturing phase.

### Event capturing (trickling)

Event capturing is the opposite of event bubbling. The event is captured from the outermost element towards the target element. By setting `{ capture: true}`, event listeners are triggered during the capturing phase instead of bubbling phase.

### stopPropagation()

`event.stopPropagation()` prevents the further propagation of an event through the DOM tree.

### Event delegation

A technique where element listens for events on behalf of it's children.

# Debouncing and Throttling

Debouncing limits the execution of a function call and waits for a certain amount of time before running it again.

See the code for [debouncing function](https://github.com/akshaitr/js-polyfills/blob/main/src/debounce.js)

Throttling is a technique to limit the execution of an event handler function even when this event is triggered continuously due to user actions.

See the code for [throttle function](https://github.com/akshaitr/js-polyfills/blob/main/src/throttle.js)

### Debouncing vs Throttling: Key Differences

- Execution Frequency: Debouncing postpones the execution until after a period of inactivity, while throttling limits the execution to a fixed number of times over an interval.
- Use Cases: Debouncing is ideal for tasks that don’t need to execute repeatedly in quick succession, such as API calls based on user input. Throttling is suited for controlling the execution rate of functions called in response to events like scrolling or resizing.

# Compose and Pipe

Compose and pipe are higher order functions used in JavaScript for function composition.

Compose takes multiple functions as arguments and return a new function that applies these functions from right to left.

See the code for [compose function](https://github.com/akshaitr/js-polyfills/blob/main/src/compose.js)

Pipe, on the other hand, applies the functions from left to right.

See the code for [pipe function](https://github.com/akshaitr/js-polyfills/blob/main/src/pipe.js)

# Prototypes

JavaScript implements inheritance by using objects. Each object has an internal link to another object called its prototype. That prototype object has a prototype of its own, and so on until an object is reached with null as its prototype.

```javascript
const myObject = {
  city: "Madrid",
  greet() {
    console.log(`Greetings from ${this.city}`);
  },
};

myObject.greet();

myObject.toString();
```

### Prototype inheritance

Constructor functions in JavaScript are used to create an object with specific properties and methods.

```javascript
function Box(value) {
  this.value = value;
}

Box.prototype.getValue = function () {
  return this.value;
};

const box1 = new Box(1);
```

Constructors are functions called with `new`. When a function is called with the `new` keyword, it will do the following things:
  - Creates a blank, plain JavaScript object i.e., a new instance
  - Points the prototype of the new instance to the constructor function's prototype
  - Executes the constructor function with the given arguments, binding the new instance as `this` context

📢 NOTES: 

> To be a constructor, a function object must have a [[Construct]] internal method.
Functions created with the function keyword are constructors, as are some built-in functions such as Date. These are the functions you can use with new.
Other function objects do not have a [[Construct]] internal method. These include arrow functions. So you can't use new with these. This makes sense since you can't set the this value of an arrow function.

### `__proto__` vs prototype

`__proto__` is an object property that points to the prototype of that object. It is used for inheritance and allows accessing the prototype chain.

prototype is a property that exist on the constructor function and is used to set an inheritance for the object created by the constructor function. It is used to define shared properties and methods for instances.

### setPrototypeOf()

A method used to set the prototype of a specified object to another object or null. It allows changing the prototype dynamically after an object has been created.

```javascript
const user = {
  name: "Akshai",
  age: 28,
};

const adminUser = { isAdmin: true };

console.log(user.isAdmin);
// Expected output: undefined

Object.setPrototypeOf(user, adminUser);

console.log(user.isAdmin);
// Expected output: true
```

### instanceof

An operator that checks if an object is an instance of a specific constructor or it's prototype chain. It returns true if the object is an instance of the constructor or a constructor's prototype chain.
```text
Function: Person
 └── .prototype → { sayHello }

Object: p1 (created using new Person())
 └── .__proto__ → Person.prototype
                         └── .__proto__ → Object.prototype

```
# Class and constructors

A class is a blueprint that defines the structure and behavior of an object. Objects are instances of a class and possess the properties and methods defined by that class.

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  calcArea() {
    return this.height * this.width;
  }
}

const rectangle = new Rectangle(4, 5);
```

Classes in JS are built on prototypes but also have some syntax and semantics that are unique to classes.

```javascript
function Rectangle(height, width) {
  this.height = height;
  this.width = width;
}

Rectangle.prototype.calcArea = function () {
  return this.height * this.width;
};
```

### Class inheritance

The extends keyword is used in class declarations or class expressions to create a class that is a child of another class.

```javascript
class Square extends Rectangle {}

Square.prototype.calcParameter = function () {
  return 2 * (this.height + this.width);
};

const square = new Square(8, 8);

console.log(square.calcArea());

console.log(square.calcParameter());
```

### Static properties and methods

Static properties cannot be directly accessed on instances of the class. Instead, they're accessed on the class itself.

Static methods are often utility functions, such as functions to create or clone objects, whereas static properties are useful for caches, fixed-configuration, or any other data you don't need to be replicated across instances.

```javascript
class ClassWithStaticMethod {
  static staticProperty = 'someValue';
  static staticMethod() {
    return 'static method has been called.';
  }
  static {
    console.log('Class static initialization block called');
  }
}

console.log(ClassWithStaticMethod.staticProperty);
// Expected output: "someValue"
console.log(ClassWithStaticMethod.staticMethod());
// Expected output: "static method has been called."
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
class Employee {
  constructor() {
    this.name = "John";
  }

  constructor() {
    this.age = 30
  }
}

const employee = new Employee();

console.log(employee.name);
```

📢 NOTES: 

> Defining a function in an object's prototype is better than defining it inside an object because it registers the method in the prototype, making it more memory efficient. When we define a method inside an object, it will create a closure for each instances created for that object.

# Event loop

Event loop in JavaScript is a mechanism responsible for managing asynchronous behavior in a single-threaded environment. It acts like a traffic controller, ensuring tasks are executed in an orderly manner by processing pending taks in queues(microtasks and macrotasks).

Event loop is necessary to handle asynchronous operations in JavaScript effectively. It manages task queues and microtask queues to ensure that tasks are executed efficiently wthout blocking the main thread.
