---
layout: post
title:      "Ruby CLI Data Gem Project:  LasVegasEvent"
date:       2019-05-26 23:31:19 -0400
permalink:  ruby_cli_data_gem_project_lasvegasevent
---


I finally compeleted my very first project! Initially I was scared to bulid a working program from scratch by myself.  I felt as if I was droped onto an island left with just a knife and an empty backpack.  I had zero idea of how to start or where to start with expectations of me building a shelter, and to hunt for my own survival.  I was lost, but luckily I had the support of some good friends willing to take their time to assist me through the process. Slowly, I began to understand more about the CLI (Command Line Interface) and how to start working on a functioning application. 

I wrote a note of what I'm going to build, thought about its concept, and how it was to interact with users. My app for this project was an app that lists events taking place in Las Vegas. Users input what event they would like to see from a list that is produced and it outputs more detailed information from the selected event such as available date, time, location, and pricing information. 

Here is how I built my CLI ruby gem. 

## Using Bundler to Set Up My Ruby Gem:
I repeatedly watched Avi's video and ran through his guide to set up my folders and files using `Bundler`. `Bundler` provides a consistent environment for Ruby projects by tracking and installing the exact gems and versions that are needed. It was pretty easy way to create a new gem just typing in: 

```
bundle gem LasVegas_event
```

It creates a gem with a README, .gemspec, Rakefile and all the directory structures.

## Setting Up My Environment:
The hardest part was ensuring that all requirements were met and that code could be executed and produce the right information. In order to do that, I had to add all the `require` files/installed gems in my environment file.

```

require 'open-uri'
require 'nokogiri'
require 'pry'
require 'colorize'


require_relative 'LasVegas_event/cli'
require_relative 'LasVegas_event/scraper'
require_relative 'LasVegas_event/event'
require_relative "LasVegas_event/version"

```

## Creat My Ruby CLI Gem:
Once all the files were set up, all that was left for me to focus on was the part I enjoy the most and that's coding.
Now I creat all the files for each purpose.

### cli.rb
:Handling CLI display logic and user input
 
 ```
 
 class LasVegasEvent::CLI

  def call
    welcome
    fetch_events
    list_events
    details
  end

  def welcome
    puts "===================================================================================="
    puts "Welcome to Las Vegas Event Calendar where you can find the best events in Las Vegas!".green
    puts "===================================================================================="
  end

  def fetch_events
    LasVegasEvent::Scraper.scrape_events_list
  end

  def list_events
    puts "---------------------"
    puts "|Special events list|".yellow
    puts "---------------------"
    LasVegasEvent::Event.all.each.with_index(1) do |event, i|
      puts "#{i}. #{event.name}".light_blue
    end
  end

  def event_website(event)
    puts "** Do you want to check out the event website? Type 'y' or 'n' **".green
    input = gets.strip.downcase

    if input == "y"
      LasVegasEvent::Scraper.get_event_page(event) unless event.event_webpage
      puts "#{event.event_webpage}".cyan
      puts "** Type 'list' to see all the list or select number again. Type 'exit' if you wish to exit. **".green
    elsif input == "n"
      puts "** Type 'list' to see all the list or select number again. Type 'exit' if you wish to exit. **".green
    else
      puts "Sorry, try again.".red
    end
  end

  def details
    puts "** Which event do you want to know more about? Type the number of event. **".green
    input = nil
    while input != "exit"
      input = gets.strip.downcase

      if input.to_i > 0 && input.to_i <= 10
          event = LasVegasEvent::Event.all[input.to_i-1]

            puts "************************************************************************************************"
            puts " #{event.name}".light_blue
            puts "#{event.date_time} - #{event.location}".cyan
            puts "#{event.events_description}\n".yellow
            puts "#{event.url}\n".cyan

          event_website(event)

      elsif input == "list"
              list_events
      elsif input == "exit"
        goodbye
      else
        puts "The event can not be found.".red
        puts "---------------------------"
        puts "** Type 'list' to see all the list or select number again. Type 'exit' if you wish to exit. **".green
      end
    end
  end

  def goodbye
    input = nil
    if input == "exit"
      puts "==========================================="
      puts " Thank you for visiting us. See you again!".green
      puts "==========================================="
    end
  end

end

```


### event.rb
:Objects - organize data and initialize all the instances

```

class LasVegasEvent::Event

  attr_accessor :name, :location, :date_time, :url, :events_description, :event_webpage
  @@all =[]

  def initialize(name, date_time, location, url, events_description=nil, event_webpage=nil)
  @name = name
  @date_time = date_time
  @location = location
  @url = url
  @events_description = events_description
  @event_webpage = event_webpage
  save
  end

  def save
    @@all << self
  end

  def self.all
    @@all
  end

end

```


### scraper.rb
:Scraping all the data from the website

```

class LasVegasEvent::Scraper

  def self.scrape_events_list

     doc = Nokogiri::HTML(open("https://events.lasvegascalendars.com/"))

        doc.css("div.timely-events-container").css("a.timely-event")[0..9].each do |timely|

          name = timely.css("span.timely-title-text").text
          date_time = timely.css("span.timely-start-time").text.gsub(/\s+/, " ")
          location = timely.css("span.timely-venue").text.gsub(/\s+/, " ")
          url = timely.attr("href")
          events_description = timely.css("div.timely-description.timely-has-venue").text.gsub(/\s+/, " ")

          LasVegasEvent::Event.new(name, date_time, location, url, events_description)

       end
  end

  def self.get_event_page(event)
# binding.pry
      event_page = Nokogiri::HTML(open(event.url))
      event_webpage = event_page.css("div a.timely-event-details-link").attr("href").value
      # binding.pry
      event.event_webpage = event_webpage
  end

end

```


## LasVegas_Event Ruby CLI Gem!
Fianlly all my files are working as it suppose to and let's run the programe!
### bin/run
```

#!/usr/bin/env ruby

require 'bundler/setup'
require 'environment'

LasVegasEvent::CLI.new.call

```


After days of struggle I was able to get my code to finally run successfully and this boosted my confidence.


## Conclusion
It took a while to complete because I was juggling with completing the project and moving to my new home. I know if I had more time I would be able to produce a finer product and I learned a lot from this experience having to start alone and kind of lost in the process. It felt great to complete it though and that's why I enjoy challenges. The feeling of accomplishing something so difficult especially for someone like me who has zero coding background and a language barrier. I expect this journey to be one of struggle, but filled with rewards of accomplishment.

