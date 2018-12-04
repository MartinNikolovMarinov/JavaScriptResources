[Back](./README.md)

# Lexical Scope

The idea here is that we have variables in the local execution context and variables in the global execution context. One intricacy of JavaScript is how it looks for variables. If it can't find a variable in its local execution context, it will look for it in its calling context. And if not found there, in its calling context. Repeatedly, until it is looking in the global execution context. (And if it does not find it there, it's undefined). Follow along with the example above, it will clarify it.

```js
1: let val1 = 2
2: function multiplyThis(n) {
3:   let ret = n * val1
4:   return ret
5: }
6: let multiplied = multiplyThis(6)
7: console.log('example of scope:', multiplied)
```

1. Declare a new variable `val1` in the global execution context and assign it the number 2.
2. Lines 2–5. Declare a new variable `multiplyThis` and assign it a function definition.
3. Line 6. Declare a new variable `multiplied` in the global execution context.
4. Retrieve the variable `multiplyThis` from the global execution context memory and execute it as a function. Pass the number 6 as argument.
5. New function call => new execution context. Create a new local execution context.
6. In the local execution context, declare a variable `n` and assign it the number
7. Line 3. In the local execution context, declare a variable `ret`.
8. Line 3 (continued). Perform an multiplication with two operands; the content of the variables `n` and `val1`. Look up the variable `n` in the local execution context. Its content is the number 6. Look up the variable `val1` in the local execution context. The local execution context does not have a variable labeled `val1`. Let's check the calling context. The calling context is the global execution context. Let's look for `val1` in the global execution context. Oh yes, it's there. It was defined in step 1. The value is the number 2.
9. Line 3 (continued). Multiply the two operands and assign it to the `ret` variable. 6 * 2 = 12. `ret` is now 12.
10. Return the `ret` variable. The local execution context is destroyed, along with its variables `ret` and `n`. The variable `val1` is not destroyed, as it was part of the global execution context.
11. Back to line 6. In the calling context, the number 12 is assigned to the `multiplied` variable.
12. Finally on line 7, we show the value of the `multiplied` variable in the console.