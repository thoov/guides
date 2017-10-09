While the Guides [assume you have a working knowledge of JavaScript](/#toc_assumptions),
when learning a new framework it can be hard to know what is JavaScript and what is framework,
especially if you are newer to the ecosystem and less familiar with JavaScript.

On top of that, many new features were introduced to JavaScript with the release of newer specifications like ECMAScript 2015,
also known as [ECMAScript 6 or ES6](https://developer.mozilla.org/en/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla).
With the current yearly cadence of ECMAScript specifications,
developers might have not had time to get familiar with all the new features.

In this guide we will be covering some common JavaScript code patterns that appear in Ember applications,
and in particular those that explore the new features provided in ES6,
so you can get a clearer sense of where the language ends and the framework starts.

## Variable declarations

JavaScript initially had two ways to declare variables, globally and `var`.
With the release of ES2015, `const` and `let` were introduced.
We will go through the different ways to declare a variable,
also called bindings because they *bind* a value to a variable name,
and why modern JavaScript tends to prefer `const` and `let`.

### `var`

`var` declarations exist in the entire body of the function where they are declared.
If you try to access a `var` outside of the function it is declared,
you will get an error that the `var` is not defined.
This is called function-scoping, the existence of the `var` is scoped to the function.

For our example, we will declare a `var` named `name`.
We will try to access it both inside the function and outside,
and see the results we get:

```javascript
console.log(name); // => name is not defined

function myFunction() {
  var name = "Tomster";

  console.log(name); // => Tomster
}
```

This also means that if you have an `if` or a `for` in your code and declare a `var` inside them,
you can still access the variable outside of those blocks:

```javascript
console.log(name); // => undefined

if (true) {
  var name = "Tomster";

  console.log(name); // => Tomster
}
```

In the previous example, we can see that the first `console.log(name)` prints out `undefined` instead of the value.
That is because of a feature of JavaScript called *hosting*.
Any variable declaration is moved by the programming language to the top of the scope it belongs to.
As we saw at the beginning, `var` is scoped to the function,
so the previous example is the same as:

```javascript
var name;
console.log(name); // => undefined

if (true) {
  name = "Tomster";

  console.log(name); // => Tomster
}
```

### `const` and `let`

There are two major differences between `var` and both `const` and `let`.
`const` and `let` are both block-level declarations, and they are *not* hoisted.

Because of this they are not accessible outside of the given block scope (meaning in a `function` or in `{}`) they are declared in.
You can also not access them before they are declared, or you will get a `foo is not defined` error.

```javascript
console.log(name) // => name is not defined

if (person) {
  console.log(name) // => name is not defined
  
  let name = 'Gob Bluth'; // => Gob Bluth
} else {
  console.log(name) // => name is not defined
}
```

`const` declarations are not hoisted either, but they differ from `let` declarations in that they are treated as constants, meaning their values cannot changed once they are set. As such, `const` declarations must be initialized where declared.

```javascript
const firstName; // invalid, no initialization
const firstName = 'Gob'; // valid
firstName = 'George Michael'; // invalid, no re-assignment
```

Both `const` and `let` declarations cannot be accessed until after their declaration,
and any attempt to access such bindings before their declarations occurs in what is known in the community as the temporal dead zone.

#### Deeper Dive, Global Variables

Additionally, when `var` is used in the global scope a new global variable is created,
which is a property on the global object (for example, the window object in a browser).

For `let` or `const`, a new variable is created, but no property on the global object is generated.

The result is that you cannot overwrite a global variable using `let` or `const` declarations,
only shadowing is possible.

#### Deeper Dive, Loop behavior

Furthermore, `let` corrects some problematic behavior of `var` in loops.

First, `let` behaves closer to expectations by restricting the accessibility of the counter variable to the block-level of the loop, while `var` does not.

```javascript
for (var i = 0; i < 3; i++) {
  // some code here using i
}
// i still accessible here
```

Because loop variables are accessible from outside the scope of a loop,
when creating a function inside of a loop, the counter variable is shared across each iteration,
potentially causing unexpected behavior.

```javascript
var someArray = [];
for (var i = 0; i < 3; i++) {
  someArray.push(function(){
    console.log(i);
  });
}

someArray.forEach(function(item) {
  item(); // logs the number "3" three times, but expected the log to be 0, then 1, then 2
});
```

While an immediately invoked function expression (IIFE) could be used to correct for this unexpected behavior,
the use of `let` declaration for the counter variable makes this unnecessary and arguably more cleanly solves the issue creating a new counter variable on each iteration through the loop.

```javascript
var someArray = [];
for (let i = 0; i < 3; i++) {
  someArray.push(function(){
    console.log(i);
  });
}

someArray.forEach(function(item) {
  item(); // logs the numbers 0, then 1, then 2, as expected
});
```

#### More Resources

For further reference you can check the MDN references for [`var`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var), [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const), and [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let). 
