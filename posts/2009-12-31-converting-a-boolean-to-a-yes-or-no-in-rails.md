--- 
layout: post
title: Converting a boolean to a yes or no in Rails
created: 1262294990
---
Every once in awhile I run across some syntactical sugar that makes me smile.

I have a boolean in a model for a project at work.  I want the users to see a simple yes or no, not a true or false.  I've struggled with how to do this in a dry, simple approach and have come up with several ideas.  So today I found myself with this problem again, and not liking any of my previous solutions I asked Google for some ideas.  I found this bit and put it in the model.

Note sla_flag is the boolean column in the DB. 
<pre>
def sla?
 self.sla_flag ? "Yes" : "No"
end
</pre>

So now in my views I just reference "sla?".

I love that it doesn't require a multi-line if block. I love that it's a one line method.

I found this nugget here:
http://www.techlists.org/archives/programming/railslist/2006-07/msg03506.shtml

Note the answer is from Yehuda Katz.  I saw him at acts_as_conference last year and heard people talking about what an amazing Rubyist he is.  When a simple answer like this can teach me so much about Ruby, I have to stop and shake my head.  Despite my co-workers high regard for my Ruby skills, I am such a poser next to guys like Yehuda.  I'm glad there are guys like him around to learn from!

This solution isn't the most dry, I'd rather make some sort of helper to handle this so that I don't have to do this in every model with a boolean.  In spite of this, I have left it in the model.  If I put it in a helper I'll never run across it again.  So for now it's in plain view in the model to remind me of this syntax for future solutions.  During some re-factoring session at a later date I'm sure I'll move it into a generic helper, or explore some of these ideas people have of extending the True class.
