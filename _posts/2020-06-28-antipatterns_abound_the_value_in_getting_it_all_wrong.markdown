---
layout: post
title:      "**Antipatterns Abound: The Value in Getting it All Wrong**"
date:       2020-06-28 21:05:35 +0000
permalink:  antipatterns_abound_the_value_in_getting_it_all_wrong
---



### Introduction

I recently completed my first project as a novice programmer, a CLI Data Gem that scrapes data from a website and presents the information to the user.  I have been studying Object Orientation in Ruby for a few months now, but this project was really my first foray into what it’s like programming an application.  It was exciting, often times quite frustrating, but in the end a rewarding experience.

I started the project feeling confident that I understood the core concepts of object orientation, as well as how to demonstrate these concepts with the Ruby programming language.  However, the task of programming an application on my own presented challenges that I did not anticipate.  Coming up with a concept, practicing the necessary skills that would be required to complete the project, devising a plan and then setting that plan into motion took a lot of time and determination, but the most challenging aspect of the process was realizing that there were gaps in my knowledge and understanding of the Object Oriented paradigm that I did not realize were there until much later on in the development process.  

By then end of this whole process, I walked away with a new appreciation for what it means to make mistakes and how they can actually present unexpected opportunities for growth and deeper understanding.  I’ll walk you step by step through my entire process of creating this application and hopefully be able to demonstrate to you where I was when I began and inevitably where I ended up.


### Beginning the Project: Off to a Good Start

The first step I took to begin my project was to go through a planning stage.  I set out to answer some very basic questions:  What will my app do?  Where will I get the data?  What classes will I be using?  I started out by exploring different websites to see if I could come up with any good ideas based on the content I would come across.  I ended up finding a website called The Week.  The idea of the site is to provide the reader with all of the relevant information they would need for the week based on the current news cycle.  They even have a page dedicated to providing the reader with summaries of the top five news stories of the week.  Once I came across this I thought it would be fun to make a companion app called FiveThings to go with this site.  This way the user wouldn’t have to go to the website and instead they could just open up the app to quickly see the most important news stories.  I then decided that the best way to organize my app would be to have three separate classes:  the CLI class, which would be responsible for user interaction, a Scraper class, responsible for pulling the information off of the site and parsing it into useable data, and lastly an Article class, whose responsibility would be to instantiate and store new Article objects based on the data gathered by the Scraper class.

The next step was to make a user story.  Why would someone need or want to use this app?  What is their pain point?  How would this app make a user’s life easier?  After giving these questions some consideration I came up with the following:

The user would like to know what the top news stories are for the week. However, he/she does not have a lot of time and would like to read the top stories in a fast, concise way.  The user can quickly use the FiveThings app to take a look at the top five stories they should know for the week:

* The user starts the app and is greeted with a list of five headlines they could choose from
* The user then chooses a headline that interests them
* The user can then be shown the short article that gives them the information they need
* The user can then choose another article to read from the list or exit the app

After the user story was written the next step was to create a flow chart.  To do this I used the draw.io platform.  Creating the flow chart was really helpful in getting a visual understanding of how the user would interact with the application and how the classes would be collaborating together to provide the user with the information they requested.

Lastly, I set aside some time to think about how the app would flow from the user’s perspective.  I figured that they best way to work through this project would be to use a top down structure and start from the user’s point of view (specifically, the CLI class) and once I understood the architecture of the user experience I would then build out the other two classes.  To begin I simply wrote out how I wanted the CLI to work in plain english:

* user runs the program
* the program will greet the user and show them a list of 5 headlines
* then it will prompt the user to either select a headline from the list or type ‘e’ to exit the program
    * if the user types ‘e’, the program will exit
    * if the user selects a number, they will be shown the article
    * if they type anything else, the user will receive a message prompting them to try again
* assuming the user chose an article, they will then be shown the article and once they are finished reading they will be asked if they would like to view the list again or exit the program
    * if they choose to see the list again, the program will start from the top
    * if they choose to exit the program, the program will terminate
    * once again, if they type anything else, the user will receive a message prompting them to try again

All of this may seem a little unnecessary to write out, but I found this to be a really important part of the process.  Writing out the flow of the app in plain English made things a lot easier once I began to “translate” everything into Ruby code.


### Walking Through Creating a Gem

At this point I felt I was ready to begin coding.  But first I had to set up my gem.  This process seemed a little confusing at first, but after watching a couple of video resources on installing a gem and reading some documentation on how to create your own gem I felt a lot better about it.  Here I’ll walk through the steps on how to create your own gem:

* In the command prompt, cd into the directory where you will be creating your gem
* once there type `bundle gem your_gem_name` - I named mine `five_things` because I decided to name my app FiveThings
* At this point you will be asked if you would like to install rspec to create your own tests; this is something that I would like to explore in the future, but since I knew that I would not be writing my own tests for this project, I said no
* Next you will be asked if you would like to include the MIT License and a Code of Conduct.  The MIT License gives permission to other developers to reuse your application, as long as they include the copy of the original MIT license, making your application open source.  The Code of Conduct is a set of standards that is meant to foster a welcoming collaborative community of developers.  Both of these documents are worth having as a part of your gem.
* The next step would be to create your bin file and the class files; the bin file goes in your bin directory and does not require an extension; the bin file name typically coincides with the gem, so I named mine `five_things`; the class files go in your lib folder and are saved with the extension ‘.rb’ — these, too, are named accordingly (i.e. Article class => article.rb)
* Now that your bin file has been made, next you’ll need to set it up so that it can properly execute your program  .  At the top of the file goes the shebang line.  This is the absolute path to the interpreter and looks like this:  `#!/usr/bin/env ruby`.  This line of code specifies which program should be called to run the script.  In this case, it would be Ruby.  The bin file also needs to require your environment file and bundler/setup.  Your environment file has already been created for you and is named after your gem.  It is located in your lib folder and has the file extension ‘.rb’.  So to require my environment file, I typed `require ‘five_things’` in the bin file
* Speaking of the environment file, this will be the place where you’ll need to require all of your program files located in your lib folder (i.e all of your class files) along with any gems you plan on using (in this case ‘pry’, ‘nokogiri’ and ‘open-uri’)
* When your gem file is created, it is made with certain files that are already executable.  However, since we just made the bin file ourselves, we are required to do this manually.  To do this, cd into the bin directory and type the following command `chmod +x name_of_bin_file`;  once this is done, you will be able to run your program with the following command `./bin/gem_name`
* This would be a good time to set up your gemspec file; this is where you will fill out some information regarding your gem (a short summary and a description of your gem, your homepage, your email, etc.).  Here you will also need to add your dependencies.  For this project I needed to add a ‘pry’ development dependency and a ‘nokogiri’ dependency for scraping purposes.  To do this, you simply type `spec.add_development_dependency ‘pry’` and `spec.add_dependency ‘nokogiri’` at the bottom of the page.  At this point your gem will be all set up for you to start coding your application.


### The Process: Coding the CLI, Article and Scraper Domains

After I set up my gem I was ready to dig in and start coding the CLI class.  I started out by setting up a `#call` method, which would be the instance method called on by the newly instantiated CLI object in the bin file upon execution (i.e. `FiveThings::CLI.new.call`).  Within the `#call` method I listed out a couple of methods that would make up my call procedure:  `#greeting` and `#main_menu`.  I then proceeded to code a simple greeting in the `#greeting` method followed by a list of fake article titles in the `#main_menu` method.  The `#main_menu` also included an input instance variable set to `gets.strip` to allow the user to select an article.  At this point, I set up a control statement that said if the input entered by the user was within the range of 1-5 (because there are only 5 articles to choose from) they would then be shown the article via the `#show_article` method.  If the user were to type ‘e’, the program would bid them farewell and terminate.  (Note:  as you continue reading, you’ll see me make references to my classes with and without the namespace.  For example, I may say “CLI” instead of “FiveThings::CLI.”  Just note that I am always referring to the class name spaced to my application, even if I am not explicitly stating it.)

Lastly, if they were to type in anything else, the program would ask them to try again.  I then coded the `#show_article` method along with an `#option` method in a similar fashion.  I coded the `#show_article` method to display the article headline and body to the user.  At this point in the process, the headline and the body were the only two attributes I was going to give to the Article class because the articles posted on this page of the website did not have authors, so I figured this was the only information that I should scrape. Later on, you’ll see that I eventually decided to add another attribute to the Article class, the source.  When I was finished the class looked something like this:


    class FiveThings::CLI
      def call
        greeting
        main_menu
      end

      def greeting
        puts <<~MESSAGE
        Welcome to Five Things!

        Here you can find the top five news stories of the week
        to help you stay informed in a fast efficient way.

        MESSAGE
      end

      def main_menu
        puts <<~LIST
        Please select an item from the list below or type ‘e’ to exit:
        1. Article One
        2. Article Two
        3. Article Three
        4. Article Four
        5. Article Five
        LIST

        @main_menu_input = gets.strip

        if (1..5).includes?(@main_menu_input.to_i)
          show_article
        elsif @main_menu_input.downcase == ‘e’
          puts “See you later!”
        else
          puts “I’m not sure what you meant, lets try that again …”
          main_menu
        end
      end

      def show_article
        puts “Article Headline”
        puts “Article Body”
        options
      end

      def options
        puts “Would you like to see more articles? (y/n)”

        @options_input = gets.strip

        case @options_input.downcase
        when ‘y’
          main_menu
        when ’n’
          puts “Come back again soon!”
        else
          puts “I’m not sure what you meant, let’s try that again …”
          options
        end
      end
    end


This idea of coding the CLI class first with “dummy data” was really helpful because it allowed me to just focus on the flow of the interface without having to worry about how to have the CLI class collaborate with the Article and Scraper classes right away.  This ended up being my workflow throughout the entire process.

I decided to tackle the Article class next (fair warning — this is where things started to go awry, but bear with me for a while and we’ll course correct later on.)  As I stated previously, I initially only gave the Article class two attributes, the headline and the body.  I set these to the `attr_accessor` macro at the top of the class.  I then wrote a simple `#save` method to save all of the newly instantiated article objects in an `@@all` class variable, followed by a `.all` class method to read all of the saved articles.  Once I finished writing these methods I then decided to write a class method called `.current_articles` that would be responsible for instantiating all of the article objects using the data scraped by the Scraper class.  The Article class coded with “dummy data” looked like the following:

    class FiveThings::Article
      attr_accessor :headline, :body

      @@all = []

      def save
        @@all << self
      end

      def self.all
        @@all
      end

      def self.current_articles
        article_1 = self.new
        article_1.headline = “Article 1 Headline”
        article_1.body = “Article 1 Body”
        @@all << article_1

        article_2 = self.new
        article_2.headline = “Article 2 Headline”
        article_2.body = “Article 2 Body”
        @@all << article_2

        article_3 = self.new
        article_3.headline = “Article 3 Headline”
        article_3.body = “Article 3 Body”
        @@all << article_3

        article_4 = self.new
        article_4.headline = “Article 4 Headline”
        article_4.body = “Article 4 Body”
        @@all << article_4

        article_5 = self.new
        article_5.headline = “Article 5 Headline”
        article_5.body = “Article 5 Body”
        @@all << article_5
      end
    end

After I coded the article class I went back to the CLI class to see if I could re-code everything so that the two class could collaborate.  The methods that I needed to work on were `#call`, `#main_menu` and `#show_article`.  This is what I came up with:

    def call
      FiveThings::Article.current_articles
      @articles = FiveThings::Article.all
      greeting
      main_menu
    end

    def main_menu
      puts “Please select an item from the list below or type ‘e’ to exit:”

      @articles.each.with_index(1) do |article, i|
        puts “#{i}. #{article.headline}”
      end

      @main_menu_input = gets.strip

       if (1..5).includes?(@main_menu_input.to_i)
         show_article
       elsif @main_menu_input.downcase == ‘e’
         puts “See you later!”
       else
         puts “I’m not sure what you meant, lets try that again …”
         main_menu
       end
    end

    def show_article
      case @main_menu_input.to_i
      when 1
        puts “#{@articles[0].headline}”
        puts “#{@articles[0].body}”
        options
      when 2
        puts “#{@articles[1].headline}”
        puts “#{@articles[1].body}”
        options
      when 3
        puts “#{@articles[2].headline}”
        puts “#{@articles[2].body}”
        options
      when 4
        puts “#{@articles[3].headline}”
        puts “#{@articles[3].body}”
        options
      when 5
        puts “#{@articles[4].headline}”
        puts “#{@articles[4].body}”
        options
      end
    end

The `#call` method in the CLI class was now using the `.current_articles` method call on the Article class (i.e. `FiveThings::Articles.current_articles`) to instantiate new article objects and save them into the `@@all` class variable in the Article class.  Also, I decided to set an instance method, `@articles`, equal to `FiveThings::Article.all` so that I could then use the variable in any of my CLI instance methods (we’ll be revisiting this issue a little later).

The `#show_article` method was redesigned, as well, in order to display the attributes of the article object chosen by the user.  To do this I decided to write a case statement.  Basically, what this says is that in the case of `@main_menu_input.to_i`, when the value is equal to any number 1-5, `puts` the associated article headline and body to the terminal for the user to view.  I’ll explain how I got this to work.  At this stage of the development process, the `@article` instance was set to `FiveThings::Articles.all`, which returns all of the instantiated articles in the `@@all` class variable, which, in turn, stores all of the objects in an array.  I decided to access the article objects via their index number in this array.  The first article would have an index of 0, the second would have an index of 1, and so on and so forth.  So, the `#show_article` method accesses the headline and body of each article object separately (i.e. `@articles.headline[0]`, `@articles.body[0]`) and then “zips” them together for the user to view as one article (much more on this folly in a bit.)

At this point I felt I needed to test everything out to see if the CLI class was reading the the article attributes correctly.  Sure enough, everything worked.  All of the fake data that I coded into the Article class output to the terminal exactly the way I intended.  Later, we’ll get into why this was a false victory.  For now, I’ll explain how I went about coding the Scraper class to finish off my “first draft.”

The Scraper class initially had three methods, `#scrape_article`, `#scraped_headlines` and `#scraped_bodies`.  The `#scrape_articles` method was responsible for exactly what it sounds like — scraping the articles from the website.  I went about this by scraping each headline and body separately and in turn storing them separately in an array of headlines and an array of bodies:

    class FiveThings::Scraper
      def scrape_articles
        site = "https://theweek.com/5things"
        doc = Nokogiri::HTML(open(site))

        headlines = doc.css("div.five-things-headline p")
        bodies = doc.css("div.five-things-text p")

        @headlines_array = []
        headlines.each.with_index(1) do |h|
          @headlines_array << "#{h.text.strip}"
        end

        @bodies_array = []
        bodies.each do |b|
          @bodies_array << b.text.strip
        end
      end

      def scraped_bodies
        @bodies_array
      end

      def scraped_headlines
        @headlines_array
      end
    end

I then re-coded the `.current_articles` method in the Article class to work with the Scraper class as follows:

    def self.current_articles
      scrape = FiveThings::Scraper.new
      scrape.scrape_articles
      article_1 = self.new
      article_1.headline = scrape.scraped_headlines[0]
      article_1.story = scrape.scraped_stories[0]
      @@all << article_1
      article_2 = self.new
      article_2.headline = scrape.scraped_headlines[1]
      article_2.story = scrape.scraped_stories[1]
      @@all << article_2
      article_3 = self.new
      article_3.headline = scrape.scraped_headlines[2]
      article_3.story = scrape.scraped_stories[2]
      @@all << article_3
      article_4 = self.new
      article_4.headline = scrape.scraped_headlines[3]
      article_4.story = scrape.scraped_stories[3]
      @@all << article_4
      article_5 = self.new
      article_5.headline = scrape.scraped_headlines[4]
      article_5.story = scrape.scraped_stories[4]
      @@all << article_5
    end

Once I finished coding the Scraper class and recoding the Article class I ran the` ./bin/five_things` file from my terminal and discovered that …

It worked just fine.  All of the article headlines and article bodies were outputting to the terminal they way I had intended.  I have to admit that I was pretty happy with myself.  This was the first time I had ever programmed my own application.  I came up with a concept, devised a plan, set that plan in motion and with a lot of determination and persistence I was able to build this thing from scratch.  I felt incredibly accomplished, until I looked a little closer at what I had actually done.


### Everything Works, But What About the Design?: Discovering Antipatterns

So here’s the thing.  I knew that the application I built worked exactly the way I had wanted, but something felt off about it.  As I was looking through my work I noticed something peculiar — none of the patterns I had designed in the Article class looked familiar to me.  They did not resemble anything I had been seeing throughout my studies of Object Orientation with Ruby.  This is when I made an interesting discovery.  I began watching more video resources and reading more articles on building CLI Data Gems, just to make sure that I had covered all of my bases with my project, when I came across a new concept that I hadn’t heard before — Antipatterns.  

What are antipatterns?  Antipatterns are essentially any patterns in software design that would be considered bad practice.  They are the antithesis of “good” design patterns, that is to say, common patterns used in software development that are considered reusable solutions to reoccurring problems.  

Let’s use Object Orientation as an example.  In an Object Oriented paradigm, the goal is to instantiate new instances of objects that will then collaborate with each other in order to change the state of the program.  The relationships are what’s important here.  Without them, we wouldn’t really be “orienting” objects.  There would be no collaboration.  Hypothetically, if you were to build an app and design each domain so that they consisted of only class methods and no instances methods (in other words, *singleton classes*) all of the methods that you built would be called on the classes themselves and there would be no reason to instantiate new objects, therefore you would not be orienting objects to collaborate with one another.

Now let’s take a moment to look at an example of good design patterns in an Object Oriented paradigm.  Then we’ll take a look at an example consisting of antipatterns.  And *then*, once we’re through with all that, I’ll explain to you why I decided to start my project over again from the beginning.

Let’s say that you are building a movie theater application.  You decide that this application will show the user all of the movies playing at whichever theater they choose from a list of local theaters.  You then decide that in order for this to work, you will need a Movie Class and a Theater Class (we won’t worry about where the data is coming from for this example).  The Movie class is responsible for instantiating and storing new movie objects and the Theater class is responsible for instantiating and storing theater objects.  As you may have guessed, each theater will most likely be showing movies.  In order to represent that in our application, each theater object needs to be collaborating with movie objects.  We can imagine a scenario like the following:

    local_theater = Theater.new(“My Local Theater”)
    => #<Theater:0x00007fbfcd110488 @name=“My Local Theater”, @movies = []>

    new_movie = Movie.new(“Summer Flick”)
    => #<Movie:0x00007fbfcd1086e8 @name=“Summer Flick”>

    another_movie = Movie.new(“Big Blockbuster”)
    => #<Movie:0x00007fbfcc8818f8 @name=“Big Blockbuster”>

    local_theater.add_movie(new_movie)
    local_theater.add_movie(another_movie)

    local_theater
    => #<Theater:0x00007fbfcd110488 @name=“My Local Theater”, @movies = [#<Movie:0x00007fbfcd1086e8 @name=“Summer Flick”>, #<Movie:0x00007fbfcc8818f8 @name=“Big Blockbuster”>] >


    local_theater.now_playing
    => Summer Flick
       Big Blockbuster

The `#add_movie` and `#now_playing` methods are allowing for the collaboration of objects to occur.  We can imagine that the code for these methods might look something like the following:

    def add_movie(movie)
      @movies << movie
    end

    def now_playing
      @movies.each do |m|
        puts "#{m.name}"
      end
    end

These design patterns, albeit simple, demonstrate how to get your objects collaborating.  Now let’s take a look at an example of some antipatterns.  For argument’s sake, let’s say that we know that all of the theaters that we will be collecting data on will only be showing three movies at a time.  We might decide to collect our movie data as follows:

    class Movie
      attr_accessor :name, :runtime

      @@all = []

      def save
        @@all << self
      end

      def self.all
        @@all
      end

      def self.new_movies
        movie_1 = self.new
        movie_1.name = “Movie 1”
        movie_1.runtime = “2 hr”
        @@all << movie_1

        movie_2 = self.new
        movie_2.name = “Movie 2”
        movie_2.runtime = “2 hr 30 min”
        @@all << movie_2

        movie_3 = self.new
        movie_3.name = “Movie 3”
        movie_3.runtime = “1 hr 30 min”
        @@all << movie_3
      end
    end

We could then retrieve this data as follows:

    Movie.new_movies        #=> saves all of the movies in the @@all class variable

    Movie.all[0].name       #=> returns the name of movie_1
    Movie.all[0].runtime    #=> returns the runtime of movie_1
    Movie.all[1].name       #=> returns the name of movie_2
    Movie.all[1].runtime    #=> returns the runtime of movie_2
    Movie.all[2].name       #=> returns the name of movie_3
    Movie.all[2].runtime    #=> returns the runtime of movie_3

This pattern may hold up fine if our theaters only show three movies.  But what happens if one of the theaters starts showing five movies?  Or ten movies?  Such a small change quickly reveals the flaws in this antipattern.  There’s no way to easily accommodate for such a seemingly simple update.  On the other hand, we can see how in the first example we could easily add as many movies as we need to a theater and the correct information would still be output to the terminal.  As developers we need to be able to adapt our programs whenever necessary.  Good design patterns make this an easier task.

You might be wondering why the previous antipattern example looks so familiar.  Well, that’s because I’ve shown it to you twice.  The antipatterns present in the fake movie theater app we built mirror almost exactly what I did in the Article class in my Five Things app.  Now, with all humility, let me walk you through the rest of the mistakes I made while building this application.

As I’ve just mentioned, the Article class was built in a similar fashion to the fake movie theater app with antipatterns.  The real problem here is that this class is not built abstractly enough.  If left alone, there would be no simple way for me to expand upon or extend the functionality of my application.  The article class will only save 5 articles, but if later I decided to save more articles without changing the current pattern, I would need to add them in one at a time.  This would be an unacceptable and unfeasible method to sustain.  The other issue with my article class as it stands is that, save for the `#save` method, there aren’t any instance methods — I basically built a singleton class.  The majority of the functionality built into the article class is acted out by the article class itself via it’s class methods, specifically the `.current_articles` method.  If this scenario were to persist, my application wouldn’t stand a chance at demonstrating real object collaboration.  

The CLI class also has some issues.  For starters, I set an instance variable equal to a class method (`@articles = FiveThings::Articles.all`).  Typically, if using good design patterns in an Object Oriented paradigm, this would never be done.  There’s no consistency here.  Generally speaking, if you were to set an instance variable equal to an object receiving a method call, chances are it would most likely be an instance method sending the call to the object.  Same goes for class variables and class methods.  Keeping usage of these variables and methods consistent makes more sense because they belong to the same scope of the program (i.e. instance scope, class scope).

The other problem with the CLI class occurs in the `#show_articles` method.  Let’s take another look at that:

    def show_article
      case @main_menu_input.to_i
      when 1
        puts “#{@articles[0].headline}”
        puts “#{@articles[0].body}”
        options
      when 2
        puts “#{@articles[1].headline}”
        puts “#{@articles[1].body}”
        options
      when 3
        puts “#{@articles[2].headline}”
        puts “#{@articles[2].body}”
        options
      when 4
        puts “#{@articles[3].headline}”
        puts “#{@articles[3].body}”
        options
      when 5
        puts “#{@articles[4].headline}”
        puts “#{@articles[4].body}”
        options
      end
    end

The problem here is that instead of accessing attributes from an object and `puts`-ing the data to the terminal, I’m separately accessing the data from the `FiveThings::Article.all` array currently being stored in the `@articles` instance variable (which has no business being an instance variable to begin with) via the index number of each headline and body attribute and then “zipping” the data back together to create the appearance of a unified object.  This is not good.  This is anti-good.

But here’s the good news.  When I went into this project, I started off feeling that I had a solid understanding of the core concepts of object oriented programming.  I had been working through my studies without any real issues and I knew that I was taking away all of the relevant information from each lesson.  However, there was obviously a gap in my understanding and it wasn’t until I jumped in and did everything wrong that the gap finally started to close, a gap that I didn’t even realize was there.  And now that I could see all of my glaring errors staring me in the face I knew exactly what I needed to do.

Reboot.


### Back to the Drawing Board:  Redesigning the App with a Focus on Patterns

Learning about the antipatterns in an Object Oriented paradigm helped to solidify the key concepts of object orientation for me.  Now that I could see that my application was riddled with antipatterns, I decided that I needed a clean slate.  So, I started from the top.  Well, not the *very* top.  I still had my concept and my plan.  That hadn’t changed.  I just needed to recode my three classes.  The decision to do this was admittedly daunting, but I felt like it would be good practice to just begin again.  For me, this was meant to be a learning experience anyway.  And, as an added bonus, while recoding the app I came up with some new ideas to implement previous methods and new methods that hadn't originally occured to me the first time around.

My workflow remained the same:  I coded the CLI class first with dummy data to understand the flow and architecture of the user interface.  I then coded the Article class with more familiar patterns that I had been seeing throughout my studies and then recoded the CLI class to collaborate with article objects.  Lastly, I recoded the Scraper class in a much simpler, cleaner way by first grabbing the entire article element and then by iterating through each article and setting the headline, body (and now, source) attributes equal to the corresponding elements from the site.  This is how each class turned out.  (You’ll notice some new functionality in the CLI and Article classes.  Don’t worry.  It’ll make sense in a moment):

##### The CLI Class


    class FiveThings::CLI
      def call
        FiveThings::Scraper.new.scrape_articles
        greeting
        menu
      end

      def greeting
        puts <<~MESSAGE
        Welcome to the Five Things app!

        Here you can find the top five news stories of the week
        to help you stay informed in a fast efficient way!

        MESSAGE
      end

      def menu
        puts "-----------------"
        puts "--- Main Menu ---"
        puts "-----------------"
        puts ""
        puts "Please select an item from the list below"
        puts "Type 1-5 to choose which article you would like to view"
        puts "Type 'e' to exit:"
        puts ""
        article_list
      end

      def article_list
        FiveThings::Article.all.each.with_index(1) do |article, i|
          puts "#{i}. #{article.headline}"
        end

        @list_input = gets.strip

        if @list_input.downcase == 'e'
          puts "See you next time!"
          exit
        elsif (1..5).include?(@list_input.to_i)
          show_article
        else
          puts "I'm not sure what you meant.  Let's try that again ..."
          puts ""
          sleep(2)
          menu
        end
      end

      def input_to_index(input)
        input.to_i - 1
      end

      def headline_frame
        symbol = "-"

        if @article.headline.size <= 80
          symbol * @article.headline.size
        else
          symbol * 80
        end
      end

      def show_article
        @article = FiveThings::Article.find_by_index(input_to_index(@list_input))

        puts headline_frame
        puts "#{@article.headline}"
        puts headline_frame
        puts ""
        puts "Word Count: #{@article.word_count}"
        puts ""
        puts "#{@article.body}"
        puts ""
        puts "Source: #{@article.source}"
        puts "------"
        puts ""

        @timer = FiveThings::Timer.new
        @timer.start
        finished_reading?
      end

      def finished_reading?
        puts "Type 'y' and press 'enter' when finished reading."

        @finished_reading_input = gets.strip

        case @finished_reading_input.downcase
        when 'y', 'yes'
          @timer.stop
          @timer.convert

          puts "---------------------"
          puts "--- Reading Stats ---"
          puts "---------------------"
          puts ""
          puts "It took you #{@timer.minutes} minute(s) and #{@timer.seconds} second(s) to finish this article."
          puts "That means your reading speed is approximately #{@timer.reading_speed(@article.word_count)} wpm."
          puts "Great Job!"
          puts ""

          option_message
        else
          puts "I'm not sure what you meant. Let's try that again ..."
          puts ""
          sleep(2)
          finished_reading?
        end
      end

      def option_message
        puts "Would you like to view the list of articles again? (y/n)"

        @option_input = gets.strip

        case @option_input.downcase
        when 'y', 'yes'
          menu
        when 'n', 'no'
          farewell
        else
          puts "I'm not sure what you meant. Let's try that again ..."
          puts ""
          sleep(2)
          option_message
        end    
      end

      def farewell
        puts "Come back again soon!"
        exit
      end
    end

##### The Article Class

    class FiveThings::Article
      attr_accessor :headline, :body, :source

      @@all = []

      def save
        @@all << self
      end

      def self.all
        @@all
      end

      def self.find_by_index(index)
        self.all[index]
      end

      def word_count
        @words = self.body.split.size
      end
    end

##### The Scraper Class

    class FiveThings::Scraper
      def scrape_articles
        site = "https://theweek.com/5things"
        doc = Nokogiri::HTML(open(site))

        doc.search("div.five-things-item").each do |a|
          article = FiveThings::Article.new
          article.headline = a.search("div.five-things-headline p").text.strip
          article.body = a.search("div.five-things-body p").text.strip
          article.source = a.search("span.source").first.text.gsub("\t", "").gsub("\n", "").gsub("Source:", "").strip
          article.save
        end
      end
    end

The Article class no longer utilizes a .`current_articles` class method.  This method was, in a way, “manually” creating a limited number of article objects.  The class no longer possesses this limitation and can technically instantiate as many article objects as we please.  There are now both instance and class methods that make up the domain of this class, as well.  The `#save` method allows each instance of the class to save itself to `@@all`.  Each instance can also return the word count of their body attribute by using the `#word_count method`.  Using the `.all` method the class itself can return every object that has been instantiated and saved to `@@all`.  It can also search for and return an object by it’s index in the `@@all` array by using the `.find_by_index` class method.

The Scraper class now only has one instance method, `#scrape_articles`.  Pretty simple, right?  This method iterates through each article element whereby a new article object is instantiated and then each attribute (i.e `article.headline`, `article.body`, `article.source`) is set equal to it’s corresponding CSS element.  Using the `#save` method from the Article class, each object is then saved and stored in the `@@all` class variable of the Article class.

Since the `.current_article` class method no longer exists, the CLI class no longer utilizes it to obtain the current article objects.  Instead, a newly instantiated scraper object receives a method call from `#scrape_articles`.  This takes care of all of the scraping and saving of article objects in one fell swoop.  A new method, `#article_list`, is responsible for listing the current articles by iterating through `FiveThings::Article.all` and `puts`-ing the headlines to the terminal, along with their corresponding index offset by 1 (the index is offset by 1 so that we aren’t listing article headlines starting with the number 0).  Another new method, `#input_to_index`, is used in conjunction with the `.find_by_index` class method of the Article class.  This method was built in order to use the user’s input to find the selected article via it’s corresponding index of the array in the `@@all` class variable of the Article class.

The `#show_article` method also comes with some upgrades.  It no longer “zips” the data back together because it is no longer necessary.  All of the attribute data that we need is exactly where it belongs — with each instance of the Article class.  Now, we use the `.find_by_index` method with the `#input_to_index` method to grab the correct article object and then `puts` the attribute data to the terminal for the user to view.

Aside from focusing more on the design patterns, one of the key differences in my thought process the second time around was in maintaining the idea that each domain should have a clear role:  the CLI class’ sole responsibility should be to provide the user with an interface to view the information; the Article class’ sole responsibility should be to instantiate and store article objects, along with providing functionality specifically related to the article class or instances (as in the `#word_count` or the `.find_by_index` methods); the Scraper class’ sole responsibility should be to, well, scrape.  THAT’S IT.  Keeping this in mind helped me to design a better application that is much more readable and can be extended upon in a much simpler way then the first iteration.


### Some Added Functionality:  The Timer Class

After redesigning my application with better design patterns and getting everything to work and look the way I wanted, I decided that it might be nice to add some more functionality that wasn’t in the original plan.  As I was recoding the application I had an idea in the back of my mind that it might be fun to time how long it takes for the user to read an article and then provide them with reading statistics, like how long it took them to read the article and what their approximate reading speed is in words per minute.  You may have noticed that the updated CLI class has a new instance variable named `@timer` that is set equal to an object called `FiveThings::Timer.new`.  Well, that’s because I decided to go ahead and try my idea out.

Initially, I started to program some timer functionality directly into the CLI class.  Fortunately, I realized pretty quick that I was falling into the same trap that I had earlier:  I wasn’t thinking about the design and its implications.  I asked myself, should it be the responsibility of the CLI class to time the user’s reading speed?  After some consideration, I decided that it wasn’t.  The CLI class is responsible for interfacing with the user — THAT’S IT.  So, which class would be responsible for carrying out the functionality of a timer?  The Article class?  The Scraper class?  The answer was that I didn’t currently have a domain that made sense to carry out the tasks of a timer.  However, the solution was fairly simple — just make a new timer class!  The best part about this is that I didn’t have to change *anything* involving the Article or Scraper class because each class has it’s own separate responsibilities.  The Timer class doesn’t know anything about articles or scraping.  It just knows how to perform the functions of a timer.  Being able to extend upon the program with this kind of flexibility is possible when you consider the design of your program and do your best to maintain the principle of *single responsibility.*  Take a look at the new domain.  The methods should be pretty self explanatory:

##### The Timer Class

    class FiveThings::Timer
      def start
        @start_timer = Time.now
      end

      def stop
        @stop_timer = Time.now
      end
			
      def difference
        @difference = @stop_timer - @start_timer
      end

      def convert
        @minutes = self.total_in_seconds / 60
        @seconds = self.total_in_seconds % 60
      end

      def total_in_seconds
        @total_time_in_seconds = self.difference
      end

      def reading_speed(word_count)
        speed = (word_count/self.total_in_seconds) * 60
        speed.round
      end

      def minutes
        @minutes.round
      end

      def seconds
        @seconds.round
      end
    end


### Last, But Not Least — The Refactoring Stage

Refactoring is just like the proofreading/editing stage of writing a paper.  Once you’ve said what you want to say, you then go back and try to say it better.  You get rid of anything that can be deemed superfluous.  You also try your best to make what you have written easy and enjoyable to read.

As I was proofreading my code, I tried to figure out a way to either minimize or further abstract what I had already done without changing the functionality of the program.  If I saw that a method was really long, I asked myself if maybe there was a way to extract a portion of the method into a method of it’s own.  The way I see it, odds are if you wrote a method that seems to be too long, it’s probably taking on more responsibility then it should.  I tried to stick to the same *single responsibility principle* with my methods as I did when redesigning my classes.  Keeping this in mind helped when moving through the refactoring process.

I also spent time trying to format all of the methods in a similar fashion to promote an easy reading experience (i.e. proper indentation, adding line spacing to separate ideas within a method, etc.)  Writing code isn’t just about getting things to work.  You also want to spend enough time making sure that your code can be read and easily understood by another developer because you will most likely be spending much of your professional time working on teams and collaborating with others.


### Conclusion

As I said at the beginning of this article, this was my first foray into programming an application.  This app is by no means perfect and I am very eager to learn more about the principles behind planning and designing an application.  However, even though there is much more to learn, I do feel that by going through this process of designing an application on my own and then dealing with the consequences of my mistakes I now have a deeper understanding of what it means to program within an object oriented paradigm and how to go about implementing those principles in my work.

Having to face the mistakes that you have made can be a gift if you let it.  Yes, it is always frustrating to learn that you made an error, or to learn that there were gaps in your understanding that became evident to you much further into the development process than you would have liked.  However, as developers, the learning process will continue well into our careers and the very nature of our work requires us to get comfortable with confronting errors and finding solutions to problems.  With every new error that you confront and with every problem that you solve you start to gain perspective.  And as you continue to gain perspective, the hidden gaps will start to close and concepts will be solidified.  Well, at least until your next error message.  Then you’ll begin to rexamine everything that you *think* you know.  And that’s the fun part.

