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
 
## Set Up

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

The controller relays data from the browser to the application, and from the application to the browser. Using separated controllers for each model helps organize all the different routes and actions. `users_controller` controls signup/login and home routes. `movie_controller` focuses on the movie list and indivisual movie reviews. `reviews_controller` deals with the most part of the application’s functionality which is posting, editing and deleting the reviews. Along with these controllers, `application_controller' makes the other controller do their job easily with `configure do` and `helpers do`.

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

end

```
