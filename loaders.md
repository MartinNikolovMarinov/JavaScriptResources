[Back](./README.md)

# Loaders
Loaders are transformations that are applied on the source code of a module. They allow you to pre-process files as you import or “load” them. Thus, loaders are kind of like “tasks” in other build tools and provide a powerful way to handle front-end build steps. Loaders can transform files from a different language (like TypeScript) to JavaScript or inline images as data URLs. Loaders even allow you to do things like import CSS files directly from your JavaScript modules!

## Example
For example, you can use loaders to tell webpack to load a CSS file or to convert TypeScript to JavaScript. To do this, you would start by installing the loaders you need:

npm install --save-dev css-loader
npm install --save-dev ts-loader
And then instruct webpack to use the css-loader for every .css file and the ts-loader for all .ts files:

```js
module.exports = {
  module: {

    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
};
```

## Using Loaders
There are three ways to use loaders in your application:

1. **Configuration** (recommended): Specify them in your webpack.config.js file.
2. **Inline**: Specify them explicitly in each import statement.
3. **CLI**: Specify them within a shell command.

### Configuration
`module.rules` allows you to specify several loaders within your webpack configuration.

Loaders are evaluated/executed from right to left. In the example below execution starts with sass-loader, continues with css-loader and finally ends with style-loader.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          },
          { loader: 'sass-loader' }
        ]
      }
    ]
  }
};
```

### Inline
It's possible to specify loaders in an import statement, or any equivalent "importing" method. Separate loaders from the resource with !. Each part is resolved relative to the current directory.

```js
import Styles from `style-loader!css-loader?modules!./styles.css`;
```
It's possible to override any loaders in the configuration by prefixing the entire rule with !.

Options can be passed with a query parameter, e.g. ?key=value&foo=bar, or a JSON object, e.g. ?{"key":"value","foo":"bar"}.

### CLI
You can also use loaders through the CLI:

```sh
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```
This uses the jade-loader for .jade files, and the style-loader and css-loader for .css files.

## Loader Features
* Loaders can be chained. Each loader in the chain applies transformations to the processed resource. A chain is executed in reverse order. The first loader passes its result (resource with applied transformations) to the next one, and so forth. Finally, webpack expects JavaScript to be returned by the last loader in the chain.
* Loaders can be synchronous or asynchronous.
* Loaders run in Node.js and can do everything that’s possible there.
* Loaders can be configured with an options object (using query parameters to set options is still supported but has been deprecated).
Normal modules can export a loader in addition to the normal main via package.json with the loader field.
Plugins can give loaders more features.
* Loaders can emit additional arbitrary files.

Loaders allow more power in the JavaScript ecosystem through preprocessing functions (loaders). Users now have more flexibility to include fine-grained logic such as compression, packaging, language translations and more.