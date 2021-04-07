---
layout: post
title:      "Flashcard App with a Ruby On Rails API and a JavaScript Frontend"
date:       2021-04-07 23:44:04 +0000
permalink:  flashcard_app_with_a_ruby_on_rails_api_and_a_javascript_frontend
---


I decided for my JavaScript project that I wanted to build a flash card application where a user could create decks of flash cards to study from.  The idea was that a user would be able to create a new deck and then add cards to that deck that would have a question and an answer.  The user could then select a deck and view the “question side” of the all of the cards and click a card to toggle the answer.

After considering the user story I concluded that I would only need two models:  Deck and Card.  A deck would have many cards and a card would belong to a deck.  The deck would also only have a subject attribute with a datatype of `string`.  A card would have three attributes:  a question and answer, both of which would be of the datatype `string`, and a `deck_id`, which would serve as the foreign key that associates the card to the deck.

## Backend:  Rails API

The backend of the application was built using a Ruby on Rails API.  Creating an API Rails application is simple.  You just have to run the command `rails new (project_name) —api`.  The `—api` flag eliminates middleware used for browser applications and skips generating views, helpers and assets when generating resources.  Also, for this project I used PostgresSQL as my database management system, as opposed to SQLite.  To use PostgresSQL in your project you could just add the flag `—database=postgresql`.

I generated models and migrations using the `rails g model` generator for each model.  This generator creates the model files and migration tables for the model.  After this I ran the command `rake db:create` to create the database and `rake db:migrate` to migrate the tables to the database.  At this point I also created some seed data to test out my model associations.

The routes for each resource were namespaced under `:api` followed by `v1`.  This was set up this way in the event that I should decide to come up with more versions of the API.  I could then just create a new namespace under `v2` and nest the new API there.

Both the DecksController and the CardsController are set up with index, show, create, update and destroy actions that handle all of the CRUD actions necessary for this application.  This way a user could create new decks and cards as well as update or delete them.  Also, each controller is built with a private strong params method that permits and limits the data that is allowed to pass through to the server.

In a Rails application that renders views you will most likely render `:erb` files that are located in the views directory.  However, this is an API so the controllers render JSON (JavaScript Object Notation).  JSON is a standard file format and is used for transmitting data in web applications.  This is the way that the frontend application communicates with the backend.

To structure the JSON I generated serializers for each model using the Fast JSON API gem.  I structured the deck JSON with their associated cards so that I could initially fetch all of the data from a single endpoint, making it easier to rebuild and associate the objects on the frontend.

## Frontend: JavaScript, HTML and CSS

The frontend of the application utilizes JavaScript, HTML and CSS.  Through the use of asynchronous programming in JavaScript, the frontend of the application is responsible for fetching resources from the API endpoint, constructing those objects into JavaScript Classes, rendering those objects as HTML and CSS, as well as listening for events and manipulating the DOM.

### Communication to the Server

Asynchronous programming is a term that refers to the events or functions that are run and occur outside of the main thread and flow of the program.  JavaScript utilizes this technique to send and retrieve data from the server by bypassing typical HTTP request behavior without disrupting the existing page display.  This process is known as AJAX (Asynchronous JavaScript and XML).

Sending and retrieving data from the API endpoint is done using the `fetch()` method.  To retrieve the JSON file you need to pass the fetch method an argument of the path or URL of the API endpoint.  This will return a promise (the eventual completion of the operation) containing the HTTP response which is then parsed as a JavaScript object using the `json()` method.  At this point in the application this is where new deck and card objects are created.  Here’s an example of what this looks like:

    function loadDecks() {
      fetch(decksEndpoint)
      .then(resp => resp.json())
      .then(decks => {
        for (const deck of decks.data) {
          const subject = deck.attributes.subject
          const deckId = parseInt(deck.id)
          const newDeck = new Deck(deckId, subject)
          const newDeckCards = deck.attributes.cards
          newDeck.renderDeck()
          createCards(newDeckCards)
        }
      })
    }

Posting, patching and destroying resources is done in a similar fashion.  Since we are sending information instead of just retrieving, a second argument other than the endpoint is necessary to state the method of the request (POST, PATCH or DELETE) and to convert the JavaScript object to JSON using the `JSON.stringify()` method:

    function postCard(question, answer, deck_id) {
      let bodyData = {question, answer, deck_id}
      fetch(cardsEndpoint, {
        method: "POST",
        headers: {"Content-Type": "application/json"},
        body: JSON.stringify(bodyData)
      })
      .then(resp => resp.json())
      .then(card => {
    …

This is what will be sent back to the server and passed through params to create, update or destroy objects.  In the event of posting or patching, the response will be the new object that was either created or updated.

### JavaScript Classes

Once the data has been retrieved from the API I wanted to rebuild the objects on the front end using JavaScript Classes.  In JavaScript you declare a class using the `class` keyword, just as you would in Ruby.  The class body is where you define the `constructor` and the methods related to the class.  The `constructor` is used to create and initialize an object.  This is also where the object attributes are set using the keyword `this`, which refers to the object it belongs to.  Here’s an example of what the constructor method in the Deck class looks like:

    class Deck {
      constructor(id, subject) {
        this.id = id
        this.subject = subject
        Deck.all.push(this)
      }
    }

    Deck.all = []

After the data from the fetch request is converted to a JavaScript object a new object is created.  For instance, a new deck is created using the code `const newDeck = new Deck(deckId, subject)`.  The `subject` and the `deckId` are extracted from the retrieved JavaScript object.  The constructor method sets the subject and id equal to `this.id` and `this.subject`.  This allows you to retrieve those attributes with dot notation (`deck.id` or `deck.subject`).

A new card is built in the same way.  After the deck object has been created a function called `createCards()` is called, taking in an object containing all of the cards associated with the deck.  This is possible because of the way the deck JSON was serialized on the backend.  

    function createCards(newDeckCards) {
      for (const card of newDeckCards) {
        const id = card.id
        const question = card.question
        const answer = card.answer
        const deckId = card.deck_id
        const newCard = new Card(id, question, answer, deckId)
      }
    }

This function iterates through each card in the collection and creates a new card object:

    class Card {
      constructor(id, question, answer, deckId) {
        this.id = id
        this.question = question
        this.answer = answer
        this.deckId = deckId
        Card.all.push(this)
      }

### JavaScript Events and Manipulating the DOM

Once the objects have been created on the frontend it’s then time to render these objects as HTML.  To create new elements using JavaScript you can use the function `createElement()`.  You can also query elements that already exist using functions such as `querySelector()` and `getElementById()`.  You can also use dot notation to set element attributes, such as `id`, `class` or `innerText`.  You can append child elements to parent elements using the function `appendChild()`.  Here’s an example of how I did this to render a new deck in my application:

    const deckList = document.getElementById("deck-list")
    const deckSelection = document.getElementById("deck-selection")
    const li = document.createElement("li")
    const subjectTag = document.createElement("p")
    const option = document.createElement("option")
    const cardList = document.getElementById("card-list")

    li.id = `deck-${this.id}`
    subjectTag.id = `deck-${this.id}-subject`
    subjectTag.innerText = `${this.subject}`
    option.id = `deck-${this.id}-option`
    option.innerText = `${this.subject}`

    deckList.appendChild(li)
    li.appendChild(subjectTag)
    deckSelection.appendChild(option)

Once a deck has been rendered I wanted the user to be able to click the deck subject and see the cards appear in a card pane.  I was able to achieve this using event listeners.  An event listener is a mechanism in JavaScript that waits for an event to occur, such as a mouse click.  In this case, an event listener was added to the `subjectTag` using the code `subjectTag.addEventListener(“click”, => (e) renderCards(this))`.  Once the `subjectTag` is clicked a function to render the cards is called.  This all occurs inside the function that renders the deck.  This function is located in the deck class so the `renderCards` function is able to take in the `this` keyword as an object.  In this case, `this` refers to the deck object because we want to render the cards for this particular deck.  To retrieve all of the cards associated with the deck I created a method called cards() in the deck class.  This function utilizes the `filter` method to iterate through the card collection and filter out all of the cards whose deck id is equal to the current deck id:

    cards() {
      const cards = Card.all.filter(card => card.deckId === this.id)
      return cards;
    }

The user can view the answer to each card question in the same way.  When they click a question, an event listener recognizes the event and triggers a function to render the answer to the DOM.


