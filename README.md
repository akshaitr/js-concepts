# Topics

1. [Execution Context](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#execution-context)
2. [Call Stack](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#call-stack)
3. [Scope](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#scope)
4. [Strict Mode](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#strict-mode)
5. [Functions](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#functions)
6. [Closures](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#closures)
7. [Objects](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#objects)
8. [Arrays](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#arrays)
9. [Type Coercion and Equality](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#type-coercion-and-equality)
10. [Map and Set](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#map-and-set)
11. [WeakMap and WeakSet](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#weakmap-and-weakset)
12. [Binding](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#binding)
13. [Prototypes](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#prototypes)
14. [Classes and constructors](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#class-and-constructors)
15. [Symbol](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#symbol)
16. [Proxy and Reflect](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#proxy-and-reflect)
17. [Event Loop](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-loop)
18. [Promises](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#promises)
19. [Error Handling](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#error-handling)
20. [AbortController and Cancellation](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#abortcontroller-and-cancellation)
21. [Event Propagation](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-propagation)
22. [Debouncing and throttling](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#debouncing-and-throttling)
23. [Generators and Iterators](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#generators-and-iterators)
24. [Modules](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#modules)
25. [Compose and Pipe](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#compose-and-pipe)
26. [Currying](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#currying)
27. [Structured Clone and Data Copying](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#structured-clone-and-data-copying)
28. [Web Workers](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#web-workers)
29. [SharedArrayBuffer and Atomics](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#sharedarraybuffer-and-atomics)
30. [WeakRef and FinalizationRegistry](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#weakref-and-finalizationregistry)

# Execution Context

Whenever JavaScript code runs, it needs an environment that keeps track of variables, functions, and the current line being executed.
That environment is called the Execution Context.

There are mainly two types:

- Global Execution Context (GEC) → created when you first run a JS file.</br>
  Stores global variables, functions, and the `this` keyword (in browsers `this` = `window`).
- Function Execution Context (FEC) → created each time a function is called.</br>
  Each function call gets its own context (variables, arguments, this, etc.)

### What does an execution context contain?

Every execution context has three components:

1. **Variable Environment** — stores variables and function declarations.
   During the creation phase, `var` variables are initialized to `undefined`
   and `let`/`const` go to the temporal dead zone.
   Function declarations are hoisted completely.

2. **Scope Chain** — a reference to the outer lexical environment.
   If a variable isn't found in the current scope,
   JavaScript follows this chain outward until it reaches the global scope.

3. **this binding** — determines what `this` refers to in that context.
   In GEC (browser): `this` = `window`
   In GEC (Node.js): `this` = `{}` (empty object in modules)
   In FEC: depends on how the function is called (see [Binding](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#binding) section)

### Creation Phase vs Execution Phase

When an execution context is created, it goes through two phases:

**Creation phase** — JS scans the code and sets up the variable environment.
Variables are allocated memory but not yet assigned their values.

**Execution phase** — JS runs the code line by line,
assigning values and executing function calls.
```javascript
var name = "Akshai";

function greet() {
  var message = "Hello";
  console.log(message + " " + name);
}

greet();
```

**Step-by-step walkthrough:**

**1. GEC Creation Phase:**
- `name` → `undefined`
- `greet` → function definition (hoisted completely)
- `this` → `window`

**2. GEC Execution Phase:**
- `name` = `"Akshai"`
- `greet()` is called → new FEC is created

**3. FEC (greet) Creation Phase:**
- `message` → `undefined`
- Scope chain → points to GEC (outer environment)
- `this` → `window` (regular function call)

**4. FEC (greet) Execution Phase:**
- `message` = `"Hello"`
- `console.log("Hello Akshai")` → finds `message` locally, finds `name` via scope chain
- Function finishes → FEC is destroyed

# Call Stack

Think of it as a stack of plates🍽️

- The bottom plate is the global execution context.
- Every time a function is called → a new plate (execution context) is added on top.
- When the function finishes → the plate is removed.

That's why it's called a stack (LIFO – Last In, First Out).
```javascript
function multiply(a, b) {
  return a * b;
}

function square(n) {
  return multiply(n, n);
}

function printSquare(n) {
  var result = square(n);
  console.log(result);
}

printSquare(5);
```

**Call stack walkthrough:**
```
Step 1: Program starts
| GEC                |  ← bottom plate

Step 2: printSquare(5) is called
| printSquare(5)     |
| GEC                |

Step 3: square(5) is called inside printSquare
| square(5)          |
| printSquare(5)     |
| GEC                |

Step 4: multiply(5, 5) is called inside square
| multiply(5, 5)     |
| square(5)          |
| printSquare(5)     |
| GEC                |

Step 5: multiply returns 25 → popped off
| square(5)          |
| printSquare(5)     |
| GEC                |

Step 6: square returns 25 → popped off
| printSquare(5)     |
| GEC                |

Step 7: console.log(25) runs, printSquare finishes → popped off
| GEC                |

Step 8: Program ends → GEC is removed
| (empty)            |
```

### Stack Overflow

When a function calls itself infinitely (or the call stack exceeds its limit), you get a stack overflow error.
```javascript
function recursive() {
  recursive();
}

recursive(); // Uncaught RangeError: Maximum call stack size exceeded
```

This is why every recursive function needs a base case — a condition that stops the recursion before the stack overflows.

📢 NOTES:

> JavaScript is single-threaded, meaning it has only one call stack. It can execute one thing at a time. This is why long-running synchronous operations block the UI — they sit on the call stack and prevent anything else from running.

> `console.trace()` is like asking JavaScript: "Show me how we got here in the call stack."
> When console.trace() is executed:
>
> - It prints a stack trace in your browser's console (or Node.js terminal).
> - The trace shows the sequence of function calls that led to this point.

# Scope

A scope is a certain region of a program where a defined variable exists and can be recognized. Beyond that it cannot be recognized.
There are three types of scopes:

- Global scope
- Function scope
- Block scope
```javascript
// Global scope — accessible everywhere
var globalVar = "I'm global";

function demo() {
  // Function scope — accessible only inside this function
  var functionVar = "I'm function scoped";

  if (true) {
    // Block scope — accessible only inside this block
    let blockVar = "I'm block scoped";
    const alsoBlock = "Me too";
    var notBlock = "I'm actually function scoped!";

    console.log(blockVar);    // ✅
    console.log(functionVar); // ✅
    console.log(globalVar);   // ✅
  }

  console.log(notBlock);      // ✅ var ignores block scope
  console.log(blockVar);      // ❌ ReferenceError
}

console.log(functionVar);     // ❌ ReferenceError
```

📢 NOTES:

> `var` is function scoped — it ignores block boundaries like `if`, `for`, `while`. It is only contained by functions.

> `let` and `const` are block scoped — they are contained by any `{}` block including `if`, `for`, `while`, and plain blocks.

### Variable Shadowing

Variable shadowing occurs when a variable declared within a certain scope has the same name as a variable declared in an outer scope.
```javascript
let num = 10;

function printNum() {
  let num = 20;     // shadows the outer num
  console.log(num); // 20
}

printNum();
console.log(num);   // 10 — outer num is untouched
```

While shadowing a variable, it should not cross the boundary of the scope.
```javascript
let num = 10;

if (true) {
  var num = 20; // ❌ SyntaxError: Identifier 'num' has already been declared
}
```

This is known as illegal shadowing. You cannot shadow a `let` variable with `var` because `var` escapes the block and tries to redeclare in the same function scope where `let` already exists. However the reverse is fine:
```javascript
var num = 10;

if (true) {
  let num = 20; // ✅ This is fine — let stays inside the block
  console.log(num); // 20
}

console.log(num); // 10
```

### Declaration

- `var` can be redeclared in the same scope
- `let` and `const` cannot be redeclared in the same scope
- `var` and `let` can be declared without initialization
- `const` cannot be declared without initialization
- `var` and `let` can be reassigned
- `const` cannot be reassigned

📢 NOTES:

> `const` prevents reassignment, not mutation. A `const` object or array can still have its contents changed.
```javascript
const user = { name: "Akshai" };
user.name = "Kumar";  // ✅ mutation is allowed
user = { name: "New" }; // ❌ TypeError: Assignment to constant variable
```

### Hoisting

When a variable is declared in JavaScript, it gets hoisted to the top of its scope, meaning the declaration happens first regardless of where the actual code is.
```javascript
console.log(a); // undefined — declared but not initialized
console.log(b); // ❌ ReferenceError — in temporal dead zone
console.log(c); // ❌ ReferenceError — in temporal dead zone

var a = 10;
let b = 20;
const c = 30;
```

📢 NOTES:

> `var` variables are hoisted and initialized to `undefined`

> `let` and `const` are also hoisted — they are bound in the scope during the creation phase — but they remain *uninitialized* until their declaration line runs. Accessing them before that line is the **temporal dead zone** (TDZ) and throws a `ReferenceError`. They are already in the scope; they are just not ready to use yet.

> Functions declared with the `function` keyword are hoisted completely — both the declaration and the body. Function expressions and arrow functions follow the hoisting rules of their variable declaration (`var`, `let`, or `const`).
```javascript
greet();       // ✅ "Hello" — function declaration is fully hoisted
sayBye();      // ❌ TypeError — sayBye is undefined at this point

function greet() {
  console.log("Hello");
}

var sayBye = function() {
  console.log("Bye");
};
```

### Scope Chain

When JavaScript encounters a variable, it first looks in the current scope. If it doesn't find it, it moves to the outer scope, then the next outer scope, until it reaches the global scope. If it's not found anywhere, it throws a ReferenceError. This lookup process is called the scope chain.
```javascript
var a = 10;

function outer() {
  var b = 20;

  function inner() {
    var c = 30;
    console.log(a + b + c); // 60
    // c found locally
    // b found in outer's scope
    // a found in global scope
  }

  inner();
}

outer();
```
```
inner() scope  →  outer() scope  →  global scope  →  not found? ReferenceError
```

# Strict Mode

Strict mode is a restricted variant of JavaScript that opts you out of some silent failures and legacy quirks. Enable it with `"use strict";` at the top of a file or function. ES modules and class bodies are always strict.

```javascript
"use strict";

function demo() {
  // this function runs in strict mode
}
```

### What changes in strict mode

**1. Assigning to undeclared variables throws**
```javascript
// Non-strict — accidentally creates a global
function leak() {
  x = 10; // creates window.x
}

// Strict — ReferenceError
"use strict";
function noLeak() {
  x = 10; // ❌ ReferenceError: x is not defined
}
```

**2. `this` is `undefined` in bare function calls**
```javascript
function show() {
  console.log(this);
}

show(); // non-strict: window / global
        // strict: undefined
```

This is why calling a method that was detached from its object often crashes in modern code (`Cannot read properties of undefined`) instead of quietly writing to `window`.

**3. Duplicate parameter names are illegal**
```javascript
// Non-strict — allowed (last one wins)
function add(a, a) {
  return a;
}

// Strict — SyntaxError
"use strict";
function add(a, a) { // ❌
  return a;
}
```

**4. `arguments` does not alias parameters**
```javascript
function update(a) {
  a = 20;
  console.log(arguments[0]);
}

update(10);
// Non-strict: 20 — arguments[0] mirrors a
// Strict: 10 — arguments is a snapshot, not a live alias
```

**5. `delete` on non-configurable bindings / plain variables throws**
```javascript
"use strict";
var x = 1;
delete x; // ❌ SyntaxError / TypeError depending on context
```

**6. Octal literals like `0123` are illegal** (use `0o123` instead).

📢 NOTES:

> You rarely write `"use strict"` by hand in modern apps — bundlers, ESM, and classes already put you in strict mode. Still know the differences: interviewers use them to probe whether you understand default `this` binding and accidental globals.

> Arrow functions do not have their own `this` or `arguments` regardless of strict mode — their behavior comes from lexical capture, not the strict/sloppy split.

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

```javascript
const square = (num) => num * num;
```

This is an arrow function, introduced in ES6. When the function body is a single expression, you can omit the `{}` and `return` keyword.

### First-class functions

In languages like JavaScript, functions can be treated as any other variables. Functions can be passed as arguments to other functions, can be returned by another function and can be assigned as values to a variable. Such functions are called first-class functions.

```javascript
// Assigned to a variable
const greet = function(name) {
  return `Hello ${name}`;
};

// Passed as an argument
function execute(fn, value) {
  return fn(value);
}
execute(greet, "Akshai"); // "Hello Akshai"

// Returned from a function
function multiplier(factor) {
  return function(num) {
    return num * factor;
  };
}

const double = multiplier(2);
double(5); // 10
```

### Parameters vs Arguments

A parameter is a variable listed in the function definition. It is a placeholder for the value the function expects to receive.

An argument is the actual value passed into the function when it is called.
```javascript
function addNumbers(num1, num2) {  // num1, num2 are parameters
  return num1 + num2;
}

let result = addNumbers(5, 10);    // 5, 10 are arguments
```

### Default Parameters

When a parameter is not passed, it defaults to `undefined`. You can set your own defaults:
```javascript
function greet(name = "stranger") {
  console.log(`Hello ${name}`);
}

greet("Akshai"); // "Hello Akshai"
greet();         // "Hello stranger"
```

📢 NOTES:

> Default parameters are evaluated at call time, not at definition time. This means if you use an object or array as a default, a new one is created for each call.
```javascript
function addToList(item, list = []) {
  list.push(item);
  return list;
}

addToList(1); // [1]
addToList(2); // [2], NOT [1, 2] — a fresh [] is created each time
```

### Spread vs Rest operators

The spread operator (`...`) expands iterables into individual elements:
```javascript
// Combining arrays
const a = [1, 2];
const b = [3, 4];
const combined = [...a, ...b]; // [1, 2, 3, 4]

// Copying arrays (shallow copy)
const original = [1, 2, 3];
const copy = [...original];

// Passing arguments to functions
const nums = [5, 10, 15];
Math.max(...nums); // 15

// Copying/merging objects
const user = { name: "Akshai" };
const admin = { ...user, role: "admin" }; // { name: "Akshai", role: "admin" }
```

The rest operator (`...`) collects remaining elements into a single variable:
```javascript
// Variable-length function arguments
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3, 4); // 10

// Array destructuring
const [first, second, ...remaining] = [1, 2, 3, 4, 5];
// first = 1, second = 2, remaining = [3, 4, 5]

// Object destructuring
const { name, ...rest } = { name: "Akshai", age: 28, city: "Chennai" };
// name = "Akshai", rest = { age: 28, city: "Chennai" }
```

📢 NOTES:

> A rest parameter must be the last parameter in a function definition. This is because the rest parameter collects all the remaining arguments, so it doesn't make sense to have any parameters after it.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const printData = (a, ...numbers, x, y) => {
  console.log(x, y);
};

printData(5, 6, 7, 8);
```

### Callback functions

A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action.

There are two ways in which the callback may be called: synchronous and asynchronous.

**Synchronous callbacks** are called immediately during the execution of the outer function.
```javascript
const numbers = [1, 2, 3];

numbers.forEach(function(num) {
  console.log(num); // called immediately for each element
});
```

**Asynchronous callbacks** are called at some point later, after an asynchronous operation has completed.
```javascript
setTimeout(function() {
  console.log("This runs later"); // called after 1 second
}, 1000);
```

### Arrow function vs Regular function

|  | Regular function | Arrow function |
|---|---|---|
| `this` binding | Dynamic — depends on how it's called | Lexical — inherits from surrounding scope |
| `arguments` object | ✅ Available | ❌ Not available (use rest params) |
| Can be a constructor | ✅ Works with `new` | ❌ Cannot use `new` |
| Hoisting | ✅ Declarations are hoisted | ❌ Follows variable hoisting rules |
| Duplicate params | ✅ Allowed in non-strict mode | ❌ Never allowed |
| Implicit strict mode | ❌ No | ✅ Yes |

**`this` binding — the most important difference:**
```javascript
function Timer() {
  this.seconds = 0;

  // Regular function — creates its own `this`
  setInterval(function() {
    this.seconds++; // ❌ `this` is window, not Timer
    console.log(this.seconds);
  }, 1000);
}

function TimerFixed() {
  this.seconds = 0;

  // Arrow function — inherits `this` from TimerFixed
  setInterval(() => {
    this.seconds++; // ✅ `this` is TimerFixed instance
    console.log(this.seconds);
  }, 1000);
}
```

**`arguments` object:**
```javascript
function regularFunc() {
  console.log(arguments); // [Arguments] { 0: 1, 1: 2, 2: 3 }
}
regularFunc(1, 2, 3);

const arrowFunc = (...args) => {
  console.log(args); // [1, 2, 3] — use rest params instead
};
arrowFunc(1, 2, 3);
```

**Constructor usage:**
```javascript
function Person(name) {
  this.name = name;
}
const p = new Person("Akshai"); // ✅

const PersonArrow = (name) => {
  this.name = name;
};
const p2 = new PersonArrow("Akshai"); // ❌ TypeError: not a constructor
```

**When to use which:**

Use regular functions when you need dynamic `this` binding, `arguments` object, or a constructor with `new`.

Use arrow functions when you want lexical `this` (callbacks, event handlers inside classes), concise syntax, or non-method functions.

📢 NOTES:

> A **function** is a block of reusable code that performs a certain task. A **method** is a function that is associated with an object. All methods are functions, but not all functions are methods.

### IIFE (Immediately Invoked Function Expression)

A function that is defined and executed immediately. Used to create a private scope and avoid polluting the global namespace.
```javascript
(function() {
  var secret = "hidden";
  console.log(secret); // "hidden"
})();

console.log(secret); // ❌ ReferenceError — not accessible outside

// With parameters
(function(name) {
  console.log(`Hello ${name}`);
})("Akshai"); // "Hello Akshai"

// Arrow function IIFE
(() => {
  console.log("I run immediately");
})();
```

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

# Closures

A closure is created when a function retains access to variables from its outer (lexical) scope, even after the outer function has finished executing and its execution context is destroyed.
```javascript
function outer() {
  let count = 0;

  function inner() {
    count++;
    console.log(count);
  }

  return inner;
}

const counter = outer(); // outer() finishes executing
counter(); // 1 — but inner still has access to count
counter(); // 2
counter(); // 3
```

`outer()` has finished executing. Its execution context is gone from the call stack. But `inner` still has access to `count` because of closure — it "closed over" the variable from its outer scope.

📢 NOTES:

> In JavaScript, a closure is created every time a function is created, at function creation time.

> Closures don't store the *value* of the variable — they store a *reference* to it. This is why the counter example above works — `inner` always has access to the latest value of `count`, not a snapshot of it.

### Lexical Scope

Lexical scoping means a function's access to variables is determined by *where it is written* in the source code, not where it is called. Nested functions have access to variables declared in their outer scope.
```javascript
function outer() {
  const secret = "12345";

  function inner() {
    const secret = "54321";
    console.log(secret); // "54321" — uses its own local variable first
  }

  inner();
}

outer();
```

### Why do we need closures?

**1. Data privacy / encapsulation**

Closures let you create variables that can't be accessed from outside, similar to private fields in other languages.
```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // private — no one can access directly

  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    withdraw(amount) {
      if (amount > balance) return "Insufficient funds";
      balance -= amount;
      return balance;
    },
    getBalance() {
      return balance;
    }
  };
}

const account = createBankAccount(100);
account.deposit(50);     // 150
account.withdraw(30);    // 120
account.getBalance();    // 120
account.balance;         // undefined — can't access directly
```

**2. Function factories**

Create specialized functions from a general template.
```javascript
function createMultiplier(multiplier) {
  return function(num) {
    return num * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

double(5);  // 10
triple(5);  // 15
```

**3. Memoization**

Cache expensive function results to avoid recalculation.
```javascript
function memoize(fn) {
  const cache = {}; // persists across calls because of closure

  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key] !== undefined) {
      console.log("From cache");
      return cache[key];
    }
    console.log("Computing");
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

const expensiveAdd = memoize((a, b) => a + b);
expensiveAdd(1, 2); // "Computing" → 3
expensiveAdd(1, 2); // "From cache" → 3
expensiveAdd(3, 4); // "Computing" → 7
```

**4. Maintaining state in async operations**
```javascript
function createLogger(prefix) {
  return function(message) {
    console.log(`[${prefix}] ${message}`);
  };
}

const errorLog = createLogger("ERROR");
const infoLog = createLogger("INFO");

setTimeout(() => errorLog("Something broke"), 1000);   // [ERROR] Something broke
setTimeout(() => infoLog("Server started"), 2000);     // [INFO] Server started
```

### The classic closure trap

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}
```

> Output: `5 5 5 5 5` — all callbacks share the same `i` (var is function scoped). By the time setTimeout fires, the loop has finished and `i` is 5.

**Three ways to fix this:**
```javascript
// Fix 1: Use let — each iteration gets its own copy
for (let i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}
// Output: 0 1 2 3 4

// Fix 2: Use an IIFE — create a new scope for each iteration
for (var i = 0; i < 5; i++) {
  (function(index) {
    setTimeout(function() {
      console.log(index);
    }, index * 1000);
  })(i);
}
// Output: 0 1 2 3 4

// Fix 3: Helper defined outside the loop (pass i as an argument)
function schedulePrint(index) {
  setTimeout(function() {
    console.log(index);
  }, index * 1000);
}

for (var i = 0; i < 5; i++) {
  schedulePrint(i);
}
// Output: 0 1 2 3 4
```

📢 NOTES:

> Prefer `let` (Fix 1) in modern code. Avoid declaring a `function` *inside* a `for` loop — block-scoped function declarations behave differently across modes and engines, which makes that pattern a poor interview answer.

### Module pattern

The module pattern uses an IIFE to encapsulate private variables and functions, exposing only a public interface. It protects the pieces from the global scope, avoiding possible errors and conflicts.
```javascript
const calculator = (function() {
  // Private
  let result = 0;

  function validate(num) {
    if (typeof num !== "number") throw new Error("Invalid input");
  }

  // Public interface
  return {
    add(num) {
      validate(num);
      result += num;
      return this;
    },
    subtract(num) {
      validate(num);
      result -= num;
      return this;
    },
    getResult() {
      return result;
    },
    reset() {
      result = 0;
      return this;
    }
  };
})();

calculator.add(10).add(5).subtract(3).getResult(); // 12
calculator.result;    // undefined — private
calculator.validate;  // undefined — private
```

### Closures vs Scopes

- **Scope** refers to the visibility and accessibility of variables within a specified context (global scope, function scope, or block scope). It exists while the code in that scope is running.

- **Closure** is what happens when a function *remembers* its scope even after the scope has finished executing. The scope is gone from the call stack, but the function still holds a reference to its variables.
```javascript
// Scope in action — nothing special
function demo() {
  let x = 10;
  console.log(x); // 10 — x is in scope
}
demo();
// x is gone — scope is destroyed

// Closure in action — function outlives its scope
function demo() {
  let x = 10;
  return function() {
    console.log(x); // 10 — x's scope is gone but closure keeps it alive
  };
}
const fn = demo();
fn(); // 10
```

### Common closure pitfalls

**1. Accidental reference sharing**
```javascript
function createFunctions() {
  var functions = [];
  for (var i = 0; i < 3; i++) {
    functions.push(function() {
      return i;
    });
  }
  return functions;
}

const fns = createFunctions();
fns[0](); // 3 — not 0!
fns[1](); // 3 — not 1!
fns[2](); // 3 — not 2!
// All three functions share the same `i` reference
```

**2. Memory leaks**

Closures keep their outer variables alive in memory. If a closure references a large object and the closure itself is long-lived (e.g., attached to an event listener), that object won't be garbage collected.
```javascript
function setup() {
  const hugeData = new Array(1000000).fill("data");

  // This closure keeps hugeData alive as long as the listener exists
  document.getElementById("btn").addEventListener("click", function() {
    console.log(hugeData.length);
  });
}

// Fix: remove the event listener when no longer needed
// or only close over the specific data you need, not the entire object
```

# Objects

An object is a collection of key-value pairs. Keys are strings (or Symbols), and values can be anything — primitives, arrays, functions, or other objects.
```javascript
const user = {
  name: "Akshai",
  age: 28,
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

// Accessing properties
user.name;           // "Akshai" — dot notation
user["age"];         // 28 — bracket notation

// Adding properties
user.city = "Chennai";

// Deleting properties
delete user.city;
```

📢 NOTES:

> `delete` only works on object properties. It does not work on variables or function parameters.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const result = (function (num) {
  delete num;
  return num;
})(5);

console.log(result);
```

### Object keys are always strings

When you use a non-string value as a key, JavaScript converts it to a string using `.toString()`.

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

### Duplicate keys

When an object has duplicate keys, the last one wins.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const obj = {
  a: "one",
  b: "two",
  a: "three",
};

console.log(obj);
```

### Object references

Objects are reference types. Variables don't hold the object — they hold a pointer to it.

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

### Spread operator with objects

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

📢 NOTES:

> Spread order matters. Properties that come later overwrite earlier ones:
```javascript
const defaults = { theme: "light", lang: "en" };
const userPrefs = { theme: "dark" };

const settings = { ...defaults, ...userPrefs };
// { theme: "dark", lang: "en" } — userPrefs.theme overwrites defaults.theme
```

### Default parameter gotcha with objects

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

> When no argument is passed, the default `{ ...values }` creates a new copy each time. When `values` is passed directly, mutations affect the original object.

### Useful object methods

**Object.keys(), Object.values(), Object.entries()**
```javascript
// freeze — cannot add, remove, or modify properties
const frozen = Object.freeze({ name: "Akshai", age: 28 });
frozen.name = "Kumar";   // silently fails (or throws in strict mode)
frozen.city = "Chennai"; // silently fails
delete frozen.name;      // silently fails
console.log(frozen);     // { name: "Akshai", age: 28 }

// seal — cannot add or remove, but CAN modify existing properties
const sealed = Object.seal({ name: "Akshai", age: 28 });
sealed.name = "Kumar";   // ✅ works
sealed.city = "Chennai"; // silently fails
delete sealed.name;      // silently fails
console.log(sealed);     // { name: "Kumar", age: 28 }
```

📢 NOTES:

> Both `freeze` and `seal` are shallow. Nested objects are not affected:
```javascript
const user = Object.freeze({
  name: "Akshai",
  address: { city: "Chennai" }
});

user.address.city = "Mumbai"; // ✅ works — nested object is not frozen
```

**Object.assign()**
```javascript
const target = { a: 1 };
const source = { b: 2, c: 3 };

Object.assign(target, source);
console.log(target); // { a: 1, b: 2, c: 3 } — target is mutated
```

### JSON methods

**JSON.stringify() — object to string**

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

**JSON.parse() — string to object**
```javascript
const json = '{"name":"Akshai","age":28}';
const obj = JSON.parse(json);
console.log(obj.name); // "Akshai"
```

📢 NOTES:

> `JSON.stringify()` ignores `undefined`, functions, and Symbols:
```javascript
const obj = {
  name: "Akshai",
  greet: function() {},
  age: undefined,
  id: Symbol("id")
};

JSON.stringify(obj);
// '{"name":"Akshai"}' — greet, age, and id are all stripped
```

> Circular references throw an error:
```javascript
const a = {};
a.self = a;
JSON.stringify(a); // ❌ TypeError: Converting circular structure to JSON
```

### How to clone/deep copy an object in JavaScript?

```javascript
let user = {
  name: "user",
  age: 28,
};
```

**Shallow copy methods** — only copy the top level. Nested objects are still shared:
```javascript
// Spread operator
const clone1 = { ...user };

// Object.assign
const clone2 = Object.assign({}, user);
```

**Deep copy methods** — create completely independent copies including nested objects:
```javascript
// JSON parse + stringify
// ⚠️ Loses functions, undefined, Symbols, Dates become strings
const clone3 = JSON.parse(JSON.stringify(user));

// structuredClone (modern, recommended)
// ✅ Handles nested objects, Dates, Maps, Sets, ArrayBuffers
// ❌ Cannot clone functions or DOM elements
const clone4 = structuredClone(user);
```

**When to use which:**

- Simple flat object → spread operator (fastest, most readable)
- Flat object with many properties → `Object.assign()`
- Nested object without functions → `structuredClone()` (modern) or `JSON.parse(JSON.stringify())` (legacy)
- Full control needed → write a recursive deep clone function

[Polyfill for deep clone](https://github.com/akshaitr/js-polyfills/blob/main/src/deepClone.js)

### Destructuring

```javascript
// Object destructuring
const user = { name: "Akshai", age: 28, city: "Chennai" };
const { name, age } = user;

// With renaming
const { name: userName } = user; // userName = "Akshai"

// With defaults
const { country = "India" } = user; // country = "India" (doesn't exist in user)

// Nested destructuring
const response = {
  data: {
    user: {
      name: "Akshai"
    }
  }
};

const { data: { user: { name: deepName } } } = response;
console.log(deepName); // "Akshai"
```

### Optional chaining and nullish coalescing
```javascript
const user = {
  name: "Akshai",
  address: null
};

// Without optional chaining
const city = user.address && user.address.city; // null

// With optional chaining
const city = user.address?.city;        // undefined — no crash
const zip = user.address?.zip?.code;    // undefined — safe at any depth

// Works with methods too
user.getName?.(); // undefined if getName doesn't exist

// Nullish coalescing — default for null/undefined only
const theme = user.theme ?? "light";    // "light"

// Difference from ||
const count = 0;
count || 10;   // 10 — because 0 is falsy
count ?? 10;   // 0 — because 0 is not null/undefined
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

# Arrays

Arrays are objects with numeric keys, a special `length` property, and methods inherited from `Array.prototype`. Most day-to-day frontend work is array transformation — so interviewers probe the edge cases, not just `.map`.

### Array basics worth remembering
```javascript
const nums = [10, 20, 30];

nums.length;        // 3
nums[0];            // 10
nums[nums.length];  // undefined — last valid index is length - 1

// Arrays are objects
typeof [];                  // "object"
Array.isArray([]);          // true — preferred check
Array.isArray({ length: 1 }); // false
```

### Sparse arrays (holes)

A sparse array has "holes" — indexes that were never set. Holes are not the same as `undefined`.
```javascript
const sparse = [1, , 3]; // hole at index 1
sparse.length;           // 3
1 in sparse;             // false — hole
2 in sparse;             // true

const dense = [1, undefined, 3];
1 in dense;              // true — key exists, value is undefined
```

Many array methods *skip holes*; some do not:
```javascript
[1, , 3].map((x) => x);           // [1, empty, 3] — hole preserved
[1, , 3].forEach((x) => console.log(x)); // logs 1, then 3 — skips hole
[1, , 3].join("-");               // "1--3" — hole becomes empty string
[...[1, , 3]];                    // [1, undefined, 3] — hole becomes undefined
```

📢 NOTES:

> Prefer dense arrays. Creating holes with `new Array(3)` or deleting indexes leads to surprising `map` / `forEach` behavior in interviews and bugs in production.

### Common mutation vs non-mutation methods
```
Mutating (change original)     | Non-mutating (return new)
-------------------------------|---------------------------
push, pop, shift, unshift      | concat, slice
splice, reverse, sort          | map, filter, flat, flatMap
fill, copyWithin               | toSorted, toReversed, toSpliced (ES2023)
                               | with (ES2023)
```
```javascript
const a = [3, 1, 2];
a.sort();          // mutates a → [1, 2, 3]
const b = [3, 1, 2];
b.toSorted();      // [1, 2, 3] — b unchanged
```

### sort() pitfalls

`sort()` converts elements to strings by default — and the comparator must return a number.
```javascript
[10, 2, 1].sort();
// ["1", "10", "2"] as strings → [1, 10, 2] numerically wrong for humans

[10, 2, 1].sort((a, b) => a - b); // [1, 2, 10] — correct numeric ascending
[10, 2, 1].sort((a, b) => b - a); // [10, 2, 1] — descending
```

📢 NOTES:

> Modern JS engines use a stable sort — equal elements keep their relative order. Don't rely on stability in very old environments without checking.

> Returning a boolean from the comparator (`a > b`) is a common bug — coerce to `-1` / `0` / `1` (or use `a - b` for numbers).

### reduce — powerful and easy to misuse
```javascript
const sum = [1, 2, 3].reduce((acc, n) => acc + n, 0); // 6

// Build an object
const users = [
  { id: 1, name: "Akshai" },
  { id: 2, name: "Kumar" },
];

const byId = users.reduce((acc, user) => {
  acc[user.id] = user;
  return acc;
}, {});
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const result = ["1", "2", "3"].map(parseInt);
console.log(result);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log([1, 2, 3] + [4, 5]);
console.log([] + []);
console.log([] + {});
console.log({} + []);
```

### Array-like objects
```javascript
function demo() {
  console.log(arguments.length);       // array-like, not a real array
  console.log(Array.from(arguments));  // convert to real array
  console.log([...arguments]);         // also works if iterable
}

document.querySelectorAll("div"); // NodeList — use Array.from / spread for map/filter
```

# Type Coercion and Equality

Type coercion is when JavaScript automatically converts a value from one type to another. It shows up in `==`, arithmetic, string concatenation, `if` conditions, and `ToPrimitive` hooks.

### Truthy and falsy
```javascript
// Falsy values — everything else is truthy
false, 0, -0, 0n, "", null, undefined, NaN

Boolean([]);  // true — empty array is truthy
Boolean({});  // true — empty object is truthy
Boolean("0"); // true — non-empty string
```

### Explicit vs implicit conversion
```javascript
// Explicit
Number("42");     // 42
String(42);       // "42"
Boolean(1);       // true
parseInt("08", 10); // 8 — always pass radix

// Implicit
"5" + 1;   // "51" — + prefers string concatenation if either side is string
"5" - 1;   // 4  — - forces numeric conversion
"5" * "2"; // 10
```

### == vs ===

`===` (strict equality) compares type and value — no coercion.
`==` (loose equality) coerces operands using the Abstract Equality Comparison algorithm.
```javascript
0 === false;  // false
0 == false;   // true

null === undefined; // false
null == undefined;  // true — special case

"" == 0;      // true
"0" == 0;     // true
"0" == false; // true

NaN === NaN;  // false — use Number.isNaN(x) or Object.is(x, NaN)
```

📢 NOTES:

> Default to `===` / `!==`. Reach for `==` only when you intentionally want `null == undefined` style nullish checks — and even then `x == null` is the rare idiomatic case.

> `Object.is(a, b)` is like `===` except it treats `NaN` as equal to `NaN`, and distinguishes `+0` from `-0`.

### ToPrimitive — how objects become primitives

When JS needs a primitive from an object (for `+`, `==`, `>`, `String()`, etc.), it calls `ToPrimitive`:
1. Prefer `obj[Symbol.toPrimitive](hint)` if present
2. Else for hint `"string"`: try `toString()` then `valueOf()`
3. Else for hint `"number"` / `"default"`: try `valueOf()` then `toString()`
```javascript
const obj = {
  valueOf() {
    return 10;
  },
  toString() {
    return "ten";
  },
};

console.log(obj + 1);      // 11 — valueOf used for default/number hint
console.log(String(obj));  // "ten" — toString used for string hint
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log([] == false);
console.log([] == ![]);
console.log({} == true);
console.log([1] == 1);
console.log([1, 2] == "1,2");
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
console.log(null == 0);
console.log(null >= 0);
console.log(null > 0);
console.log(undefined == 0);
console.log(undefined >= 0);
```

# Map and Set

`Map` and `Set` are built-in collections that fix common pain points of using plain objects and arrays as data stores.

### Map — key/value with real key identity

Object keys are strings (or symbols). Map keys can be *any* value — objects, functions, numbers, `NaN`.
```javascript
const map = new Map();

const user = { id: 1 };
map.set(user, "Akshai");
map.set(42, "answer");
map.set(NaN, "not a number");

map.get(user); // "Akshai"
map.get(42);   // "answer"
map.get(NaN);  // "not a number" — NaN is treatable as a key

map.size;          // 3
map.has(user);     // true
map.delete(42);    // true
map.clear();
```

**Iteration order is insertion order:**
```javascript
const map = new Map([
  ["b", 2],
  ["a", 1],
]);

for (const [key, value] of map) {
  console.log(key, value); // "b" 2, then "a" 1
}

[...map.keys()];   // ["b", "a"]
[...map.values()]; // [2, 1]
[...map.entries()]; // [["b", 2], ["a", 1]]
```

**When to prefer Map over a plain object:**
- Keys are not strings/symbols (especially objects)
- You need reliable `.size`
- You frequently add/delete keys and want to avoid prototype-key pitfalls (`__proto__`, inherited keys)
- Key order matters and keys are dynamic

**When a plain object is fine:**
- Fixed string keys, JSON-shaped data, simple records

### Set — unique values
```javascript
const set = new Set([1, 2, 2, 3]);
set.size;        // 3
set.add(4);
set.has(2);      // true
set.delete(1);

// Deduplicate an array
const unique = [...new Set([1, 1, 2, 3, 3])]; // [1, 2, 3]

// Fast membership checks vs array.includes for large collections
const allowed = new Set(["admin", "editor"]);
allowed.has("admin"); // true — O(1) average
```

Equality in a Set/Map uses SameValueZero — like `===`, but `NaN` equals `NaN`.
```javascript
new Set([NaN, NaN]).size; // 1
new Set([+0, -0]).size;   // 1 — +0 and -0 are the same key
```

### Map vs Object vs Set vs Array
```
Need                         | Prefer
-----------------------------|--------
JSON / record shape          | Object
Arbitrary keys + size        | Map
Unique list / membership     | Set
Ordered list, duplicates OK  | Array
GC-friendly object keys      | WeakMap / WeakSet (next section)
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const a = { id: 1 };
const b = { id: 1 };
const map = new Map();

map.set(a, "first");
map.set(b, "second");
map.set({ id: 1 }, "third");

console.log(map.size);
console.log(map.get(a));
console.log(map.get({ id: 1 }));
```

# WeakMap and WeakSet

### The garbage collection problem

Regular Maps and Sets hold strong references to their keys/values — even if the original object is no longer used elsewhere, the Map/Set prevents it from being garbage collected.
```javascript
let user = { name: "Akshai" };
const metadata = new Map();
metadata.set(user, { lastLogin: "today" });

user = null; // we're done with user
// BUT the Map still holds a reference to the object
// It will NEVER be garbage collected as long as the Map exists
```

### WeakMap

A WeakMap holds **weak references** to its keys. If the key object has no other references, it gets garbage collected automatically — and the entry disappears from the WeakMap.
```javascript
let user = { name: "Akshai" };
const weakMeta = new WeakMap();
weakMeta.set(user, { lastLogin: "today" });

user = null;
// The { name: "Akshai" } object can now be garbage collected
// The WeakMap entry is automatically removed
```

**Constraints:**
- Keys MUST be objects (not primitives)
- Not iterable — no `.forEach()`, `.keys()`, `.values()`, `.entries()`
- No `.size` property
- Only has `.get()`, `.set()`, `.has()`, `.delete()`

**Why these constraints?** Because garbage collection is non-deterministic — you can't know when or if an entry will be removed, so iteration would give unpredictable results.

**Practical use case — private data:**
```javascript
const privateData = new WeakMap();

class User {
  constructor(name, password) {
    this.name = name;
    privateData.set(this, { password }); // truly private, not on the instance
  }

  checkPassword(input) {
    return privateData.get(this).password === input;
  }
}

const user = new User("Akshai", "secret123");
user.name;               // "Akshai" — public
user.password;           // undefined — not on the object
user.checkPassword("secret123"); // true
```

**Practical use case — caching expensive computations:**
```javascript
const cache = new WeakMap();

function expensiveProcess(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }

  const result = /* expensive computation */ obj.data.length * 100;
  cache.set(obj, result);
  return result;
}

let bigData = { data: new Array(1000000) };
expensiveProcess(bigData); // computes
expensiveProcess(bigData); // from cache

bigData = null;
// Cache entry is automatically cleaned up — no memory leak
```

**Practical use case — DOM element metadata:**
```javascript
const elementData = new WeakMap();

function trackClicks(element) {
  if (!elementData.has(element)) {
    elementData.set(element, { clicks: 0 });
  }

  element.addEventListener("click", () => {
    const data = elementData.get(element);
    data.clicks++;
  });
}

// When the DOM element is removed and garbage collected,
// the WeakMap entry is automatically cleaned up
```

### WeakSet

Same concept as WeakMap but for values instead of key-value pairs. Tracks whether an object is in the set without preventing garbage collection.
```javascript
const visited = new WeakSet();

function processOnce(obj) {
  if (visited.has(obj)) {
    console.log("Already processed");
    return;
  }

  visited.add(obj);
  console.log("Processing:", obj.name);
}

let user1 = { name: "Akshai" };
let user2 = { name: "Kumar" };

processOnce(user1); // "Processing: Akshai"
processOnce(user1); // "Already processed"
processOnce(user2); // "Processing: Kumar"

user1 = null; // WeakSet automatically cleans up the entry
```

### Map vs WeakMap

See also the full [Map and Set](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#map-and-set) section for when to use `Map` vs plain objects.
```
Feature        | Map                  | WeakMap
---------------|----------------------|-------------------
Keys           | Any type             | Objects only
Garbage collect| No — strong ref      | Yes — weak ref
Iterable       | Yes                  | No
.size          | Yes                  | No
Use case       | General key-value    | Metadata, caching,
               | storage              | private data
```

# Binding

`this` in JavaScript is determined by *how* a function is called, not where it's defined. This makes it fundamentally different from most other languages (including Java, where `this` always refers to the current object instance).

There are four ways `this` gets bound:

### 1. Default binding

When a function is called without any context, `this` defaults to `window` (browser) or `global` (Node.js). In strict mode, it's `undefined`.
```javascript
function showThis() {
  console.log(this);
}

showThis(); // window (or global in Node.js)

"use strict";
function showThisStrict() {
  console.log(this);
}

showThisStrict(); // undefined
```

### 2. Implicit binding

When a function is called as a method of an object, `this` refers to the object that's calling it — the object to the *left of the dot*.

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

**The implicit binding trap — losing `this`:**

When you extract a method from an object, it loses its implicit binding.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const user = {
  name: "Akshai",
  logName() {
    console.log(this.name);
  },
};

user.logName();

const fn = user.logName;
fn();

setTimeout(user.logName, 1000);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
const user = {
  name: "Akshai",
  logName() {
    console.log(this.name);
  },
};

setTimeout(function () {
  user.logName();
}, 1000);

setTimeout(() => user.logName(), 1000);
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function createUser() {
  return {
    name: "John",
    ref: this,
  };
}

const user1 = createUser();
console.log(user1.ref.name);

const user2 = new createUser();
console.log(user2.ref.name);
```

### 3. Explicit binding — call, apply, and bind

When you want to manually set `this`, you use `call`, `apply`, or `bind`.

**call** — invokes the function immediately with a specified `this` and individual arguments.

```javascript
var user = {
  name: "Akshai",
};

function greeting(greetingText, punctuation) {
  return `${greetingText} ${this.name}${punctuation}`;
}

console.log(greeting.call(user, "Hello", "!!")); // "Hello Akshai!!"
```

**apply** — same as `call` but arguments are passed as an array.

```javascript
console.log(greeting.apply(user, ["Hello", "!!"])); // "Hello Akshai!!"
```

**bind** — does NOT invoke the function. Returns a new function with `this` permanently bound.
```javascript
const greetUser = greeting.bind(user);
console.log(greetUser("Hello", "!!")); // "Hello Akshai!!"

// Useful for callbacks where you'd lose this
const user = {
  name: "Akshai",
  logName() {
    console.log(this.name);
  },
};

setTimeout(user.logName.bind(user), 1000); // "Akshai" — this is locked
```

**Quick memory trick:**
- **call** → **c**omma separated arguments
- **apply** → **a**rray of arguments
- **bind** → **b**ookmarks `this` for later

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

### 4. new binding

When a function is called with `new`, `this` refers to the newly created instance.
```javascript
function Person(name) {
  this.name = name;
  this.greet = function() {
    console.log(`Hi, I'm ${this.name}`);
  };
}

const p1 = new Person("Akshai");
const p2 = new Person("Kumar");

p1.greet(); // "Hi, I'm Akshai"
p2.greet(); // "Hi, I'm Kumar"
```

What `new` does behind the scenes:
```javascript
function Person(name) {
  // 1. Creates a new empty object: {}
  // 2. Sets this = that new object
  // 3. Links the object's prototype to Person.prototype
  this.name = name;
  // 4. Returns this (implicitly)
}
```

### Binding precedence

When multiple rules could apply, this is the priority order:

> new binding  >  explicit binding (call/apply/bind)  >  implicit binding  >  default binding

```javascript
function greet() {
  console.log(this.name);
}

const obj1 = { name: "obj1", greet };
const obj2 = { name: "obj2" };

// Implicit vs explicit — explicit wins
obj1.greet.call(obj2); // "obj2"

// bind vs call — bind wins (once bound, can't be overridden)
const bound = greet.bind(obj1);
bound.call(obj2); // "obj1" — bind takes priority
```

### The `arguments` and `this` trick

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
    arguments[0]();
  },
};

obj.method(callback, 2, 3);
```

> This is one of the trickiest `this` questions in JavaScript interviews. The key insight is that `arguments` is an array-like *object*, and calling a function stored inside it makes `this` point to `arguments`.

# Prototypes

Every object in JavaScript has a hidden internal link to another object called its **prototype**. When you access a property or method on an object, JavaScript first looks on the object itself. If it doesn't find it, it follows the prototype link and looks there. This continues up the chain until it either finds the property or reaches `null`.

This is called the **prototype chain** — and it's how JavaScript implements inheritance.
```javascript
const myObject = {
  city: "Madrid",
  greet() {
    console.log(`Greetings from ${this.city}`);
  },
};

myObject.greet();      // ✅ found on myObject itself
myObject.toString();   // ✅ found on Object.prototype (inherited)
myObject.someMethod(); // ❌ not found anywhere in chain → TypeError
```

**How did `toString()` work?** We never defined it on `myObject`. JavaScript looked up the prototype chain:
```
myObject                    → doesn't have toString()
  └── Object.prototype      → has toString() ✅
        └── null             (end of chain)
```

### Visualizing the prototype chain
```javascript
const arr = [1, 2, 3];

// Where does .map() come from?
arr.map(n => n * 2);
```
```
arr (instance)
  └── Array.prototype        → has map(), filter(), reduce(), push(), etc.
        └── Object.prototype  → has toString(), hasOwnProperty(), etc.
              └── null         (end of chain)
```
```javascript
function greet() {
  console.log("Hello");
}

// Where does .call() come from?
greet.call(someObj);
```
```
greet (function instance)
  └── Function.prototype      → has call(), apply(), bind()
        └── Object.prototype   → has toString(), hasOwnProperty()
              └── null
```

Everything in JavaScript eventually chains back to `Object.prototype`, and `Object.prototype`'s prototype is `null` — that's where the chain ends.

### `__proto__` vs `prototype`

These two are easily confused but serve very different purposes:

**`__proto__`** — exists on every object. It's a pointer to that object's prototype (the object it inherits from).

**`prototype`** — exists only on functions. It's the object that will become the `__proto__` of instances created with `new`.
```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hi, I'm ${this.name}`;
};

const p = new Person("Akshai");

// p.__proto__ points to Person.prototype
console.log(p.__proto__ === Person.prototype); // true

// Person.prototype.__proto__ points to Object.prototype
console.log(Person.prototype.__proto__ === Object.prototype); // true

// Object.prototype.__proto__ is null — end of chain
console.log(Object.prototype.__proto__ === null); // true
```
```
p (instance)
  │
  │ p.__proto__
  ▼
Person.prototype          { greet() }
  │
  │ Person.prototype.__proto__
  ▼
Object.prototype          { toString(), hasOwnProperty(), ... }
  │
  │ Object.prototype.__proto__
  ▼
null
```

📢 NOTES:

> `__proto__` is the legacy way to access an object's prototype. The modern methods are:
> - `Object.getPrototypeOf(obj)` — read the prototype
> - `Object.setPrototypeOf(obj, proto)` — set the prototype
> - `Object.create(proto)` — create a new object with a specific prototype

### Constructor functions and prototypes

Constructor functions are the original way JavaScript handled "classes" before the `class` keyword existed.
```javascript
function Box(value) {
  this.value = value;
}

Box.prototype.getValue = function() {
  return this.value;
};

Box.prototype.setValue = function(newValue) {
  this.value = newValue;
};

const box1 = new Box(1);
const box2 = new Box(2);

box1.getValue(); // 1
box2.getValue(); // 2
```

**Why define methods on `prototype` instead of inside the constructor?**
```javascript
// BAD — creates a new function for EVERY instance
function Box(value) {
  this.value = value;
  this.getValue = function() {
    return this.value;
  };
}

const box1 = new Box(1);
const box2 = new Box(2);
box1.getValue === box2.getValue; // false — two separate functions in memory

// GOOD — all instances share ONE function on the prototype
function Box(value) {
  this.value = value;
}

Box.prototype.getValue = function() {
  return this.value;
};

const box3 = new Box(3);
const box4 = new Box(4);
box3.getValue === box4.getValue; // true — same function, shared via prototype
```

If you create 1000 instances, the bad approach creates 1000 copies of `getValue` in memory. The prototype approach has just one copy shared by all instances.

### What `new` does behind the scenes

When a function is called with `new`, JavaScript does four things:
```javascript
function Person(name) {
  // 1. Creates a new empty object: {}
  // 2. Sets the new object's __proto__ to Person.prototype
  // 3. Binds `this` to the new object and executes the constructor
  this.name = name;
  // 4. Returns `this` (unless the constructor explicitly returns an object)
}

// Simulating what new does:
function simulateNew(Constructor, ...args) {
  const obj = {};                                    // step 1
  Object.setPrototypeOf(obj, Constructor.prototype); // step 2
  const result = Constructor.apply(obj, args);       // step 3
  return result instanceof Object ? result : obj;    // step 4
}
```

📢 NOTES:

> Arrow functions don't have a `prototype` property and can't be used with `new`. This makes sense because arrow functions don't have their own `this`, which is essential for constructors.
```javascript
const Person = (name) => {
  this.name = name;
};

new Person("Akshai"); // ❌ TypeError: Person is not a constructor
```

### Prototype inheritance

Objects can inherit from other objects through the prototype chain:
```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  return `${this.name} makes a sound`;
};

function Dog(name, breed) {
  Animal.call(this, name); // call parent constructor with this context
  this.breed = breed;
}

// Set up inheritance — Dog.prototype inherits from Animal.prototype
Dog.prototype = Object.create(Animal.prototype);

// Fix the constructor reference (it got overwritten in the line above)
Dog.prototype.constructor = Dog;

// Add Dog-specific methods
Dog.prototype.fetch = function() {
  return `${this.name} fetches the ball`;
};

const dog = new Dog("Rex", "Labrador");
dog.speak();  // "Rex makes a sound" — inherited from Animal
dog.fetch();  // "Rex fetches the ball" — own method
```
```
dog (instance)
  └── Dog.prototype        → has fetch()
        └── Animal.prototype → has speak()
              └── Object.prototype
                    └── null
```

### instanceof

Checks if an object exists anywhere in the prototype chain of a constructor.
```javascript
console.log(dog instanceof Dog);    // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Object); // true

// instanceof walks up the prototype chain:
// dog.__proto__ === Dog.prototype? → yes → true for Dog
// Dog.prototype.__proto__ === Animal.prototype? → yes → true for Animal
// Animal.prototype.__proto__ === Object.prototype? → yes → true for Object
```

### hasOwnProperty vs `in`
```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hi, I'm ${this.name}`;
};

const p = new Person("Akshai");

// hasOwnProperty — only checks the object itself, not the chain
p.hasOwnProperty("name");  // true — defined on p directly
p.hasOwnProperty("greet"); // false — greet is on the prototype

// in operator — checks the entire prototype chain
"name" in p;   // true
"greet" in p;  // true — found on prototype
```

This distinction matters when iterating over objects:
```javascript
for (const key in p) {
  console.log(key); // "name", "greet" — includes inherited properties
}

for (const key in p) {
  if (p.hasOwnProperty(key)) {
    console.log(key); // "name" — only own properties
  }
}

// Modern alternative
Object.keys(p);    // ["name"] — only own enumerable properties
Object.values(p);  // ["Akshai"]
Object.entries(p);  // [["name", "Akshai"]]
```

### Object.create()

Creates a new object with a specified prototype. A cleaner alternative to constructor functions for simple inheritance.
```javascript
const animal = {
  speak() {
    return `${this.name} makes a sound`;
  }
};

const dog = Object.create(animal);
dog.name = "Rex";
dog.fetch = function() {
  return `${this.name} fetches the ball`;
};

dog.speak(); // "Rex makes a sound" — inherited from animal
dog.fetch(); // "Rex fetches the ball" — own method

// Creating an object with NO prototype
const bare = Object.create(null);
bare.toString; // undefined — no prototype chain at all
```

### setPrototypeOf()

Dynamically changes an object's prototype after creation. Use sparingly — it's slow and can cause unexpected behavior.
```javascript
const user = {
  name: "Akshai",
  age: 28,
};

const adminUser = {
  isAdmin: true,
  deleteUser() {
    return "User deleted";
  }
};

console.log(user.isAdmin); // undefined

Object.setPrototypeOf(user, adminUser);

console.log(user.isAdmin);      // true — inherited from adminUser
console.log(user.deleteUser());  // "User deleted"
console.log(user.name);          // "Akshai" — own property still works
```

📢 NOTES:

> `Object.setPrototypeOf()` is discouraged in performance-critical code. V8 and other engines optimize objects based on their prototype chain at creation time. Changing it later forces the engine to deoptimize. Prefer `Object.create()` to set the prototype at creation time.

### Prototype chain complete picture
```
                          ┌──────────────────────┐
                          │         null         │
                          └──────────┬───────────┘
                                     │
                          ┌──────────▼────────────┐
                          │  Object.prototype     │
                          │  toString()           │
                          │  hasOwnProperty()     │
                          │  valueOf()            │
                          └──────────┬────────────┘
                 ┌───────────────────┼───────────────────┐
                 │                   │                   │
         ┌───────▼─────────┐  ┌──────▼────────┐  ┌───────▼───────┐
         │ Array.prototype │  │Function.proto │  │ Your object   │
         │ map()           │  │ call()        │  │ { key: val }  │
         │ filter()        │  │ apply()       │  └───────────────┘
         │ reduce()        │  │ bind()        │
         └────────┬────────┘  └───────────────┘
                  │
         ┌────────▼────────┐
         │ [1, 2, 3]       │
         │ (array instance)│
         └─────────────────┘
```

# Class and constructors

Classes in JavaScript are syntactic sugar over prototypes. They don't introduce a new inheritance model — they just provide a cleaner syntax for what we did in the Prototypes section with constructor functions.
```javascript
// Prototype way (old)
function Rectangle(height, width) {
  this.height = height;
  this.width = width;
}

Rectangle.prototype.calcArea = function() {
  return this.height * this.width;
};

// Class way (modern) — same thing underneath
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  calcArea() {
    return this.height * this.width;
  }
}

const rect = new Rectangle(4, 5);
rect.calcArea(); // 20
```

Both produce the exact same prototype chain. The `class` syntax is just easier to read and write.
```javascript
// Proof that classes are just prototypes
typeof Rectangle; // "function" — not "class"
rect.__proto__ === Rectangle.prototype; // true
```

### Constructor

The `constructor` method is a special method that runs when you create an instance with `new`. A class can only have one constructor.

```javascript
class Employee {
  constructor() {
    this.name = "John";
  }

  constructor() {
    this.age = 30;
  }
}

const employee = new Employee();
console.log(employee.name);
```

> ❌ SyntaxError: A class may only have one constructor — JavaScript doesn't allow multiple constructors like Java does with method overloading.

### Class expressions

Just like functions, classes can be defined as expressions:
```javascript
// Named class expression
const MyClass = class Rectangle {
  constructor(h, w) {
    this.height = h;
    this.width = w;
  }
};

// The name "Rectangle" is only accessible inside the class body
console.log(MyClass.name); // "Rectangle"
new Rectangle(4, 5);       // ❌ ReferenceError — not accessible outside

// Anonymous class expression
const Square = class {
  constructor(side) {
    this.side = side;
  }
};
```

### Class hoisting

Unlike function declarations, classes are NOT hoisted. They behave like `let` and `const` — they exist in the temporal dead zone until the declaration is reached.
```javascript
const rect = new Rectangle(4, 5); // ❌ ReferenceError — can't access before declaration

class Rectangle {
  constructor(h, w) {
    this.height = h;
    this.width = w;
  }
}
```

### Methods
```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // Instance method — defined on the prototype, shared by all instances
  greet() {
    return `Hi, I'm ${this.name}`;
  }

  // Getter — access like a property, not a method call
  get displayName() {
    return `${this.name} <${this.email}>`;
  }

  // Setter — assign like a property
  set displayName(value) {
    const [name, email] = value.split(" ");
    this.name = name;
    this.email = email;
  }
}

const user = new User("Akshai", "akshai@email.com");

user.greet();                // "Hi, I'm Akshai" — method call with ()
user.displayName;            // "Akshai " — no () needed
user.displayName = "Kumar kumar@email.com";
user.name;                   // "Kumar"
```

### Static properties and methods

Static members belong to the class itself, not to instances. They're accessed on the class directly.
```javascript
class MathUtils {
  static PI = 3.14159;

  static square(n) {
    return n * n;
  }

  static max(...nums) {
    return nums.reduce((a, b) => a > b ? a : b);
  }
}

// Accessed on the class
MathUtils.PI;          // 3.14159
MathUtils.square(5);   // 25
MathUtils.max(1, 5, 3); // 5

// NOT on instances
const m = new MathUtils();
m.PI;       // undefined
m.square;   // undefined
```

**When to use static:**
- Utility functions that don't need instance data (like `Math.max`, `Math.random`)
- Factory methods that create instances
- Constants shared across all instances
- Counters or caches at the class level
```javascript
class User {
  static count = 0;

  constructor(name) {
    this.name = name;
    User.count++; // track total instances created
  }

  static getCount() {
    return User.count;
  }
}

new User("Akshai");
new User("Kumar");
User.getCount(); // 2
```

### Private fields and methods

Properties prefixed with `#` are truly private — not accessible outside the class. This is a recent addition to JavaScript (ES2022).
```javascript
class BankAccount {
  #balance;        // private field
  #accountNumber;

  constructor(accountNumber, initialBalance) {
    this.#accountNumber = accountNumber;
    this.#balance = initialBalance;
  }

  deposit(amount) {
    if (amount <= 0) throw new Error("Invalid amount");
    this.#balance += amount;
    this.#logTransaction("deposit", amount);
  }

  withdraw(amount) {
    if (amount > this.#balance) throw new Error("Insufficient funds");
    this.#balance -= amount;
    this.#logTransaction("withdrawal", amount);
  }

  get balance() {
    return this.#balance;
  }

  // Private method
  #logTransaction(type, amount) {
    console.log(`${type}: ${amount}, Balance: ${this.#balance}`);
  }
}

const account = new BankAccount("ACC001", 1000);
account.deposit(500);     // "deposit: 500, Balance: 1500"
account.balance;          // 1500 — via getter
account.#balance;         // ❌ SyntaxError — private field
account.#logTransaction;  // ❌ SyntaxError — private method
```

**Before `#` existed, developers used closures or naming conventions:**
```javascript
// Convention only — not actually private (anyone can access)
class User {
  constructor(name) {
    this._name = name; // underscore = "please don't touch" (but you can)
  }
}

// Closure — actually private but awkward
class User {
  constructor(name) {
    let _name = name; // truly private via closure
    this.getName = () => _name;
  }
}
```

### Class inheritance

The `extends` keyword creates a child class that inherits from a parent class. `super` calls the parent's constructor or methods.
```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }

  toString() {
    return `[Animal: ${this.name}]`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);       // MUST call super() before using `this`
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks`;  // overrides parent method
  }

  fetch() {
    return `${this.name} fetches the ball`;
  }
}

class GuideDog extends Dog {
  constructor(name, breed, owner) {
    super(name, breed);
    this.owner = owner;
  }

  assist() {
    return `${this.name} guides ${this.owner}`;
  }
}

const dog = new Dog("Rex", "Labrador");
dog.speak();    // "Rex barks" — overridden method
dog.toString(); // "[Animal: Rex]" — inherited from Animal

const guide = new GuideDog("Buddy", "Retriever", "Akshai");
guide.speak();  // "Buddy barks" — inherited from Dog
guide.assist(); // "Buddy guides Akshai" — own method

// Prototype chain
guide instanceof GuideDog; // true
guide instanceof Dog;      // true
guide instanceof Animal;   // true
guide instanceof Object;   // true
```

📢 NOTES:

> `super()` must be called in the child constructor before accessing `this`. Otherwise you get a ReferenceError. This is because the child doesn't create its own `this` — it gets it from the parent via `super()`.
```javascript
class Child extends Parent {
  constructor() {
    this.name = "test"; // ❌ ReferenceError — must call super() first
    super();
  }
}
```

### Calling parent methods with super
```javascript
class Animal {
  speak() {
    return "Some generic sound";
  }
}

class Dog extends Animal {
  speak() {
    const parentSound = super.speak(); // call parent's version
    return `${parentSound}... actually, WOOF!`;
  }
}

new Dog().speak(); // "Some generic sound... actually, WOOF!"
```

### Static methods and inheritance

Static methods are also inherited by child classes:
```javascript
class Animal {
  static create(name) {
    return new this(name); // `this` refers to the class being called
  }

  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name);
    this.type = "dog";
  }
}

const animal = Animal.create("Generic");  // Animal instance
const dog = Dog.create("Rex");            // Dog instance — static method inherited
console.log(dog.type);                    // "dog"
```

### Classes vs Prototypes — when to use which
```
Feature                  | Class syntax        | Prototype pattern
-------------------------|---------------------|-------------------
Readability              | Clean, familiar     | Verbose
Private fields           | #field              | Closures (workaround)
Static members           | static keyword      | Constructor.prop
Inheritance              | extends + super     | Object.create + call
Hoisting                 | No (TDZ)            | Yes (function declarations)
Under the hood           | Prototypes          | Prototypes
```

In modern JavaScript, use classes for most object-oriented code. Use raw prototypes only when you need something classes can't do (like dynamic prototype manipulation) or when working with legacy codebases.

### Mixins — multiple inheritance workaround

JavaScript doesn't support multiple inheritance (a class can only extend one parent). Mixins are a pattern to work around this:
```javascript
const Serializable = (Base) => class extends Base {
  serialize() {
    return JSON.stringify(this);
  }

  static deserialize(json) {
    return Object.assign(new this(), JSON.parse(json));
  }
};

const Validatable = (Base) => class extends Base {
  validate() {
    for (const [key, value] of Object.entries(this)) {
      if (value === null || value === undefined) {
        throw new Error(`${key} is required`);
      }
    }
    return true;
  }
};

// Compose multiple behaviors
class User extends Serializable(Validatable(class {})) {
  constructor(name, email) {
    super();
    this.name = name;
    this.email = email;
  }
}

const user = new User("Akshai", "akshai@email.com");
user.validate();    // true — from Validatable
user.serialize();   // '{"name":"Akshai","email":"akshai@email.com"}' — from Serializable
```

📢 NOTES:

> This is the same compose pattern from the Compose and Pipe section — `Serializable(Validatable(Base))` is function composition applied to classes.

# Symbol

### What is a Symbol?

A Symbol is a primitive type that is guaranteed to be unique. Every `Symbol()` call creates a completely new value, even with the same description.
```javascript
const a = Symbol("id");
const b = Symbol("id");
console.log(a === b);   // false — every Symbol is unique
console.log(typeof a);  // "symbol"
```

### Why Symbols exist

**1. Guaranteed unique property keys — no collisions**
```javascript
// Problem: two libraries both want to add metadata to the same object
// Without Symbols — collision risk
obj.id = "library1-id";
obj.id = "library2-id"; // overwrites library1's id!

// With Symbols — guaranteed unique
const lib1Id = Symbol("id");
const lib2Id = Symbol("id");

obj[lib1Id] = "library1-id";
obj[lib2Id] = "library2-id"; // no collision — different Symbols
```

**2. Hidden properties — don't show up in normal iteration**
```javascript
const role = Symbol("role");

const user = {
  name: "Akshai",
  age: 28,
  [role]: "admin"
};

Object.keys(user);         // ["name", "age"] — Symbol not included
JSON.stringify(user);      // '{"name":"Akshai","age":28}' — Symbol not included
for (const key in user) {} // only "name" and "age"

// Accessing Symbol properties requires the Symbol reference
user[role];                          // "admin"
Object.getOwnPropertySymbols(user);  // [Symbol(role)]
```

### Well-known Symbols

JavaScript uses built-in Symbols to let you customize object behavior:
```javascript
// Symbol.iterator — makes an object iterable (covered in Generators section)
const range = {
  [Symbol.iterator]() {
    let i = 0;
    return { next: () => ({ value: i++, done: i > 3 }) };
  }
};
[...range]; // [0, 1, 2]

// Symbol.toPrimitive — controls type coercion
class Money {
  constructor(amount, currency) {
    this.amount = amount;
    this.currency = currency;
  }

  [Symbol.toPrimitive](hint) {
    if (hint === "number") return this.amount;
    if (hint === "string") return `${this.amount} ${this.currency}`;
    return this.amount; // default
  }
}

const price = new Money(100, "USD");
+price;             // 100 (number hint)
`${price}`;         // "100 USD" (string hint)
price + 50;         // 150 (default hint)
```

### Symbol.for() — global registry

`Symbol()` always creates a new Symbol. `Symbol.for()` checks a global registry first and reuses existing ones:
```javascript
const a = Symbol.for("shared");
const b = Symbol.for("shared");
console.log(a === b); // true — same Symbol from registry

// Look up the key for a global Symbol
Symbol.keyFor(a); // "shared"

// Regular Symbols are NOT in the registry
const c = Symbol("local");
Symbol.keyFor(c); // undefined
```

Use case: when separate parts of your code (or separate libraries) need to share the same Symbol.

# Proxy and Reflect

### Proxy

A Proxy wraps an object and lets you intercept and customize operations performed on it — property access, assignment, function calls, and more.
```javascript
const user = {
  name: "Akshai",
  age: 28
};

const proxy = new Proxy(user, {
  get(target, prop) {
    console.log(`Accessing: ${prop}`);
    return prop in target ? target[prop] : `Property ${prop} doesn't exist`;
  },

  set(target, prop, value) {
    console.log(`Setting: ${prop} = ${value}`);
    if (prop === "age" && typeof value !== "number") {
      throw new TypeError("Age must be a number");
    }
    target[prop] = value;
    return true;
  }
});

proxy.name;           // logs "Accessing: name", returns "Akshai"
proxy.unknown;        // logs "Accessing: unknown", returns "Property unknown doesn't exist"
proxy.age = 29;       // logs "Setting: age = 29"
proxy.age = "old";    // ❌ TypeError: Age must be a number
```

### Practical use cases

**Validation:**
```javascript
function createValidated(schema) {
  return new Proxy({}, {
    set(target, prop, value) {
      if (schema[prop]) {
        const { type, required, min, max } = schema[prop];
        if (type && typeof value !== type) {
          throw new TypeError(`${prop} must be ${type}`);
        }
        if (min !== undefined && value < min) {
          throw new RangeError(`${prop} must be >= ${min}`);
        }
        if (max !== undefined && value > max) {
          throw new RangeError(`${prop} must be <= ${max}`);
        }
      }
      target[prop] = value;
      return true;
    }
  });
}

const user = createValidated({
  age: { type: "number", min: 0, max: 150 },
  name: { type: "string" }
});

user.name = "Akshai";  // ✅
user.age = 28;         // ✅
user.age = -5;         // ❌ RangeError: age must be >= 0
user.age = "old";      // ❌ TypeError: age must be number
```

**Negative array indices:**
```javascript
function createNegativeArray(arr) {
  return new Proxy(arr, {
    get(target, prop) {
      const index = Number(prop);
      if (index < 0) {
        return target[target.length + index];
      }
      return target[prop];
    }
  });
}

const arr = createNegativeArray([10, 20, 30, 40, 50]);
arr[-1]; // 50 — last element (like Python)
arr[-2]; // 40
arr[0];  // 10 — positive indices work normally
```

**Observable objects (change detection):**
```javascript
function observable(target, onChange) {
  return new Proxy(target, {
    set(obj, prop, value) {
      const oldValue = obj[prop];
      obj[prop] = value;
      onChange(prop, oldValue, value);
      return true;
    }
  });
}

const state = observable({ count: 0 }, (prop, oldVal, newVal) => {
  console.log(`${prop} changed: ${oldVal} → ${newVal}`);
});

state.count = 1;  // "count changed: 0 → 1"
state.count = 5;  // "count changed: 1 → 5"
```

📢 NOTES:

> This is conceptually how Vue.js reactivity works. Vue 2 used `Object.defineProperty()` and Vue 3 switched to Proxy for more comprehensive change detection.

### Reflect

Reflect provides methods that mirror Proxy traps. It gives you a clean way to perform default object operations.
```javascript
const user = { name: "Akshai" };

// These do the same thing:
user.name;                      // "Akshai"
Reflect.get(user, "name");     // "Akshai"

user.age = 28;                  // sets age
Reflect.set(user, "age", 28); // sets age

"name" in user;                 // true
Reflect.has(user, "name");     // true

delete user.age;                // deletes age
Reflect.deleteProperty(user, "age"); // deletes age
```

**Why use Reflect?** Inside Proxy handlers, it's the correct way to forward operations to the original object:
```javascript
const proxy = new Proxy(user, {
  get(target, prop, receiver) {
    console.log(`Accessed: ${prop}`);
    return Reflect.get(target, prop, receiver); // proper forwarding
  },

  set(target, prop, value, receiver) {
    console.log(`Set: ${prop} = ${value}`);
    return Reflect.set(target, prop, value, receiver); // proper forwarding
  }
});
```

# Event loop

JavaScript is single-threaded — it has one call stack and can execute one thing at a time. Yet it handles asynchronous operations like API calls, timers, and user events without blocking. The event loop is the mechanism that makes this possible.

### The architecture
```
     ┌──────────────────────────┐
     │       Call Stack         │  Executes synchronous code
     │  (one thing at a time)   │  LIFO — Last In, First Out
     └────────────┬─────────────┘
                  │
                  │ When async operation completes,
                  │ callback is placed in a queue
                  │
     ┌────────────▼─────────────┐
     │       Event Loop         │  Continuously checks:
     │                          │  1. Is the call stack empty?
     │  "traffic controller"    │  2. Is there anything in the queues?
     │                          │  3. If both yes → move task to stack
     └────────────┬─────────────┘
                  │
        ┌─────────┴────────┐
        │                  │
  ┌─────▼──────┐    ┌──────▼───────┐
  │ Microtask  │    │  Macrotask   │
  │   Queue    │    │    Queue     │
  │            │    │              │
  │ Higher     │    │ Lower        │
  │ priority   │    │ priority     │
  └────────────┘    └──────────────┘
```

### What goes where

**Call Stack** — all synchronous code. Functions go on, execute, and come off.

**Web APIs / Node APIs** — the browser (or Node.js) handles async operations outside of JavaScript. When you call `setTimeout`, `fetch`, or `addEventListener`, the browser takes over and tracks the operation. JavaScript continues executing the next line.

**Microtask Queue** (high priority):
- `Promise.then` / `.catch` / `.finally` callbacks
- `queueMicrotask()`
- `MutationObserver`
- `async/await` continuations (code after `await`)

**Macrotask Queue** (lower priority):
- `setTimeout` / `setInterval`
- `setImmediate` (Node.js)
- I/O operations
- UI rendering events (e.g. click, message)

**Rendering / animation** (separate from the timer macrotask queue):
- `requestAnimationFrame` — scheduled to run before the next paint, after microtasks. Do not treat it as "just another `setTimeout`"; browsers may order rAF vs timer callbacks differently.

### The event loop cycle
```
1. Execute all synchronous code on the call stack until it's empty
         │
         ▼
2. Empty the ENTIRE microtask queue
   (if a microtask adds another microtask, that runs too — before any macrotask)
         │
         ▼
3. Run ONE macrotask from the macrotask queue
         │
         ▼
4. Empty the ENTIRE microtask queue again
         │
         ▼
5. Browser may repaint if needed
         │
         ▼
6. Go back to step 3
```

The critical rule: **microtasks always run before the next macrotask**. The entire microtask queue is drained before a single macrotask runs.

### Step-by-step example
```javascript
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => {
  console.log("3");
});

console.log("4");
```

**Walkthrough:**
```
Step 1: Call stack executes console.log("1")
        Output: "1"

Step 2: setTimeout callback → sent to Web API → after 0ms → placed in MACROTASK queue
        Macrotask queue: [() => log("2")]

Step 3: Promise.resolve().then() → callback placed in MICROTASK queue
        Microtask queue: [() => log("3")]

Step 4: Call stack executes console.log("4")
        Output: "4"

Step 5: Call stack is empty → event loop checks microtask queue first
        Runs: console.log("3")
        Output: "3"

Step 6: Microtask queue is empty → event loop takes from macrotask queue
        Runs: console.log("2")
        Output: "2"

Final output: "1", "4", "3", "2"
```

### setTimeout(fn, 0) doesn't mean "run immediately"

It means "run as soon as possible after the current synchronous code and all microtasks finish." The `0` is the minimum delay, not a guarantee.
```javascript
console.log("start");

setTimeout(() => {
  console.log("timeout");
}, 0);

console.log("end");

// Output: "start", "end", "timeout"
// Even with 0ms delay, it goes to the macrotask queue
// and waits for the call stack to clear
```

### Microtasks can starve macrotasks

Because the entire microtask queue is drained before any macrotask runs, microtasks that keep adding more microtasks will block macrotasks indefinitely:
```javascript
// ⚠️ DANGEROUS — this will freeze the browser
function blockForever() {
  Promise.resolve().then(() => {
    blockForever(); // adds a new microtask endlessly
  });
}

blockForever();
// setTimeout callbacks, click handlers, rendering — NOTHING will ever run
// because the microtask queue never empties
```

This is different from a stack overflow. The call stack isn't growing — but the microtask queue is always full, so the event loop never gets to step 3.

### Complex example — nested microtasks and macrotasks
```javascript
console.log("start");

setTimeout(() => {
  console.log("timeout 1");
  Promise.resolve().then(() => {
    console.log("promise inside timeout 1");
  });
}, 0);

setTimeout(() => {
  console.log("timeout 2");
}, 0);

Promise.resolve().then(() => {
  console.log("promise 1");
}).then(() => {
  console.log("promise 2");
});

console.log("end");
```

**Walkthrough:**
```
Synchronous:
  "start"
  setTimeout → macrotask queue: [timeout1]
  setTimeout → macrotask queue: [timeout1, timeout2]
  Promise.resolve().then() → microtask queue: [promise1]
  "end"

Call stack empty → drain microtask queue:
  "promise 1" → .then() chains → adds promise2 to microtask queue
  "promise 2" → microtask queue now empty

Run one macrotask:
  "timeout 1" → Promise.resolve().then() adds to microtask queue

Drain microtask queue:
  "promise inside timeout 1"

Run one macrotask:
  "timeout 2"

Final output:
  "start"
  "end"
  "promise 1"
  "promise 2"
  "timeout 1"
  "promise inside timeout 1"
  "timeout 2"
```

Notice how `"promise inside timeout 1"` runs before `"timeout 2"`. Even though timeout 2 was queued first, the microtask created inside timeout 1 has higher priority and runs before the next macrotask.

### async/await and the event loop

`await` splits an async function into synchronous and asynchronous parts. Everything before the first `await` runs synchronously. Everything after `await` is scheduled as a microtask.
```javascript
async function demo() {
  console.log("1 - before await"); // synchronous
  await Promise.resolve();
  console.log("2 - after await");  // microtask — same as .then()
}

console.log("3 - script start");
demo();
console.log("4 - script end");
```
**Walkthrough:**
```
"3 - script start"  → synchronous
"1 - before await"   → synchronous (inside demo, before await)
"4 - script end"     → synchronous (await paused demo, execution returns here)
"2 - after await"    → microtask (continuation after await)

Output: "3", "1", "4", "2"
```

Think of `await` as a `.then()` that splits the function:
```javascript
// This async function:
async function demo() {
  console.log("before");
  await Promise.resolve();
  console.log("after");
}

// Is essentially:
function demo() {
  console.log("before");
  Promise.resolve().then(() => {
    console.log("after");
  });
}
```

### queueMicrotask

A direct way to add to the microtask queue without creating a promise:
```javascript
console.log("start");

queueMicrotask(() => {
  console.log("microtask");
});

console.log("end");

// Output: "start", "end", "microtask"
```

Useful when you need something to run after the current synchronous code but before any macrotasks — without the overhead of creating a Promise.

### requestAnimationFrame

`requestAnimationFrame` (rAF) runs before the browser repaints — after microtasks but before the next macrotask from the timer queue. It syncs with the display refresh rate (~60fps).
```javascript
console.log("start");

requestAnimationFrame(() => {
  console.log("rAF");
});

setTimeout(() => {
  console.log("timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("promise");
});

console.log("end");

// Most likely output: "start", "end", "promise", "rAF", "timeout"
// But rAF and timeout order can vary by browser
```

📢 NOTES:

> The exact ordering of `requestAnimationFrame` relative to `setTimeout` can vary across browsers. The only guaranteed order is:
> 1. Synchronous code always first
> 2. Microtasks always before macrotasks
> 3. rAF runs before repaint

### Complete priority order
```
1. Synchronous code (call stack)
       ↓
2. Microtasks (Promise.then, queueMicrotask, await continuations)
       ↓
3. requestAnimationFrame (before repaint)
       ↓
4. Browser repaint / render
       ↓
5. Macrotasks (setTimeout, setInterval, I/O)
       ↓
   Back to step 2
```

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");

setTimeout(() => {
  console.log("setTimeout");
}, 0);

async1();

new Promise((resolve) => {
  console.log("promise1");
  resolve();
}).then(() => {
  console.log("promise2");
});

console.log("script end");
```

# Promises

A Promise represents a value that may not be available yet but will be at some point in the future (or it will fail).

Think of it like ordering food online — you get an order confirmation (the promise). The food is either **delivered** (fulfilled) or **cancelled** (rejected). Until then, your order is **pending**.

A Promise is in one of these states:

- **pending** — initial state, the operation is still in progress
- **fulfilled** — the operation completed successfully, the promise has a value
- **rejected** — the operation failed, the promise has a reason (error)

Once a promise is fulfilled or rejected, it's **settled** — it can never change state again.
```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Order delivered");  // fulfills the promise
    // reject("Order cancelled"); // would reject the promise
  }, 1000);
});
```

### Consuming promises — then, catch, finally
```javascript
myPromise
  .then((value) => {
    console.log(value); // runs if fulfilled
  })
  .catch((error) => {
    console.log(error); // runs if rejected
  })
  .finally(() => {
    console.log("Done"); // runs regardless of outcome
  });
```

📢 NOTES:

> `.then()` returns a new promise, which is why chaining works. Whatever you return from a `.then()` callback becomes the resolved value of the next `.then()`.
```javascript
Promise.resolve(1)
  .then(val => val + 1)   // returns 2
  .then(val => val * 3)   // returns 6
  .then(val => console.log(val)); // 6
```

> If you don't return anything from `.then()`, the next `.then()` receives `undefined`.

### Promise chaining

Each `.then()` receives the result of the previous one. If a `.then()` throws or returns a rejected promise, the chain skips to the nearest `.catch()`. After a `.catch()`, the chain continues normally.

${\textsf{\color{khaki}Guess\ the\ output}}$
```javascript
function job(state) {
  return new Promise(function(resolve, reject) {
    if (state) {
      resolve("Success");
    } else {
      reject("Error");
    }
  });
}

let promise = job(true);

promise
  .then(function(data) {
    console.log(data);
    return job(false);
  })
  .catch(function(error) {
    console.log(error);
    return "Error caught";
  })
  .then(function(data) {
    console.log(data);
    return job(true);
  })
  .catch(function(error) {
    console.log(error);
  });
```
### Promise inside a promise

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

### Creating pre-resolved/rejected promises
```javascript
// Already resolved
const resolved = Promise.resolve("Done");
resolved.then(console.log); // "Done"

// Already rejected
const rejected = Promise.reject("Failed");
rejected.catch(console.log); // "Failed"

// Useful for: returning early, mocking in tests, starting a chain
function getUser(id) {
  if (!id) return Promise.reject("ID required");
  return fetch(`/api/users/${id}`).then(res => res.json());
}
```

### Promise static methods

**Promise.all()** — all must succeed, fails fast on first rejection
```javascript
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(2);
const p3 = Promise.resolve(3);

Promise.all([p1, p2, p3]).then(console.log); // [1, 2, 3]

// If any one fails:
const p4 = Promise.reject("Failed");
Promise.all([p1, p2, p4])
  .then(console.log)
  .catch(console.log); // "Failed" — entire thing fails
```

Use case: fetching multiple independent resources that are all required.

[Polyfill for Promise.all()](https://github.com/akshaitr/js-polyfills/blob/main/src/allPromise.js)

**Promise.allSettled()** — waits for all to finish regardless of outcome
```javascript
const p1 = Promise.resolve("OK");
const p2 = Promise.reject("Error");
const p3 = Promise.resolve("Done");

Promise.allSettled([p1, p2, p3]).then(console.log);
// [
//   { status: "fulfilled", value: "OK" },
//   { status: "rejected", reason: "Error" },
//   { status: "fulfilled", value: "Done" }
// ]
```

Use case: firing off multiple operations where you want results from all of them even if some fail (e.g., saving to multiple services).

**Promise.race()** — first one to settle wins (fulfilled or rejected)
```javascript
const slow = new Promise(resolve => setTimeout(() => resolve("Slow"), 2000));
const fast = new Promise(resolve => setTimeout(() => resolve("Fast"), 500));

Promise.race([slow, fast]).then(console.log); // "Fast"
```

Use case: timeout pattern — race your fetch against a timer.
```javascript
function fetchWithTimeout(url, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject("Timeout"), ms)
  );
  return Promise.race([fetch(url), timeout]);
}
```

**Promise.any()** — first one to *fulfill* wins (ignores rejections)
```javascript
const p1 = Promise.reject("Error 1");
const p2 = new Promise(resolve => setTimeout(() => resolve("P2"), 500));
const p3 = new Promise(resolve => setTimeout(() => resolve("P3"), 100));

Promise.any([p1, p2, p3]).then(console.log); // "P3" — first fulfilled

// If ALL reject:
Promise.any([
  Promise.reject("A"),
  Promise.reject("B")
]).catch(console.log); // AggregateError: All promises were rejected
```

Use case: trying multiple sources and using whichever responds first successfully.

**Quick comparison:**
```
                    | Waits for all? | Short-circuits on?
--------------------|----------------|--------------------
Promise.all()       | Yes            | First rejection
Promise.allSettled()| Yes            | Never
Promise.race()      | No             | First settlement (any)
Promise.any()       | No             | First fulfillment
```

### Async / Await

`async` and `await` are syntactic sugar over promises. They make asynchronous code look and behave like synchronous code.

**async function** — always returns a promise. If you return a value, it's wrapped in `Promise.resolve()`. If you throw, it's wrapped in `Promise.reject()`.
```javascript
async function greet() {
  return "Hello World";
}

// Equivalent to:
function greet() {
  return Promise.resolve("Hello World");
}

greet().then(console.log); // "Hello World"
```

**await** — pauses execution of the async function until the promise settles. Can only be used inside an `async` function.
```javascript
async function fetchUser() {
  const response = await fetch("/api/user");  // pauses here
  const data = await response.json();         // pauses here
  console.log(data);                          // runs after both complete
}
```

**Error handling with try/catch:**
```javascript
async function riskyOperation() {
  throw new Error("Something went wrong!");
}

async function run() {
  try {
    const res = await riskyOperation();
    console.log(res);
  } catch (err) {
    console.error("Caught:", err.message); // "Caught: Something went wrong!"
  }
}

run();
```

**Sequential vs Parallel execution:**
```javascript
// Sequential — each waits for the previous one (SLOW)
async function sequential() {
  const user = await fetchUser();      // waits 1 sec
  const posts = await fetchPosts();    // waits 1 sec after user
  // Total: ~2 seconds
}

// Parallel — both start at the same time (FAST)
async function parallel() {
  const [user, posts] = await Promise.all([
    fetchUser(),    // starts immediately
    fetchPosts()    // starts immediately
  ]);
  // Total: ~1 second (whichever is slower)
}
```

📢 NOTES:

> A common mistake is using `await` in a loop when the operations are independent:
```javascript
// BAD — sequential, 5 seconds total
async function loadAll(ids) {
  for (const id of ids) {
    const data = await fetch(`/api/${id}`); // waits each time
  }
}

// GOOD — parallel, ~1 second total
async function loadAll(ids) {
  const promises = ids.map(id => fetch(`/api/${id}`));
  const results = await Promise.all(promises);
}
```

### Microtasks vs Macrotasks

This is where promises interact with the event loop. Understanding this order is critical for predicting output in interview questions.

**Macrotasks:** `setTimeout`, `setInterval`, `setImmediate`, I/O operations
**Microtasks:** `Promise.then/catch/finally`, `queueMicrotask`, `MutationObserver`

**Execution order:**
1. Run current synchronous code to completion
2. Empty the entire microtask queue
3. Run one macrotask
4. Empty the entire microtask queue again
5. Repeat from step 3
```javascript
console.log("1 - sync");

setTimeout(() => {
  console.log("2 - macrotask");
}, 0);

Promise.resolve().then(() => {
  console.log("3 - microtask");
});

console.log("4 - sync");
```

> Output:
> `"1 - sync"` — synchronous, runs first
> `"4 - sync"` — synchronous, runs second
> `"3 - microtask"` — microtask queue emptied before any macrotask
> `"2 - macrotask"` — macrotask runs last

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

[Polyfill for Promise](https://github.com/akshaitr/js-polyfills/blob/main/src/promise.js) <br/>
Reference: [Polyfill for Javascript Promise](https://medium.com/@manojsingh047/polyfill-for-javascript-promise-81053b284e37)

# Error Handling

### try, catch, finally
```javascript
try {
  const data = JSON.parse("invalid json");
} catch (error) {
  console.log(error.name);    // "SyntaxError"
  console.log(error.message); // "Unexpected token i in JSON at position 0"
  console.log(error.stack);   // full stack trace
} finally {
  console.log("Always runs"); // runs whether error occurred or not
}
```

📢 NOTES:

> `finally` runs even if `try` or `catch` has a `return` statement:
```javascript
function demo() {
  try {
    return "from try";
  } finally {
    console.log("finally runs"); // this STILL runs
  }
}

demo(); // logs "finally runs", then returns "from try"
```

### Error types
```javascript
// Built-in error types
new Error("Generic error");           // base error
new SyntaxError("Bad syntax");        // invalid code
new TypeError("Wrong type");          // wrong type operation
new ReferenceError("Not defined");    // accessing undefined variable
new RangeError("Out of range");       // number outside valid range
new URIError("Bad URI");              // malformed URI

// When do they occur?
undefined.prop;               // TypeError — can't read property of undefined
console.log(x);              // ReferenceError — x is not defined
JSON.parse("{bad}");          // SyntaxError — unexpected token
[].length = -1;              // RangeError — invalid array length
decodeURIComponent("%");     // URIError — malformed URI sequence
```

### Custom errors
```javascript
class ValidationError extends Error {
  constructor(field, message) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

class NotFoundError extends Error {
  constructor(resource) {
    super(`${resource} not found`);
    this.name = "NotFoundError";
    this.statusCode = 404;
  }
}

// Usage
function validateAge(age) {
  if (typeof age !== "number") {
    throw new ValidationError("age", "Age must be a number");
  }
  if (age < 0 || age > 150) {
    throw new ValidationError("age", "Age must be between 0 and 150");
  }
}

try {
  validateAge("twenty");
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`${error.field}: ${error.message}`);
    // "age: Age must be a number"
  } else {
    throw error; // rethrow unexpected errors
  }
}
```

📢 NOTES:

> Always rethrow errors you don't know how to handle. A catch block that swallows all errors silently hides bugs:
```javascript
// BAD — hides all errors
try {
  doSomething();
} catch (error) {
  console.log("Something went wrong"); // which error? no idea
}

// GOOD — handle known errors, rethrow unknown ones
try {
  doSomething();
} catch (error) {
  if (error instanceof ValidationError) {
    showFieldError(error.field, error.message);
  } else {
    throw error; // let it propagate
  }
}
```

### Error handling in async code

**Promises — use `.catch()`:**
```javascript
fetch("/api/user")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(error => console.error("Fetch failed:", error));

// .catch() at the end catches errors from ANY .then() in the chain
```

**async/await — use try/catch:**
```javascript
async function loadUser() {
  try {
    const res = await fetch("/api/user");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    return data;
  } catch (error) {
    console.error("Failed to load user:", error);
  }
}
```

**Unhandled promise rejections:**
```javascript
// This promise rejection is NOT caught — it will crash Node.js
// and show a warning in browsers
Promise.reject("something failed");

// Always handle rejections
Promise.reject("something failed").catch(console.error);
```
```javascript
// Global handler for uncaught promise rejections
window.addEventListener("unhandledrejection", (event) => {
  console.error("Unhandled rejection:", event.reason);
  event.preventDefault(); // prevents default browser logging
});
```

# AbortController and Cancellation

Long-running async work (especially `fetch`) often needs to be cancelled — navigation away, a newer search query, a timeout, or an unmounted component. `AbortController` is the standard cancellation signal for web APIs.

### Basic usage with fetch
```javascript
const controller = new AbortController();

fetch("/api/search?q=js", { signal: controller.signal })
  .then((res) => res.json())
  .then(console.log)
  .catch((err) => {
    if (err.name === "AbortError") {
      console.log("Request was cancelled");
      return;
    }
    console.error(err);
  });

// Cancel later
controller.abort();
```

Aborting rejects the fetch promise with an `AbortError` (DOMException). Always distinguish cancellation from real failures.

### Timeout pattern
```javascript
async function fetchWithTimeout(url, ms) {
  const controller = new AbortController();
  const timerId = setTimeout(() => controller.abort(), ms);

  try {
    const res = await fetch(url, { signal: controller.signal });
    return await res.json();
  } finally {
    clearTimeout(timerId);
  }
}
```

Modern browsers also support `AbortSignal.timeout(ms)`:
```javascript
const res = await fetch(url, { signal: AbortSignal.timeout(3000) });
```

### Cancelling the previous request (search-as-you-type)
```javascript
let controller = null;

function search(query) {
  controller?.abort(); // cancel in-flight request
  controller = new AbortController();

  return fetch(`/api/search?q=${encodeURIComponent(query)}`, {
    signal: controller.signal,
  }).then((res) => res.json());
}

search("ja");
search("jav");
search("java"); // only the last request should matter
```

### One signal, many listeners
```javascript
const controller = new AbortController();
const { signal } = controller;

signal.addEventListener("abort", () => {
  console.log("aborted:", signal.reason);
});

fetch(url, { signal });
element.addEventListener("click", handler, { signal }); // auto-removes on abort

controller.abort("user navigated away");
```

📢 NOTES:

> In React, abort in-flight fetches inside a `useEffect` cleanup (or when the query changes). Cancelling avoids both wasted network and the "setState on unmounted component" class of bugs.

> `AbortSignal.any([signalA, signalB])` aborts when *any* of the signals abort — useful for combining timeout + user cancel.

> Prefer AbortController over ad-hoc `let cancelled = false` flags when the underlying API supports `signal` (fetch, addEventListener, some streams).

# Event Propagation

When an event occurs on a DOM element, it doesn't just fire on that element alone. The event travels through the DOM tree in a specific order. This complete journey is called event propagation.

Event propagation has three phases:

1. **Capturing phase** — event travels from `window` down to the target element
2. **Target phase** — event reaches the actual element that was clicked/triggered
3. **Bubbling phase** — event travels back up from the target to `window`
```
         Capturing ↓                    ↑ Bubbling
         ┌───────────────────────────────────────┐
         │ window                                │
         │  ┌───────────────────────────────────┐│
         │  │ document                          ││
         │  │  ┌────────────────────────────────┐│
         │  │  │ <html>                         ││
         │  │  │  ┌─────────────────────────────┐│
         │  │  │  │ <body>                      ││
         │  │  │  │  ┌──────────────────────────┐│
         │  │  │  │  │ <div>                    ││
         │  │  │  │  │  ┌───────────────────────┐│
         │  │  │  │  │  │ <button> ← TARGET     ││
         │  │  │  │  │  └───────────────────────┘│
         │  │  │  │  └──────────────────────────┘│
         │  │  │  └─────────────────────────────┘│
         │  │  └────────────────────────────────┘│
         │  └───────────────────────────────────┘│
         └───────────────────────────────────────┘
```

By default, event listeners fire during the **bubbling phase** (bottom up).

### Event Bubbling

The event starts at the target element and bubbles up to its ancestors.
```html
┌──────────────────────────┐  // grandparent
|  ┌────────────────────┐  |  // parent
|  |    ┌──────────┐    |  |  // child
|  |    | Click me │    |  | 
|  |    └──────────┘    |  |
|  └────────────────────┘  |
└──────────────────────────┘  
```
```javascript
document.getElementById("grandparent").addEventListener("click", () => {
  console.log("Grandparent");
});

document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent");
});

document.getElementById("child").addEventListener("click", () => {
  console.log("Child");
});

// Click the button:
// Output: "Child" → "Parent" → "Grandparent"
```

📢 NOTES:

> Not all events bubble. Events like `focus`, `blur`, `mouseenter`, `mouseleave`, `load`, `unload`, `scroll` do not bubble. Their bubbling alternatives are `focusin`/`focusout` and `mouseover`/`mouseout`.

### Event Capturing (Trickling)

The opposite of bubbling — the event is caught from the outermost ancestor down to the target. Enable it by passing `{ capture: true }` as the third argument.
```javascript
document.getElementById("grandparent").addEventListener("click", () => {
  console.log("Grandparent");
}, { capture: true });

document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent");
}, { capture: true });

document.getElementById("child").addEventListener("click", () => {
  console.log("Child");
}, { capture: true });

// Click the button:
// Output: "Grandparent" → "Parent" → "Child"
```

**Mixing capturing and bubbling:**
```javascript
document.getElementById("grandparent").addEventListener("click", () => {
  console.log("Grandparent - Capture");
}, { capture: true });

document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent - Bubble");
}); // default is bubble

document.getElementById("child").addEventListener("click", () => {
  console.log("Child - Bubble");
});

// Click the button:
// Output:
// "Grandparent - Capture"  ← capturing phase (top down)
// "Child - Bubble"          ← target phase
// "Parent - Bubble"         ← bubbling phase (bottom up)
```

### event.target vs event.currentTarget vs this
```javascript
document.getElementById("parent").addEventListener("click", function(event) {
  console.log("target:", event.target.id);         // element that was CLICKED
  console.log("currentTarget:", event.currentTarget.id); // element that HANDLES the event
  console.log("this:", this.id);                    // same as currentTarget
});

// Click the child button:
// target: "child"          ← the actual element clicked
// currentTarget: "parent"  ← the element with the event listener
// this: "parent"           ← same as currentTarget (in regular functions)
```

📢 NOTES:

> In arrow functions, `this` does NOT refer to `currentTarget`. Arrow functions inherit `this` from their lexical scope. Use `event.currentTarget` instead if you need the listener element.
```javascript
// Regular function — this = currentTarget
element.addEventListener("click", function(event) {
  console.log(this === event.currentTarget); // true
});

// Arrow function — this = outer scope (probably window)
element.addEventListener("click", (event) => {
  console.log(this === event.currentTarget); // false
  // use event.currentTarget instead
});
```

### stopPropagation()

Prevents the event from continuing to the next element in the propagation chain. Works in both capturing and bubbling phases.
```javascript
document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent");
});

document.getElementById("child").addEventListener("click", (event) => {
  event.stopPropagation(); // stops here — Parent will NOT fire
  console.log("Child");
});

// Click the button:
// Output: "Child" (only)
```

### stopImmediatePropagation()

`stopPropagation()` stops the event from reaching other elements, but if the *same element* has multiple listeners, they all still fire. `stopImmediatePropagation()` stops everything — even other listeners on the same element.
```javascript
const btn = document.getElementById("child");

btn.addEventListener("click", (event) => {
  console.log("First listener");
  event.stopImmediatePropagation();
});

btn.addEventListener("click", () => {
  console.log("Second listener"); // NEVER runs
});

document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent"); // NEVER runs
});

// Click the button:
// Output: "First listener" (only)
```

### preventDefault()

Stops the browser's default behavior for an event — NOT the same as stopping propagation. The event still bubbles, but the default action is cancelled.
```javascript
// Prevent link navigation
document.querySelector("a").addEventListener("click", (event) => {
  event.preventDefault(); // link won't navigate
  console.log("Link clicked but not followed");
});

// Prevent form submission
document.querySelector("form").addEventListener("submit", (event) => {
  event.preventDefault(); // page won't reload
  console.log("Form submitted via JS");
});

// Prevent right-click context menu
document.addEventListener("contextmenu", (event) => {
  event.preventDefault();
  console.log("Custom right-click menu");
});
```

### Event Delegation

Instead of attaching event listeners to every child element, attach one listener to the parent and use `event.target` to determine which child was clicked. This is more memory efficient and automatically works for dynamically added elements.
```javascript
// BAD — one listener per item
document.querySelectorAll("li").forEach(item => {
  item.addEventListener("click", () => {
    console.log(item.textContent);
  });
});

// GOOD — one listener on the parent
document.getElementById("list").addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log(event.target.textContent);
  }
});
```

**Why event delegation works:** because of bubbling. When you click an `<li>`, the event bubbles up to the `<ul>` where your listener catches it.

**Real-world use case — dynamically added elements:**
```javascript
const list = document.getElementById("list");

// This listener handles items that don't even exist yet
list.addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    event.target.classList.toggle("completed");
  }
});

// Adding new items later — they automatically work
const newItem = document.createElement("li");
newItem.textContent = "New task";
list.appendChild(newItem); // click handler works without adding a new listener
```

**Event delegation in React:**

You've been using event delegation without knowing it. React doesn't attach event listeners to individual DOM elements — it uses a single listener at the root and delegates internally. This is why React's synthetic event system is efficient.
```javascript
// React — looks like individual listeners but React delegates internally
function TodoList({ items }) {
  return (
    <ul onClick={(e) => {
      // delegation pattern — one handler for all items
      if (e.target.tagName === "LI") {
        console.log(e.target.textContent);
      }
    }}>
      {items.map(item => {item.text})}
    
  );
}
```

# Debouncing and Throttling

Both are techniques to control how often a function executes. They solve the same problem — preventing excessive function calls — but in different ways.

### The problem
```javascript
// Without any control — fires on EVERY keystroke
searchInput.addEventListener("input", (e) => {
  fetchSearchResults(e.target.value); // API call on every single character
});

// User types "javascript" → 10 API calls
// Most of those calls are wasted because the user is still typing
```

### Debouncing

Debouncing delays the execution until the user *stops* performing the action for a specified time. If the action is repeated before the delay ends, the timer resets.

Think of it like an elevator door — it keeps resetting the closing timer every time someone walks in. It only closes after nobody has entered for a few seconds.

```javascript
function debounce(fn, delay) {
  let timerId;

  return function(...args) {
    clearTimeout(timerId); // reset the timer every time
    timerId = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}
```

```javascript
// Usage
const debouncedSearch = debounce((query) => {
  console.log("Searching:", query);
  fetchSearchResults(query);
}, 300);

searchInput.addEventListener("input", (e) => {
  debouncedSearch(e.target.value);
});

// User types "javascript" quickly:
// Only ONE API call after they stop typing for 300ms
// Searching: "javascript"
```

**Timeline visualization:**
```
User types:  j---a---v---a---s---c---r---i---p---t
Timer:       [300ms reset each time................]
Executes:                                           → "javascript" (once)
```

**Common use cases:**
- Search input / autocomplete
- Window resize handler
- Auto-saving form drafts
- Validating input fields after user finishes typing

### Throttling

Throttling limits execution to at most once per specified interval. No matter how many times the action is triggered, the function fires at a steady rate.

Think of it like a machine gun with a fire rate cap — no matter how fast you pull the trigger, it only fires once per interval.

```javascript
function throttle(fn, limit) {
  let inThrottle = false;

  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}
```

```javascript
// Usage
const throttledScroll = throttle(() => {
  console.log("Scroll position:", window.scrollY);
  updateScrollIndicator();
}, 200);

window.addEventListener("scroll", throttledScroll);

// User scrolls continuously for 1 second:
// Fires at 0ms, 200ms, 400ms, 600ms, 800ms → 5 calls
// Without throttle → could be 50+ calls
```

**Timeline visualization:**
```
Events:    x-x-x-x-x-x-x-x-x-x-x-x-x-x-x
Throttle:  ✓-----✓-----✓-----✓-----✓-----
           [200ms][200ms][200ms][200ms]
```

**Common use cases:**
- Scroll event handlers (infinite scroll, parallax, scroll indicators)
- Mouse move tracking
- Rate-limiting API calls
- Game loop inputs (keyboard/mouse)

### Debouncing vs Throttling
```
                  | When it fires                    | # of executions
------------------|----------------------------------|------------------
Debounce          | After user STOPS for X ms        | Once (at the end)
Throttle          | Every X ms while action happens  | Multiple (at steady rate)
```
```
User clicks rapidly for 1 second (20 clicks), delay = 300ms:

Debounce:  ________________________________✓  (1 call, 300ms after last click)

Throttle:  ✓________✓________✓________✓       (4 calls, every 300ms)
```

**How to choose:**
- Need the *final* value after activity stops? → **Debounce** (search input, resize, auto-save)
- Need *consistent updates* during activity? → **Throttle** (scroll, mousemove, game input)

### Leading vs Trailing execution

The implementations above are **trailing** — they execute *after* the delay. Sometimes you want **leading** execution — fire immediately on the first trigger, then wait.

**Leading debounce:**
```javascript
function debounceLeading(fn, delay) {
  let timerId;

  return function(...args) {
    if (!timerId) {
      fn.apply(this, args); // fire immediately on first call
    }
    clearTimeout(timerId);
    timerId = setTimeout(() => {
      timerId = null; // reset so next burst triggers immediately again
    }, delay);
  };
}
```
```
Trailing:  ___________✓  (fires after user stops)
Leading:   ✓___________  (fires immediately, then waits)
```

Use case for leading: a submit button you want to respond to immediately but prevent double-clicks.

### requestAnimationFrame as a throttle

For visual/DOM updates, `requestAnimationFrame` is a better throttle than `setTimeout` because it syncs with the browser's repaint cycle (~60fps = every ~16ms).
```javascript
function rafThrottle(fn) {
  let ticking = false;

  return function(...args) {
    if (!ticking) {
      ticking = true;
      requestAnimationFrame(() => {
        fn.apply(this, args);
        ticking = false;
      });
    }
  };
}

// Usage — smooth scroll handler
window.addEventListener("scroll", rafThrottle(() => {
  // DOM updates here run at 60fps max
  updateParallax();
}));
```

📢 NOTES:

> In React, you typically debounce/throttle inside a `useRef` or `useCallback` to prevent the function from being recreated on every render:
```javascript
function SearchInput() {
  const debouncedSearch = useRef(
    debounce((query) => fetchResults(query), 300)
  ).current;

  // cleanup on unmount
  useEffect(() => {
    return () => debouncedSearch.cancel?.();
  }, []);

  return <input onChange={(e) => debouncedSearch(e.target.value)} />;
}
```

See the code for [debouncing function](https://github.com/akshaitr/js-polyfills/blob/main/src/debounce.js)
See the code for [throttle function](https://github.com/akshaitr/js-polyfills/blob/main/src/throttle.js)

# Generators and Iterators

### Iterables and the for...of loop

An iterable is any object that implements the `Symbol.iterator` method. Arrays, strings, Maps, and Sets are all iterable. Plain objects are NOT.
```javascript
// These are iterable — work with for...of
for (const char of "hello") console.log(char);     // h, e, l, l, o
for (const num of [1, 2, 3]) console.log(num);     // 1, 2, 3
for (const val of new Set([1, 2])) console.log(val); // 1, 2

// Plain objects are NOT iterable
for (const val of { a: 1 }) console.log(val); // ❌ TypeError: not iterable

// Use Object.entries() to iterate objects
for (const [key, val] of Object.entries({ a: 1, b: 2 })) {
  console.log(key, val); // "a" 1, "b" 2
}
```

### Custom iterator

You can make any object iterable by implementing `Symbol.iterator`:
```javascript
const range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    let current = this.from;
    const last = this.to;

    return {
      next() {
        if (current <= last) {
          return { value: current++, done: false };
        }
        return { done: true };
      }
    };
  }
};

for (const num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}

// Also works with spread and destructuring
console.log([...range]);          // [1, 2, 3, 4, 5]
const [first, second] = range;   // first = 1, second = 2
```

The iterator protocol: an object with a `next()` method that returns `{ value, done }`. When `done` is `true`, iteration stops.

### Generators

A generator function is a special function that can pause and resume its execution. It produces values on demand using `yield`.
```javascript
function* numberGenerator() {
  console.log("Start");
  yield 1;
  console.log("After first yield");
  yield 2;
  console.log("After second yield");
  yield 3;
  console.log("End");
}

const gen = numberGenerator(); // does NOT execute the function body

gen.next(); // { value: 1, done: false } — runs until first yield, logs "Start"
gen.next(); // { value: 2, done: false } — resumes, logs "After first yield"
gen.next(); // { value: 3, done: false } — resumes, logs "After second yield"
gen.next(); // { value: undefined, done: true } — resumes, logs "End"
```

📢 NOTES:

> Calling a generator function returns a generator object — it doesn't execute the body. The body executes incrementally with each `.next()` call, pausing at each `yield`.

### Generators are iterable
```javascript
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

// Take first 8 fibonacci numbers
const fib = fibonacci();
for (let i = 0; i < 8; i++) {
  console.log(fib.next().value);
}
// 0, 1, 1, 2, 3, 5, 8, 13

// Using with spread (⚠️ only works with finite generators)
function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

console.log([...range(1, 5)]); // [1, 2, 3, 4, 5]
```

### Passing values into generators

`yield` is two-way — it can send values out AND receive values in:
```javascript
function* conversation() {
  const name = yield "What is your name?";
  const age = yield `Hello ${name}! How old are you?`;
  return `${name} is ${age} years old`;
}

const chat = conversation();
console.log(chat.next());           // { value: "What is your name?", done: false }
console.log(chat.next("Akshai"));   // { value: "Hello Akshai! How old are you?", done: false }
console.log(chat.next(28));         // { value: "Akshai is 28 years old", done: true }
```

The argument to `.next()` becomes the value that `yield` evaluates to inside the generator.

### Practical use cases

**Unique ID generator:**
```javascript
function* idGenerator(prefix = "id") {
  let id = 1;
  while (true) {
    yield `${prefix}_${id++}`;
  }
}

const userId = idGenerator("user");
userId.next().value; // "user_1"
userId.next().value; // "user_2"
userId.next().value; // "user_3"
```

**Paginated data fetching:**
```javascript
async function* fetchPages(url) {
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const res = await fetch(`${url}?page=${page}`);
    const data = await res.json();
    hasMore = data.hasMore;
    page++;
    yield data.items;
  }
}

// Usage
const pages = fetchPages("/api/users");
const page1 = await pages.next(); // fetches page 1
const page2 = await pages.next(); // fetches page 2 only when needed
```

# Modules

### CommonJS (CJS) — Node.js default
```javascript
// math.js — exporting
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

module.exports = { add, subtract };

// OR export individually
exports.add = (a, b) => a + b;

// app.js — importing
const { add, subtract } = require("./math");
add(1, 2); // 3
```

**Key characteristics:**
- Synchronous loading — modules are loaded one at a time
- Runs at runtime — `require()` can be inside conditionals
- Returns a copy of the exported value
- Used in Node.js by default
```javascript
// Conditional require — valid in CJS
if (process.env.NODE_ENV === "development") {
  const debugTools = require("./debug");
}
```

### ES Modules (ESM) — modern standard
```javascript
// math.js — named exports
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// OR
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;
export { add, subtract };

// Default export — one per file
export default class Calculator {
  add(a, b) { return a + b; }
}

// app.js — importing
import { add, subtract } from "./math.js";
import Calculator from "./math.js";      // default import
import * as math from "./math.js";       // namespace import
```

**Key characteristics:**
- Asynchronous loading — can be loaded in parallel
- Runs at parse time — statically analyzed before execution
- Returns a live binding (reference) — not a copy
- Cannot use inside conditionals (static structure)
- Used in browsers and modern Node.js
```javascript
// This is INVALID in ESM — imports must be at top level
if (condition) {
  import { add } from "./math.js"; // ❌ SyntaxError
}

// Use dynamic import() for conditional loading
if (condition) {
  const { add } = await import("./math.js"); // ✅ returns a Promise
}
```

### CJS vs ESM
```
Feature         | CommonJS (CJS)          | ES Modules (ESM)
----------------|-------------------------|-------------------
Syntax          | require / module.exports| import / export
Loading         | Synchronous             | Asynchronous
Evaluation      | Runtime                 | Parse time (static)
Exports         | Copy of value           | Live binding (reference)
Conditional     | ✅require() anywhere    | ❌ static only (use dynamic import())
Tree shaking    | ❌Not possible          | ✅ Dead code elimination
Default in      | Node.js                 | Browsers, modern Node.js
```

### Live binding vs copy
```javascript
// CommonJS — exports a COPY
// counter.js
let count = 0;
const increment = () => count++;
module.exports = { count, increment };

// app.js
const { count, increment } = require("./counter");
increment();
console.log(count); // 0 — still the original copy

// ES Modules — exports a LIVE BINDING
// counter.js
export let count = 0;
export const increment = () => count++;

// app.js
import { count, increment } from "./counter.js";
increment();
console.log(count); // 1 — reflects the updated value
```

## Dynamic import
```javascript
// Lazy load a module — returns a Promise
const module = await import("./heavyModule.js");
module.doSomething();

// React lazy loading uses this
const LazyComponent = React.lazy(() => import("./HeavyComponent"));
```

This is what powers code splitting in React — `import()` tells the bundler to create a separate chunk that's loaded only when needed.

# Compose and Pipe

Both are techniques for combining multiple functions into a single function. Instead of nesting function calls, you create a clean pipeline where data flows through a series of transformations.

### The problem
```javascript
// Without compose/pipe — deeply nested, read inside-out
const result = uppercase(trim(addExclamation("  hello world  ")));
// Hard to read: which function runs first?

// With pipe — read left to right, like a recipe
const transform = pipe(trim, addExclamation, uppercase);
const result = transform("  hello world  ");
// Clear: trim first, then add exclamation, then uppercase
```

### Compose

Compose takes multiple functions and returns a new function that applies them **right to left**. The output of each function becomes the input of the next one to its left.
```javascript
function compose(...fns) {
  return function(arg) {
    return fns.reduceRight((result, fn) => fn(result), arg);
  };
}
```

```javascript
const add10 = (num) => num + 10;
const multiply2 = (num) => num * 2;
const subtract5 = (num) => num - 5;

const compute = compose(subtract5, multiply2, add10);

compute(5);
// Step 1 (rightmost): add10(5) = 15
// Step 2: multiply2(15) = 30
// Step 3 (leftmost): subtract5(30) = 25
```

📢 NOTES:

> Compose reads right to left — this matches how nested function calls work mathematically: `f(g(x))` means apply `g` first, then `f`. If you're comfortable with math notation, compose feels natural. If not, pipe is easier.

### Pipe

Pipe does the same thing but applies functions **left to right**. This reads more naturally for most people since it follows the order you'd describe the steps.
```javascript
function pipe(...fns) {
  return function(arg) {
    return fns.reduce((result, fn) => fn(result), arg);
  };
}
```
```javascript
const compute = pipe(add10, multiply2, subtract5);

compute(5);
// Step 1 (leftmost): add10(5) = 15
// Step 2: multiply2(15) = 30
// Step 3 (rightmost): subtract5(30) = 25
```

Same result, same functions — just different order of arguments.

### Compose vs Pipe
```
compose(f, g, h)(x)  →  f(g(h(x)))     // right to left
pipe(f, g, h)(x)     →  h(g(f(x)))     // left to right

// Same result if you reverse the function order:
compose(subtract5, multiply2, add10)(5)  // 25
pipe(add10, multiply2, subtract5)(5)     // 25
```

### Practical examples

**String transformation pipeline:**
```javascript
const trim = (str) => str.trim();
const toLowerCase = (str) => str.toLowerCase();
const replaceSpaces = (str) => str.replace(/\s+/g, "-");

const slugify = pipe(trim, toLowerCase, replaceSpaces);

slugify("  Hello World  "); // "hello-world"
slugify("  JavaScript Is Fun  "); // "javascript-is-fun"
```

**Data processing:**
```javascript
const filterActive = (users) => users.filter(u => u.active);
const sortByName = (users) => [...users].sort((a, b) => a.name.localeCompare(b.name));
const getNames = (users) => users.map(u => u.name);

const getActiveSortedNames = pipe(filterActive, sortByName, getNames);

const users = [
  { name: "Zara", active: true },
  { name: "Akshai", active: true },
  { name: "Kumar", active: false },
  { name: "Priya", active: true },
];

getActiveSortedNames(users);
// ["Akshai", "Priya", "Zara"]
```

### Where you see this pattern

You already use this concept without calling it compose/pipe:
```javascript
// Array method chaining — this IS piping
const result = users
  .filter(u => u.active)
  .sort((a, b) => a.name.localeCompare(b.name))
  .map(u => u.name);

// Redux middleware — compose pattern
const store = createStore(
  reducer,
  compose(
    applyMiddleware(thunk, logger),
    devToolsEnhancer()
  )
);

// Express middleware — pipe pattern
app.use(authenticate);
app.use(validate);
app.use(handleRequest);
// Request flows: authenticate → validate → handleRequest
```

📢 NOTES:

> Each function in a compose/pipe chain must take one argument and return one value. This constraint is called a **unary function**. If a function needs multiple arguments, wrap it using currying or partial application:
```javascript
// This won't work in a pipe — multiply takes two arguments
const multiply = (a, b) => a * b;

// Curried version — works in a pipe
const multiplyBy = (factor) => (num) => num * factor;

const transform = pipe(add10, multiplyBy(2), subtract5);
transform(5); // 25
```

See the code for [compose function](https://github.com/akshaitr/js-polyfills/blob/main/src/compose.js)
See the code for [pipe function](https://github.com/akshaitr/js-polyfills/blob/main/src/pipe.js)

# Currying

Currying transforms a function that takes multiple arguments into a sequence of functions that each take a single argument.
```javascript
// Normal function
function add(a, b, c) {
  return a + b + c;
}
add(1, 2, 3); // 6

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}
curriedAdd(1)(2)(3); // 6

// Arrow function shorthand
const curriedAdd = (a) => (b) => (c) => a + b + c;
```

### Why currying is useful

**1. Creating specialized functions from generic ones**
```javascript
const multiply = (a) => (b) => a * b;

const double = multiply(2);
const triple = multiply(3);
const tenTimes = multiply(10);

double(5);   // 10
triple(5);   // 15
tenTimes(5); // 50

// Without currying you'd write:
function double(n) { return multiply(2, n); }
function triple(n) { return multiply(3, n); }
// More boilerplate, same result
```

**2. Configurable utility functions**
```javascript
const log = (level) => (prefix) => (message) => {
  console.log(`[${level}] ${prefix}: ${message}`);
};

const errorLog = log("ERROR");
const errorAuth = errorLog("AUTH");
const errorDB = errorLog("DB");

errorAuth("Invalid token");  // [ERROR] AUTH: Invalid token
errorDB("Connection lost");  // [ERROR] DB: Connection lost

// Configure once, use everywhere — no repeating level and prefix
```

**3. Works perfectly with compose and pipe**
```javascript
// Compose/pipe require unary functions (one argument)
// Currying makes multi-argument functions work in pipelines

const filterBy = (key) => (value) => (arr) =>
  arr.filter(item => item[key] === value);

const mapTo = (key) => (arr) =>
  arr.map(item => item[key]);

const sortBy = (key) => (arr) =>
  [...arr].sort((a, b) => a[key].localeCompare(b[key]));

const getActiveUserNames = pipe(
  filterBy("status")("active"),
  sortBy("name"),
  mapTo("name")
);

const users = [
  { name: "Zara", status: "active" },
  { name: "Akshai", status: "active" },
  { name: "Kumar", status: "inactive" },
];

getActiveUserNames(users); // ["Akshai", "Zara"]
```

**4. Event handler factories in React**
```javascript
// You already do this — it's currying
const handleChange = (field) => (event) => {
  setForm(prev => ({ ...prev, [field]: event.target.value }));
};

<input onChange={handleChange("email")} />
<input onChange={handleChange("password")} />
```

### Generic curry utility

A function that converts any regular function into a curried one:
```javascript
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

// Usage
function add(a, b, c) {
  return a + b + c;
}

const curriedAdd = curry(add);

// All of these work:
curriedAdd(1)(2)(3);    // 6
curriedAdd(1, 2)(3);    // 6
curriedAdd(1)(2, 3);    // 6
curriedAdd(1, 2, 3);    // 6
```

📢 NOTES:

> `fn.length` returns the number of parameters a function expects. The curry utility uses this to know when all arguments have been collected.
```javascript
function add(a, b, c) {}
add.length; // 3

// Rest params and defaults don't count
function demo(a, b, ...rest) {}
demo.length; // 2

function demo2(a, b = 10) {}
demo2.length; // 1 — stops counting at first default
```

### Partial application vs Currying

These are related but different:
```javascript
// Currying — always returns unary functions in a chain
const add = (a) => (b) => (c) => a + b + c;
add(1)(2)(3);

// Partial application — fix some arguments, return function for the rest
function add(a, b, c) {
  return a + b + c;
}

const addTo10 = add.bind(null, 10);     // fixes first arg
addTo10(2, 3);                           // 15

const addTo10And20 = add.bind(null, 10, 20); // fixes first two args
addTo10And20(3);                               // 33
```

Currying always produces a chain of single-argument functions. Partial application fixes some arguments and lets you pass the rest in one call. The curry utility above actually supports both patterns.

# Structured Clone and Data Copying

### The three levels of copying

**1. Assignment — no copy at all**
```javascript
const original = { name: "Akshai", hobbies: ["reading", "coding"] };
const ref = original;

ref.name = "Kumar";
console.log(original.name); // "Kumar" — same object
```

**2. Shallow copy — copies top level only**
```javascript
const original = { name: "Akshai", hobbies: ["reading", "coding"] };
const shallow = { ...original };

shallow.name = "Kumar";
console.log(original.name); // "Akshai" — top-level string is independent ✅

shallow.hobbies.push("gaming");
console.log(original.hobbies); // ["reading", "coding", "gaming"] — nested array is shared ❌
```
Shallow copy methods: spread operator `{ ...obj }`, `Object.assign({}, obj)`, `Array.from(arr)`, `[...arr]`

**3. Deep copy — completely independent at every level**
```javascript
const original = { name: "Akshai", hobbies: ["reading", "coding"] };
const deep = structuredClone(original);

deep.hobbies.push("gaming");
console.log(original.hobbies); // ["reading", "coding"] — completely independent ✅
```

### structuredClone — the modern way
```javascript
const original = {
  name: "Akshai",
  date: new Date(),
  pattern: /hello/gi,
  data: new Map([["key", "value"]]),
  nested: { deep: { value: 42 } }
};

const clone = structuredClone(original);

// Everything is deeply copied, including:
// ✅ Nested objects and arrays
// ✅ Date objects (remain Date, not string)
// ✅ RegExp
// ✅ Map, Set
// ✅ ArrayBuffer, Blob
// ✅ Circular references

// Cannot clone:
// ❌ Functions
// ❌ DOM elements
// ❌ Symbols
// ❌ Property descriptors (getters/setters)
// ❌ Prototype chain
```

### JSON.parse(JSON.stringify()) — the legacy way
```javascript
const clone = JSON.parse(JSON.stringify(original));

// Limitations:
// ❌ Functions → removed
// ❌ undefined → removed
// ❌ Symbol → removed
// ❌ Date → becomes string
// ❌ RegExp → becomes empty object {}
// ❌ Map/Set → becomes empty object {}
// ❌ NaN → becomes null
// ❌ Infinity → becomes null
// ❌ Circular references → throws error
```
```javascript
const obj = {
  fn: () => {},
  date: new Date("2025-01-01"),
  undef: undefined,
  regex: /test/gi,
  nan: NaN,
  map: new Map([["a", 1]])
};

const clone = JSON.parse(JSON.stringify(obj));
console.log(clone);
// {
//   date: "2025-01-01T00:00:00.000Z",  ← string, not Date
//   regex: {},                           ← empty object
//   nan: null,                           ← null
//   map: {}                              ← empty object
//   (fn and undef are gone)
// }
```

### When to use what
```
Method                  | Depth   | Handles special types | Performance
------------------------|---------|----------------------|------------
= assignment            | None    | N/A                  | Instant
{ ...obj }              | Shallow | N/A                  | Fast
Object.assign()         | Shallow | N/A                  | Fast
JSON parse/stringify    | Deep    | ❌ Many limitations   | Slow
structuredClone()       | Deep    | ✅ Most types         | Medium
Custom recursive clone  | Deep    | ✅ Full control       | Depends
```

# Web Workers

JavaScript on the main thread shares the call stack with rendering and user input. CPU-heavy work (large JSON parse, image processing, crypto, data transforms) can freeze the UI. Web Workers run scripts on a **background thread** so the main thread stays responsive.

### Creating a worker
```javascript
// main.js
const worker = new Worker("worker.js");

worker.postMessage({ nums: [1, 2, 3, 4, 5] });

worker.onmessage = (event) => {
  console.log("Sum:", event.data); // 15
};

worker.onerror = (err) => {
  console.error("Worker error:", err.message);
};

// worker.js
self.onmessage = (event) => {
  const sum = event.data.nums.reduce((a, b) => a + b, 0);
  self.postMessage(sum);
};
```

Workers do **not** share memory with the main thread by default. `postMessage` uses the [structured clone](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#structured-clone-and-data-copying) algorithm (or transferables — below).

### What workers cannot do

- No DOM access (`document`, `window` UI APIs)
- No direct access to main-thread variables
- Communication is message-based only

They *can* use `fetch`, timers, `IndexedDB`, `WebAssembly`, and many other APIs.

### Transferable objects (zero-copy)

For large `ArrayBuffer`s, cloning is expensive. Transfer ownership instead — the sender loses access, the receiver gains it with no copy.
```javascript
const buffer = new ArrayBuffer(1024 * 1024); // 1MB
worker.postMessage(buffer, [buffer]);
// buffer is now detached (byteLength === 0) on the main thread
```

### Types of workers

- **Dedicated Worker** — `new Worker(url)` — one page owns it (most common)
- **Shared Worker** — shared across tabs/windows of the same origin
- **Service Worker** — network proxy / offline caching (different lifecycle; not covered here)

### OffscreenCanvas (why this matters for FE)

You can move canvas rendering off the main thread:
```javascript
const canvas = document.querySelector("canvas");
const offscreen = canvas.transferControlToOffscreen();
worker.postMessage({ canvas: offscreen }, [offscreen]);
```

📢 NOTES:

> Use a worker when work is CPU-bound and long enough to drop frames. Don't spawn workers for tiny tasks — setup + messaging overhead can cost more than the work itself.

> Module workers: `new Worker("worker.js", { type: "module" })` lets the worker use `import` / `export`.

> For shared memory between threads (no copying), see [SharedArrayBuffer and Atomics](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#sharedarraybuffer-and-atomics) below.

# SharedArrayBuffer and Atomics

### The problem

[Web Workers](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#web-workers) let you run code in parallel, but they communicate by copying (or transferring) data. For large shared datasets that both threads must read and write, copying is too slow.
```javascript
// Normal worker communication — copies the data
const worker = new Worker("worker.js");
const hugeArray = new Float64Array(1000000);
worker.postMessage(hugeArray); // copies entire array — slow
```

### SharedArrayBuffer

SharedArrayBuffer creates memory that multiple threads can access simultaneously — no copying needed.
```javascript
// Main thread
const shared = new SharedArrayBuffer(1024); // 1KB shared memory
const view = new Int32Array(shared);
view[0] = 42;

const worker = new Worker("worker.js");
worker.postMessage(shared); // passes reference, no copy

// worker.js
onmessage = function(e) {
  const view = new Int32Array(e.data);
  console.log(view[0]); // 42 — reading from shared memory
  view[0] = 100;        // main thread can see this change
};
```

### Atomics — safe concurrent access

When multiple threads read/write the same memory, you get race conditions. Atomics provides thread-safe operations.
```javascript
const shared = new SharedArrayBuffer(4);
const view = new Int32Array(shared);

// Without Atomics — race condition
view[0]++;  // NOT safe — read, increment, write can be interrupted

// With Atomics — guaranteed atomic
Atomics.add(view, 0, 1);      // thread-safe increment
Atomics.load(view, 0);        // thread-safe read
Atomics.store(view, 0, 42);   // thread-safe write
Atomics.compareExchange(view, 0, 42, 100); // if value is 42, set to 100
```

📢 NOTES:

> SharedArrayBuffer requires specific HTTP headers due to Spectre vulnerability mitigations:
> ```
> Cross-Origin-Opener-Policy: same-origin
> Cross-Origin-Embedder-Policy: require-corp
> ```
> Without these headers, SharedArrayBuffer is not available in the browser.

> This is a niche topic — most frontend developers never use it directly. But understanding it is valuable because it explains how tools like `OffscreenCanvas`, WebAssembly threads, and high-performance computation work under the hood.
# WeakRef and FinalizationRegistry

### WeakRef

A WeakRef lets you hold a reference to an object without preventing it from being garbage collected. Unlike WeakMap where the key is weak, WeakRef gives you a weak reference to any object.
```javascript
let largeObject = { data: new Array(1000000).fill("x") };
const weakRef = new WeakRef(largeObject);

// Access the object — returns the object or undefined if GC'd
console.log(weakRef.deref()); // { data: [...] }

largeObject = null; // remove strong reference

// At some point after GC runs:
console.log(weakRef.deref()); // undefined — object was collected
```

📢 NOTES:

> You must always check if `deref()` returns undefined before using the value. Garbage collection is non-deterministic — you can't predict when or if the object will be collected.

**Practical use case — caching with automatic cleanup:**
```javascript
class WeakCache {
  #cache = new Map();

  set(key, value) {
    this.#cache.set(key, new WeakRef(value));
  }

  get(key) {
    const ref = this.#cache.get(key);
    if (!ref) return undefined;

    const value = ref.deref();
    if (value === undefined) {
      this.#cache.delete(key); // clean up dead reference
    }
    return value;
  }
}

let bigData = { items: new Array(1000000) };
const cache = new WeakCache();
cache.set("data", bigData);

cache.get("data"); // { items: [...] }

bigData = null;
// Eventually after GC:
cache.get("data"); // undefined — automatically cleaned up
```

### FinalizationRegistry

Lets you register a callback that runs when an object is garbage collected. Useful for cleanup of external resources.
```javascript
const registry = new FinalizationRegistry((heldValue) => {
  console.log(`Object with id ${heldValue} was garbage collected`);
  // Clean up external resource (close file, release memory, etc.)
});

let user = { name: "Akshai" };
registry.register(user, "user-123"); // "user-123" is passed to the callback

user = null;
// Eventually: "Object with id user-123 was garbage collected"
```

📢 NOTES:

> WeakRef and FinalizationRegistry should be used sparingly — they're low-level tools for specific scenarios like caching and resource management. Don't use them for general application logic. GC behavior is unpredictable, so your code should never depend on the callback firing at a specific time.

