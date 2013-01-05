--- 
layout: post
title: Setting Content-Disposition on PDF files in Nginx
created: 1318095628
---
Adobe and Internet Explorer team up to cause PDF's to automatically open up in your current browser window.  I personally find this rather convenient, but your typical web user probably doesn't.  In fact, while working on a client website, this "non-technical" client requested that PDFs linked on the website open in a new window. Blech! I don't like doing that. So I go to Jakob Nielsen's website to arm myself with a good explanation on why this is a bad idea.  Instead, I run across this interesting piece by Nielsen: http://www.useit.com/alertbox/open_new_windows.html

Nielsen says:
"When using PC-native file formats such as PDF or spreadsheets, users feel like they're interacting with a PC application. Because users are no longer browsing a website, they shouldn't be given a browser UI."

I like his thoughts on the matter, so I set out to implement his solution.

The client's site is a Radiant CMS site, and I'm trying to keep the content management as simple as possible so that non-technical people can update the site's content.  I really didn't want to go messing about with this within Radiant or Rails.  Then I struck me, this really isn't a problem for the framework, it's static content, it's a problem for the webserver to handle.  In my case, Nginx was going to handle it.

Here's what I came up with.  Note this goes within your server block:
{% highlight %}
location ~* \.pdf$ {        
 # determine the PDF filename        
 if ($uri ~ "^/upload/(.+\.pdf)$") {                
  set $filename $1;        
 }        
 add_header Content-Disposition"attachment;filename=$filename";      
}
</pre>

Then a reload of nginx, and a quick wget -S to check if my Content-Disposition header was there (yay it was!). Also, I had to close then reopen IE before it seemed to notice (probably caching).  And done!

I like Nginx.

Internet Explorer now pops up a dialog box asking if I want to Open or Save the PDF.  I like that!  But, clicking open still causes the PDF to be opened in a browser window, albeit a new browser window with Adobe PDF controls at the bottom.  I decide this is a good compromise.
