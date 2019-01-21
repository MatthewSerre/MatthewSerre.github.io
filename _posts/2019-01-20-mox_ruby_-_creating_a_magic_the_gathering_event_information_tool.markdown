---
layout: post
title:      "Mox Ruby - Creating a Magic: The Gathering Event Information Tool"
date:       2019-01-20 20:58:54 -0500
permalink:  mox_ruby_-_creating_a_magic_the_gathering_event_information_tool
---

For about as long as I have had an interest in computers and learning more about them, which is the better part of the past twenty years, I have also had an interest in Magic: The Gathering, arguably the world's oldest and most well known collectible card game.  I have played Magic at casual and competitive levels for a long time and one of the best aspects of doing so is all of the events you can attend both locally and throughout the rest of the country and world for that matter.

Initially I had another idea for my Ruby gem CLI project that ended up being inappropriate in my view for the structure of the program I needed to create.  I thought for a few moments and Magic came to mind; what if I could create a tool to scrape information about upcoming events hosted by the largest game stores in the world?

Enter Mox Ruby, the name (which occurred to me when I sat down to write this post) of my CLI tool for displaying and sorting events based an option selected by the user.  Why Mox Ruby you may think (or not)?

![From left to right: Mox Pearl, Mox Sapphire, Mox Jet, Mox Ruby, and Mox Emerald.](https://gemr.com/wp-content/uploads/2015/11/tumblr_ndhlybGl2q1qia2dho1_1280-1024x287.jpg)

The first three base sets released for Magic contained Mox Pearl, Mox Sapphire, Mox Jet, Mox Ruby, and Mox Emerald, a series of five cards referred to collectively as Moxen.  In Magic, players rely on lands to produce mana to case creatures and spells.  However, players can only play one land per turn barring exceptions made by other cards.  Moxen, which are artifacts with a cost of zero, can produce mana just like lands, so being able to play a land and one or more Moxen in a turn effectively allows you to cheat and produce more mana than you should be able to produce early in the game, thereby allowing you to cast stronger creatures and spells earlier than an opponent could do the same.  Therefore, Moxen are very powerful.

The hope is that my tool will also be powerful; as a result, it needs a name to reflect that!

Anyway, the idea is for my CLI to welcome the user and display a list of options from which to select a criterion for displaying and sorting events, including the date of the event, the format of the event (which tells players which cards they are allowed to have in their decks), and the location of the event.  Once the user selects an option, they are presented with a list of events grouped or sorted based on that criterion and they can enter a number corresponding to an event in the list to see more information about it.

I am excited about building this tool because I think it will be personally useful to me and potentially others.  So far, I have a lot of the interface stubbed out and some of it working, including parts that rely on the data my scraping class is outputting.  I am looking forward to continuing the project and recording my progress on it as I work through the remaining pieces I need to build and perfect.
