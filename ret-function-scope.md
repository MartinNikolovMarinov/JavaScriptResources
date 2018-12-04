[Back](./README.md)

# A function that returns a function

A function can return anything. Let's look at an example of a function that returns a function, as this is essential to understand closures.
```js
 1: let val = 7
 2: function createAdder() {
 3:   function addNumbers(a, b) {
 4:     let ret = a + b
 5:     return ret
 6:   }
 7:   return addNumbers
 8: }
 9: let adder = createAdder()
10: let sum = adder(val, 8)
11: console.log('example of function returning a function: ', sum)
```

1. Line 1. We declare a variable `val` in the global execution context and assign the number 7 to that variable.
2. Lines 2-8. We declare a variable named `createAdder` in the global execution context and we assign a function definition to it. Lines 3 to 7 describe said function definition. As before, at this point, we are not jumping into that function. We just store the function definition into that variable (`createAdder`).
3. Line 9. We declare a new variable, named `adder`, in the global execution context. Temporarily, `undefined` is assigned to `adder`.
4. Still line 9. We see the brackets (); we need to execute or call a function. Let's query the global execution context's memory and look for a variable named `createAdder`. It was created in step 2. Ok, let's call it.
5. Calling a function. Now we're at line 2. A new local execution context is created. We can create local variables in the new execution context. The engine adds the new context to the call stack. The function has no arguments, let's jump right into the body of it.
6. **Still lines 3-6. We have a new function declaration. We create a variable `addNumbers` in the local execution context. `addNumbers` exists only in the local execution context. We store a function definition in the local variable named `addNumbers`.**
7. Now we're at line 7. We return the content of the variable `addNumbers`. The engine looks for a variable named `addNumbers` and finds it. It's a function definition. Fine, a function can return anything, including a function definition. So we return the definition of `addNumbers`. Anything between the brackets on lines 4 and 5 makes up the function definition. We also remove the local execution context from the call stack.
8. Upon return, the local execution context is destroyed. The `addNumbers` variable is no more. The function definition still exists though, it is returned from the function and it is assigned to the variable `adder`; that is the variable we created in step 3.
9. Now we're at line 10. We define a new variable `sum` in the global execution context. Temporary assignment is `undefined`.
10. **We need to execute a function next. Which function? The function that is defined in the variable named `adder`. We look it up in the global execution context, and sure enough we find it. It's a function that takes two parameters.**
11. Let's retrieve the two parameters, so we can call the function and pass the correct arguments. The first one is the variable `val`, which we defined in step 1, it represents the number 7, and the second one is the number 8.
12. Now we have to execute that function. The function definition is outlined lines 3-5. A new local execution context is created. Within the local context two new variables are created: `a` and `b`. They are respectively assigned the values 7 and 8, as those were the arguments we passed to the function in the previous step.
13. Line 4. A new variable is declared, named `ret`. It is declared in the local execution context.
14. Line 4. An addition is performed, where we add the content of variable `a` and the content of variable `b`. The result of the addition (15) is assigned to the `ret` variable.
15. The `ret` variable is returned from that function. The local execution context is destroyed, it is removed from the call stack, the variables `a`, `b` and `ret` no longer exist.
16. The returned value is assigned to the `sum` variable we defined in step 9.
17. We print out the value of `sum` to the console.

# Conclusion

First, a function definition can be stored in a variable, the function definition is invisible to the program until it gets called. Second, every time a function gets called, a local execution context is (temporarily) created. That execution context vanishes when the function is done. A function is done when it encounters `return` or the closing bracket `}`.