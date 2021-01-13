---
layout: post
title:      "Building an Application with Sinatra and ActiveRecord"
date:       2021-01-13 18:52:45 +0000
permalink:  building_an_application_with_sinatra_and_activerecord
---


As a developer in training I have come across many important concepts that have helped me to understand the finer intricacies of building a simple web application, as well as augment the broader concepts surrounding programming as a whole.  I’d like to share my experience in developing a simple CRUD application using the Sinatra framework and ActiveRecord for the FlatIron School, while also delving into some of these important core concepts that I have learned, such as web frameworks, ORMS, the MVC paradigm, as well as how interactions occur between a user and an application.

# A Few Important Topics

## What is Sinatra?

When building any web application there are many patterns that will be universally used.  A web framework is software that takes care of many of these standard requirements by abstracting them into code that will provide these functionalities without you having to worry about building them out yourself.  Sinatra is a *DSL* (Domain Specific Language) implemented in Ruby and is used as a web framework for writing web applications.  Sinatra is considered to be a *microframework* due it’s being a much smaller framework compared to Ruby on Rails.  It was designed with the intention of being able to build web applications quickly and with little effort.

## What is ActiveRecord?

Another important aspect of building a web application is a concept referred to as *Object Relational Mapping* (ORM).  ORM is a technique that allows us to connect the objects or models of our application to tables stored in a database without having to directly write *SQL* (Structured Query Language) statements ourselves.  ActiveRecord is a Ruby gem and ORM framework that can be used when building web applications.  ActiveRecord gives programmers some powerful capabilities such as representing models, their data, the associations between these models, as well as validating models before they are persisted to the database, all while performing these operations within the object oriented paradigm.

ActiveRecord allows programmers to conveniently alter the database schema over time in a consistent and easy way with the help of *migrations*.  The schema starts off as a blank slate and every migration modifies the schema (i.e. add/remove tables, columns or other entries) while also acting as a new version of the database.  You should note that the schema will only change *after* you migrate the changes to the database.


## Model View Controller Paradigm

Using Sinatra and ActiveRecord makes the process of building a web application much simpler by taking care of tasks at a low level of abstraction which in turn allows the programmer to focus on tasks at a higher level, like domain modeling or implementing logic specifically pertaining to the project.  In conjunction with these systems, using a an organizational system or framework to separate an application's code based on function can make writing, reading and debugging your application much simpler, as well.  The *Model View Controller* paradigm (MVC) is a widely used framework to achieve this goal.

The MVC file structure is used to separate concerns where groups of files perform specific tasks and work together with each other in distinct ways.  At the highest level of abstraction, your application can be placed into two categories:  the *backend* and the *frontend*.  The backend of the application refers to everything that occurs on the server-side, everything that happens behind the scenes. The frontend contains everything related to the user interface.  This is the part of the application that you would interact with as the user.  The Models are on the backend of the application and contain all (or most) of the logic.  This is where your objects and database live.  The View is the frontend of the application.  This is the only part of the application that the user directly interacts with.  Here you’ll find all of your HTML, CSS and JavaScript.  Lastly, the Controller acts as the middle man between the Models and the Views.  It’s the Controller’s job to relay data from the browser to the application, and then from the application back to the browser.

## Request/Response Flow and RESTful Routing

This is a good point to briefly discuss the request/response flow of a web application.  This is an important process to understand because it explains the relationship and interactions between the user and the application.

To understand the request/response flow of a Sinatra application, we first need to understand the foundation of any communication between the client and server of any application.  HTTP (Hypertext Transfer Protocol) is the language of the internet.  It is the protocol that allows the retrieval of information, such as HTML documents.  All of the internet is dictated by the request and response cycle, the call and answer, of HTTP.  Let’s see how this plays out from the perspective of a Sinatra application.

Simply put, the request/response flow of a Sinatra app runs as follows.  First the client makes an HTTP request, which can take the form of clicking a button or a link to retrieve information or be taken to a new page, or by filling out and submitting a form with user defined data.  Basically, the user is able to interact with the application by requesting URLs via HTTP requests.  Sinatra will then grab the request and route it to the correct controller action.  When Sinatra receives a request it matches the route to a specific controller action that matches the URL pattern.  That is because in Sinatra, a route is basically just an HTTP method/verb (i.e. `GET` or `POST`) that is coupled with a URL-matching pattern.  The convention for how to handle URL’s and manipulate content by mapping HTTP verbs like get, post, put delete and patch to controller CRUD actions (create, read, update, delete actions) is a design pattern known as *RESTful routing*.  It is a useful standard practice for making things consistent for users and programmers alike.  So, for example, if your app gets a request `GET /posts/1` the controller action it would match would be `get /posts/:id`.  These routes that are created based on the attributes within the url of the request are referred to as *dynamic routes* and are used so that programmers do not have to hard code every single possible route in their application’s controllers.

When using RESTful routing practices, the five HTTP verbs (GET, POST, PATCH, PUT, DELETE) are normally associated with specific actions.  The HTTP GET verb is associated with the *index*,* new,* *show* and *edit* actions.  The *index* action is used to display all content associated with a specific model (i.e. all the books a user currently has on their reading list).  The *new* action is used to display a form meant to create content.  The *show* action is used to display the content associated with a single object (i.e. title and author of a single book).  The *edit* action is used to display the form meant to update the content of a specific object.

THe HTTP POST verb is associated with the *create* action, which is used to create an object and, in most cases, persist it to the database.  The HTTP verb PATCH is associated with the *update* action, which is responsible for modifying an object’s attributes.  Similarly, the HTTP verb PUT is also associated with the *update* action, but is used when your intention is to replace an existing object instead of merely modifying it’s attributes.  Lastly, the HTTP DELETE verb is associated with the *delete* action, which is responsible for deleting a specific object from the database.

Now moving on with the request/response flow, once the request has been properly routed to the correct controller action, the code that is written into the controller action will be executed, and in most cases it will be responsible for retrieving objects from the models.  So the action asks the model for objects and then the model will in turn send a request for data from the database.  The database will respond to this request and the model will send the objects back to the actions.  At this point, the action will send the objects to the view to be rendered as HTML.  Finally, the controller will send the rendered HTML back to the client in an HTTP response.

# Project Pre-Work

The primary of the goal of this particular project was to build a web application using Sinatra and ActiveRecord that demonstrates the four basic functions of persisting data to a database, which includes being able to create, read, update or delete data, often referred to as *CRUD* functionality.  The application also needed to include more than one model class, as well as at least one `has_many` and `belongs_to` relationship.  The user would also need to be able to sign up and login with unique login credentials.  Furthermore, this project required that users could not modify content of other users and that bad data could not be persisted to the database.  Keeping all of this in mind, I began the project by completing some pre-work, namely, coming up with a central idea about what I would be building, identifying some of the crucial elements of the application that would help me to achieve these goals, as well as scaffolding the application structure and setting up a Git repository.

## Coming Up With An Idea

The first step was to choose a domain for my project and come up with a user story.  After giving it some thought I decided that I wanted to build a reading list application that would allow a user to sign up, login, logout, create books, view their books on their reading list, edit their books by updating the title or author, and lastly delete any book from their own list.

I decided that for this project I would only need two models:  a User model and a Book model.  The User would be the `has_many` relationship (a user has many books) and would have the following attributes: name, email and password.  In turn, the Book model would be the `belongs_to` part of the relationship so it would have the *foreign key* *(or `user_id`) along with the following attributes: title and author.  The *foreign key* is used to establish a relationship between two tables in a database.  Giving the book the foreign key enabled me to link the book to a specific user, hence, a book *belongs to* a user.  The Book model would also be the model the user would be able to perform CRUD actions on, as previously stated.

## Scaffolding the App

There is a Sinatra app generator called Corneal that some people use to take care of a lot of the essential parts of the scaffolding process.  I chose not to use this and to instead scaffold the project out by hand to ensure that there wasn’t any superfluous code or files in my project and to make sure that I understood all of the pieces that would make up the file structure.  I’ll go through all of the folders and files that make up my project file structure.

### App Directory

The `app` directory is where the separation of concerns of the MVC paradigm is implemented.  This directory consists of three folders labeled *controllers*, *models* and *views*.  As you might have guessed, the *models* directory contains all of your applications models (i.e. `user.rb`, post.rb).  The *views* folder contains all of your ERB files (ERB will be discussed in more detail later on). In the `controllers` directory sits all of your applications controllers.  The *Application Controller* is particularly important namely because it inherits from the Sinatra::Base, which is defined on the rubydoc website as the “base class for all Sinatra applications and middleware.”  Basically, it gives us all of the functionality inherent in the Sinatra library that we will need for our application to work.  All of the other controllers that you will build will inherit from the Application Controller, thereby inheriting from SInatra::Base, as well.  This is also where the programmer can configure the public folder path and the views path, as well as enable *sessions* and write *helper methods* (both of which we will discuss in more detail a little later).

### Config Directory

The *config directory* is where your *environment file* lives.  This is the file that gives your application’s components the ability to communicate with one another.  In this file you’ll find `require ‘bundler/setup’` which will find your *Gemfile* and make all of your gems and their dependencies available to Ruby by adding them to the *load path*.  `Bundler.require(:default, ENV[‘SINATRA_ENV'])` is a line of code that requires all of our gems as well as our deployment environment to be used with the ActiveRecord gem.  ENV is considered to be a hash-like accessor for Ruby environment variables that will change based on what information Ruby needs.  It will hold all kinds of information for Ruby, including all frameworks and gems (i.e. we define `SINATRA_ENV` so this is added as a key to the ENV).

The following lines of code are responsible for establishing the connection to the database:

  `ActiveRecord::Base.establish_connection( 
   :adapter => “sqlite3", 
   :database => “db #{ENV[‘SINATRA_ENV']}.sqlite")`

Finally, `require_all ‘app’` will load all other files nested under *app* to run the program. 

### db Directory

This directory holds all files pertaining to the database.  The *migrate* folder contains all of the migration files for your project.  Once you migrate the changes you have made your `schema.rb` file, which represents the current version of your database, will be created.  The db directory also contains your `seeds.rb` file, which is where you can put all of your test data.

### Public Directory

The *public directory* is where you can place all of your images, stylesheets and CSS files.

### config.ru

This is the Rack configuration file that allows Ruby to offer a standard protocol for web servers to execute programs.  Basically, it is the file that allows your app to run and where you require your environment files.  We need to implement the code `run ApplicationController` because this is the line of code that will run the application.  For every other controller that we use in our application we need to mount it above this line of code (i.e. `use UserController`).

It is also important to note that this is where we implement code that allows us to use PATCH and DELETE routes by intercepting POST requests.  This *Sinatra Middleware* is called `Rack::MethodOverride`.  

### Gemfile

The *Gemfile* is where all of your gems will be loaded.  You can run `bundle init` to generate the the gemfile.  Once you put all of the gems you plan on using in the file you can install all of the gems by running `bundle install`.
 
### Rakefile

The *Rakefile* handles all of the administrative commands or tasks using the Ruby language and allows you to execute them on the command line.

### README File

The *README* file is just a text file that contains useful information for the user about the application.  This can include anything from instructions on how to install and use the app to any other additional information you might deem helpful to the user when running your application.

## Building a Git Repository

Lastly, you’ll want to set up a *git repository* which is responsible for tracking all the changes made to your project files throughout the course of building your application.  First, while inside of your project directory, type `git init` into the command line.  This will create a new git repository.  After you make an initial commit, your git repository will be stored locally.  You’ll also want to submit these changes to a remote repository on Github.  To do this you can simply create a new repository from your Github account and follow the instructions on the site.

# The Backend

## Models & Migrations: The Backbone of Your Application

Once the scaffolding was all set I decided to start coding my project from the backend.  In my models folder I created two files, `book.rb` and `user.rb`.  These would become my Book and User models.  Here is an example of what the user and book models looked like.

    class User < ActiveRecord::Base
      has_many :books
      has_secure_password
    end

    class Book < ActiveRecord::Base
      belongs_to :user
    end

Both models need to inherit from `ActiveRecord::Base` in order to use everything that ActiveRecord has to offer.  The User model gets the `has_many :books` macro, which declares the one-to-many relationship with the Book model.  The Book model gets the `belongs_to :user` macro, which creates the one-to-one relationship with the User model.  Both of these macros give their respective classes a number of methods that can be used in relation to their association.  In the above example, the User model is given the `has_many :books` relationship.  By using this bit of code you can now retrieve all of the books associated with a particular user by writing `user.books`.  You can also find out how many books a user has book writing `user.books.size`.  If you wanted to find a book belonging to a particular user by title you could query the database by writing `user.books.find_by(title: “Title of Book”)`.  These are just some of the methods you have access to by coding `has_many` into your model.

The User model also gets another macro: `has_secure_password`.  The functionality of this macro is to encrypt a user’s password and comes with several instance methods, one of which helps you to authenticate passwords in conjunction with using the *bcrpyt* gem.  You should note that you need to use `password_digest` in your user table instead of just `password` in order to properly encrypt the user’s password.

The next step in building out the backend of the application was to start creating migrations to build tables and migrate them to a database.  To do this you can use the rake command `rake db:create_migration`.  The migration files will be generated in the *migrate* folder in your *db directory*.  It is in these files that you can create your tables.  This is what my books and users tables look like:


    class CreateBooks < ActiveRecord::Migration[5.2]
      def change
        create_table :books do |t|
          t.string :title
          t.string :author
          t.integer :user_id
        end
      end
    end

    class CreateUsers < ActiveRecord::Migration[5.2]
      def change
        create_table :users do |t|
          t.string :name
          t.string :email
          t.string :password_digest
        end
      end
    end

The users table was created with three columns, `name`, `email` and `password_digest`, each with the data type `string`.  The books table was created with there columns, as well, named `title` ,`author` and `user_id`.  The `user_id` is the foreign key that will eventually link the book object with the user object via each user’s primary key.

Once the tables were all set up I then migrated the database using the rake command `rake db:migrate`.  At this point, the `schema.rb` file was generated and added to the *migrate directory*.  Now that the database was set up I decided that I wanted to use some seed data to test my application throughout the build process.  I set up the test data in the `seeds.rb` file also located in the *migrate directory*.  To load the seed data you can simply run the rake command `rake db:seed`.

## The Controllers - Where All the Action Happens

My project has four controllers.  We have already spoken briefly about the *Application Controller*.  The other three are the *Users Controller*, the *Books Controller* and the *Sessions Controller*.  The Users Controller and Books Controller have routes and controller actions that pertain to their models and views, respectively.  The Sessions Controller has routes that specifically pertain to logging in, logging out and signing up for the application.

### What is a Session?

Before we get into setting up the controllers, we should first discuss *sessions* because they will become important momentarily.  Sessions are what allows a user to log in and out of their account on a website.  To start, it is important to understand that the internet does not remember who you are. HTTP does not store your information in any sort of internal memory.  It considered to be a stateless protocol meaning that each new HTTP request is independent from the last and is unable to use any information from previous requests.  This is where sessions come in.  A session is a connection that is made between the client and the web server.  It is an object that behaves like a hash and is able to store data relating to a user’s interactions with a web application at any single point in time.  The session is set when a user logs into their account, so instead of having to repeatedly authenticate yourself as you navigate a site from page to page, a session allows a website to keep track of your movement up until you log out, at which point the session is cleared.

### Helper Methods

Each controller utilizes a number of *helper methods* that have been built in the Application Controller.  Helper methods, in this context, are used to perform tasks that are repetitive throughout each of the controllers.  Before moving on you can take a look at them in the example below so that you will understand how they work when they come up again later on:

    class ApplicationController < Sinatra::Base
      configure do
        set :public_folder, 'public'
        set :views, 'app/views'
        enable :sessions
        set :session_secret, "secret"
        register Sinatra::Flash
      end

      get '/' do
        if !logged_in?
          erb :index
        else
          redirect "/users/#{current_user.id}"
        end
      end

      helpers do
        def logged_in?
          !!current_user
        end

        def current_user
          @current_user ||= User.find_by(id: session[:id])
        end

        def log_user_in
          session[:id] = @user.id
        end

        def log_user_out
          session.clear
        end

        def find_user_by_current_user_id
          @user = User.find_by(id: current_user.id)
        end

        def find_book_by_id
          @book = Book.find_by(id: params[:id])
        end

        def book_exists
          @book != nil
        end

        def book_belongs_to_current_user
          @book.user_id == current_user.id
        end

        def valid_book_form_submission
          params[:title] != "" && params[:author] != ""
        end

        def set_and_save_title_and_author
          @book = Book.new(params)
          @book.user_id = current_user.id
          @book.save
        end

        def save_changes_to_book
          @book.title = params[:title]
          @book.author = params[:author]
          @book.save
        end

        def log_in_to_view_page
          flash[:message] = "You need to log in to view this page."
        end

        def fill_out_all_fields
          flash[:message] = "Please make sure all fields are filled out before submitting form."
        end

        def valid_sign_up_form_submission
          params[:name] != "" && params[:email] != "" && params[:password] != ""
        end

        def unique_name
          !User.find_by(name: params[:name])
        end

        def user_exists
          @user != nil
        end

        def password_is_authenticated
          @user.authenticate(params[:password])
        end

        def valid_login_form_submission
          params[:name] != "" && params[:password] != ""
        end
      end
    end

### Sessions Controller

Below you will find the code for the Sessions Controller that I built for my project:

    class SessionsController < ApplicationController

      get '/signup' do
        if !logged_in?
          erb :'/sessions/signup'
        else
          flash[:message] = "You have already signed up."
          redirect "/users/#{current_user.id}"
        end
      end

      post '/signup' do
        if valid_sign_up_form_submission && unique_name
          @user = User.create(params)
          log_user_in
          redirect "/users/#{@user.id}"
        elsif !unique_name
          flash[:message] = "Name already in use.  Please choose another."
          redirect "/signup"
        elsif !valid_sign_up_form_submission
          fill_out_all_fields
          redirect "/signup"
        end
      end

      get '/login' do
        if !logged_in?
          erb :'/sessions/login'
        else
          flash[:message] = "You are already logged in."
          redirect "/users/#{current_user.id}"
        end
      end

      post '/login' do
        @user = User.find_by(name: params[:name])
        if user_exists && password_is_authenticated && valid_login_form_submission
          log_user_in
          redirect "/users/#{current_user.id}"
        else
          flash[:message] = "Your login credentials are invalid.  Please try again."
          redirect "/login"
        end
      end

      get '/logout' do
        if logged_in?
          find_user_by_current_user_id
          erb :'/sessions/logout'
        else
          log_in_to_view_page
          redirect "/login"
        end
      end

      post '/logout' do
        log_user_out
        redirect '/login'
      end
    end

There are six controller actions in the Sessions Controller.  The *sign up* functionality has two controller actions:  a GET and a POST action.  When a user tries to access the sign up page, an HTTP GET request is sent to the controller and matched with the GET controller action in the Sessions Controller.  If a user is not already logged in, the sign up page will be *rendered*.  If a user is already logged in, the user will be *redirected* to their home page in their account.  Once the sign up page is rendered for the user, they can then fill out and submit a sign up form with their chosen credentials (name, email, password).  The HTTP POST request is then sent to the controller and matched with the POST controller action pertaining to the signup route in the Sessions Controller.  Simply put, if all of the user’s credentials are *validated*, they will be logged in — meaning their `@user.id` will be stored in the session hash — and then redirected to their account landing page.  If their credentials are not valid they will be redirected back to the sign up page and shown a *flash message*, stating that their credentials were invalid and that they should try again.  (We’ll go through what flash messages are and how they work in a little bit).  It is important to validate user input to ensure that “bad data” is not persisted to the database.  This bad data can take the form of empty space `’ ‘` in one or more of the form fields, an invalid email address, a username that has already been persisted to the database, or any other kind of data that you as the programer would deem to be unacceptable for your database.  For this project, valid input constitutes completing all form fields and using a unique username.

Notice how there are times when we *render* a new page for the user or *redirect* them elsewhere.  Generally speaking, when a user sends an HTTP GET request (by clicking on, say, a link to the *login* page), the view that is associated with the matching controller action will be rendered for them.  It is the responsibility of the GET controller action to render new pages for the client.  However, if the user fills out a form and hits submit, thereby sending an HTTP POST request to the matching POST route, the controller action is responsible for creating or retrieving data from the database and then redirects to the GET route where the rendering occurs.  The *single responsibility principle* is they key point here.  The GET controller action and the POST controller action serve different functions.  Because it is the GET controller action’s responsibility to render views, we do not give the same responsibility to the POST controller.

Now, let’s move on to the *login* functionality.  Logging in is similar to signing up.  If a user tries to access the login page a GET request is sent to the controller and matched to the appropriate GET action pertaining to the login route.  Then the code in the controller action is executed:  if the user is not already logged in, then the user will be logged in to their account by storing their user ID in the session hash and the login page will then be rendered for the user; if the user is already logged in, they will then be redirected back to their account landing page and presented with a flash message, alerting them to the fact that their credentials are invalid.

The logout GET request/response cycle works the same as the previous two.  However, if the logout page is rendered for the user and they submit a request to log out of their account, the POST request is matched to the POST logout route in the Session Controller and the user is logged out of their account by clearing the session hash of the user’s ID, at which point the user will be redirected back to the login page.

### Books Controller

The Books Controller holds all of the controller actions pertaining to the *books* routes.  Specifically, this is where all of the CRUD actions occur.  Take a look below:

    class BooksController < ApplicationController

      get '/books' do
        if !logged_in?
          log_in_to_view_page
          redirect "/login"
        else
          find_user_by_current_user_id
          erb :'/books/index'
        end
      end

      get '/books/new' do
        if !logged_in?
          log_in_to_view_page
          redirect "/login"
        else
          find_user_by_current_user_id
          erb :'/books/new'
        end
      end

      post '/books' do
        if valid_book_form_submission
          set_and_save_title_and_author
          redirect "/books/#{@book.id}"
        else
          fill_out_all_fields
          redirect "/books/new"
        end
      end

      get '/books/:id' do
        find_book_by_id

        if !logged_in?
          log_in_to_view_page
          redirect "/login"
        elsif book_exists && book_belongs_to_current_user
          find_user_by_current_user_id
          erb :'/books/show'
        else
          flash[:message] = "Click the 'View Reading List' link at the top of the page to view your books."
          redirect "/users/#{current_user.id}"
        end
      end

      get '/books/:id/edit' do
        find_book_by_id
        if !logged_in?
          log_in_to_view_page
          redirect "/login"
        elsif book_exists && book_belongs_to_current_user
          find_user_by_current_user_id
          erb :"/books/edit"
        else
          flash[:message] = "Click the 'View Reading List' link at the top of the page to select a book to edit."
          redirect "/users/#{current_user.id}"
        end
      end

      patch '/books/:id' do
        find_book_by_id
        if valid_book_form_submission
          save_changes_to_book
          redirect "/books/#{@book.id}"
        else
          fill_out_all_fields
          redirect "/books/#{@book.id}/edit"
        end
      end

      delete '/books/:id' do
        find_book_by_id

        if !logged_in?
          redirect "/login"
        elsif book_belongs_to_current_user
          @book.destroy
          redirect "/books"
        else
          redirect "/users/#{current_user.id}"
        end
      end
    end

The POST books action is responsible for the Create action.  If the form has been fully completed and validated then a new book will be created for the user to view once they are successfully redirected either to their reading list or to the book show page.  Which brings us to the GET books and GET books/:id routes, which are responsible for the Read action.  If a user is logged in and the book they are trying to view is in fact their own, the GET books action will render the index page of the *books* view and the GET books/:id action will render the book’s show page.

The PATCH books/:id action is responsible for the Update (edit) action.  If the edit form has been entirely filled out and is validated, the changes will be persisted to the database and the user will be redirected elsewhere.  The DELETE books/:id action is responsible for the Delete action.  If the user decides that they would like to remove one of the books that they have created they can do so by clicking the delete button on the book’s show page.  Doing so will submit an HTTP POST request, which will be intercepted using the Rack middleware `Rack::MethodOverride`, which will in turn match the request to the DELETE books/:id action.  At this point the book will be deleted from the database and the user will be redirected back to their reading list.

### Users Controller

The Users Controller only has one controller action.  This is the action that will render the user’s show view, which is the page that functions as the user’s landing page when they are logged into their account.  It is from this page that they can begin to navigate to the different pages of the application.  A user is only authorized to view this page if they are logged in and are, in fact, the user that they claim to be.  The controller action also protects against the application breaking if the user tries to access a URL that does not exist.  You can take a look at the controller action below:

    class UsersController < ApplicationController

      get '/users/:id' do
        @user = User.find_by(id: params[:id])
        if !logged_in?
          log_in_to_view_page
          redirect "/login"
        elsif (logged_in? && !user_exists) || (logged_in? && @user != current_user)
          flash[:message] = "Click the links above to navigate the site."
          redirect "/users/#{current_user.id}"
        elsif logged_in? && @user == current_user
          erb :"/users/show"
        end
      end
    end

# The Frontend

## The Views: The Face of Your Application

Now that we’ve gone through all of the backend architecture or the program, let’s dive into the components that make up the frontend of the application.  We’ll discuss everything that takes place in the *views* portion of the MVC paradigm, from HTML and Embedded Ruby to how to build a form and how the *layout* view works.

### HTML and Embedded Ruby

The *View* portion of the MVC paradigm is the part of the application the user interacts with directly.  It represents what the user sees on their end.  Sinatra comes with a templating engine that allows us to render HTML content from files that are separate from the controllers and models.  These are the files that are referred to as the *views.*  These files are saved with the `.erb` extension because they utilize Embedded Ruby (ERB).  ERB is the templating engine that Sinatra uses which allows us to render HTML code *along* with Ruby code in the views.  When rendering pages, Sinatra will, by default, look for files with the `.erb` extension in the views directory.   The convention is to name both your erb files and routes the same name to makes things easier to track throughout your project.

In order to embed Ruby code into the HTML in your view files you need to wrap the Ruby code in ERB tags.  There are two main types of ERB tags.  Substitution tags `<%= %>` are used to evaluate Ruby code while also displaying the results has HTML.  Scripting tags `<% %>` evaluate Ruby code, as well, but they do so without displaying the results.  A common use case that demonstrates the use of both tags is iteration through a collection and displaying the results to the user as a list.  Take a look at the code in my project for generating a user’s reading list:

    <div class="reading-list-page">
      <h1><%= @user.name %>'s Reading List</h1>

      <% if @user.number_of_books != 0 %>
        <h2>What would you like to read?</h2>
        <div class="reading-list">
          <ol>
            <% @user.books.each do |book| %>
              <li><a href="/books/<%= book.id %>"><%= book.title %></a></li>
            <% end %>
          </ol>
        </div>
      <% else %>
        <div class="first-book">
          <p>Let's get started by adding your <a href="/books/new">first book.</a></p>
        </div>
      <% end %>
    </div>

The current user object is stored in the instance variable `@user`.  We can iterate through the collection of books and list them one by one simply by using the `each` method.  Here you can see that I have utilized the scripting tag for the parts of the code that I do not want displayed and would only like to be evaluated: `@user.books.each do |book|`, and `end`, which closes the block.  The block is the only part of the code that needs to be displayed to the user:  `book.title`, which is obviously the book’s title.  As you can see, each title is wrapped in `<li>` tags inside an ordered list.  Using both HTML and ERB we can create lists that change dynamically based on what information is stored in the database with relation to the current user without letting the user see any of the code that is responsible for retrieving the data from the backend of the application.

### How to Build a Form

The view files are also where you will construct HTML forms.  Forms are a very important part of a web application as they are the most common way for a user to communicate data to an application.  If a user would like to signup or log in to a site they first need to fill out a form with their credentials.  If they would like to retrieve their data from the backend or create new content they will need to fill out a form, specifying the piece of information they would either like to see returned to them or persisted to the database.  Here is an example of the new book form:

    <div class="new-book-page">
      <h1>Hey <%= @user.name %>. <span>Let's add a book to your list.</span></h1>

      <form class="new-book-form" action="/books" method="post">
        <label for="title">Title: </label>
        <input type="text" name="title">
        <br>
        <br>
        <label for="author">Author: </label>
        <input type="text" name="author">
        <br>
        <br>
        <input type="submit" class="button" value="Add Book">
      </form>
    </div>

HTML forms are wrapped in form tags `<form>`.  There are two important attributes that are placed in the tag:  the *method* and the *action*.  The method attribute tells the form what kind of request should be sent to the controller.  In most cases, the method will be a POST request because the user will almost always be “posting” data to the server.  The action attribute let’s the form know which route should receive the request.  In this example, we are sending a POST request to the POST /books route.

Another important part of the form is the input tag `<input>`.  This is where the user inputs their data.  The input tag has two important attributes:  the *type* and the *name*.  The type attribute states what kind data the input represents.  In this case the data the the user is inputing is “text”.  We can make a submit button by giving an input tag the type “submit.”  We can label the button using the *value* attribute and setting it equal to whatever we would like to name the button, in this case, “Add Book.”

In order to explain the name attribute of the input tag, we should first discuss how information is passed from the form to the controller.  When a user submits a form, the data is temporarily stored in *params*.  Params is a hash that can be accessed from any of the controllers.  It keeps track of the *parameters* that are filled out in the form by the user.  Params works just like any other hash.  It can store any number of keys that point to their corresponding values.  When a user submits a form, the data that they input is the *value* part of the key/value pair.  The *name* attribute is what represents the key.  So in the example above there are two input fields:  one for the author (with the name attribute set to “author”) and one for the title (with the name attribute set to “title”).  If I were to fill the form out with “The Sun Also Rises” as the title and “Ernest Hemingway” as the author, once I click submit the params hash would be populated with key/value pairs supplied by the form:

`{“title” => “The Sun Also Rises”, “author” => ”Ernest Hemingway”}`

We can then use this data in the controller action to create a new book and store it in the database for the user.  As you can see, forms are one of the most crucial tools in allowing a user to interact with an application in any meaningful way.

### The Layout View

When building a web application there will inevitably be information that you will want to have displayed consistently throughout each page, like the navigation links in the header or important information located in the footer.  In order to achieve this without having to repeat the same code in every view file you can create a `layout.erb` file.  This is the file that will contain all of the code we want to exist on every single web page.  To do this you first begin by coding the HTML and ERB that you want to persist through the site.  Once you have done this you have to implement what is called a `yield` statement.  The `yield` statement is placed where we want another page’s content to be loaded.  We wrap the yield statement in ERB substitution tags `<%= yield %>` so the content of another page will be displayed to the user.  If you want your navigation links to remain the same on every page, you would first code them into the layout file and then place the `yield` statement below it where you would like the contents of another page to be seen.

When a controller action is triggered and an ERB method is called to render a view, the controller will first look to see if there is a view title *layout.erb*.  If it finds one, it will load that content around the view file in question.

# Stretch Goals

When I was planning out my application I set aside some stretch goals that I wanted to implement once I had completed all of the core criteria of the project.  To round out the finishing touches I wanted to make sure that I included flash messages to alert users of any important information that I would want to communicate to them, as well as add some more content to further enrich the user experience.

## Implementing Flash Messages

A  *flash message* is something you can use in Sinatra to relay important messages to user.  Let’s say that a user is trying to log in to your site, but they have entered the wrong login credentials into the form field.  There are a number of things that could happen at this point, depending on how you have designed your program.  In my application, if the user submits an HTTP request with the wrong login credentials, they will be redirected back to the login page to try again.  However, if I didn’t implement a flash message in this scenario, the user may not understand why they have been redirected back to the login screen.

A flash message is basically just a hash.  It stores key/value pairs just like any other hash.  However, a flash message has a very short life cycle that only lasts for one HTTP request.  Once the HTTP request cycle has finished, the flash message will be cleared. 

When a user sends an HTTP POST request to log in to the site, the request is sent to the login POST controller action.  If the credentials are invalid, the flash message will be set to display a warning to the user.  Here is an example of what that might look like in the POST controller action:

`flash[:message] = “Your login credentials are invalid.  Please try again.”
redirect  “/login”`

When the user is redirected back to the login page, the flash message will be displayed to them.  To display the message to the user, you would have to code logic into the layout view to check if a flash message exists.  If the flash hash has been populated with a key/value pair, the message will be displayed.  If the hash is empty then the layout view will display it’s contents as usual.  Here is an example of what that code might look like in your layout view:

    <% if flash[:message] %>
      <p><%= flash[:message] %></p>
    <% end %>

To set up flash messages in your project you first need to add the `sinatra-flash` gem  to your gemfile and *bundle install*.  You also need to make sure that you have already enabled sessions in your application controller.  Next you will need to register `Sinatra::Flash` inside the configure block in the application controller.  You could also `require ‘sinatra/flash’` at the top of your application controller file to load all of the necessary code to get flash messages working, but this would not be necessary if you were already requiring the environment file.

## Some More Content

To finish off my project I decided to add some more content to give the application more life.  When a user logs into their account they are taken to their landing page, which is the *user show page*.  To begin with, this page did not offer any information or content.  It was just a place to begin navigating the site.  I decided that from this page I wanted to be able to display the total amount of books that the user had already created and stored in their reading list.  I also wanted to notify a user if their reading list was empty and provide them with a link to the page where they could create a new book.  Lastly, I wanted to be able to display the next book on their reading list without showing them the entire list.  Below you can take a look at the code  I built into the User Model that enables this functionality:

    class User < ActiveRecord::Base
      has_many :books
      has_secure_password

      def number_of_books
        self.books.size
      end

      def book_list_stats
        if number_of_books == 0
          empty_list_message
        elsif number_of_books == 1
          "You have 1 book on your list."
        elsif number_of_books > 1
          "You have #{self.number_of_books} books on your list."
        end
      end

      def next_on_list
        if number_of_books != 0
          next_book
        else
          empty_list_message
        end
      end

      def next_book
        self.books.first.title
      end

      def next_book_id
        Book.find_by(title: self.next_book).id
      end

      def empty_list_message
        "You do not have any books on your list."
      end
    end

As you can see, there is a lot that goes into the planning and execution of a programming project, even one as simple as a reading list application.  There is no question that is a very primitive application that has very limited functionality.  However, in working on this program I learned a lot of valuable concepts.  From the broader topics of web frameworks, ORMS and file structure, to more intricate discussions of routing conventions and data transmission, I have come away from this project with a greater understanding of the many abstractions inherent in web developing.  By working through my thoughts and process outlined in this article some of these abstractions have been rendered a little more concrete.  If you have read up until this point, I hope the same has occurred for you.  If not, allow me to redirect you elsewhere:  

- www.sinatrarb.com
- www.guides.rubyonrails.org

