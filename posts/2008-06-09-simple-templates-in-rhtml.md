--- 
layout: post
title: Simple templates in RHTML
created: 1213042440
---
I'm creating a very simple site (4 static pages) for some friends and didn't need to do anything fancy (yet), so I elected to use plain HTML and Javascript and not use a framework (like Rails).  I like Rails, but I just can't see myself deploying the whole Rails tree just for a 4 page static site.

However, I hate having to repeat myself, like everything in the head tags, the masthead, and the master div that wraps everything.  In Rails, you have your "layout" and you put a yield statement where you want your dynamic content to appear.  So how to get this feature without loading a big framework?

Yes, PHP would be an easy way, but I've been very interested in Ruby lately.  It's really a joy to write Ruby, and it has become my "Swiss Army knife" anytime I need a shell script in Linux.  The best way to learn a new language is to use it.  So I'm constantly finding excuses to use Ruby, and I'm finding I can apply it without Rails in lots of places.

To use Ruby to solve this problem, I used RHTML.   RHTML is Ruby's imitation of PHP.   You can escape HTML and embed Ruby, the webserver will pre-process the Ruby commands. You need eruby installed and your webserver configured to handle .rhtml files.  With this in place, I found it fairly easy to roll my own simple template system with a few lines of Ruby embedded in my static HTML files, which are now RHTML files.

First I created a template.rhtml file and put all the HTML markup that's common to all pages of the site in here.  It looks like this:
<pre>
<% def template %>
<html>
<head>
<title>Belle Amitie</title>
<link rel="stylesheet" type="text/css"
media="screen,projection" href="css/belleamitie.css" />
<script src="js/prototype.js" type="text/javascript"></script>
<script src="js/scriptaculous.js" type="text/javascript"></script>
<script src="js/belleamitie.js" type="text/javascript"></script>
</head>
<body>
<div id="wrapper">
<div id="main">
<div id="masthead">
<img src="images/belleamitie.gif"><br>
<img src="images/tokens.gif">
</div>
<% yield %>
</div>
</div>
</body>
</html>
<% end %></pre>
The 3 lines of Ruby in there simply define a function named "template" that wraps all the HTML. Then there's the yield where I want my content inserted. In Ruby, yield is a little different than what I'm used to in C#.  In Ruby, we have "yield" and "super" as ways to pass control among blocks.  So in this case, the calling block  (that you'll see later) can take control at this yield statement, and when the calling block is finished, execution resumes after the yield.  I'll leave it up to you to find out about how "super" works, but it's also pretty useful when extending classes.

Next, in each static page, I wrapped all the HTML in a function named "content". Then I import the template file with "Eruby.import". Lastly, I make a call to the template function and pass the content method into it, like so:
<pre>
<% def content %>
<div id="navbar">
<a class="nav" href="index.rhtml">Home</a> -
<a class="nav" href="retailers.rhtml">Where to Buy</a> -
<a class="nav" href="wholesale.rhtml">Wholesale Inquiries</a> -
Contact Us
</div>
<div id="content">
<h3>How to Contact Us</h3>
We'd love to hear from you!
Any questions or comments may be sent to:
info@belleamitie.com
</div>
<% end %>
<% ERuby.import("template.rhtml") %>
<% template { content } %>
</pre>
The magic is in that last line.  It calls template and passes content into it.

That's it!  It's quick, it's easy, maybe a little hackish, but it doesn't require a large framework.  There's also an added benefit.  Should my friends decide to add a shopping cart or something that justifies using Rails, I will only need to remove a few lines of eruby in these and that will convert them to a layout and several views.
