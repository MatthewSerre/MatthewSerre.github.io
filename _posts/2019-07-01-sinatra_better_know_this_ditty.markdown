---
layout: post
title:      "Sinatra Better Know This Ditty"
date:       2019-07-01 19:30:18 +0000
permalink:  sinatra_better_know_this_ditty
---


I have really come to enjoy working with Ruby over the past several months and learning more about widely used gems and libraries built with it.  I had wondered since the CLI project how Ruby was actually used to make a website because I didn't understand how the code I was writing could translate into some a user could see.  I started to see the value of SQL and how ActiveRecord allowed me to use it in conjunction with Ruby, but again, that was all happening behind the scenes.  As I got into Rack and saw how much goes into accessing a website and rendering its contents for the user, I was almost there.  Enter Sinatra and it finally all clicked.

Sinatra and the Model View Controller structure, in which the views seen by the end user are made from erb, or embedded Ruby, files containing data generated from the models and ordered and delivered via the controllers, allowed me to see how all the work I was doing up to that point would provide me with the tools to create a functioning application.  Admittedly I was frustrated with some of the labs toward the end of the section, but by the time I was working on the Fwitter lab, everything made sense.

I had been thinking about what I wanted to do for my own MVC application and it occurred to me an idea I had for an internal app at the organization where I worked most recently.  I had wanted to create a better version of the vehicle reservation system we were using.  We had around 10 vehicles that employees signed out for varying days and times and while it worked well for the most part, a lot of basic functionality and usability was missing.  Fast forward to today and I have created something worse in probably every possible way, but at least now I have a basis for understanding how a service like that works from the inside and how I can keep building on it.

I created a simple structure in which the models consist of the User and Reservation classes.  Users interact with the front end to create, read, update, and delete those reservations; all of those inputs or actions are delivered via the controllers to the models, which generate the data, store or retrieve it from the database, and deliver it back to the User via the controllers to the views, which contain a bit of HTML and CSS as well as embedded Ruby, which I mentioned earlier.  The embedded Ruby contains information accessed and stored in the controllers to generate dynamic content in the views.

With my system, users can create a new account, login to their account, make new and edit or delete existing reservations, and view their reservations as well as all reservations.  The system is very basic because the vehicles, or resources as I call them as they could hypothetically be conference rooms, tables, etc., are not themselves models, but simply strings.  Eventually, unless I find it is easier to start from scratch with a Rails app in the next section, I will implement resources and perhaps the reservation slots themselves as classes so the app can be a bit more robust, but for now, the app does its job of demonstrating the capability of Rack, Sinatra, and Ruby as a whole.

I learned a lot about what goes into creating a service like that at every part of the stack and I am excited to keep learning about how I can make better and better web applications.
