---
layout: post
title:      "BookRemark: A React App with a Rails API"
date:       2021-05-03 16:02:52 +0000
permalink:  bookremark_a_react_app_with_a_rails_api
---


I recently wrapped up my first React application with a Rails API and I would like to walk through the structure of the application as well as the process of how I developed it. Building this application took a lot of time and effort to put together. It is, admittedly, a simple application. However, it has been one of the more complex endeavors that I have taken on since beginning to learn the fundamentals of web development. The application was built with a Ruby on Rails API and a React frontend, complete with CSS styles and Redux as the state management library.

As a point of reference you can check out the github repositories for both the [frontend](https://github.com/tbanish/react-reading-goal-frontend) and [backend](https://github.com/tbanish/react-reading-goal-backend) of this application.

## App Idea

The application I built is called BookRemark. It allows a user to set a goal for how many books they want to read and to keep track of the books they have read on their `BookShelf`, as well as the ones they want to read on their `BookList`. On top of that the application allows a user to set up timed reading sessions where they can take notes. At this stage in development, a user is able to:

- add books to their reading list
- edit the book title, author, description and page count
- finish a book and add it to their book shelf
- delete books
- create many reading sessions for each book
- take and add many notes during each timed session
- edit the reading session title
- edit their notes
- delete a reading session along with all of it's notes
- delete individual notes from the reading session
- set a yearly book goal
- view their goal progress as they finish more and more books
- edit or delete their goal

I have more ideas about how I would like to develop this application, like adding user sign up, log in, and log out functionality, as well as allowing a user to share their reading lists and book recommendations and review their books which will be posted publicly for others to view and comment on. But for now, the user story stated above is what the application offers.

## Backend: Rails API

The backend of the application was built with Ruby on Rails. I chose to use postgresql for my database because I have the intention of deploying the application on Heroku. 

To set up the API without the Rails view system I used the command `rails new app_name --api`.

### Models

The application has 4 models: Book, ReadingSession, Note and Goal. Each model also has its own controller as well as its own serializer.

#### Book

The Book model has four attributes: `title`, `author`, `description`, `page_count` and `finished`.  The `title` and `author` attributes are required to create a new book, but the `description` and the `page_count` are optional details the user can add if they wish. The `finished` attribute is of the type boolean. When a book is created this attribute is set to false by default. When a user finishes a book, the attribute is changed to true.

As far as associations go, a book `has_many :reading_sessions` and `has_many :notes, through: :reading_sessions`. Also, for this model I utilized a scope method `scope:finished, -> {where(finished:true)}`, which allows me to query the database for every book that has been marked `finished: true`.

#### ReadingSession

The ReadingSession model has four attributes, as well: `title`, `duration`, `date`, and `book_id`. A ReadingSession `belongs_to` a `:book`, so it has a foreign key. It also `has_many :notes`.

When a user creates a new reading session I wanted them to also be able to add multiple notes at the same time, so the ReadingSession model also `accepts_nested_attributes_for :notes`.

#### Note

The Note model only has three attributes: `title`, `content` and `reading_session_id`. It only has one association: `belongs_to :reading_session`.

#### Goal

The Goal model is also pretty simple. It only has two attributes: `total` and `remaining`. It has no associations. `total` is the total book goal that the user sets them self. `remaining` is determined by how many books the user has finished (using the scope method `:finished`) subtracted from the `total`.

### Controllers

Each controller is equipped with an `index`, `create`, `update` and `destroy` action as well as `strong_params`. However, the `NotesController` does not need a `create` action because notes are only created when a reading session is submitted, so note creation occurs in the `ReadingSessionController` `create` action.

### Serializers

I used the `fast_jsonapi` gem to generate serializers for each model.

#### Books

The books serializer has the most attributes: `title`, `author`, `description`, `page_count`, `finished`, `reading_sessions`, and `notes`. On top of all the book attributes, I wanted to include all of the associated reading sessions and notes because the initial fetch request occurs at this endpoint and I wanted to be able to grab all of the resources at once.

#### ReadingSessions

The reading sessions serializer has five attributes: `title`, `duration`, `date`, `book` and `notes`. `book`and `notes` are included because they are associated with each reading session.

#### Notes

The note serializer has three attributes: `title`, `content` and `reading_session_id`.

#### Goals

Lastly, the goal serializer has two attributes: `total` and `remaining`.

## Frontend: React and Redux

The frontend of the application was built with the React framework. React is a JavaScript library for building user interfaces. In this application structure, it is the Rails API that handles the "M" and "C" of the Model View Controller paradigm and it is React that handles the "V". The Redux library is used to manage the state of the application on the client side. Redux is also a JavaScript library that allows you to manage the state of an entire application in a single immutable store.

### Initial Steps

#### create-react-app and Dependencies

To get started I used the `create-react-app` command to set up my React app. Once all the initial files were installed I installed all of the additional dependencies that I knew I would be using.

	npm install react-redux --save
	npm install redux --save
	npm install redux-thunk --save
	npm install react-router-dom --save

Above you can see two dependencies that have not been mentioned yet: `redux-thunk` and `react-router-dom`.

Redux Thunk is middleware that allows developers to write action creators that return functions. It is also what allows you to make asynchronous requests to an API from within an action by delaying the dispatch of an action to occur after the promise has been fulfilled.

React Router is a React library that is used for client side routing.

#### Setting Up the Store

Once all of the necessary dependencies were installed the next step was to set up the Redux store. To do this you need to import several tools that come with the installed dependencies listed above.

##### CreateStore

`createStore` comes from `redux`. It creates a store that is responsible for holding the global state of your application. There is only ever one store per application so you only need to do this once.

##### ApplyMiddleWare

`applyMiddleware` also comes from `redux` and allows you to combine multiple middleware like `compose` and `thunk` into your application. This way you can easily extend Redux with custom functionality. It isn't necessary to use `applyMiddleware` with Redux, but it is a very useful tool if you choose to use it.

##### Compose

`compose` is used to apply several store enhancers. I used it so that I could use `REDUX_DEVTOOLS_EXTENSION_COMPOSE` in my browser to inspect the Redux store as I was developing my application.

##### Thunk

`thunk`, as mentioned previously, is middleware that allows you to write action creators that return a function and is necessary if you plan on making asynchronous fetch calls to an API.

##### Provider

`Provider` is the component that makes the Redux store accessible throughout the entire application. By wrapping the `<App />` component in `Provider`, the Redux store becomes available to any nested components that may need to access it.

This is what the `index.js` file looks like after the store is set up:

	import React from 'react';
	import ReactDOM from 'react-dom';
	import App from './App';
	import { createStore, applyMiddleware, compose } from 'redux';
	import thunk from 'redux-thunk';
	import { Provider } from 'react-redux';
	import { rootReducer } from './reducers/rootReducer'
	import {BrowserRouter as Router} from 'react-router-dom'
	import './css/index.css'

	let composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

	let store = createStore(rootReducer, composeEnhancers(applyMiddleware(thunk)))

	ReactDOM.render(
	    <Router>
	      <Provider store={store}>
	        <App />
	      </Provider>
	    </Router>,
	  document.getElementById('root')
	);


### Reducers and Actions

#### Reducers and rootReducer

Each model has a reducer that handles its state in the Redux store. As you can see above, `createStore` takes in an argument of the `rootReducer`. This is the main reducer that is located in the reducer directory. `rootReducer` imports `comineReducers` from `redux` to combine all of the reducers into one location. The rest of the reducer files are located in the same directory.

	import { combineReducers } from 'redux'
	import { books } from './books.js'
	import { readingSessions } from './readingSessions.js'
	import { notes } from './notes.js'
	import { date } from './date.js'
	import { goals } from './goals.js'

	export const rootReducer = combineReducers({
	  books,
	  readingSessions,
	  notes,
	  date,
	  goals
	})

#### Actions

Each reducer also has a corresponding action file located in the actions directory. The action files contain action creators that are responsible for dispatching actions to the reducer. The actions are what determine the kind of change that needs to be made to the state. Here is an example of what the books reducer and action files look like:

	// ./reducers/books.js
	export const books = (state = [], action) => {
	  switch (action.type) {
	    case 'LOAD_BOOKS':
	      return action.payload
	    case 'ADD_BOOK':
	      return state.concat(action.payload)
	    case 'UPDATE_BOOK':
	      return state.map(book => {
	        return book.id === action.payload.id ? action.payload : book
	      })
	    case 'DELETE_BOOK':
	      return state.filter(book => book.id !== action.payload)
	    case 'FINISH_BOOK':
	    return state.map(book => {
	      return book.id === action.payload.id ? action.payload : book
	    })
	    default:
	      return state
	  }
	}

	// ./actions/books.js
	const booksEndpoint = 'http://localhost:3001/books'

	export const loadBooks = () => {
	  return dispatch => {
	    fetch(booksEndpoint)
	      .then(resp => resp.json())
	      .then(books => {
	        dispatch({type: 'LOAD_BOOKS', payload: books.data})
	        dispatch({type: 'LOAD_READING_SESSIONS', payload: books.data})
	        dispatch({type: 'LOAD_NOTES', payload: books.data})
	      })
	  }
	}

	export const addBook = (book) => {
	  const body = {
	    title: book.title,
	    author: book.author,
	    description: book.description,
	    page_count: book.pageCount
	  }
	  return dispatch => {
	    fetch(booksEndpoint, {
	      method: 'POST',
	      headers: {
	        "Content-Type": "application/json"
	      },
	      body: JSON.stringify(body)
	    })
	      .then(resp => resp.json())
	      .then(newBook => {
	        dispatch({type: 'ADD_BOOK', payload: newBook.data})
	      })
	  }
	}
	// ... more actions follow

### App, Home, NavBar and NoMatch Components

#### App

The `<App />` component is responsible for connecting to the store and loading all of the resources on the initial render. This is accomplished by connecting to the store using `connect` from `react-redux`, mapping the `loadBooks()` and `loadGoals()` actions to props and then calling them inside of the React life cycle method `componentDidMount()`. `componentDidMount()` is a method that is invoked immediately after a component is mounted so it is a great place to initially send fetch requests to an API.

#### Home

`<App />` is also responsible for rendering the main routes of the application using React Router. One of the main routes renders the `<Home />` component at the path '/'. This is the main page of the application that introduces the functionality the app has to offer. It is a purely presentational function component that renders primarily static HTML.

#### NavBar

The `<App />` component also renders the `<NavBar />` component. `<NavBar />` is also a function component that is responsible for providing the main navigation links on every page of the app.

#### NoMatch

Next up is the `<NoMatch />` component. This component will only render when no other route matches the provided URL pattern. It is a function component that presents the user with a `404` error message if they happen upon a path they shouldn't be on.

### Book Components

`<App />` also sets up the routes for the `<BookContainer />` the `<ReadingSessionContainer />` and the `<GoalContainer />`.

#### BookContainer

The `<BookContainer />` is a class component that is responsible for rendering all of the routes that pertain to books and notes. This includes the `<BookList />`, `<BookShelf />`, `<Book>`, `<BookNewForm />`, `<BookEditForm />`, `<Note />` and `<NoteEditForm />`.

The `<BookContainer />` also connects to the store to map the current state of books, readingSessions, notes and goals to props, as well as import and map actions to props in order to pass them down to the appropriate child components.

#### BookList and BookShelf

`<BookList />` is a function component who's main responsibility is to render links to all of the books that have *not* been finished. To do this the `<BookContainer />` passes `state.books` down as props, which then has the `filter` method applied to it to filter out all of the books who's `finished` attribute does not equal true. Then, using `Link` provided by `react-router-dom`, links for every book are rendered in the return statement of the component with a path to `/books/${book.id}`.

The `<BookShelf />` component works in a similar matter except that it will only render books that *have* been finished. This way a user has two separate places to view the books they have and have not finished.

#### Book

The contents of each book is rendered from the `<Book />` component. Each of these components also renders the `<NoteList />` and `<ReadingSessionList />` components, which in turn render links to the associated notes and reading sessions. This way a user can access all of the note and reading sessions from the individual book pages.

When a user has completed reading a book it is from this page that they mark the book as finished. When a user clicks the finished button a function is triggered that calls the `finishBook()` action that is passed down as a prop. This action is responsible for sending a PATCH request to the server to update the `finished` attribute and set it to `true`. Once this has been completed two dispatches are then called. The first one dispatches an action `FINISH_BOOK` to the book reducer to update the book's state in the store. If a user has a goal when they finish a book, the second dispatch fires `updateGoal()`, which is responsible for updating the `remaining` attribute of the goal on the server side and in the Redux store. If a user does not have a goal set up at the time they finish a book, the second dispatch does not fire.

#### BookNewForm and BookEditForm

The `<BookNewForm />` and `<BookEditForm />` components are both class components that operate in a similar fashion. Each component has a controlled form with inputs corresponding to the book attributes that a user can submit. They both have their own local state that reflects what the user has typed into the input fields via an `onChange` event handler. The main difference between both of these components is that the edit component has an initial state of the attributes of the book that is passed down through props by the `<BookContainer />`. This way a user can see the current attributes of the book that they wish to edit.

Both of these components are passed down action props from the `<BookContainer />`. The `<BookNewForm />` is passed the `addBook()` action as a prop which is called in the `handleSubmit()` function that is triggered when a user submits the form. This action sends a POST fetch request to the Rails API to create a new book. Once the book has been created and the promise is fulfilled, the new book data is dispatched to the reducer to add the new book to the state.

The same thing occurs in the `<BookEditForm />`, except it is instead passed the `updateBook()` action, which sends a PATCH request to the server to update the book.

### ReadingSession Components

#### ReadingSessionContainer

The `<ReadingSessionContainer />` is a class component that is responsible for rendering routes that are associated with the reading sessions. This includes the `<ReadingSession />`, `<ReadingSessionNewForm />` and the `<ReadingSessionEditForm />`.

#### ReadingSessionList

The `<ReadingSessionList />` is the only reading session component that is not rendered from the `<ReadingSessionContainer />`. Because I decided that I wanted the user to be able to access links to each reading session associated with a book, it made more sense to me to have the `<Book />` component render this component.

#### ReadingSession

The `<ReadingSession />` component is a functional component that renders all of the reading session content, as well as each note associated with the reading session. From this page a user can cycle through each reading session and scroll through all of the notes for that reading session. A user can also edit/delete the reading session content or delete an individual note from the reading session. This page also has a link to the new reading session form.

#### ReadingSessionNewForm

The `<ReadingSessionNewForm />` is probably the most complex component in this application. It allows a user to time their reading session as well as take multiple notes during the session. 

All of the notes that the user submits are stored as objects in an array inside of the component's local state. When the session is done and the user submits the form, the collection of notes along with the reading session contents are passed to the `addReadingSession()` action. This action sends a POST request to the Rails server. When the `create` action in the `ReadingSessionsController` catches the request it creates a new reading session as well as a new note for each note in the collection that comes through params. Afterwards the action sends two dispatches. The first one is responsible for telling the reducer to add a new reading session to the store. The second is responsible for telling the reducer to add all of the new notes to the state.

#### Timer

To time the session I had to create a separate `<Timer />` component to be rendered from within the form component. The timer utilizes the `setInterval()` and `clearInterval()` JavaScript functions to work. When a user clicks the start button an `onClick` function is triggered that calls the `setInterval()` function with a function called `increaseTime()` passed to it as an argument. The `increaseTime()` function is called every 1000 milliseconds and uses `setState` to update the state of the timer.

When the user clicks the stop button the `clearInterval()` function is called to stop the timer. Another function called `setDuration` that is passed down from the `<ReadingSessionNewForm />` is also called immediately afterwards. This function takes the timer state as an argument. The state of the timer is then used to update `state.duration` of the `<ReadingSessionNewForm />`.

#### ReadingSessionEditForm

The `<ReadingSessionEditForm />` is a simpler component. It allows a user to update the title of the reading session after it has been created. It works similarly to the `<BookEditForm />`. It utilizes the `updateReadingSession()` action to update the reading session in the backend as well as in the Redux store.

### Note Components

#### NoteList

The `<NoteList />` component is rendered from the `<Book />` component. It is responsible for rendering all of the links to each note associated with the book. When a user clicks on one of these links they are able to view the contents of the `<Note />` component.

#### Note

The `<Note />` component renders the title and content of the note. It also renders a delete button and an edit link. The delete button, when clicked, triggers a function that calls the `deleteNote()` action passed down as a prop, which in turn sends a DELETE request to the server to destroy the note and then finally removes the note from the global Redux store.

#### NoteEditForm

The edit link takes the user to the `<NoteEditForm />` component. This is a simple component that works just like the other edit form components. From this page the user is able to update the title and contents of the note in both the database and the Redux store.

### Goal Components

#### Goal

The `<Goal />` component renders the users book goal. This component displays to the user how many books they have read with a link to every book on their bookshelf, as well as how many more books they need to read to reach their goal. A user also has the option to delete or edit their goal at anytime.

#### GoalForm

Lastly, the `<GoalForm />` component functions as the new and edit form for the goal. This is a simple form with one select tag that contains a range of numbers that a user can choose from to set their goal.

## Conclusion

The process of building this application has been very rewarding. It took a lot of time and patience to complete this task, but I am very pleased with where the application currently stands. At the time of writing this article there is still a good deal of refactoring that must be done to clean it up before it can be deployed, but all in all I am happy with the work that I have put into this project to see it through to fruition. My hope is that by reading through this article it will offer some insight to beginner programmers as to what it is like to build an application with a Rails API and a React/Redux frontend.
