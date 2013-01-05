--- 
layout: post
title: Keeping php-cgi running under nginx
created: 1300987702
---
I just moved this blog to Amazon EC2, I really like the pricing model, and the flexibility. On the new gentoo instance, I decided to give Nginx a whirl for running hairforecast.com and jeremywalworth.com and other sites I host. So far it's working well, except for fastcgi for PHP (for Drupal which is what jeremywalworth.com is on).

The problem is php-cgi just mysteriously dies. Poking around on the internets reveals several people with the problem, but no solid solutions other than supervisord or some other watch dog to restart php-cgi when it dies. I'd like to eventually move my blog to Radiant, so I wasn't ambitious about finding a solid solution. I'm also not excited about installing yet another package (supervisord).  So I whipped up this humble little Ruby script and put it in root's cron to run every minute.
<pre>
#!/usr/bin/ruby
a = `netstat -l | grep phpcgi.socket`
unless a.size > 0 
 exec 'php-cgi -b /tmp/phpcgi.socket &'
end 
</pre>

It uses netstat to determine if php-cgi has a listening socket, if not, it tries to start it.

It's simple and solves the problem, at least for now!
