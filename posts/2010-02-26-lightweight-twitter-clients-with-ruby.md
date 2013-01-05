--- 
layout: post
title: Lightweight twitter clients with Ruby
created: 1267194096
---
I'm a minimalist.  It's why I use Gentoo and not RedHat. It's why I enjoy programming microcontrollers. It's why I like microblogging a la Twitter.

I love Tweetdeck too.  But Tweetdeck annoys me with it's appetite for memory. I know Adobe Air in part is to blame for that.  Why should something so simple require so much memory!?  Tweetdeck and Adobe Air are like using a Greyhound bus to take your child to preschool.  

One feature that I love about Tweetdeck that lightweight Twitter clients often lack is a search feature.  I like watching Tweets with "Tallahassee" to see what's going on in town.  I like watching Tweets with "microcontroller" to see what other chip hackers like me are doing.  So what's a Ruby hacker to do when he wants to have scrolling tweets about his favorite subjects on his screen while he works?

He writes some Ruby of course....

<pre>
require 'twitter'

if ARGV[0].blank?
 puts "usage: tweetsearch.rb <search query>"
else
 tweets = Twitter::Search.new(ARGV[0]) 

 while(1)
  if tweets.entries.size > 0
   lastid = tweets.entries[0].id
   tweets.entries.reverse.each do |tweet|
    puts "#{Time.parse(tweet.created_at).strftime('%H%M%S')} #{tweet.from_user}: #{tweet.text}"
   end 
  end

  begin 
   sleep(15)
   tweets = Twitter::Search.new(ARGV[0]).since(lastid)
  end while(tweets.nil?)

 end
end
</pre>
That tiny bit o' Ruby provides a nice command line scroller of the tweets of your choice, updated every 15 seconds.

It doesn't beep. I doesn't display the user's profile pics. The links aren't even clickable. So it's nothing like the Tweetdeck experience.  But it's a nice start and my first try at working with the twitter gem in Ruby.

I've already been playing with OAuth and have another Ruby script that logs me in to Twitter.

By the way, I love TIRCD, but it kept maxing out the API limit for some reason.  Maybe I need to write a TIRCD clone in Ruby? Or dust off my Perl and contribute to TIRCD? 
