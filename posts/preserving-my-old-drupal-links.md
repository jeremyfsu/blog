---
title: Preserving my old Drupal links
date: '2013-01-08'
description:
categories:
---

About a year ago, I built a wine rack out of an Ikea bed and
[blogged about it](http://jeremywalworth.com/2011/11/07/ikea-bed-re-purposed-into-a-wine-rack.html).  I sent it in to
[Hack a Day](http://hackaday.com) and they promptly
[posted it](http://hackaday.com/2011/11/07/ikea-wine-rack-originally-sold-as-a-mattress-holder/). Then
[Lifehacker](http://lifehacker.com) [featured it](http://lifehacker.com/5864092/repurpose-an-old-ikea-bed-into-a-huge-60+bottle-diy-wine-rack), and this has become the most frequently visited blog post I've ever composed.
Now that I've gone "minimalist" on my blog (all static HTML with [RuhOh](http://ruhoh.com)), I didn't want these old
links to go dead on Hack a Day and LifeHacker.  They are linking to a Drupal URL that refers to index.php and a
query string with the Drupal node number.  Not exactly easy to make static.

When I converted my old Drupal posts to static HTML using Jekyll, it conveniently arranged them all into permalink style
folders based on date (year/month/day).  Jekyll also created markdown files in folders by node number with meta data at
the top that tells Jekyll (or RuhOh) how to find them by permalink. So I got the idea to make a static HTML file but name
it index.php and put a little javascript in it that reads the query string then redirects the browser to the relevant node
folder.  So I came up with this:
<pre>
    uri = new miuri(location.href);
    window.location = uri.query('q');
</pre>

[MiURI](https://github.com/radmen/miuri.js) is a simple URI parser in Javascript that I included in the index.php.  That's all it took, so now all my old index.php links out there in the internets still work.
