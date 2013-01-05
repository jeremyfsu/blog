--- 
layout: post
title: Using method_missing in Ruby
created: 1215569914
---
I first learned about method_missing when <a href="http://devlicio.us/blogs/christopher_bennage/">Christopher Bennage</a> asked me about it.  Even after looking it up and explaining it to Christopher, I didn't find any practical use for it in the things I was doing.

At least until tonight, that is.

I've been working on expanding <a href="http://hairforecast.com">Hair Forecast</a> into Canada.  I have the Canadian weather data described in active record like so:
<pre>
    create_table :canadaforecasts do |t|
      t.column :fcstdate, :date
      t.column :lat, :float
      t.column :lon, :float
    end
</pre>
<!--break-->
<pre>
    create_table :canadaparameters do |t|
      t.column :canadaforecast_id, :integer
      t.column :parameter, :string
      t.column :interval, :int
      t.column :value, :float
    end
</pre>

As you can imagine from that, a canadaforecast has_many

canadaparameters.  Each parameter is one of T for temperature, D for dewpoint, W for wind, etc.  Each parameter has an interval, which is described in hours past 0:00 UTC.  This is so I can have each weather parameter for morning, day, night, etc.

Now here's the interesting part.  When I'm retrieving a forecast, and wish to access temperature for the 12th hour of the period, I would end up with this:
<pre>
forecast = Canadaforecast.find(some_criteria)
forecast.canadaparameters.find_by_parameter_and_interval("T", 12)
</pre>
That second line just ain't that pretty.  Ruby is supposed to be pretty.  So I decided to simplify it.

I could create a fetch method in the Canadaforecast model that simply takes two args (parameter and interval) and returns the value.  So then I'd have a bunch of lines like:
<pre>
t = forecast.fetch("T", 12)
d = forecast.fetch("D", 12)
</pre>
But what if I thought the following was more readable and just plain looked cool?
<pre>
t = forecast.T(12)
d = forecast.D(12)
</pre>
Well, method_missing is the tool for this.  Consider the following model in my Rails app:
<pre>
class Canadaforecast < ActiveRecord::Base
  has_many :canadaparameters

  Valid_parameters = [ "T", "D", "C", "W", "P", "H" ]
  
  def method_missing(method, *args)
        if Valid_parameters.include?(method.to_s)
          fetch(method.to_s, args[0])
        else
          super
        end
  end
  
  private
  def fetch(parametername, interval)
    parameter = self.canadaparameters.find_by_parameter_and_interval(parametername, interval)
    if parameter.nil?
      nil
    else
      parameter.value
    end
  end
end
</pre>
The method_missing method checks to see if you are trying one of the valid forecast parameters as a method.  If not it just sends you back to the super and throws an error.  If you have used a valid parameter as a method, then it calls the simple fetch method and passes in the parameter and interval you are trying to get.  Note how method_missing provides an array of args.

Now I can fetch any forecast parameter for any interval with very easy to read code.

No model would be complete without a proper test, so just to show off my (lack of) testing skills, here's the test for the model above:
<pre>
require File.dirname(__FILE__) + '/../test_helper'

class CanadaForecastTest < Test::Unit::TestCase
  fixtures :canadaforecasts, :canadaparameters

  def test_fetching_parameters_by_using_parameter_name_as_method
    forecast = Canadaforecast.find(1)
    assert_not_nil forecast  
    value = forecast.H(12)
    assert_equal 10, value
  end

  def test_fetching_parameters_not_found
    forecast = Canadaforecast.find(1)
    assert_not_nil forecast  
    value = forecast.H(1)
    assert_nil value
  end
end
</pre>
