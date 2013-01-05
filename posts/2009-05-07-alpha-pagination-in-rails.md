--- 
layout: post
title: Alpha Pagination in Rails
created: 1241711545
---
One the first cool tricks probably everyone learns when learning Ruby are the range tricks.
<pre>
(0..5).to_a #=> [0, 1, 2, 3, 4, 5]
(0...5).to_a #=> [0, 1, 2, 3, 4]
</pre>

So I was looking to "alpha paginate" my user list in a Rails app.  It was already paginated (by another team member of mine) with the pagination helper, but that's useless if I wanted to find a user with the last name beginning with L.

I've seen an alpha paginator out there, but I just couldn't help thinking that it was excruciatingly simple to implement my own.

I took a look at the Ruby, Range class, I figured if the Ruby creators were slick enough to give us cool things like (0..5), why not something like ('A'..'Z')?  Well, sure enough, it's in there. 

http://www.ruby-doc.org/core/classes/Range.html

Have I mentioned how much I love Ruby?

The first thing I did was write a helper method in application_helper.rb so that I could use this anywhere in my Rails app:
<pre>
def alpha_links(action)
 return_text = '[ '
  ('A'..'Z').to_a.each do |letter|
   return_text = return_text + link_to(
    "#{letter} ", 
    :action => action, 
    :id => letter)
   end    
 return return_text + ' ]'
end
</pre>
I figured it would be nice to have a single method call that generates all the links, and allows me to set the controller method that's called when a user clicks one of the links.

Then in the view, I added this line:
<pre>
<%= alpha_links('list') %>
</pre>

Now on the view I have a nifty block that looks like this:
{% highlight %}
[ A B C D E F G H I J K L M N O P Q R S T U V W X Y Z  ] 
</pre>

where each letter is a link to:
/user/list/A or /user/list/B etc etc.

Finally, I modified the "list" controller method as follows:
<pre>
def list
 if params[:id].nil?
  alpha = 'A%'
 else
  alpha = params[:id] + '%'
 end

 @users = User.find(
  :all,
  :order => 'last_name',  
  :conditions => ["last_name like ?", alpha])

end
</pre>

Oops! As DHH used to say. My alpha pagination is done.

Note that ActiveRecord will sanitize the conditions hash in the example above, to avoid SQL inject attacks.  It's worth mentioning that if I wrote that line like so: 
<pre>
 :conditions => "last_name like '#{alpha}%'")
</pre>
ActiveRecord would NOT sanitize it and SQL could very easily be injected right from the URL!
