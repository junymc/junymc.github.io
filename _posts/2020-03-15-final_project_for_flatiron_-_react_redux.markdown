---
layout: post
title:      "Final Project for Flatiron - React/Redux "
date:       2020-03-15 17:52:32 -0400
permalink:  final_project_for_flatiron_-_react_redux
---

## Adventure Challenge
Whoo.. 4 weeks of this journey with React/Redux project is finally done! I feel like it was way longer than 4 weeks.. As always, I started with struggle and ended with gaining lots of knowledge from it.

I got this idea from a book that I received as a gift from my husband for our anniversary. The book is about doing things that I have no idea what it is till I scratch off from the list and putting pics as the proof after I finish the challenge. My App has the same concept. I have adventure list and it won't show exactly what it is till the user scratchs off the adventure. Then the user can see the name and description of the adventure and can post an evidnece with a photo.

I built this App with React/Redux Frontend and Rails API Backend. 
### Rails API Backend
In order to connect the backend with frontend, I needed to install `gem 'rack-cors'`. Rack::Cors provides support for Cross-Origin Resource Sharing (CORS) for Rack compatible web applications. The CORS spec allows web applications to make cross domain AJAX calls without using workarounds such as JSONP.

```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
    allow do
      origins 'localhost:3000'
      resource '*',
        headers: :any,
        methods: [:get, :post, :put, :patch, :delete, :options, :head],
        credentials: true
    end
  end
  Rails.application.config.action_controller.forgery_protection_origin_check = false
	```
	
	So, this cors.rb file in the config folder allows the frontend to access `localhost:3000` and communicate with backend.
	
I have 4 models and some nested routes associated with the models relationship. There's basic CRUD actions in the controller so user can signup/login and logout.

```
class Api::V1::UsersController < ApplicationController

    def create
        @user = User.new(user_params)
        if @user.save
            session[:user_id] = @user.id
            render json: @user, only: [:id, :username], status: 200
        else
            render json: {error: "Unable to create an account."}, status: 400
        end
    end

    def current_user
        @user = User.find_by(id: session[:user_id])
        if @user
            render json: @user, except: [:password_digest, :created_at, :updated_at], status: 200
        end
    end


    private
    def user_params
        params.require(:user).permit(:username, :password)
    end

end
```

Because of setting up the current user in the backend and render it to the frontend when it needs, the user is allowed to scratch(default value of scratch:false changes to true) the adventure and adding the evdence.

### React/Redux Frontend
When I started learning about the React, I was enjoying it so much! It was much easier than plain Javascript and the fact that it uses JSX makes it code less but woks same way.
What is JSX then? JSX is an XML/HTML-like syntax used by React that extends ECMAScript so that XML/HTML-like text can co-exist with JavaScript/React code. Easy way to explain is, JSX allows us to write HTML elements in JavaScript and place them in the DOM without any createElement()  and/or appendChild() methods and combine the Javascrip code and HTML like syntax in one file! How great it is?
