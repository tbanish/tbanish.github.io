---
layout: post
title:      "The Scoop on Scope in JavaScript: A Brief Overview"
date:       2021-04-30 22:37:16 +0000
permalink:  the_scoop_on_scope_in_javascript_a_brief_overview
---


## What is Scope?
As a developer writing code in JavaScript it is important to understand where functions and variables are accessible in your program.  The context in which variables and functions are accessible is referred to as it’s scope.  Before we can understand how scope works in JavaScript we first need to understand the fundamentals of how the JavaScript engine works.  Once we establish the phases of a running JavaScript program we can then dive into the different levels of scope that can be found in the program, as well as how different variables “behave” based on their scope in the program.

## The JS Engine: Compilation and Execution Phase
When we run our JavaScript code the JavaScript engine is the program that actually runs the code for us. The engine does this by making two passes over our code.  The first pass is called the Compilation Phase.  The second is called the Execution Phase.

### Compilation Phase
During the compilation phase the JavaScript engine first allocates memory to all variable and function declarations.  It's important to note that variables are not yet assigned a value at this point and functions are not yet executed or invoked.  It is only a reference to the variable and the function that is stored in memory. 

### Execution Phase
The execution of the code begins when the Javascript engine makes its second pass.  This is when the engine assigns values to variables (through a process known as identifier resolution) and invokes functions that have already been allocated memory.

Now that we have a brief overview of how the JavaScript engine works we can take a look at how scope plays role in how our code is evaluated.

## Types of Scope in JavaScript
When talking about scope you'll often hear the term "scope chain" used.  The scope chain is what establishes the scope for a given function.  Every function has its own scope where it has access to the variables declared within it.  It also has access to the variables declared in it's outer-scope. This is how the scope chain is established. It's important to note that the scope chain only moves in one direction - the inner-scope to the outer-scope.  What this means is that variables declared from within one scope are accessible from an inner-scope, but not from an outer-scope.  To understand this further we'll go through the three main types of scope: Global, Function and Block.

### Global Scope
The Global scope is the highest level scope in a JavaScript file.  The variables and functions that are declared at this top level - the global execution context - are accessible everywhere in the code.  This would be like declaring a variable at the top of your JavaScript file outside of any function.  That variable would be accessible from every function that you build throughout the file.

### Function Scope
When you build and declare a function, that function is given its own scope.  This is known as function scope.  At this point we are no longer in the global execution context and, rather, are in a new execution context.  Variables and functions that are declared within the function are accessible from within that function, but are not accessible from the global scope.  This is an important point.  Remember, the scope chain that is established only moves in one direction - inner-scope to outer-scope. 

Every nested function also has its own scope. A nested function would have access to its own variables, as well as to the scope of its parent scope.  This is known as lexical scope, which states that a child function is bound to the scope of its parent.

### Block Scope
A block statement is a way to group statements together and is denoted with open and closed curly brackets. It's a way of using multiple statements where JavaScript only expects one. You'll see this a lot when using conditional statements or loops.

Block statements have their own scope, just like functions. However, it's important to note that this is not consistent with all variables.  Variables declared with `let` or `const` will be block scoped, meaning that if you declare a variable `const greeting = 'hello'` globally and then declare another variable `const greeting = 'goodbye'` within a block, this would be considered legal because the block has its own individual scope. The same would be true using `let`.  However, the same would not be true if you were using the `var` declaration, which brings us to the next topic in our quest to understand scope.

## Hoisting: Var, Let and Const
In JavaScript, function and variable declarations are sometimes said to be raised, or 'hoisted' to the top of the scope chain.  This is because of how the JavaScript engine executes the code.  Remember, there are two phases that occur when running your code: the compilation phase and the execution phase. The compilation phase is when all variable and function declarations are allocated memory. It's only after this phase has completed that the execution of the code - the assigning of variables and invoking of functions - actually occurs. Because the compilation occurs first and your declarations are being evaluated before the rest of the code is executed, it almost feels like the declarations are raised to the top of the scope.

Hoisting can cause problems when it is not understood, especially when using the `var` declaration.  When using `let` and `const`, declarations are technically hoisted, but the JavaScript engine does not allow them to be referenced before they have been initialized. This promotes the best practice of declaring variables at the top of the scope.  However, if you declare a variable with `var`, JavaScript will allow you to reference it before it has been initialized, which will produce a "silent" error - `undefined`. This can prove to be difficult to debug so it's best to try and declare all of your variables at the top of the scope and to avoid using `var` if you can.

## Conclusion

Scope is an important concept to understand when writing code with JavaScript. Having a good understanding of how the JavaScript engine runs your code, how the scope chain is established and how function and variable declarations are hoisted can help to save you a lot of time when it comes to debugging problems as you are developing your application. This has only been a brief discussion of an otherwise deep topic, but hopefully this overview has shed some light on how scope works in JavaScript.
