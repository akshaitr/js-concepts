# Topics

1. [Execution Context](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#execution-context)
2. [Call Stack](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#call-stack)
3. [Scope](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#scope)
4. [Functions](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#functions)
5. [Closures](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#closures)
6. [Objects](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#objects)
7. [Binding](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#binding)
8. [Promises](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#promises)
9. [Event Propagation](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-propagation)
10. [Debouncing and throttling](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#debouncing-and-throttling)
11. [Compose and Pipe](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#compose-and-pipe)
12. [Prototypes](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#prototypes)
13. [Classes and constructors](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#class-and-constructors)
14. [Event Loop](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#event-loop)
15. [Async and await](https://github.com/akshaitr/JS-Concepts/blob/main/README.md#async-and-await)

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
   In FEC: depends on how the function is called (see Binding section)

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

> `let` and `const` variables are hoisted but not initialized until the line they are declared. They are said to be hoisted to temporal dead zone. (i.e, they are in the scope but not yet declared)

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

// Fix 3: Pass to a named function
for (var i = 0; i < 5; i++) {
  function print(index) {
    setTimeout(function() {
      console.log(index);
    }, index * 1000);
  }
  print(i);
}
// Output: 0 1 2 3 4
```

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
