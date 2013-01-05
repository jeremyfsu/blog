--- 
layout: post
title: Simple Webrick server for testing plain HTML
created: 1211556180
---
Sometimes I find myself in need of a quick and dirty HTTP server when I'm working in plain HTML with a little Javascript. For example: when I'm building banner ads that use scriptaculous. Rather than mess with lighttpd and get the files where it can see them, I found myself wanting to the do the old "ruby script/server" as we do in Rails.

So I looked at the Webrick home page, and ended up with this:
<pre>
    require 'webrick'
    include WEBrick
    s = HTTPServer.new(
     :Port => 3000,
     :DocumentRoot => Dir::pwd
    )
    trap("INT"){ s.shutdown }
    s.start
</pre>
I named it server.rb and just drop it in the directory containing the html files I want to test. A simple "ruby server.rb" fires it up and then I can then use a URL like: http://192.168.2.45:3000 to see my files. Now when I'm developing on my Linux box, I can use IE on a separate (WinXP) box to quickly test what I've done. No need to add symlinks or otherwise wire things up such that lighty can see my work.

There are two problems with this, 1. I could not get eRuby (.rhtml) to work with Webrick and 2. it is dreadfully slow. So for testing eRuby stuff, I'm still using lighty.
