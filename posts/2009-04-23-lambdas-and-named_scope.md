--- 
layout: post
title: Lambdas and named_scope
created: 1240493546
---
I'm been trying to wrap my head around lambdas. Like with most concepts, I try to educate myself on the subject, then as I'm writing code I try to find ways to employ the concepts to further sink them into my brain.  I just haven't been able to do this with lambdas for some reason.

Then, one of my dev team members passed this link to the team to tell us about named_scopes: http://ryandaigle.com/articles/2008/3/24/what-s-new-in-edge-rails-has-finder-functionality

Named_scopes are really cool, as a matter of fact, I was already doing something similar, but I was defining a method on the model and putting my custom finder there.  Named_scopes just makes for less typing when doing this.

When I saw lambda in the named_scope example, I realized that I'm already using lambdas in my code, I just didn't make the connection between the concept and the application.  Probably because in the context I'm employing lambdas, I don't need the lambda keyword.

I love the collect method on the enumerable class, and it turns out, it employs a lambda. Let's say I need an array of the catalog numbers for the line items on an order.
<pre>
order = Order.find(:first)
catalog_numbers = order.line_items.find(:all).collect 
 { |item| item.cat_no }
</pre>
I'm passing in the code block: 
<pre>
{ |item| item.cat_no }
</pre>
to the collect method, and this is the concept of a lambda.  You pass a code block into a method like a value.

Another example is the find method on enumerable.  Let's say I have an array of hash objects (like what you get with an activerecord collection), and you want to find one of the hash objects (without going to the DB again).
<pre>
my_item = order.line_items.target.find 
 { |item| item.cat_no == '231-5543' }
</pre>
Note: if order is an activerecord object, calling the find method will call activerecord's find method.  In this case I want enumerable's find method, hence the target method inserted in there.  If order was just an array of hash objects, you can omit the 'target' method.

Also note, even with eager loading, calling order.line_items.find would generate another SELECT query.  So in order to take advantage of eager loading, I'm using this pattern to pick out a single child.

So, looks like I'm using lambda's already, and didn't realize it.

