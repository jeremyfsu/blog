--- 
layout: post
title: Rounding floats in Ruby
created: 1264517707
---
I think it's odd that Ruby doesn't have a way to round to the nearest tenth or hundredth etc. The Float class has a rounding method that rounds to the nearest integer, but I found nothing to round like I needed. I suppose I could use String format, but why should I have to do that when I have no other reason to convert the value to a string?  I was feeding RRDTool directly using the Ruby bindings, so I could leave it as a string, but I just didn't like that approach.

After Googling, I noted that some people were extending the Float class with some interesting ideas. However, the most intriguing was a flat out hack. Here it is:
<pre>
irb(main):001:0> f = 55.34745
=> 55.34745
irb(main):002:0> f.round
=> 55
irb(main):003:0> (f * 10.0).round / 10.0
=> 55.3
irb(main):004:0> (f * 100.0).round / 100.0
=> 55.35
irb(main):005:0> 
</pre>

How about that for a hack?  It's simple, but it reminds me of when I'm too lazy to look up the API for the right way to do something and instead write extra code.

Anybody else have good ideas for rounding in Ruby?
