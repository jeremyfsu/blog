--- 
layout: post
title: Shuffling my collection of girls
created: 1217013266
---
Yes, this is a dev post, no, it is not a post about my personal life.  I'm happily married to only one woman. ;-)

Now that I've clarified that.  If you've ever looked at HairForecast.com, you'll note that I have some graphics that depict the weather, and there's a different girl in each graphic.  The girls wear coats when it's cold, they have umbrellas when it rains, their hair is blown around when it's windy, etc.  You'll also note that I have 3 different girls that I rotate.  I wanted to have the girls appear in random order, but never have the same girl appear twice (on the same forecast day).
<!--break-->
Originally, I wrote some hasty code, that I'm certainly not proud of, that's a private method on the controller that simply returns an array with the 3 girls in it.  The array is shuffled each time it's called.  It's pretty ugly, and I'll show it further down in the post.

While writing the code for Canada, I wanted this to be testable and not in the controller so that Cananda and USA Hair Forecast code can share it.  So for Canada I re-wrote this code and moved it to it's own class.  It's a pretty amusing class, since it's about shuffling a collection of girls and making sure you don't pick the same girl twice until you've exhausted the collection.  Sort of reminds me of my dating life just before I met my  wife.

While re-writing this class, I was reminded that as you learn a new language, sometimes you write more code than you need early on.  Only later, as you learn more about the language, do you realize that you could have written far less code and still completed the same task.

For example, at the time, I was unable to find anyway to shuffle an array in Ruby.  (Note there is now a shuffle method on Array, allegedly).  So I set out to make my own in a overly complex way.  Consider this messy code:
<pre>
def shuffle_girls  
  hash = Hash.new
  girls = ["Ann", "Bridget", "Clara"]

  girls.each do |girl|
    hash[girl] = rand
  end
  
  shuffled_hash = hash.sort {|a,b| a[1] <=> b[1]}
  
  shuffled_girls = []
  (0..2).each do |i|
    shuffled_girls.push shuffled_hash.at(i).at(0)
  end
  shuffled_girls
end
</pre>

I actually adapted an example I found on the net, believe it or not.  Despite it's complexity, it's a good exercise in things Ruby can do with arrays and hashes.

Here's a step by step on how it works:
First we declare a hash and a collection of girls.  Then we create a key/value hash table from the girls.  The girls are the keys, and the values are random numbers.  Next we sort the hash table on it's values, not it's keys.  That <=> operator is an array comparison operator.  It's essentially telling the sort method to compare the values (index of 1) not the keys (index of 0) of the pairs in the hash table.  Next, we want to return a flat array of shuffled girls, so the next block iterates through the hash table, extracting the keys out and "pushing" each onto the "shuffled_girls" array.  Then it returns "shuffled_girls".

Yep, that's pretty ugly. I don't like writing code that after 4 months I can't understand it anymore.  This was the case with that snippet.  I knew there had to be a better way, especially one that I could understand later when revisiting it.

The Ruby API claims that there is now a shuffle method on the Array class.  However, I'm using Ruby 1.8.6 and trying the .shuffle method throws an "undefined method" error!?  So I googled and I found a golden little post about someone who was having trouble finding the shuffle method as well.  The reply was to simply extend Array with your own shuffle method that looked like:
<pre>
def shuffle
 sort_by { rand }
end
</pre>
Wow that's easy.  So, my GirlShuffler class lives in the "model" folder, since it's data related.  It inherits from array, and adds the shuffle method.  In this case though, it's pretty much an array that has a default collection of values in it.  Here's what I ended up with:
<pre>
class GirlShuffler < Array
  private
  def fill
    self.push("Ann", "Bridget", "Clara")
    shuffle!
  end
  
  public
  def initialize
    super
    fill
  end
  
  def shuffle
    sort_by { rand }
  end 
  
  def shuffle!                  
    a = shuffle                 
    self.clear                  
    a.each {|i| self.push(i)}   
  end                           
                                
  def pop                       
    girl = super                
    unless girl.nil?            
      girl                      
    else                        
      fill                      
      super                     
    end                         
  end                           
end
</pre>
Now the interesting thing came with setting up a default collection for my array.  You can't just set self equal to something, so I made use of the inherited push method to fill the collection. 

For those of you new to Ruby, here's brief walk through.  "Super"   passes control to the parent object.  In my initialize method, you can see that first I pass control to the parent (Array) initialize method.  After that I call my private fill method.

Note I have "shuffle" and "shuffle!" methods.  This is the standard syntax in Ruby (and other langs?) where the first returns a new object and the latter does an in place operation on the object and returns nothing.  So in this case, the "shuffle" method returns a new shuffled array, but "shuffle!" does an in place shuffle.  Shuffle! is a little more complex then one would think it needs to be, but remember I can't just set self = shuffle.  So it needs a little loop to push the values on one by one.

Next, note that I've extended the pop method.  In Array's pop, it pops off the last element, and when it runs out returns nil.  I wanted my popper to be endless.  As soon as it is empty I call the fill method to refill self with shuffled girls, and then pop the last value from the newly refreshed self.  So you can see where I call super, check it for a nil, and go from there.  

I've been trying to get into the habit of writing unit tests first.  In this case, I did start with a test that pop was resetting after being exhausted, and that the girls are not repeated unless the collection is exhausted.  Here's both of those tests:
<pre>
  def test_pop_produces_uniq_results
    girls = GirlShuffler.new
    girl_size = girls.size
    a = Array.new
    
    (0..girl_size - 1).each do |i|
      a[i] = girls.pop
    end
    assert_equal a.size, a.uniq.size
  end
  
  def test_pop_resets_after_exhaustion
    girls = GirlShuffler.new
    girl_size = girls.size
    a = Array.new
    
    (0..girl_size).each do |i|
      a[i] = girls.pop
    end

    assert_not_nil a[girl_size]
  end
</pre>
While writing the class, I wanted to make sure that it was indeed shuffling.  So I wrote this flawed test:
<pre>
  def test_shuffle_works
    girls1 = GirlShuffler.new
    girls2 = GirlShuffler.new
    
    assert_not_equal girls1, girls2
  end
</pre>
It didn't take very long for my folly to reveal itself when the test failed after a few passing runs.  Then it occurred to me, how do you test non-deterministic results?  It seemed like that was a problem that surely others have been faced with.  So Google revealed some interesting results, but this one I found particularly interesting: <a href="http://www.devx.com/Java/Article/9305/1954">Two Critical Tips for Unit Testing in Java</a>.  It shows a technique by which you run the test several times, and then check what percentage of the time it passed.  I know that I have 6 possible combinations, I'm no statistician, but I think that means a 1 in 6 chance of pulling up the same shuffle order twice.  I believe that means my test above only has an 83% chance of passing.

In light of this, I decided to run the test 100 times and then assert that it passed at least 70% of the runs.  Here's my rewrite:
<pre>
  def test_shuffle_works
    standard = GirlShuffler.new
    passing = 0
    (1..100).each do
      test = GirlShuffler.new
      unless test == standard
        passing += 1
      end
    end
    
    assert_operator(70, '<', passing)
  end
</pre>
Now I have a test that I'm comfortable with.

It's arguable that this is overkill.  But the academic value of this (for me) is great.  Remember that I'm learning Ruby, and I'm still learning TDD.  Sometimes solving simple problems with a little overkill grants us the ability to learn new stuff, and this is no exception.

Then there's the humor benefit.  I laughed several times while writing the GirlShuffler class, thinking how it was a good model of my dating style in college. ;-)
