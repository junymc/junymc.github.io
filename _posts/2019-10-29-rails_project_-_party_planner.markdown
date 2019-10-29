---
layout: post
title:      "Rails Project - Party Planner"
date:       2019-10-29 14:50:11 -0400
permalink:  rails_project_-_party_planner
---

This project was really hard for me. Rails is such a powerful framework but in order to use all the magical tools, I had to know what it does and how it works. I think I just memorized the syntax and code itself rather than actually understanding the logic. As further I move on, I learned one by one and now, I understand a lot more than when I strated. I'm going to share how I built my application and what I've learned while I was working on it.

My rails project is building an application that helps you to track all your party list as a host or a guest.

## Creating a new project

First, create a new project using the command below:
```
$ rails new rails_project_party_planner
```
This will give you nice basic structure that you can strat with.
Then type `bundle install` in your terminal to install all the gems and dependencies that you need for the applaication. You will need add a few more gems later when we work on the `omniauth` , `bootstrap`  and more.. But for now, we will set up models and migrations first.

I used `rails generator` to create models and migrations.
Type the following command in your termianl:
```
$ rails g model account email password_digest
```
This will create a model and a migration for `Account`. You can do the same way for the other models.
After create all the migration, set the associations in the models.
I have these relationships between models:
```
class Account < ApplicationRecord
   belongs_to :accountable, :polymorphic => true, optional: true
end

class Guest < ApplicationRecord
    has_one :account, :as => :accountable
    has_many :invites
    has_many :parties, through: :invites
end

class Host < ApplicationRecord
    has_one :account, :as => :accountable
    has_many :parties
end

class Invite < ApplicationRecord
    belongs_to :party
    belongs_to :guest
end

class Party < ApplicationRecord
    belongs_to :host
    has_many :invites
    has_many :guests, through: :invites
end
```



### Ploymorphic Association

The polymophic association was complicated for me at first. I wasn't sure why I need it and how I'm gonna use it.. I was really confused. So what is `polymorphic association`?
> "With polymorphic associations, a model can belong to more than one other model, on a single association."  - [Ruby on Rails Guide](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations)

Why did I neet the polymorphic? 
My App allows users to sign up with two different types of accounts, as a host or a guest. So I set the `Account` model as the polymorphic. The `Host` and the `Guest` share the `Account` as `:accountable`. `Account` will have `:accountable_type`  which is "Host" or "Guest" when it creates and  `:accountable_id` which is same as `host.id` and `guest.id` when the `Host` and the `Guest` create. Now, they have `has_one` and `belongs_to` relationship so you will be able to call something like `host.account`, `account.accountable_type`, `account.accountable.` ... etc.

Also, in order to make this work, you will have to set the polymophic in the migration.
Add this line in the accounts table:
```
t.references :accountable, polymorphic: true, index: true
```

Run the command below to create your database:
```
$ rails db:migrate
```
Check  your schema to make sure everything looks good before you move on. The accounts table will look like this in the schema.
```
create_table "accounts", force: :cascade do |t|
    t.string "email"
    t.string "password_digest"
    t.string "accountable_type"
    t.integer "accountable_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
   
    t.index ["accountable_type", "accountable_id"], name: "index_accounts_on_accountable_type_and_accountable_id"
 end
 ```
 
 
### Controllers

Now, we need our controllers to implant all the logic and actions.
```
$ rails g controller accounts
```
Do the same for the other controllers.
I tried to stick with RESTful routes, so my methods in the controller are pretty simple. Mainly I have all the CRUD actions. `parties_controller` is going to have most logic and actions. Here is what it looks like:
```
class PartiesController < ApplicationController
 before_action :verify_accountable
 before_action :find_party, :party_host, only: [:edit, :update, :destroy]
 
  def index
    @parties = Party.all
  end

  def new
    host?
    if params[:host_id]
      find_host
    end
    @party = Party.new
  end

  def create
    host?
    @party = Party.new(party_params)
    if params[:host_id]
      find_host
      @party.host = @host
    end
  
    if @party.save
      redirect_to host_party_path(@party.host, @party)
    else
      render :new
    end
  end

  def show
    find_party
  end

  def edit
    if authorized_host
      render :edit
    else
      flash[:danger] = "You are not an authorized user."
      redirect_to party_path(@party)
    end
  end

  def update
    if authorized_host
      @party.update(party_params)
      redirect_to host_party_path(@party.host, @party)
    else
      flash[:danger] = "You are not an authorized user."
      redirect_to parties_path
    end
  end

  def destroy
    @host = @party.host
    if authorized_host
      @party.destroy
      redirect_to host_path(current_user)
    else
      flash[:danger] = "You are not an authorized user."
      redirect_to parties_path
    end
  end

  private

  def party_params
    params.require(:party).permit(:name, :description, :location, :date_time, :dress_code, :host_id)
  end

  def find_party
    @party = Party.find(params[:id])
  end

  def find_host
    @host = Host.find(params[:host_id])
  end

  def party_host
    if params[:host_id]
      find_host
      @party.host = @host
    end
  end

  def authorized_host
    current_user == @host
  end

end

```

I used `helpers` to reducing repetition of codes so it looks DRY(Don't Repeat Yourself). You can let the helpers run before you run any actions by adding `before_action` on top of your controller.

### Routes

After set up all the controllers, what we need to do is set the routes. The routes will connect the controllers and views so it dispalys the objects and actions on the browser. Since I have `many-to-many` realtionship, I used nested resources.
My routes looks like below:
```
# app/config/routes.rb

Rails.application.routes.draw do

  root to: 'welcome#root'
  get '/login' => 'sessions#new'
  post '/login' => 'sessions#create'
  get 'auth/:provider/callback', to: 'sessions#googleAuth'
  get 'auth/failure' => 'welcome#root'
  get '/auth_login' => 'welcome#auth_login'
  get '/logout' => 'sessions#destroy'
  delete '/logout' => 'sessions#destroy'

  
  resources :guests do
    resources :invites
  end

  resources :parties do
    resources :invites
  end

  resources :hosts do
    resources :parties
  end

  resources :accounts, only: [:new, :create]
  resources :parties
  resources :invites
  
  
end
```


