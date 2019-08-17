---
layout: post
title:      "Sinatra Project Movieview"
date:       2019-08-17 20:16:51 +0000
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


	
	
