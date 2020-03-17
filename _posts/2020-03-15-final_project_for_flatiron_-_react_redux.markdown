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
React is a JavaScript library created by Facebook. 
When I started learning about the React, I was enjoying it so much! It was much easier than plain Javascript and JSX makes me code less but it woks the same way.
What is JSX then? JSX is an XML/HTML-like syntax used by React that extends ECMAScript so that XML/HTML-like text can co-exist with JavaScript/React code. Easy way to explain is, JSX allows us to write HTML elements in JavaScript and place them in the DOM without any createElement()  and/or appendChild() methods, basically, it combine the Javascript code and HTML like syntax in one file! How great it is?

Here is the one of the components.


```

import React from 'react'
import { withRouter } from 'react-router-dom'

class Home extends React.Component {

    componentDidMount() {
        this.props.get_token()
        this.props.setCurrentUser()
    }

    deleteHandler = () => {
        this.props.logoutUser(this.props.token)
        console.log("Logged out")
        this.props.history.push("/logout")
    }

    render() {
        return (
            <div>
                <h1 id="home-top">Welcome to </h1>
                <h3 id="home-middle">the</h3>
                <h1 id="home-bottom">Adventure Challenge</h1>
            </div>
        )
    }
}

export default withRouter(Home);

```


React components implement a render() method that takes input data and returns what to display.
Components are independent and reusable bits of code. It has properties called `props` and parent component pass it down to its child component just like arguments.
In my application, App.js is the very top parent component of all. It pass down user, token and setCurrentUser to most of the components so they can access to those props.


```

import React from 'react'
import './App.css'
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom'
import NavBar from './components/NavBar'

import AdventuresContainer from './containers/AdventuresContainer'
import EvidenceContainer from './containers/EvidenceContainer'
import AdventureEvidence from './components/evidences/AdventureEvidence'
import Home from './components/Home'
import LoginContainer from './containers/LoginContainer'
import SignupContainer from './containers/SignupContainer'
import User from './components/users/User'
import Logout from './components/Logout'
import { getToken } from './actions/auth'
import { signup, login, logout, setCurrentUser } from './actions/user'
import { connect } from 'react-redux'
import UserContainer from './containers/UserContainer'

class App extends React.Component {

componentDidMount() {
    this.props.get_token()
    this.props.setCurrentUser()
  }  

render() {
  return (
    <Router>
      <div className="App">
        <NavBar user={this.props.user}/>
        <Switch>
          <Route exact path="/adventures">
            <AdventuresContainer 
            token={this.props.token} 
            setCurrentUser={this.props.setCurrentUser}
            user={this.props.user}/>
          </Route>
          
          .  .  .
					
          <Route path="/logout">
            <Logout />
          </Route>
          <Route exact path="/" >
            <Home 
            get_token={this.props.get_token} 
            token={this.props.token} 
            setCurrentUser={this.props.setCurrentUser}
            logoutUser={this.props.logoutUser} 
            user={this.props.user}/>
          </Route>
        </Switch>
      </div>
    </Router>
  );
 }
}

const mapStateToProps = state => {
  return {
    token: state.csrf_token,
    user: state.user
  }
}

const mapDispatchToProps = dispatch => ({
    get_token: () => dispatch(getToken()),
    signupUser: (token, username, password) => dispatch(signup(token, username, password)),
    loginUser: (token, username, password) => dispatch(login(token, username, password)),
    logoutUser: token => dispatch(logout(token)),
    setCurrentUser: () => dispatch(setCurrentUser())
})

export default connect(mapStateToProps, mapDispatchToProps)(App);

```


As you see my App class is wrapped with `connect`. This is imported from `react-redux`.
Redux is a state container for JavaScript apps. It makes it easy to manage the state of my application. It took me a while to understand these `state` and `props`. 
State is a JavaScript object that stores a component's dynamic data and determines the component's behaviour. It is local or owned by that specific component. Because state is dynamic, it enables a component to keep track of changing information in between renders and for it to be dynamic and interactive. Also state can only be used within a class component. So I have to map the state to props so I can pass it down to child component. They are immutable and read-only in the child component.
So basically Redux is a store for the data, and you can do three things with the data. You can call the action which is event and this is the only way you can send data from your application to your Redux store. The data can be from user interactions, API calls, or even form submissions. The action must contain the type property and the payload to be stored.


```

export const signup = (csrf_token, username, password) => {
    
    return async function (dispatch) {
        try{
            const res = await fetch("http://localhost:3001/api/v1/signup", {
                method: 'POST',
                headers: {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json',
                    'X-CSRF-TOKEN': csrf_token
                },
                body: JSON.stringify({user: {username, password}}),
                credentials: 'include'
            })
            if(!res.ok){
                throw res
            }
            const user = await res.json()
            dispatch({
                type: 'SET_USER',
                payload: user
            })
        }catch(error){
            console.log(error)
        }
    }
}

```


Above the file is my user action. It send the data from the form and post request to the Rails API and store the user in the payload. This action type and payload is set in the reducer file. Reducer takes the previous state of the app and return a new state based on the action passed to it. Then all of this data will be stored in the redux store.


```

import React from 'react';
import ReactDOM from 'react-dom';

import './index.css';
import App from './App';

import rootReducer from './reducers'

import { Provider } from 'react-redux'
import { createStore, applyMiddleware, compose } from 'redux'
import thunk from 'redux-thunk'

const store = createStore(rootReducer,
compose(
    applyMiddleware(thunk),
    window.devToolsExtension ? window.devToolsExtension() : f => f
  ))

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>, 
document.getElementById('root')
);

```


As you see the code above, the most important thing you need to have connection with backend is this `react-thunk`.
Redux Thunk middleware allows you to write action creators that return a function instead of an action. Thunk is a functional programming technique used to delay computation. Instead of performing some work now, you produce a function body or unevaluated expression (the “thunk”) which can optionally be used to perform the work later. 
