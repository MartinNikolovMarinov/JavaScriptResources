[back](./README.md)

# Mobx State Management

## Observable State

Observable state is one of the main concepts of MobX. The idea behind this concept is to make an object able to emit changes to itself to listening observers. In the lateset Mobx versions this is acehived by using `ES6 Proxies`. You can use a `Proxy` to **determine behavior** whenever the properties of a target object are accessed. A handler object can be used to configure traps for your `Proxy`, more information on proxies [here](https://ponyfoo.com/articles/es6-proxies-in-depth).

You can make something observable by using the `@observable` decorator :
```js
@observable counter = 0
```

Observable values can be JS primitives, references, plain objects, class instances, arrays and maps. The following conversion rules are applied, but can be fine-tuned by using modifiers. See below.

1. If value is an ES6 Map: a `new Observable Map` will be returned.
2. If value is an array, a `new Observable Array` will be returned.
3. If value is an object without prototype, all its current properties will be made observable.
4. If value is an object with a prototype, a JavaScript primitive or function, observable will throw. Use Boxed Observable observables instead if you want to create a stand-alone observable reference to such a value. MobX will not make objects with a prototype automatically observable; as that is considered the responsibility of its `constructor` function. Use `extendObservable` in the `constructor`, or `@observable` / decorate in its class definition instead.

## Computed Values on MobX

With MobX you can define values that will be derived automatically when relevant data is modified. Computed values work in hand with observable states.

**All Computed values should be pure. They are not supposed to change state!**

Use the `@computed` decorator on any getter of a class property to declaratively create computed properties :

```js
class OrderLine {
    @observable price = 0;
    @observable amount = 1;

    constructor(price) {
        this.price = price;
    }

    @computed get total() {
        return this.price * this.amount;
    }
}
```

The computed function `total` is only calculated when price, or amount have changed.

## Reactions on MobX

Reactions are very similar to computed values. The difference here is that, instead of computing and returning a value, a reaction simply triggers a side effect, more like it performs a side operation. Reactions occur as a result of changes on observables. Reactions could affect the UI, or they could be background actions. MobX provides three main types of reaction functions: `when`, `autorun`, and `reaction`.

The `when` reaction accepts two functions as parameters, the `predicate` and the `effect`. This reaction runs and observes the first function (the predicate) and, when this one is met, it runs the effect function :
```js
when(
  () => this.isEnabled, // predicate
  () => this.exit() // effect
);
```
The function that returns `isEnabled` must be a function that reacts. That is, `isEnabled` must be marked with `@computed` so that the value is automatically computed or, better yet, marked with an `@observable` decorator.

The next reaction function is the `autorun` function. This function takes in one function and keeps running it until it is manually disposed. Here you can see how you can use an `autorun` function:
```js
@observable age = 10
const dispose = autorun(() => {
  console.log("My age is: ", age.get())
})
dispose()
```
With this in place, anytime the variable age changes, the anonymous function passed to autorun logs it out. This function is disposed once you call dispose.

The next one, the `reaction` function, mandatorily accepts two functions: the data function and side effect function. This function is similar to the autorun function but gives you more control on which observables to track. Here, the data function is tracked and returns data to be used inside effect function. Whereas an autorun function reacts to everything used in its function, the reaction function reacts to observables you specify :
```js
const todos = observable([
  {
    title: "Read Auth0 Blog",
    done: false,
  },
  {
    title: "Write MobX article",
    done: true
  }
]);

const reactionSample = reaction(
  () => todos.map(todo => todo.title),
  titles => console.log("Reaction: ", titles.join(", "))
);
```
**In this case, the reaction function reacts to changes in the length and title of the list.**

Another reaction function available for React developers is the `observer` function. This one is not provided by the main MobX package but, instead, provided by the `mobx-react` library. To use the observer function, you can simply add the `@observer` decorator in front of it like so:
```js
@observer class ClassName extends React.Component {
  // ...
}
```
**With this reaction function, if an object tagged with the @observable decorator is used in the render method of the component and that property changes, the component is automatically re-rendered. The observer function uses autorun internally.**


## Actions on MobX

Actions are anything that modifies the state. You can mark your actions using the `@action` decorator. As such, you are supposed to use the `@action` on any function that modifies observables or has side effects. A simple example is this:
```js
@observable variable = 0;

@action setVariable(newVariable){
  this.variable = newVariable;
}
```
This function is updating the value of an `observable`, and so it is marked with `@action`.


## Debugging with trance

[Trace](https://github.com/mobxjs/mobx/blob/gh-pages/docs/best/trace.md) is a small utility that helps to find out why your computed values, reactions or components are re-evaluating.

## What does Mobx React to ?

[Need to know from Official Documentation](https://mobx.js.org/best/react.html)
