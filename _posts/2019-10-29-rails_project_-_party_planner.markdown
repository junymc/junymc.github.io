---
layout: post
title:      "Rails Project - Party Planner"
date:       2019-10-29 14:50:11 -0400
permalink:  rails_project_-_party_planner
---

This project was really hard for me. Rails is such a powerful framework but in order to use all the magical tools, I had to know what it does and how it works. I think I just memorized the syntax and code itself rather than actually understanding the logic. As further I move on, I learned one by one and now, I understand a lot more than when I strated. I'm going to share how I built my application and what I've learned while I was working on it.

My rails project is building an application that helps you to track all your party list as a host or a guest.

##Creating a new project

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

run the command below to create your database:
```
$ rails db:migrate
```

Now, we need our controllers to implant all the actions.
```
$ rails g controller accounts
```
Do the same for the other controllers.


