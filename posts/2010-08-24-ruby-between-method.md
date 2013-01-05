--- 
layout: post
title: Ruby "between" method
created: 1282685267
---
Just discovered this today, does it even need an explanation for why it's cool?
<pre>
4.between?(1,5) => true
4.between?(5,10) => false
</pre>
No more code that looks like:
<pre>
if value > 1 and value < 5
 yadda yadda
end
</pre>
It'll simply be written like so:
<pre>
if value.between?(1,5)
 yadda yadda
end 
</pre>
