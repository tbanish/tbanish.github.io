---
layout: post
title:      "Understanding Redux"
date:       2021-05-01 18:43:12 +0000
permalink:  understanding_redux
---


When working on the frontend of an application developers need a way to manage the state of their application. Building an application with a framework like React offers its own way of managing state, like setting up local state in a component, handling changes with tools like `setState` and passing data down from parent to child components.

This is a great way of handling state when your application is small and simple.  But as your application begins to grow this pattern can become unwieldy. Another way of handling application state is by keeping the state separated from your applications components in one centralized place. That is exactly what the Redux pattern of state management offers as a solution.

## What is Redux?

Redux is a JavaScript library and, more importantly, it is a pattern designed with the purpose of managing and updating application state. The idea is that your state will live in one centralized place called the `store` and it can be accessed from anywhere in your app. 

It's also worth noting that the state is really just a plain old JavaScript object. Nothing more. This should be comforting if you have a decent amount of experience with JavaScript, but you are just coming to Redux for the first time. It's just JavaScript.  What makes Redux a powerful tool has more to do with the pattern of how the information flows.

## Redux Flow

The Redux pattern is meant to be predictable. In order to maintain predictability Redux enforces rules about how state should be managed and updated. The most important rule has to do with *immutability*. To change the application state we:
- *create an action* that holds information for how the state should be updated
- *call a reducer*, a pure function that behaves like a switch board, using the action type to determine how to update the state
- *change the state* without mutating the previous state.

Let's dive a little deeper into how this all works.

### The Store

You can think of the store as a container that holds the global state of your application. The global state is stored just as a plain old JavaScript object. Nothing too fancy about it. What's important to note is that there should only be one store per application. The idea is that any given piece of data should only exist in one place, creating a *single source of truth*. This, in turn, makes it easier to understand and inspect the state of your application, as well as easing the pains that come with debugging.

A fundamental rule that should be understood from the start is that the state should never be mutated. Destructively manipulating the state makes it difficult to debug your application because there is no record of how the state previously existed. You should only use non-destructive techniques and strategies when updating state, which we will get into in a little bit.

The store works by taking in a reducer as an argument. It has a couple of built in methods that are essential for it to function: `getState` and `dispatch`. The former is responsible for retrieving the current value of the state. The ladder takes in an action as an argument and calls the reducer function with said action as an argument.

### Create an Action

Once the store is set up you can create an action.  An action is, again, just a plain JavaScript object. The object has, at a minimum, a `type` field, which describes the action that needs to occur. The action object often has another field called `payload` that can hold more information pertaining to how the state needs to be updated. A typical action might look something like this:

    {type: 'ADD_ITEM', payload: newItem}

The action is passed as an argument to the dispatch function in the store. Dispatch then calls the reducer function with the action as an argument:

	const addItem = (newItem) => {
		return dispatch {
		  dispatch({type: 'ADD_ITEM', payload: newItem});
		}
	}

### Call a Reducer

The reducer is a function that takes in the initial state and action as arguments. The initial state is a default parameter typically set to an empty object. The body of the reducer is a switch statement that looks at the `action.type` to determine how to update the state and will return either a new state object or the existing state object. The default return value is always the current value of the state.

    let initialState = [];

    const items = (state = initialState, action) {
      switch (action.type) {
        case 'ADD_ITEM':
          return action.payload
        default:
          return state
      }
    }

An important point to note is that the reducer must be a *pure function.* A pure function will always return the same output given the same input. It also has no side effects and no asynchronous logic. In terms of the reducer function, given the same action, it should always return the same state.

### Change the State

When speaking about state, what we are really referring to is the condition of the application at any specific point in time. We've also been talking about how the state should remain immutable. In order to do this, we need to employ non-destructive strategies for updating the state. Instead of altering the state directly we can update the state by copying the existing state and then updating the copy.

There are several ways that we can update the state without altering it directly. A common way of doing this would be to use the JavaScript object spread operator `...`. We would first copy the existing state using the spread operator and then any updates that need to be made would occur on the copy. Alternatively, using non-destructive methods like `map`, `concat`, and `filter` are also useful tools that can be utilized when writing code to update the state. Finally, the updated copy of the state would be returned, replacing the previous state. Here's an example of a reducer that performs some standard CRUD-like actions on the state:

	const items = (state=[], action) => {
	  switch (action.type) {
	    case 'LOAD_ITEMS':
	      return action.payload
	    case 'ADD_ITEM':
	      return state.concat(action.payload)
	    case 'UPDATE_ITEM':
	      return state.map(item => {
	        return item.id === action.payload.id ? action.payload : item
	      })
	    case 'DELETE_ITEM':
	      return state.filter(item => item.id !== action.payload)
	    default:
	      return state
	  }
	}

## Conclusion

The Redux library and pattern is a great way to manage the state of your application. The methods and ideas outlined in this article are meant to be a brief overview of how Redux works. However, by understanding and applying the fundamentals described above you will be able to manage the state of your application in a simple, predictable and efficient way that can potentially save you a lot of time when it comes to inspecting and debugging your application state.
