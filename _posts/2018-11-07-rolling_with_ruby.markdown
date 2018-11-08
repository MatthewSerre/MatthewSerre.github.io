---
layout: post
title:      "Rolling with Ruby"
date:       2018-11-08 01:18:32 +0000
permalink:  rolling_with_ruby
---


I have been growing more comfortable with using Ruby as I have continued working through the lessons on Object Orientation.  While difficult, I enjoy the challenge of using classes to solve problems I encountered while working through the Procedural lessons.  I feel like the concepts can be hard to grasp because they seem so abstract at first, but I also feel like I am increasing my general programming and problem-solving abilities enough where the labs do not necessarily seem daunting.

In fact, I experienced a rare occurence where I coded something correctly the first time without any errors, which of course led me to believe I had fallen asleep at my computer and started dreaming.

```
class Song
  
  @@count = 0
  @@genres = []
  @@artists = []
  
  attr_accessor :name, :artist, :genre
  
  def initialize(name,artist,genre)
    @name = name
    @artist = artist
    @genre = genre
    @@count += 1
    @@genres << genre
    @@artists << artist
  end
  
  def self.count
    @@count
  end
  
  def self.genres
    @@genres.uniq
  end
  
  def self.artists
    @@artists.uniq
  end
  
  def self.genre_count
    genre_count = {}
    @@genres.uniq.each do |genre|
      genre_count[genre] = 0
    end
    @@genres.each do |genre|
      genre_count[genre] +=1
    end
    genre_count
  end

  def self.artist_count
    artist_count = {}
    @@artists.uniq.each do |artist|
      artist_count[artist] = 0
    end
    @@artists.each do |artist|
      artist_count[artist] +=1
    end
    artist_count
  end
end
```

After learning about uniq, I applied it to my last several methods, including the final two in which I coded correctly on my first attempt the iteration over and count of unique genres and artists.  Each iterates through the class variable genres and artists arrays and sets a key for each unique element equal to zero while the next each iterates over all the elements in the class variable arrays and increments the value, starting at zero, one for each occurrence of the element.  I think that everything looks elegant and makes sense at a quick glance.

Ultimately this is a trivial accomplishment because what matters is that code eventually works after careful implementation and review, but I was happy that something seemed to go right right away for once!
