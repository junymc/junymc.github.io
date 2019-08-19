---
layout: post
title:      "Sinatra Project Movieview"
date:       2019-08-17 16:16:52 -0400
permalink:  sinatra_project_movieview
---

I really enjoyed completing this project and felt I learned more than our initial project with CLI. I also felt more comfortable because I had a better grasp and understanding of what what was necessary to finish it. It's a simple application and I was able to accomplish it with the requirements listed below:
 
```
 - MVC(Model View Controller)
 - RESTful routes
 - ActiveRecord(Models, Associations)
 - A sqlite database
 - Login/Logout
 ```
 
 This project is building a simple Content Management System (CMS) utilizing the tools we've learned so far.
The application is capable of alllowing users to register/sign up with a username and password.  Once they have registered an account they are able to create, edit, or delete their own movie review posts. They can modify their own entries, but are limited to viewing other user's posts. 

 
 The code below was used to set this application up:
 
## Setting Up

### Structure

I had to create all the files and folders from scratch. Here's what MVC structure looks like:
 ```
 
├── app
│   ├── controllers
│   │   └── application_controller.rb
│   │   └── movies_controller.rb
│   │   └── reviews_controller.rb
│   │   └── users_controller.rb
│   ├── models
│   │   └── movie_review.rb
│   │   └── movie.rb
│   │   └── user.rb
│   └── views
│          ├── movies
│          │   └── index.erb
│          │   └── show.erb
│          ├── reviews
│          │   └── edit.erb
│          │   └── index.erb
│          │   └── new.erb
│          ├── users
│          │   └── home.erb
│          │   └── login.erb
│          │   └── signup.erb
│          ├── error.erb
│          ├── index.erb
│          └── layout.erb
├── config
│   └── environment.rb
├── db
│   ├── migrate
│   └── seeds.rb
├── public
│   └── css
│   └── style.css
├── config.ru
├── Gemfile
├── README.md
├── Rakefile
└── spec.md

```


### Gemfile

Add all these required gems for the application below and simply type `bundle install` in your terminal then it will install all the gems including the `Gemfile.lock`.

```
source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', '~> 4.2', '>= 4.2.6', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3', '~> 1.3.6'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem 'tux'

group :test do
  gem 'rspec'
  gem 'capybara'
  gem 'rack-test'
  gem 'database_cleaner', git: 'https://github.com/bmabey/database_cleaner.git'
end
```


## MVC (Model View Controller)

### Model  :  The logic of a web application


The model is where data is managed. Models represent the data required for an application to work using ActiveRecord. The `User` class has write and edit permissions to the  `MovieReview` class. The `Movie` class contains all the reviews that end-users write. The `User` and `Movie` class  is related with the `MovieReview` class through the has_many ActiveRecord association.  `MovieReview` belongs to `User` and `Movie`. These associations work and successfully runs applications by using ActiveRecord making it very convenient.

 
 ```
 class MovieReview < ActiveRecord::Base
    belongs_to :user
    belongs_to :movie
end

class Movie < ActiveRecord::Base
    has_many :movie_reviews
    has_many :users, through: :movie_reviews
end

class User < ActiveRecord::Base
    has_secure_password
    has_many :movie_reviews
    has_many :movies, through: :movie_reviews
end
```


### View : The 'front-end', user-facing part of a web application


The front-end or user-facing portion of an application is where the end-user interacts directly with an application. `Views` are generally made of HTML and CSS code. Sinatra uses `erb` files (Embedded Ruby) to dynamically create
html web pages and serve requested content through the browser.  The `Views`  correspond to the routes/actions in the associated controllers so there are `Views` for showing movie lists/reviews, displaying sign-up or login forms etc. 


```
<form method="post" action="/reviews">
   Movie Name : <input type="text" name="movie_name">
   <br><br>
   Review : <textarea name="review" id="review" cols="30" rows="5"></textarea>
   <br><br>
   Rate : 1<input type="radio" name="rate" value="1">
          2<input type="radio" name="rate" value="2">
          3<input type="radio" name="rate" value="3">
          4<input type="radio" name="rate" value="4">
          5<input type="radio" name="rate" value="5">
   <br><br>
   <input type="submit" value="Submit">
   <br>
   <p> <a href="/home">Home</a> </p>
</form>
```


### Controller : The go-between for models and views.



The controller bridges data between the application and the browser.  Separated controllers for each model help organize all the different routes and actions an application can use.The  `users_controller` controls signup/login and home routes. The `movie_controller` focuses on the movie list and individual movie reviews. The  `reviews_controller` handles the most of the applications functionality to include, CRUD or the posting, editing, and deleting review functions.
The  `application_controller` inherits the `configure` , `helpers` and other methods so they do not need to be repeated in every controller. 


```
require "./config/environment"

class ApplicationController < Sinatra::Base

    configure do
        set :views, 'app/views'
        set :public_folder, 'public'
        enable :sessions
        set :session_secret, "password_security"
    end

    helpers do
      def logged_in?
         !!session[:user_id]
      end

      def current_user
		  User.find_by(id: session[:user_id])
      end

     def authorize
         if !logged_in? || current_user.nil?
             redirect '/login'
         end
     end

     def clean(text)
         Rack::Utils.escape_html(text)
     end
		 
end

    get '/' do
        erb :index
    end

    not_found do
        @msg = "This resource was not found."
        status 404
        erb :error
    end

end
```



## Environment

### config.ru

In order to run the application successfully it would require me to have all the files and code, but also ensure that the environment is set up correctly. 


```
require './config/environment'

if ActiveRecord::Migrator.needs_migration?
  raise 'Migrations are pending. Run `rake db:migrate` to resolve the issue.'
end

use Rack::MethodOverride
use ReviewsController
use MoviesController
use UsersController
run ApplicationController
```


After everything is set up correctly I run the application by typing the following command:

```
 > shotgun
```


This summarizes the main part of the Sinatra project.


## Conclusion

Using ActiveRecord made this project incredibly easier to complete because of things like RESTful route that make data manipulation easier. At times I was confused, but towards the end of the project all the concepts seemed to come together and make more sense. I have to admit that coding in itself is definitely a challenge combined with the language barrier makes everything more of a struggle, but it also makes completing something that more satisfying. With each struggle and overcoming each obstacle I am getting closer and closer to my end goal of becoming a developer. 





