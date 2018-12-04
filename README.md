# A collection of resources for front end developers.

# Table Of Content

<ul>
  <li>
    <a href="#strict">What does 'use strict' do?</a>
  </li>
  <li>
    <a href="#this">The keyword this in JavaScript</a>
    <ul>
      <li><a href="#global">Global</li>
      <li><a href="#methods">Methods</li>
      <li><a href="#explicit-bind">Binding explicitly</a></li>
      <li><a href="#new-instance">New instances</a></li>
      <li><a href="#dom-event-listeners">DOM Event Listeners</a></li>
    </ul>
  </li>
  <li>
    <a href="#hoisting">Variable Hoisting</a>
  </li>
  <li>
    <a href="#clojure">What is a Clojure</a>
  </li>
  <li>
    <a href="#js-most-important">Most important thing about functions in JavaScript ! (advanced topic)</a>
  </li>
  <li>
    <a href="#modules">JavaScript Modules</a>
    <ul>
      <li><a href="#revealing-module-pattern">The Revealing Module Pattern</li>
      <li><a href="#commonJS">CommonJS</a></li>
      <li><a href="#amd">Asynchronous Module Definition (AMD)</a></li>
      <li><a href='#es2015'>ES2015 Modules</a></li>
      <li><a href="#umd">Universal Module Definition (UMD)</a></li>
      <li><a href="#system.js">The All-in-One Solution: System JS</a></li>
    </ul>
  </li>
  <li>
    <a href="#babel">
      What is Babel?
    </a>
  </li>
  <li>
    Linting ...
  </li>
  <li>
    <a href="#what-is-webpack">What is WebPack</a>
    <ul>
      <li><a href="#entry">Entry</a></li>
      <li><a href="#output">Output</a></li>
      <li><a href="#loaders">Loaders</a></li>
      <li><a href="#plugins">Plugins</a></li>
      <li><a href="#mode">Mode</a></li>
      <li><a href="#module">Module</a></li>
      <li><a href="#manifest">Manifest</a></li>
      <li><a href="#targets">Targets</a></li>
      <li>
        <a href="#hot-module-replacement">
          Hot Module Replacement
        </a>
      </li>
      <li>
        Webpack optimization ...
      </li>
      <li>
        Examples ... Start Here Next ...
      </li>
    </ul>
  </li>
  <li>
    React ...
  </li>
  <li>
    State Management ..
  </li>
  <li>
    <a href="#sources">Sources</a>
  </li>
</ul>

<h1 id="strict">What does 'use strict' do?</h1>

Strict Mode is a feature in ECMAScript 5 that allows you to place a program, or a function, in a "strict" operating context. Strict mode helps out in a couple ways :
* It catches some common coding bloopers, throwing exceptions.
* It prevents, or throws errors, when relatively "unsafe" actions are taken (such as gaining access to the global object).
* It disables features that are confusing or poorly thought out.

## How do you enable strict mode?

Simple. Toss this at the top of a program to enable it for the whole script:

```js
// First way is to use it globally.
'use strict';

function imStrict() {
  // Second way is to use it per function.
  'use strict';
}

(function() {
  // You can also define your library strictly.
  'use strict';
})();
```

## Variables and Properties

An attempt to assign `foo = 'bar';` where `foo` hasn't been defined will fail. Previously it would assign the value to the foo property of the global object (e.g.  window.foo), now it just throws an exception. Also, deleting a variable, a function, or an argument will result in an error.

Examples :
```js
foo = 'test'; // Error, foo is not defined.
var foo = 'test';
function test(){}

delete foo; // Error, can't delete variables outside of objects.
delete test; // Error, can't delete functions outside of objects.

function test2(arg) {
  delete arg; // Error, can't delete function arguments.
}

{ foo: true, foo: false } // Error, duplicate object keys.
```

## eval

Virtually any attempt to use the name `eval` is prohibited - as is the ability to assign the `eval` function to a variable or a property of an object.

```js
// All generate errors...
obj.eval = 'test'
obj.foo = eval
var eval = 'test'
for ( var eval in ... ) {}
function eval(){}
function test(eval){}
function(eval){}
new Function('eval')

// Additionally, attempts to introduce new variables through an eval will be blocked.
eval('var a = false;');
console.log(typeof a); // undefined
```

## Functions

Attempting to overwrite the arguments object will result in an error:
```js
  arguments = []; // not allowed
```
Defining identically-named arguments will result in an error:
```js
function( foo, foo ) {}
```

Access to `arguments.caller` and `arguments.callee` now throw an exception. Thus any anonymous functions that you want to reference will need to be named, like so :
```js
setTimeout(function later() {
  // do stuff...
  setTimeout( later, 1000 );
}, 1000 );
```

The `arguments` and `caller` properties of other functions no longer exist - and the ability to define them is prohibited :
```js
function test(){
  function inner(){
    // Don't exist, either
    test.arguments = ...; // Error
    inner.caller = ...; // Error
  }
}
```

Finally, a long-standing (and very annoying) bug has been resolved. Cases where null or undefined is coerced into becoming the global object. Strict mode now prevents this from happening and throws an exception instead :
```js
(function(){ ... }).call( null ); // Exception
```

## with(){}

`with(){} `statements are dead when strict mode is enabled - in fact it even appears as a syntax error.



<h1 id="this"> The keyword this in JavaScript </h1>

<h2 id="global"> Global </h2>

By default **this** refers to the global object. That could be either window, if the code is running in the **Browser**, or it could be **global** if the code is running in **NodeJS**. If **use strict** is used then **this**, used outside of an object, will always refer to **undefined**.

The following code is written it the global context, ie. no modules, no IIFE, no object/classes, etc. just plain procedural code :
```js
function fn() {
  const context = this;
  console.log(context === window); // true
}

function strictFn() {
  'use strict'
  const context = this;
  console.log(context === window); // false
}

fn();
strictFn();
console.log(this === window) // true
```

<h2 id="methods"> Methods </h2>

A method is a function attached to an object.

```js
const car = {
  maker: 'Ford',
  model: 'Fiesta',
  info() {
    console.log(`${this.maker} ${this.model}`)
  }
}

car.info2 = function() {
  console.log(`${this.maker} ${this.model}`)
}

/*
  When using arrow funcitons, the scope is inherited from the parent.
  In this case the window, so in this case - this.maker is undefined.
*/
car.info3 = () => {
  'use strict' // using strict doesn't prevent this === window !!
  console.log(`${this.maker} ${this.model}`)
}

car.info(); // Ford Fiesta
car.info2(); // Ford Fiesta
car.info3(); // undefined undefined
```

<h2 id="explicit-bind"> Binding explicitly </h2>

JavaScript offers a few ways to map **this** to any object you want.

#### Using **bind()**, at the function declaration step :
```js
const car = {
  maker: 'Ford',
  model: 'Fiesta'
}

const drive = function() {
  console.log(`${this.maker} ${this.model}`)
}

drive.bind(car);
drive() // Ford Fiesta
```

#### You could also bind an existing object method to remap its this value (Which is a BAD idea in general, but a good example) :
```js
const car = {
  maker: 'Ford',
  model: 'Fiesta',

  drive() {
    console.log(`${this.maker} ${this.model}`)
  }
}

const anotherCar = {
  maker: 'Audi',
  model: 'A4'
}

car.drive.bind(anotherCar)
car.drive() // Audi A4
```

#### Using **call()** or **apply()**, at the function invocation step :
```js
const car = {
  maker: 'Ford',
  model: 'Fiesta'
}

const drive = function(kmh) {
  console.log(`Driving a ${this.maker} ${this.model} car at ${kmh} km/h!`)
}

drive.call(car, 100) // Driving a Ford Fiesta car at 100 km/h!

drive.apply(car, [100]) // Driving a Ford Fiesta car at 100 km/h!
```

The first parameter you pass to **call()** or **apply()** is always bound to **this**. The difference between **call()** and **apply()** is just that the second one wants an array as the arguments list, while the first accepts a variable number of parameters, which passes as function arguments.

<h2 id="new-instance"> New instances </h2>

When a function is invoked with **new** keyword then the function is known as constructor function and returns a new instance. In such cases, the value of **this** refers to newly created instance:
```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;

  this.displayName = function() {
    console.log(`${this.firstName} ${this.lastName}`);
  }
}

let person = new Person("John", "Reed");
person.displayName();  // John Reed
let person2 = new Person("Paul", "Adams");
person2.displayName();  // Paul Adams
```

<h2 id="dom-event-listeners"> DOM Event Listeners </h2>

Your JavaScript code can register a handler function which is triggered when a particular event occurs. Most browsers pass a single object to the function which contains information about the event, i.e. what keys were pressed, the position of the cursor, etc. In addition, **this** may be changed.

#### Inline events
These were the first event handlers browsers supported :
```html
<!-- This will be the window : -->
<a id="link" onclick="EventHandler();">click me</a>

<!-- The a element is passed and becomes this -->
<a id="link" onclick="return EventHandler2(this);">click me</a>

<script>
function EventHandler() {
  console.log(this);
}
</script>
```

#### Traditional DOM0 events
Here's an example using traditional event handling :
```html
<!-- The a element is the this object: -->
<p><a id="link">click me</a></p>

<script>
var link = document.getElementById("link");
link.onclick = EventHandler;

function EventHandler() {
  console.log(this.id); // link
}
</script>
```
In EventHandler(), every browser sets **this** to the element which fired the event - our anchor tag. It's reliable but has a major drawback: we can only assign a single handler for each event type.

#### Modern DOM2 events
Finally, we have modern event handling which allows multiple handlers to be specified for the same event. Unfortunately, Microsoft and the W3C had a few differences of opinion with regard to their implementation and only IE9 supports **addEventListener()**. We can, however, use a little object detection to create a cross-browser event-attaching function which works in all browsers :
```html
<a id="link">click me</a>

<script>
function AttachEvent(element, type, handler) {
  if (element.addEventListener) element.addEventListener(type, handler, false);
  else element.attachEvent("on" + type, handler);
}

function EventHandler(e) {
  console.log(this); // the a element in all browsers IE8 +
}

var link = document.getElementById("link");
AttachEvent(link, "click", EventHandler);
</script>
```

As with DOM0, all browsers set **this** to the element which fired the event … except one. Internet Explorer 8.0 and below only reference the event handler so **this** is always the global window object.

Fortunately we can determine the target element from the event object instead:
```js
function EventHandler(e) {
  e = e || window.event;
  let target = e.target || e.srcElement;
  console.log(target);
}
```

<h1 id="hoisting">Variable Hoisting</h1>

Before a function gets executed, its body gets parsed/scanned for variable and function declarations. Resulting in :
* All variables are declared as undefined.
* All functions create a local object and assign to it a function object.

so the following :
```js
var a = 5;
test(); // this is not an error !
new A(a).printA(); // this just appears to work, it actually prints undefined!

function test () { console.log(a); }
function A(a) {
  this.a = a;
  function printA() { console.log(this.a); }
  return {
    printA
  }
}
```
becomes :
```js
var test = { /* a reference to the test function object */ }
var A = { /* nothing is defined here */ }
var a = undefined;

a = 5;
test(); // prints 5
new A(a).printA(); // prints undefined !
```

<h1 id="clojure">What is a Clojure</h1>

Every time a JavaScript program is executed it starts in the global execution context. Some variables are declared within the global execution context. We call these global variables. When the program calls a function, what happens? A few steps :
1. JavaScript creates a new local execution context.
2. That local execution context will have its own set of variables.
3. The new execution context is thrown onto the execution stack. Think of the execution stack as a mechanism to keep track of where the program is in its execution.

When does the function end? When it encounters a `return` statement or it encounters a closing bracket `}`. When a function ends, the following happens :
1. The local execution contexts pops off the execution stack.
2. The functions sends the return value back to the calling context. If the function has no return statement, undefined is returned.
3. The local execution context is destroyed. This is **important!** **Destroyed!** All the variables that were declared within the local execution context are erased. **They are no longer available. That's why they're called local variables.**

## Important Step-by-Step Examples :

* <a href="./basic-scope.md">Basic Javascript Program Execution</a>
* <a href="./lexical-scope.md">Lexical Scope</a>
* <a href="./ret-function-scope.md">A Function That Returns A Function</a>
* <a href="./clojure.md">Clojure</a>

<h1 id="js-most-important"> Most important thing about functions in JavaScript ! (advanced topic) </h1>

**Is that they are objects on the heap. They have a virtual stack on the heap. JavaScript doesn't easily take advantage of the low-level processor stack at all. Only way is through interpreter optimizations.**

**If you want to implement a Clojure, functions must be objects and they must have a virtual stack on the heap.**

Why ?

**Because Closures are the links in the scope chain(a link list of function scopes), which means that function stack memory can't be freed after a functions gets popped off the stack!**

So what ?

**Using heap memory also means that Closure can be a source of memory leaks, if we hold them unnecessarily, or if we hold in them something we don't need!**

<h1 id='modules'>JavaScript Modules</h1>
Different pieces of software are usually developed in isolation until some requirement needs to be satisfied by a previously existing piece of software. At the moment that other piece of software is brought into the project a dependency is created between it and the new piece of code. Since these pieces of software need to work together, it is of importance that no conflicts arise between them. This may sound trivial, but without some sort of encapsulation it is a matter of time before two modules conflict with each other.

In the following example scripts are manually loaded in the correct order:
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Backbone.js Todos</title>
        <link rel="stylesheet" href="todos.css"/>
    </head>

    <body>
        <script src="../../test/vendor/json2.js"></script>
        <script src="../../test/vendor/jquery.js"></script>
        <script src="../../test/vendor/underscore.js"></script>
        <script src="../../backbone.js"></script>
        <script src="../backbone.localStorage.js"></script>
        <script src="todos.js"></script>
    </body>

    <!-- (...) -->

</html>
```
As JavaScript development gets more and more complex, dependency management can get cumbersome. Refactoring is also impaired: where should newer dependencies be put to maintain proper order of the load chain?

JavaScript module systems attempt to deal with these problems and others. They were born out of necessity to accommodate the ever growing JavaScript landscape. Let's see what the different solutions bring to the table.

## Solutions :
<h3 id="revealing-module-pattern">Revealing Module Pattern</h3>
Most module systems are relatively recent. Before they were available, a particular programming pattern started getting used in more and more JavaScript code: the revealing module pattern.

```js
var myRevealingModule = (function () {
    var privateVar = "Ben Cherry",
        publicVar = "Hey there!";

    function privateFunction() {
        console.log( "Name:" + privateVar );
    }

    function publicSetName( strName ) {
        privateVar = strName;
    }

    function publicGetName() {
        privateFunction();
    }

    // Reveal public pointers to
    // private functions and properties
    return {
        setName: publicSetName,
        greeting: publicVar,
        getName: publicGetName
    };
})();

myRevealingModule.setName( "Paul Kinlan" );
```
JavaScript scopes (at least up to the appearance of let in ES2015) work at the function level. In other words, whatever binding is declared inside a function cannot escape its scope, also **NO** scope is created in block statements like **if**, **for**, **while**, **try-catch**, **etc.** blocks. It is for this reason the revealing module pattern relies on functions to encapsulate private contents.

In the example above, public symbols are exposed in the returned object. All other declarations are protected by the function scope enclosing them.

This pattern has been in use for quite some time in JavaScript projects and deals fairly nicely with the encapsulation matter. It does not do much about the dependencies issue. Proper module systems attempt to deal with this problem as well. Another limitation lies in the fact that including other modules cannot be done in the same source (unless using eval).

**pros :**
1. Simple enough to be implemented anywhere (no libraries, no language support required).
2. Multiple modules can be defined in a single file.

**cons :**
1. No way to programmatically import modules (except by using eval).
2. Dependencies need to be handled manually.
3. Asynchronous loading of modules is not possible.
4. Circular dependencies can be troublesome.
5. Hard to analyze for static code analyzers.

<h3 id="commonJS">CommonJS</h3>
CommonJS is a project that aims to define a series of specifications to help in the development of server-side JavaScript applications. One of the areas the CommonJS team attempts to address are modules. Node.js developers originally intended to follow the CommonJS specification but later decided against it. When it comes to modules, Node.js's implementation is very influenced by it:

```js
// In circle.js
const PI = Math.PI;
exports.area = (r) => PI * r * r;
exports.circumference = (r) => 2 * PI * r;
```
```js
// In some file in the same directory
const circle = require('./circle.js');
console.log( `The area of a circle of radius 4 is ${circle.area(4)}`);
```
In both Node's and CommonJS's modules there are essentially two elements to interact with the module system: `require` and `exports`. `require` is a function that can be used to import symbols from another module to the current scope. The parameter passed to `require` is the id of the module. In Node's implementation, it is the name of the module inside the node_modules directory (or, if it is not inside that directory, the path to it). `exports` is a special object: anything put in it will get exported as a public element. Names for fields are preserved. A peculiar difference between Node and CommonJS arises in the form of the `module.exports` object. In Node, `module.exports` is the real special object that gets exported, while `exports` is just a variable that gets bound by default to `module.exports`. CommonJS, on the other hand, has no `module.exports` object. The practical implication is that in Node it is not possible to export a fully pre-constructed object without going through `module.exports`:
```js
// This works as expected.
module.exports = (width) => {
  return {
    area: () => width * width
  };
}
```
CommonJS modules were designed with server development in mind. Naturally, the API is synchronous. In other words, modules are loaded at the moment and in the order they are required inside a source file.

**pros:**
1. Simple: a developer can grasp the concept without looking at the docs.
2. Dependency management is integrated: modules `require` other modules and get loaded in the needed order.
3. `require` can be called anywhere: modules can be loaded programmatically.
4. Circular dependencies are supported.

**cons:**
1. Synchronous API makes it not suitable for certain uses (client-side).
2. One file per module.
3. Browsers require a loader library or transpiling.
4. No constructor function for modules (Node supports this though).
5. Hard to analyze for static code analyzers.

**Implementations**

We have already talked about one implementation (in partial form): Node.js.

For the client there are currently two popular options: webpack and browserify. Browserify was explicitly developed to parse Node-like module definitions (many Node packages work out-of-the-box with it!) and bundle your code plus the code from those modules in a single file that carries all dependencies. Webpack on the other hand was developed to handle creating complex pipelines of source transformations before publishing. This includes bundling together CommonJS modules.

<h3 id="amd">Asynchronous Module Definition (AMD)</h3>
AMD was born out of a group of developers that were displeased with the direction adopted by CommonJS. In fact, AMD was split from CommonJS early in its development. The main difference between AMD and CommonJS lies in its support for asynchronous module loading.

```js
//Calling define with a dependency array and a factory function
define(['dep1', 'dep2'], function (dep1, dep2) {

    //Define the module value by returning a value.
    return function () {};
});

// Or:
define(function (require) {
    var dep1 = require('dep1'),
        dep2 = require('dep2');

    return function () {};
});
```

Asynchronous loading is made possible by using JavaScript's traditional closure idiom: a function is called when the requested modules are finished loading. Module definitions and importing a module is carried by the same function: when a module is defined its dependencies are made explicit. An AMD loader can therefore have a complete picture of the dependency graph for a given project at runtime. Libraries that do not depend on each other for loading can thus be loaded at the same time. This is particularly important for browsers, where startup times are essential to a good user experience.

**pros:**
1. Asynchronous loading (better startup times).
2. Circular dependencies are supported.
3. Compatibility for `require` and `exports`.
4. Dependency management fully integrated.
5. Modules can be split in multiple files if necessary.
6. Constructor functions are supported.
7. Plugin support (custom loading steps).

**cons:**
1. Slightly more complex syntactically.
2. Loader libraries are required unless transpiled.
3. Hard to analyze for static code analyzers.

**Implementations:**

Currently the most popular implementations of AMD are require.js and Dojo.

Using require.js is pretty straightforward: include the library in your HTML file and use the **data-main** attribute to tell require.js which module should be loaded first. Dojo has a similar setup.

<h3 id='es2015'>ES2015 Modules</h3>
Fortunately, the ECMA team behind the standardization of JavaScript decided to tackle the issue of modules. The result can be seen in the latest release of the JavaScript standard: ECMAScript 2015 (previously known as ECMAScript 6). The result is syntactically pleasing and compatible with both synchronous and asynchronous modes of operation.

```js
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main.js ------
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

The `import` directive can be used to bring modules into the namespace. This directive, in contrast with `require` and `define` is not dynamic (i.e. it cannot be called at any place). The `export` directive, on the other hand, can be used to explicitly make elements public.

The static nature of the `import` and `export` directive allows static analyzers to build a full tree of dependencies without running code. ES2015 does not support dynamic loading of modules, but a draft specification does:
```js
System.import('some_module')
  .then(some_module => {
      // Use some_module
  })
  .catch(error => {
      // ...
  });
```
This solution, by virtue of being integrated in the language, lets runtimes pick the best loading strategy for modules. In other words, when asynchronous loading gives benefits, it can be used by the runtime.

**pros:**
1. Synchronous and asynchronous loading supported.
2. Syntactically simple.
3. Support for static analysis tools.
4. Integrated in the language (eventually supported everywhere, no need for libraries).
5. Circular dependencies supported.

**cons:**
1. Still not supported everywhere.

**Implementations:**

Unfortunately none of the major JavaScript runtimes support ES2015 modules in their current stable branches. This means no support in Firefox, Chrome or Node.js. Fortunately many transpilers do support modules and a polyfill is also available. Currently, the ES2015 preset for Babel can handle modules with no trouble.

<h3 id="umd">Universal Module Definition (UMD)</h3>
A "universal" pattern that supports both AMD na CommonJS styles. This pattern is useful for libraries that work both on the client and on the server.

The pattern is admittedly ugly, but is both AMD and CommonJS compatible, as well as supporting the old-style "global" variable definition:
```js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS-like
        module.exports = factory(require('jquery'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.jQuery);
    }
}(this, function ($) {
    //    methods
    function myFunc(){};

    //    exposed public method
    return myFunc;
}))
```

**pros:**
1. All benefits from both CommonJS and AMD.

**cons:**
1. All negatives from both CommonJS and AMD.
2. Makes the code base bigger and uglier.

<h3 id="system.js">The All-in-One Solution: System.js</h3>
You may find yourself trying to move away from legacy code using one module system. Or you may want to make sure whatever happens, the solution you picked will still work. Enter System.js: a universal module loader that supports CommonJS, AMD and ES2015 modules. It can work in tandem with transpilers such as Babel or Traceur and can support Node and IE8+ environments. Using it is a matter of loading System.js in your code and then pointing it to your base URL:

```html
  <script src="system.js"></script>
  <script>
    // set our baseURL reference path
    System.config({
      baseURL: '/app',
      // or 'traceur' or 'typescript'
      transpiler: 'babel',
      // or traceurOptions or typescriptOptions
      babelOptions: {

      }
    });

    // loads /app/main.js
    System.import('main.js');
  </script>
```

As System.js does all the job on-the-fly, using ES2015 modules should generally be left to a transpiler during the build step in production mode. When not in production mode, System.js can call the transpiler for you, providing seamless transition between production and debugging environments.

<h1 id="babel">What is Babel?</h1>

**Reminder:**

Javascript is changing syntactically and in terms of features very rapidly. Browsers on the other hand are slow to implement/support those new features, also older browser versions can't support them. In some cases these features are essential for developing large complicated systems and the only way to use them is to transpile your JavaScript to an older targeted JavaScript version (like **es5**).

---

Babel is a toolchain that is mainly used to convert ECMAScript 6+ code into a backwards compatible version of JavaScript in current and older browsers or environments.

## Plugins
Babel is a compiler (source code => output code). Like many other compilers it runs in 3 stages: parsing, transforming, and printing.

Now, out of the box Babel doesn't do anything. You will need to add plugins for Babel to do anything. Plugins are a way to support syntax from versions of EcmaScript that are not yet supported in most browsers, or are experimental.

[All Plugins are listed here.](https://babeljs.io/docs/en/plugins)

## Presets
Instead of individual plugins, you can also enable a set of plugins in a preset.

[Official presets are listed here.](https://babeljs.io/docs/en/presets)

<h1 id="what-is-webpack">What is WebPack</h1>
Webpack simplifies web development by solving a fundamental problem: bundling. It takes in various assets, such as JavaScript, CSS, and HTML, and transforms them into a format that's convenient to consume through a browser. Doing this well takes a significant amount of pain away from web development.

At its core, webpack is a static module bundler for JavaScript applications. When webpack processes your application, it internally builds a dependency graph which maps every module your project needs and generates one or more bundles.

## Core Concepts :

<h3 id="entry"><a href="./entry.md">Entry</a></h3>
An entry point indicates which module webpack should use to begin building out its internal dependency graph. webpack will figure out which other modules and libraries that entry point depends on (directly and indirectly).

<h3 id="output"><a href="./output.md">Output</a></h3>
The output property tells webpack where to emit the bundles it creates and how to name these files.

<h3 id="loaders"><a href="./loaders.md">Loaders</a></h3>
Out of the box, webpack only understands JavaScript and JSON files. Loaders allow webpack to process other types of files and convert them into valid modules that can be consumed by your application and added to the dependency graph.

<h3 id="plugins"><a href="./plugins.md">Plugins</a></h3>
While loaders are used to transform certain types of modules, plugins can be leveraged to perform a wider range of tasks like bundle optimization, asset management and injection of environment variables.

<h3 id="mode"><a href="./mode.md">Mode</a></h3>
By setting the mode parameter to either development, production or none, you can enable webpack's built-in optimizations that correspond to each environment. The default value is production.

<h3 id="module"><a href="./module.md">Module</a></h3>
In modular programming, developers break programs up into discrete chunks of functionality called a module.

<h3 id="manifest"><a href="./manifest.md">Manifest</a></h3>
In a typical application or site built with webpack, there are three main types of code:

1. The source code you, and maybe your team, have written.
2. Any third-party library or "vendor" code your source is dependent on.
3. A webpack runtime and **manifest** that conducts the interaction of all modules.

<h3 id="targets"><a href="./targets.md">Targets</a></h3>
Because JavaScript can be written for both server and browser, webpack offers multiple deployment targets that you can set in your webpack configuration.

<h3 id="hot-module-replacement"><a href="./hot-module-replacement.md">Hot Module Replacement</a></h3>
Hot Module Replacement (HMR) exchanges, adds, or removes modules while an application is running, without a full reload.

<h1 id="sources">Sources/References</h1>

[The Webpack Documentation](https://webpack.js.org/concepts/)

[The auth0 Blog](https://auth0.com/blog/javascript-module-systems-showdown/)

[Babel Documentation](https://babeljs.io/docs/en/)

[This in JavaScript](https://flaviocopes.com/javascript-this/)

[This in Event Handlers](https://www.sitepoint.com/javascript-this-event-handlers/)

[Use Strict Mode](https://johnresig.com/blog/ecmascript-5-strict-mode-json-and-more/)

[Closures](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8)

[Closures in depth video](https://www.youtube.com/watch?v=zRZNb4GDOPI)