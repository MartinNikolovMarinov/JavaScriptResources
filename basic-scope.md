[Back](./README.md)

# A very basic example

```js
1: let a = 3
2: function addTwo(x) {
3:   let ret = x + 2
4:   return ret
5: }
6: let b = addTwo(a)
7: console.log(b)
```

In order to understand how the JavaScript engine really works, let's break this down in great detail.

1. On line 1 we declare a new variable `a` in the global execution context and assign it the number 3.
2. Lines 2 through 5 are really together. What happens here? We declare a new variable named `addTwo` in the global execution context. And what do we assign to it? A function definition. Whatever is between the two brackets `{ ... }` is assigned to `addTwo`. The code inside the function is not evaluated, not executed, just stored into a variable for future use.
3. So now we're at line 6. It looks simple, but there is much to unpack here. First we declare a new variable in the global execution context and label it `b`. As soon as a variable is declared it has the value of `undefined`.
4. Next, still on line 6, we see an assignment operator. We are getting ready to assign a new value to the variable `b`. Next we see a function being called. Flash forward, every function returns something (either a value, an object or undefined). Whatever is returned from the function will be assigned to variable `b`.
5. But first we need to call the function labeled `addTwo`. JavaScript will go and look in its global execution context memory for a variable named `addTwo`. It found one, it was defined in step 2 (or lines 2-5). Note that the variable a is passed as an argument to the function. JavaScript searches for a variable `a` in its global execution context memory, finds it, finds that its value is 3 and passes the number 3 as an argument to the function. Ready to execute the function.
6. Now the execution context will switch. A new local execution context is created, let's name it the 'addTwo execution context'. The execution context is pushed onto the call stack.
7. You may be tempted to say, 'A new variable ret is declared in the local execution context'. That is not the answer. The correct answer is, we need to look at the parameters of the function first. A new variable `x` is declared in the local execution context. And since the value 3 was passed as an argument, the variable `x` is assigned the number 3.
8. The next step is: A new variable `ret` is declared in the local execution context. Its value is set to `undefined`. (line 3)
9. Still line 3, an addition needs to be performed. First we need the value of `x`. JavaScript will look for a variable `x`. It will look in the local execution context first. And it found one, the value is 3. And the second operand is the number 2. The result of the addition (5) is assigned to the variable `ret`.
10. Line 4. We return the content of the variable `ret`. Another lookup in the local execution context. `ret` contains the value 5. The function returns the number 5. And the function ends.
11. Lines 4–5. The function ends. The local execution context is destroyed. The variables `x` and `ret` are wiped out. They no longer exist. The context is popped of the call stack and the `return` value is returned to the calling context. In this case the calling context is the global execution context, because the function `addTwo` was called from the global execution context.
12. Now we pick up where we left off in step 4. The returned value (number 5) gets assigned to the variable `b`. We are still at line 6 of the little program.
13. In line 7 the content of variable `b` gets printed in the console.