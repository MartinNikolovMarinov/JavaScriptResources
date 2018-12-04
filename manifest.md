[Back](./README.md)


# Manifest
The runtime, along with the manifest data, is basically all the code webpack needs to connect your modularized application while it's running in the browser. It contains the loading and resolving logic needed to connect your modules as they interact. This includes connecting modules that have already been loaded into the browser as well as logic to lazy-load the ones that haven't.

Once your application hits the browser in the form of index.html file, some bundles and a variety of other assets required by your application must be loaded and linked somehow. That /src directory you meticulously laid out is now bundled, minified and maybe even split into smaller chunks for lazy-loading by webpack's optimization. So how does webpack manage the interaction between all of your required modules? This is where the manifest data comes in...

As the compiler enters, resolves, and maps out your application, it keeps detailed notes on all your modules. This collection of data is called the "Manifest" and it's what the runtime will use to resolve and load modules once they've been bundled and shipped to the browser. No matter which module syntax you have chosen, those import or require statements have now become **\_\_webpack_require\_\_** methods that point to module identifiers. Using the data in the manifest, the runtime will be able to find out where to retrieve the modules behind the identifiers.