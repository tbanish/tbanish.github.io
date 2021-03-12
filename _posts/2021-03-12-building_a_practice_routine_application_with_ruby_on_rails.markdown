---
layout: post
title:      "Building a Practice Routine Application with Ruby on Rails"
date:       2021-03-12 18:53:06 +0000
permalink:  building_a_practice_routine_application_with_ruby_on_rails
---


My third assignment for the Flatiron School was to build a complete Ruby on Rails application that can manage related data through the use of complex forms and the RESTful routing convention.  This is was a challenging yet fun project to work on as it required me to deepen my understanding of how to build and structure an application with multiple associated models while also allowing me to explore more features and functionality then I have been able to work with up until this point.

## What is Rails?

Rails is a web application framework written with the Ruby programming language.  It was designed to take care of many of the repetitive tasks that all web applications require so that developers can focus primarily on the code that makes their applications unique.  Rails privileges convention over configuration.  What this means is that, according to Rails, there is a right way to do things.  This can be very beneficial for developers because once you learn the conventions of Rails, you no longer need to worry about the small, trivial details that comes with configuring things on your own.

## Rails File Structure

Creating a new Rails application is very simple.  As long as you have Rails installed on your system and you are currently in the the directory where you plan to work, all you have to do is run the command `rails new (app name)`.  This will generate a scaffolding for your project.  There are many files and directories that come bundled into a new Rails application.  However, in the beginning some of them are more important to understand than others.

The `app` directory is where you’ll find your models, views and controllers directories.  Each model that lives in the models directory will have an associated controller in the controllers directory.  The model is named with the singular tense where as the controller is given the plural tense.  For instance, in my application the `Routine` model has a controller called `RoutinesController.`  Each model also has it’s own folder in the views directory.  This folder is given the plural tense (i.e. `routines`).  These are some of the naming conventions that are used in Rails.  By using these conventions Rails can make certain assumptions about how to wire all of the models, controllers and their respective view files together.

The `app` directory also has two other directories that are worth noting.  The `assets` directory is where your stylesheets will be.  Rails will create a stylesheet for each model, allowing you to create styles that pertain to each individual model.  The `helpers` directory has a helper file for each model, as well.  These helper files are where you can extract and place logic or duplicated code from your views, enabling you to keep your view files from becoming too complex.

The next directory you should know about is the `config` directory.  This is where you will find your `routes.rb` file.  This is the file where you associate a URL path and HTTP request to a specific controller action.  For instance, to create a route to the signup page I placed the following code in my routes file:  `get '/signup' => ‘users#new’`.  This is a get request that matches the ‘/signup’ path to the ‘new’ action in the ‘users’ controller (more on actions later).  There are other files and directories in the `config` folder, but the `routes.rb` file is the one you’ll be using the most.

The `db` directory is where you’ll find everything related to your database, which includes your migrations, your schema and your seeds file.  The `gemfile` is, of course, where you will find all of the gems that you would like to use in your application.  The `config.ru` file is the rackup file that runs your app.  The `rakefile` is where you can specify administrative tasks.  Rails comes with a bunch of rake tasks built into it natively.  

There are many other files and directories that come with a new Rails app, but most of them I did not have to alter at all for this project so I won’t get into what they all do.

## Project Idea: Routine App

As a musician that plays multiple instruments I am always trying to put together a solid practice routine that I can stick to on a daily or weekly basis.  So I decided that for my Rails app I wanted to build a program that would allow a user to create practice routines for themselves.  I wanted a user to be able to sign up and log in to their account, create a routine for whatever instrument they choose, and then add items to the routine.  An item would be anything that the user might want to cover during their practice session.  It could be anything from working on an etude, playing through a concert piece, transcribing something by ear or working on a finger exercise.  I also wanted the user to be able to add notes to each item so that they could keep track of their progress from day to day.

I decided that the application needed 5 models:  User, Routine, Instrument, Item and Note.  A User has two attributes, a username and a password, and it has many routines and many instruments through routines.  A Routine has three attributes, a name, an instrument_id and user_id.  It belongs to an instrument and a user and it also has many items.  An Instrument only has one attribute - a name.  It has many routines and many users through routines.  An Item has four attributes:  a name, a description, a goal and a routine_id.  It belongs to a routine and has many notes.  A Note has two attributes, content and item_id, and belongs to an item.

## Setting up the Migrations, Models and Controllers with Rails Generators
Instead of having to manually create your database tables, models, controllers and view files by hand, Rails comes with generators that take care of some of the more tedious aspects of these tasks.  The main generators that Rails has to offer are Migrations, Models, Controllers and Resources.  To use one of the generators you simply type the command `rails g (name of generator) (options)`.  For example, to create a migration for the User model you could type the command `rails g migration CreateUsers`.  This would create the migration file without any attributes.  If you wanted to include attributes you could write the command `rails g migration CreateUsers username:string password:password_digest`.  This would create a table with a username and password column set to a string and password_digest, respectively.  You could then run the rake command `rake db:migrate` to migrate the table to your schema.

The model generator is a little more advanced than the migration generator.  For instance, we could generate a User model by typing in the command `rails g model User username:string password:password_digest`.  This command will not only generate the User model file that will inherit from ApplicationRecord in the models directory, but it will also generate the migration file with the table for the users and columns that we indicated.

The controller generator will create even more code and functionality than the model generator.  When you run this generator you will not only get the model and migration file, you will also get the controller file that inherits from ApplicationController, a directory for the view templates, a view helper file, and several other useful files.

Lastly, the resource generator will create the following for you if you choose to use it:  a migration file, a model file, the controller, a view directory without view templates, a view helper file, `scss` stylesheet for the controller, as well as `resources` in your route file. Using `resources` in the route file will give you access to each route that you will need to satisfy all of your CRUD actions.  This is the generator I chose to use when creating my migrations, models and controllers.

The important thing to note is that using generators is very beneficial because it helps to ensure that you will be sticking to the conventions that Rails has established as the “right” way to do things.  This way you will know that all of your migrations, models, views and controllers are wired up correctly.  However, you want to make sure that you are using the appropriate generator for the correct job.  Generators can create more code then you may actually need, so you should be cautious when choosing which one to use.

## Models

Each of the models inherit from ApplicationRecord, which in turn inherits from ActiveRecord::Base.  ActiveRecord gives us access to many different tools, including macros that establish associations between models, like `belongs_to`, `has_many` and `has_many through`, as well as scope and validation methods.

Each model utilizes the validation methods to help ensure that bad data does not persist to the database.  For instance, to prevent a routine from being saved to the database without a name or an associated instrument, I used the code `validates :name, :instrument_id, presence: true`.  The validation is run before the SQL `INSERT` operation is sent to the database and the routine is saved.  If the validation fails (in this case, if the routine does not have a name or an assigned instrument_id) then the object will be marked as invalid and the SQL will not fire.  Otherwise, the object will be persisted to the database.  

ActiveRecord offers many different types of validations.  For my Instrument model I used `validates :name, uniqueness: { case_sensitive: false }, presence: true`.  This ensures that an instrument object will not be persisted to the database if the name is blank or if the name already exists in the database.  This validation also excludes case sensitivity.  The Item model utilizes `validates :description, :goal, presence: true, length: { maximum: 150 }`, which will ensure that each item description and goal will be present before persisting to the database.  Also, the description and the goal can not be more than 150 characters.

The User model not only utilizes a validation to ensure that the username and password are present and unique (`validates :username, presence: true, uniqueness: true`) and a minimum length (`validates :password, length: { minimum: 6}`), it also utilizes the `has_secure_password` macro to set and authenticate a user submitted password against a BCrypt password.

My Instrument model also uses a scope method.  Scoping is an ActiveRecord tool that allows you to query the database for specific information as a method call.  I wanted to be able to show the user the most popular instrument being used on the application by all users.  To do this I used a scope method `scope :most_routines, -> {joins(:routines).group(:id).order('count(instruments.id) desc’)}`.  What this line of code does is name the method `most_routines`, join the instruments table with the routines table, group them by id and then list them in descending order by the number of times an instrument_id occurs.  The instrument at the top of the list would be the one whose id number occurs the most often, making it the most popular instrument used.

## Controllers

This application has seven controllers, one for each model, as well as an ApplicationController, a UsersController and a SessionsController.  The model controllers handle the CRUD actions associated with their respective models.  The UsersController is responsible for the actions related to creating a new user and rendering their show page.  The SessionsController is responsible for logging a user in and out of their account.

Each controller inherits from the ApplicationController, which in turn inherits from ActionController::Base.  The ApplicationController has several helper methods that are accessible to all of the controllers.  There are the `logged_in?` and `current_user` methods, both of which are used in conjunction to determine who the current user is and if they are logged in.  There is a `require_login` method which requires a user to be logged in to their account in order to view specific pages.  There are also two methods, `set_routine` and `set_item`, which find the object in the database by the parameter provided in the URL and then bind that object to an instance variable.  All of these methods are used as a `before_action` declared throughout the controllers, which will run any method that you would like at the beginning of a controller action.  For instance, in the UsersController I declared `before_action :require_login, only: [:show]`, which will run the `require_login` method only for the show action.

Each controller action is wired to a route that is declared in the `routes.rb` file.  Most of the controllers use some if not all of the basic CRUD actions:  new, create, index, show, edit, update and destroy.  The routes declared in the `routes.rb` file point to a specific controller action.  For instance, `get ‘/routines’ => ‘routines#index’` declares that when a user tries to access the `/routines` path, the HTTP request will be routed to the index action in the RoutinesController.  The actions in the controller are declared just like any other Ruby method.  Any instance methods that are set within these actions will be accessible to it’s associated view when rendered.  So in the case of the index action in the RoutinesController, `@routines` is set equal to `current_user.routines`.  That collection of routines is then accessible in the index view in the routines directory via the `@routines` instance method.

Each of the controllers also use Strong Parameters, which help to protect unwanted user submitted data from entering the application.  For example, when a new item form is filled out and submitted, the attributes come through to the `create` controller action in the ItemsController.  The `item_params` method, which runs the code `params.require(:item).permit(:name, :description, :goal, :routine_id)`, requires the Item model and permits only the name, description, goal and routine_id from passing through to the controller.  The `create` action will then run the code `@item = Item.new(item_params)` to instantiate a new item with the parameters filtered through the `item_params` method.

## Views

Each model has it’s own individual directory in the views directory.  This is where each of the view files for that particular model is located.  These view files are named in accordance with their associated controller action.  So the index action in the RoutinesController will intrinsically render the index view in the routines folder in the view directory unless it is explicitly told to do otherwise.  This is another example of how Rails takes care of configuration through convention.  

The index views for each model use the collection of resources stored in an instance variable that is set in the controller action.  For example, the `@instruments` instance variable set in the index action in the InstrumentsController stores the collection of instruments associated with the current user and is iterated through in the instrument’s index view and is then displayed in an unordered list.  Most of the index views behave in the same way.  

The show views display details pertaining to a specific resource that is identified by it’s `id` number in the URL when it is passed via params to the show action in the controller.  Most of the show views offer links to either edit or delete the resource utilizing the Rails helper method `link_to` as well as the route helpers.  The following code is a simple way to create a link on a page:  `link_to "edit routine", edit_routine_path(@routine)`.  The `link_to` method generates the HTML `a tag`.  The `edit_routine_path(@routine)` part of the code is the route helper.  It generates the path to the specified route (in this case the edit view for the object stored in the @route instance method).  Lastly, “edit routine” is the text that shows up on the page. 

The new and edit views render forms that allow a user to either create a new resource or edit and update an existing one.  These forms are rendered using partials.  A partial template is another nice Rails tool that is used to eliminate duplicate code throughout your view files.  Because the new and edit forms for each resource are similar, if not exactly the same, you can move the form to another view file named `_form.html.erb`.  By doing this you can render the form in any other view file that you would like.  For instance, both the new and edit view files in the routines view directory use the code `render ‘form’` to render the form that is in the `_form.html.erb` file in the same directory.  This beats having to write the same code twice in both files.

The forms are generated using the `form_for` helper method provided by Rails.  Here’s an example of the form used to create and edit a routine:

	<%= form_for(@routine) do |f| %>
	  <div>
	    <%= f.label :name %>
	    <%= f.text_field :name %>
	  </div>
	  <br>
	  <div>
	    <h2>Select an instrument:</h2>
	    <%= f.collection_select(:instrument_id, Instrument.all, :id, :name, prompt: true) %>
	  </div>
	  <br>
	  <div>
	    <h2>If you can't find your instrument, create a new one below:</h2>
	    <%= f.fields_for(:instrument) do |i| %>
	      <%= i.label :name %>
	      <%= i.text_field :name, :value => ""  %>
	    <% end %>
	  </div>
	  <br>
	  <%= f.submit %>
	<% end %>

The `form_for` method takes in an object and then yields a block that generates the input fields.  The `f` variable is the FormBuilder that carries with it all of the knowledge about the object.  The `collection_select` method is used to generate a collection of all the instruments that have already been created and that are available to the user for selection.  If the collection of instruments does not provide the instrument that the user is looking for I wanted to be able to give the user another way to create a new one.  To do this, the form also utilizes the `fields_for` method which enables you to create another resource associated with the primary one.  In order for this to work properly I also used the method `accepts_nested_attributes_for :instruments` in the Routine model.  This creates an attributes writer on the model (i.e. `instrument_attributes=(attributes)`).  As a final step, I also needed to make sure that the `routine_params` method would permit these attributes with the following code: `params.require(:routine).permit(:name, :instrument_id, instrument_attributes: [:name])`.

## Standard Authentication and Omniauth

The last thing I would like to discuss is the authentication system I build for this application.  This app uses two systems for logging a user in:  a standard authentication system and a third party system using Omniauth.  The standard authentication system utilizes a form with an input field for the username and password.  A user fills out the form and then sends an HTTP post request to the create action in the SessionsController.  This action finds the user in the database using the username stored in params.  Then if the user if found and their password is authenticated using the `authenticate` method, a new session is created by creating a `:user_id` key in the session hash and setting it’s value equal to the `@user.id`.  The user is then redirected to their user show page, thereby completing the login cycle.

I also wanted to allow a user to log into their account using their gmail account credentials.  This is where OmniAauth comes in.  OmniAuth is a library that allows developers to create strategies to authenticate a user via multiple providers.  To do this, you first need to install a few gems:  `omniauth` - the Omniauth Library, `omniauth-google-oauth2` - the Oauth2 strategy for Google, and `dotenv-rails` - sets up the environment variables directly in the local `ENV` hash.  The next step is to register the application with the provider (in this case, Google).  This is when you will obtain the `client_id` and `client_secret`, which will be stored in the `.env` file.  The next step is to create an `omniauth.rb` file in the initializers folder in the config directory.  This is where code is written to tell Omniauth which provider you will be using.  It looks something like this:

	Rails.application.config.middleware.use OmniAuth::Builder do
	  provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET'], skip_jwt: true
	end

In the routes.rb file we also need to establish the callback route:  `get '/auth/google_oauth2/callback' => ‘sessions#omniauth’`.  This is where the provider will redirect the user in the callback part of the login cycle.

The `omniauth` action in the SessionsController is where we create the session for the user using their third party credentials.  The action implements a method called `create_user_with_google(auth)` that has been written in the User model.  This method takes in an argument `auth` which is a hash that holds the user’s information sent back from the provider.  The `create_user_with_google(auth)` method takes this information, specifically the email address, and finds or creates a new user with it.  The method also utilizes the `SecureRandom.hex` method to generate a random password for the user.  Once the user is found or created, a new session is created and the user is redirected to their user show page.
