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
│            └── style.css
├── config.ru
├── Gemfile
├── README.md
├── Rakefile
└── spec.md

```
```
 
  |── app
  |             |── controllers
  |             |     └ application_controller.rb
  |             |     └ movies_controller.rb
	|             |     └ reviews_controller.rb
	|             |     └ users_controller.rb
	|             |── models
	|             |     └ movie_review.rb
	|             |     └ movie.rb
	|             |     └ user.rb
	|           └── views
	|                            |── movies
	|                            |    └ index.erb
	|                            |    └ show.erb
  |                            |── reviews
	|                            |    └ edit.erb
	|                            |    └ index.erb
	|                            |    └ new.erb
	|                            |── users
	|                            |    └ home.erb
	|                            |    └ login.erb
	|                            |    └ signup.erb
	|                            |── error.erb
	|                            |── index.erb
	|                          └── layout.erb
	|── config
  |             └─ environment.rb
	|── db
	|             |── migrate
	|             |     └ create_users.rb
	|             |     └ create_movies.rb
	|             |     └ create_movie_reviews.rb
	|             |── schema.rb
	|           └── seeds.rb
	|── public
  |             └─ css
	|                   └ style.css
	|── config.ru
	|── Gemfile
	|── License.md
	|── Rakefile
	|── README.md
└── spec.md
```

## MVC(Model View Controller)
### Model : The logic of a web application
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
