# Hoisting

## What is hoisting

Hoisting mean that **variable** and **function declarations** are physically moved to the top of our code, but this is not in fact what happens. Instead, the variable and function declarations are put into memory during the *compile* phase, but stay exactly where we typed them.

Example:

```javascript
console.log(hoistedFunction('10'));

function hoistedFunction(x) {
  return x;
}

// The result will be "10"
```

## Only declarations are hoisted

JavaScript only hoists declarations, not initializations. If a variable is declared and initialized after using it, the value will be undefined. For example:

```javascript
console.log(variable); // Returns undefined 

var variable;

variable = 10;
```

If we tried the prervious example using `let` or `const` it will through a `ReferenceError` but that doesn't mean that variables declared with them are not hoisted. The main difference between them is in the **initialization**. Variables declared using `var` and function exprerssions are initalized with `undefined` at the top of the scope. `let` and `const` however stay **uninitialised**. This means that a `ReferenceError` exception is thrown if we try to access it. It will only get initialised when the `let`/`const` statement is evaluated [¹][1]. Same applies for **class declarations**.

```javascript
var p = new Rectangle(); // ReferenceError

class Rectangle {}
```

[1]: https://www.ecma-international.org/ecma-262/6.0/#sec-let-and-const-declarations	"ES6 Let and Const Declarations"

