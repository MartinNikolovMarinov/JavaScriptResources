[Back](./README.md)

# Clojure

## Incorrect assumptions following from the previous examples.

```js
 1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

**The following step-by-step is incorrect !**

1. Lines 1–8. We create a new variable `createCounter` in the global execution context and it get's assigned function definition.
2. Line 9. We declare a new variable named `increment` in the global execution context..
3. Line 9 again. We need call the `createCounter` function and assign its returned value to the `increment` variable.
4. Lines 1–8 . Calling the function. Creating new local execution context.
5. Line 2. Within the local execution context, declare a new variable named `counter`. Number 0 is assigned to `counter`.
6. Line 3–6. Declaring new variable named `myFunction`. The variable is declared in the local execution context. The content of the variable is yet another function definition. As defined in lines 4 and 5.
7. Line 7. Returning the content of the `myFunction` variable. Local execution context is deleted. `myFunction` and `counter` no longer exist. Control is returned to the calling context.
8. Line 9. In the calling context, the global execution context, the value returned by `createCounter` is assigned to `increment`. The variable `increment` now contains a function definition. The function definition that was returned by `createCounter`. It is no longer labeled `myFunction`, but it is the same definition. Within the global context, it is labeled `increment`.
9. Line 10. Declare a new variable (`c1`).
10. Line 10 (continued). Look up the variable `increment`, it's a function, call it. It contains the function definition returned from earlier, as defined in lines 4–5.
11. Create a new execution context. There are no parameters. Start execution the function.
12. Line 4. `counter = counter + 1`. Look up the value `counter` in the local execution context. We just created that context and never declare any local variables. Let's look in the global execution context. No variable labeled `counter` here. Javascript will evaluate this as `counter = undefined + 1`, declare a new local variable labeled `counter` and assign it the number 1, as undefined is sort of 0.
13. Line 5. We return the content of `counter`, or the number 1. We destroy the local execution context, and the `counter` variable.
14. Back to line 10. The returned value (1) gets assigned to `c1`.
15. Line 11. We repeat steps 10–14, `c2` gets assigned 1 also.
16. Line 12. We repeat steps 10–14, `c3` gets assigned 1 also.
17. Line 13. We log the content of variables `c1`, `c2` and `c3`.

You'll notice that it is not logging 1, 1, and 1 as you may expect from the explanation above. Instead it is logging 1, 2 and 3.

**So what gives?**

## How It Actually Works

Somehow, the `increment` function remembers that `counter` value. How is that working?

Is `counter` part of the global execution context? Try console.log(`counter`) and you'll get `undefined`. So that's not it.

Maybe, when you call `increment`, somehow it goes back to the the function where it was created (`createCounter`)? How would that even work? The variable `increment` contains the function definition, not where it came from. So that's not it.

**Here is how it works. Whenever you declare a new function and assign it to a variable, you store the function definition, as well as a closure. The closure contains all the variables that are in scope at the time of creation of the function. It is analogous to a backpack. A function definition comes with a little backpack. And in its pack it stores all the variables that were in scope at the time that the function definition was created.**

Does every function have a closure, even functions created in the global scope? The answer is yes. Functions created in the global scope create a closure. But since these functions were created in the global scope, they have access to all the variables in the global scope. And the closure concept is not really relevant.

Let's go through the example again :

```js
 1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

* 1-5 Same as above
* 6 . Line 3–6. Declaring new variable named `myFunction`. The variable is declared in the local execution context. The content of the variable is yet another function definition. As defined in lines 4 and 5. Now we also create a closure and include it as part of the function definition. The closure contains the variables that are in scope, in this case the variable `counter` (with the value of 0).
* 7 . Line 7. Returning the content of the `myFunction` variable. Local execution context is deleted. `myFunction` and `counter` no longer exist. Control is returned to the calling context. So we are returning the function definition and its closure, the backpack with the variables that were in scope when it was created.
* 8 . Line 9. In the calling context, the global execution context, the value returned by `createCounter` is assigned to `increment`. The variable `increment` now contains a function definition (and closure). The function definition that was returned by `createCounter`. It is no longer labeled `myFunction`, but it is the same definition. Within the global context, it is called `increment`.
* 9 . Line 10. Declare a new variable (`c1`).
* 10 . Line 10 (continued). Look up the variable `increment`, it's a function, call it. It contains the function definition returned from earlier, as defined in lines 4–5. (and it also has a backpack with variables)
* 11 . Create a new execution context. There are no parameters. Start execution the function.
* 12 . Line 4. `counter = counter + 1`. We need to look for the variable `counter`. Before we look in the local or global execution context, let's look in our backpack. Let's check the closure. Lo and behold, the closure contains a variable named `counter`, its value is 0. After the expression on line 4, its value is set to 1. And it is stored in the backpack again. The closure now contains the variable counter with a value of 1.
* 13 . Line 5. We return the content of `counter`, or the number 1. We destroy the local execution context.
* 14 . Back to line 10. The returned value (1) gets assigned to `c1`.
* 15 . Line 11. We repeat steps 10–14. This time, when we look at our closure, we see that the `counter` variable has a value of 1. It was set in step 12 or line 4 of the program. Its value gets incremented and stored as 2 in the closure of the increment function. And `c2` gets assigned 2.
* 16 . Line 12. We repeat steps 10–14, `c3` gets assigned 3.
* 17 . Line 13. We log the content of variables `c1`, `c2` and `c3`.