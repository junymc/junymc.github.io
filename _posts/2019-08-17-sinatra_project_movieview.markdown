---
layout: post
title:      "Sinatra Project Movieview"
date:       2019-08-17 16:16:52 -0400
permalink:  sinatra_project_movieview
---

 I've learned a lot during this project and had more fun than the first project. Also I felt like I know what I was doing compared to the previous project. It's still a simple application but I built it with all these:
 
```
 - MVC(Model View Controller)
 - RESTful routes
 - ActiveRecord(Models, Associations)
 - A sqlite database
 - Login/Logout
 ```
 
 This project is building a simple Content Management System (CMS) using the tools we've learned so far.
 It can track the reviews of movies from users and user needs to sign up to see or wirte reviews. Once the user is logged in, they are able to create, edit and delete their own reviews but they will still able to see other user's posts.
 
 Let me show you how I built and what it does.
 
## Setting Up

### Structure

I had to create all the files and folders from scratch. Here's the MVC structure looks like:
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

 This is where data is manipulated and/or saved. Models would represent the data necessary for the application to work using Activerecord. `User` which would write and edit `MovieReview`, `Movie` which has all the reviews that users write. `User` and `Movie` has many relationship with `MovieReview` and `MovieReview` belongs to `User` and `Movie`. These associations work and successfully runs application by using ActiveRecord. How convenient it is!
 
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

This is the only part of the app that user interacts with directly. Views generally consist of HTML and CSS. Sinatra uses `erb` files (Embedded Ruby) to dynamically generate html web pages and serve requested content. The views correspond to the routes/actions in the associated controllers, so there are views for showing movie list and reviews, displaying a signup/login form, and so on.

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

The controller relays data from the browser to the application, and from the application to the browser. Using separated controllers for each model helps organize all the different routes and actions. `users_controller` controls signup/login and home routes. `movie_controller` focuses on the movie list and indivisual movie reviews. `reviews_controller` deals with the most part of the application’s functionality which is posting, editing and deleting the reviews. `application_controller` inherits `configure`, `helpers` and other methods to the other controllers so those don't need to be repeated in every other controllers.

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
In order to run this application, I will need all those files and code but also I have to make sure that I set up environment correctly. 

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


After everyting is set up correctly and I can check my apllication by typing:

```
shotgun
```


These are the main part of this project. Of course there's much more to talk about what I've learned and used in this Sinatra project.


## Conclusion

Using ActiveRecord is the most part that I enjoyed since it's such a great framework. It made my work much easier! I learned about RESTful route which provides mapping between HTTP verbs(get, post, put/patch, delete) to controller CRUD actions. There was time that I was confused but at the end, I understand what exactly they do.
I feel so great after all of the strugles. I just took another step to my goal which is becoming a developer. My journey continues and I will learn more and more till I get to my goal!
