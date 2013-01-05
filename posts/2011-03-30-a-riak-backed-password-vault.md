--- 
layout: post
title: A Riak backed password vault
created: 1301533168
---
Document databases have been quite the buzz lately. They are schema-less, no SQL databases that mainly just store keys and values.  One particular system called Riak has grabbed my attention recently. I discovered Riak through Sean Cribbs, with whom I'm familiar because of his work on Radiant.

From the Riak website: "Riak is a Dynamo-inspired database that is being used in production by companies like Mozilla and Comcast. Riak scales predictably and easily and simplifies development by giving users the ability to quickly prototype, test, and deploy their applications." (http://wiki.basho.com/)

I learn by doing, so I decided to build a quick and simple password vault to help me learn Riak. Since Riak uses REST and HTTP as it's interface, I thought it would be cool to build it in Javascript, and have the files served by Riak itself.

To my delight, it was quite easy!  Gentoo doesn't have a package for Riak yet, so I cloned the github and compiled it from source.  The only dependency I needed was Erlang.  In a matter of minutes I had a running Riak node

I used Basho's Javascript client, Jquery, and the GibberishAES library for encryption.

If you'd like a closer look, it's on my Githib here:
http://github.com/jeremyfsu/Riak-Backed-Password-Vault
