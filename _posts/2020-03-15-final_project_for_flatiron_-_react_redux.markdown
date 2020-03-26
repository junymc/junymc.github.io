---
layout: post
title:      "Final Project for Flatiron - React/Redux : Adventure Challenge"
date:       2020-03-15 17:52:32 -0400
permalink:  final_project_for_flatiron_-_react_redux
---


Four weeks into my React/Redux project and it's finally done! It felt a lot longer like every other project. They always start as a struggle that later ends with me gaining a lot of useful knowledge and experience. 

I got the concept of my app from a book that I received as a gift from my husband for our anniversary. The book is made of random date challenges that a couple has to commit to before scratching off the challenge and finding out what it is. Then once the couple completes the challenge they can add photos inside of the book for memories. I used the same concept for my app, but in digital form. 

 
## Rails API Backend
I built this App with React/Redux Frontend and Rails API Backend.
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
	
So, this cors.rb file in the config folder allows the frontend to access `localhost:3000` and communicate with the backend. I have 4 models and some nested routes associated with the models relationship. There's basic CRUD actions in the controller so a user has the option to signup, login or logout.

```
class Api::V1::SessionsController < ApplicationController

    def auth
        render json: {csrf_auth_token: form_authenticity_token}
    end
    
    def create
        @user = User.find_by(username: params[:user][:username])
        if @user && @user.authenticate(params[:user][:password])
            session[:user_id] = @user.id
            render json: @user, only: [:id, :username], status: 200
        else
            render json: {error: "Unable to login, try again."}, status: 400
        end
    end

    def destroy
        session.delete(:user_id)
        render json: {status: 200}
    end
end
```

Because of setting up the current user in the backend and rendering to the frontend when needed, the user is allowed to scratch(default value of scratch:false changes to true) the adventure and add the evidence.

```
def current_user
        @user = User.find_by(id: session[:user_id])
        if @user
            render json: @user, except: [:password_digest, :created_at, :updated_at], status: 200
        end
end
```


## React/Redux Frontend
React is a JavaScript library created by Facebook. When I first started learning it was a lot of fun right away and much easier than vanilla JavaScript. Using JSX is a lot more efficient because I do not have to code as much. 

What is JSX then? JSX is an XML/HTML-like syntax used by React that extends ECMAScript so that XML/HTML-like text can co-exist with JavaScript/React code. The easy wayt to explain this is that JSX allows us to write HTML elements in JavaScript and place them in the DOM without any createElement()  and/or appendChild() methods. Basically, it combines Javascript code and HTML like syntax into a single file. 

Here are the one of the components.

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

export default withRouter(Home)
```

React components implement a render() method that takes input data and returns what to display.
Components are independent and reusable bits of code. It has properties called `props` and parent component passes it down to its child component just like arguments. In my application, App.js is the very top parent component of all. It passes down `user`, `token` and `setCurrentUser` props to most of the components so they can access to those props.

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

export default connect(mapStateToProps, mapDispatchToProps)(App)
```


As you see my App class is wrapped with `connect`. This is imported from `react-redux`.
Redux is a state container for JavaScript apps. It makes it easy to manage the state of my application. It took me a while to understand these `state` and `props`. 
State is a JavaScript object that stores a component's dynamic data and determines the component's behaviour. It is local or owned by that specific component. Because state is dynamic, it enables a component to keep track of changing information in between renders and for it to be dynamic and interactive. Also state can only be used within a class component. So I have to map the state to props so I can pass it down to child component. They are immutable and read-only in the child component.
So basically Redux is a store for the data. You can call the action which is event and this is the only way you can send data from your application to your Redux store. The data can be from user interactions, API calls, or even form submissions. The action must contain the type property and the payload to be stored.

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


Above the file is my user action. It sends the data from the form and POST request to the Rails API and stores the user in the payload. This action type and payload is set in the reducer file. Reducer takes the previous state of the app and returns a new state based on the action passed to it. Then all of this data will be stored in the redux store.

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
)
```


As you can see from the code above, the most important thing you need to have is this `redux-thunk`.
Redux Thunk middleware allows you to write action creators that return a function instead of an action. Thunk is a functional programming technique used to delay computation. Instead of performing some work now, you produce a function body or unevaluated expression (the “thunk”) which can optionally be used to perform the work later. This enable us to avoid directly causing side effects in our actions, action creators, or components.

## Conclusion
React and Redux are powerful and fun to use to build an app. However, it can be confusing if you lose track of all the flows. After eleven months attending Flatiron school I've managed to build five projects. This React/Redux project took the longest time to finish, but is also the closest to a completed app. It's not 100% done, but it's an ongoing project that I plan on adding more functionality as I continue to learn and progress as a developer. It would be amazing to see an app that I designed being used by a large company like Facebook or instagram. Hopefully over time I can reach that level of developing. 
