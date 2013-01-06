---
title: I dumped Drupal
date: '2013-01-05'
description:
categories:
---

Recently I was checking over my Amazon EC2 bill and noticed that it was twice as much as normal.  The extra charges
were for Disk I/O.  I hadn't thought about this much but Amazon charges 10 cents per million I/O requests on it's Elastic
Block Store. My EC2 instance never came near a million I/O requests so it was off my radar.  Until now.  My EC2 instance
was running about a million I/O requests per hour! After some investigation I figured out that MySQL and Drupal were
responsible.  After more investigation I discovered that Russian bots were registering users on my Drupal blog at the
rate of 2 per minute.  I had over 50,000 users registered on my blog!  All but 3 of them were Russian bots wanting to
post spam comments.  All the user registrations had caused MySQL to thrash.  I shutdown Drupal and the disk I/O ops
dropped to a normal rate.

This made me think, why do I use a SQL backed CMS for my blog?  I hardly ever post, nobody ever posts comments.  I only
have a blog in hopes that something I've done and blogged about can be useful to someone.  Also, for that potential
employer that wants to see what I tinker with.  So why the heavy CMS and SQL when a static HTML blog would work fine?
I could even host it on Amazon S3 and forget about maintaining an EC2 instance.

I thought about writing some quick Ruby to grab all my Drupal posts out of SQL and then build static HTML pages out of
them.  Then I thought, surely someone's done this, and I did some Googling.  I discovered
[Jekyll](http://jekyllrb.com/).  It's the static site generator behind GitHub and it had a plug-in that
ran perfectly on my Drupal MySQL tables and turned all my old posts into Markdown pages.  Brilliant!  This led me to
then discover the author's next project: [Ruhoh](http://ruhoh.com/)

Ruhoh is a static blog generator, has Markdown baked in, as well as a templating engine, is open source, and my favorite 
part: it's written in Ruby.  You compose your blog posts, run a compile command and then deploy it.  I love it!  It's 
minimalistic yet has some cool features like tagging and categories built in.  

So far I like it, this is my first blog post composed in RuhOh, and my blog is now hosted on Amazon S3.  Static, 
minimilist, simple, and free of Russian bots!
