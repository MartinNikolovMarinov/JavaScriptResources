[Back](./index)

# Module
Each module has a smaller surface area than a full program, making verification, debugging, and testing trivial. Well-written modules provide solid abstractions and encapsulation boundaries, so that each module has a coherent design and a clear purpose within the overall application.

In contrast to Node.js modules, webpack modules can express their dependencies in a variety of ways. A few examples are:

1. An **ES2015** import statement
2. A **CommonJS require()** statement
3. An **AMD define and require** statement
4. An **@import** statement inside of a css/sass/less file.
5. An **image url** in a stylesheet (url(...)) or html (\<img src=...\>) file.

## Supported Module Types
webpack supports modules written in a variety of languages and preprocessors, via loaders. Loaders describe to webpack how to process non-JavaScript modules and include these dependencies into your bundles. The webpack community has built loaders for a wide variety of popular languages and language processors, including :

* CoffeeScript
* TypeScript
* ESNext (Babel)
* Sass
* Less
* Stylus

And others.