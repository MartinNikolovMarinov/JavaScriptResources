[Back](./README.md)

# Targets

The webpack target property is not to be confused with the **output.libraryTarget** property!

Here is [a list of available](https://webpack.js.org/configuration/target/)

To set the target property, you simply set the target value in your webpack config:

```js
// webpack.config.js
module.exports = {
  target: 'node'
};
```
In the example above, using node webpack will compile for usage in a Node.js-like environment (uses Node.js require to load chunks and not touch any built in modules like fs or path).

# Multiple Targets
Although webpack does not support multiple strings being passed into the target property, you can create an isomorphic library by bundling two separate configurations:
```js
// webpack.config.js

const path = require('path');
const serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};
const clientConfig = {
  target: 'web', // <=== can be omitted as default is 'web'
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];
```
The example above will create a **lib.js** and **lib.node.js** file in your dist folder.