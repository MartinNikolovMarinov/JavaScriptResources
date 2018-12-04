[Back](./README.md)

# Mode
Providing the mode configuration option tells webpack to use its built-in optimizations accordingly.
```js
{
  mode: 'production' | 'development' | 'none'
}
```

| Option        | Description           |
| ------------- |:-------------:|
| development   | Sets process.env.NODE_ENV on DefinePlugin to value development. Enables NamedChunksPlugin and NamedModulesPlugin. |
| production    | Sets process.env.NODE_ENV on DefinePlugin to value production. Enables FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin and UglifyJsPlugin. |
| none          | Sets process.env.NODE_ENV on DefinePlugin to value development. Enables NamedChunksPlugin and NamedModulesPlugin.|


