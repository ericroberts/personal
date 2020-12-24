---
layout: post
title: What test factories are hiding from you
---

Do you use test factories? You may be missing valuable feedback from your tests
that could lead to better design.

If you're like me, you've started using factories right about the same time you
started testing. They are a great way to create a lot of data to use in your
tests. Without them, you would have to do all of that hard work yourself.
Recently however, I've been rethinking my default "use factories" approach to
testing.

## Factories make bad design easy

Test Driven Development (TDD) offers a number of benefits. The self-testing code
that results from diligent application of TDD allows you to refactor your code
with confidence. The primary benefit of TDD however, is supposed to be an
improvement in design <sup><a href="#references">[1]</a></sup>. Yet I see people
missing out on these design benefits even when they write the tests first and
spend the time to refactor. I think that one of the problems is the ease of
using factories to generate large quantities of data. From here on out I will be
showing what I mean by going through code covered by tests written with
factories, and discussing what other methods of testing might help us to uncover
design flaws.

To begin, I will explain the feature that we are trying to build. The project's
requirements are to build a feature that returns me the minimum and maximum
projected revenue for all of my customers for the next year. Each customer has
a rate assigned to them that indicates a minimum and maximum percentage of this
year's revenue we expect to get next year.

For example, if I have a customer who provided me with $1,000,000 in revenue
last year, and it has been determined that their minimum projected revenue for
this year is 80% of that, and the maximum projected revenue for this year is
110%, the minimum projected revenue would work out to be $800,000, and the
maximum projected revenue would work out to be $1,100,000.

Finally, the actual feature is to calculate the sum of these amounts for
multiple customers. So if you have one customer projected to have a minimum
revenue of $800,000 and a maximum of $1,100,000, and another who is projected
to have a minimum of $600,000 and a maximum of $900,000, the total minimum
revenue will be $1,400,000 and the total maximum revenue will be $2,000,000. If
this example seems contrived, it's because I've changed the names of things to
protect the innocent (Boltmade's clients), and this is the best I could come up
with.

All of the code from here on out can be found at the GitHub repository
[ericroberts/factories](https://github.com/ericroberts/factories). The
individual refactorings can be found in pull requests. We'll go through them all
in this post.

Here's the test for the feature I've just described:

{% highlight ruby linenos %}
RSpec.describe Estimator do
  subject { customer.estimator }
  let(:customer) { create :customer }

  describe "#projection" do
    it "should return the sum of the estimated min and max projections" do
      expect(subject.projection).to eq [
        customer.revenue * customer.rate.min,
        customer.revenue * customer.rate.max
      ]
    end
  end
end
{% endhighlight %}

Here are the test factories:

{% highlight ruby linenos %}
FactoryGirl.define do
  factory :estimator do
  end

  factory :customer do
    association :rate
    association :estimator
    revenue 100
  end

  factory :rate do
    min 80
    max 90
  end
end
{% endhighlight %}

And finally, here's the code that actually implements the feature:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.inject([0,0]) do |(min, max), customer|
      min += customer.revenue * customer.rate.min
      max += customer.revenue * customer.rate.max
      [min, max]
    end
  end
end

class Customer < ActiveRecord::Base
  belongs_to :rate
  belongs_to :estimator

  # revenue method added by ActiveRecord
end

class Rate < ActiveRecord::Base
  def min
    self[:min] / 100.to_f
  end

  def max
    self[:max] / 100.to_f
  end
end
{% endhighlight %}

Doesn't look too bad, does it? Let's see what we can discover by refusing to use
factories.

## Without factories

We're going to write this without using factories at all. This means we will
have to provide all the data ourselves. I'm going to choose to do this with
stubs. Here's what the same test looks like when we stub all of our data:

{% highlight ruby linenos %}
RSpec.describe Estimator do
  subject { Estimator.new }

  describe "#projection" do
    let(:customer) { double }
    let(:rate) { double }

    before do
      allow(subject).to receive(:customers).and_return([customer])
      allow(customer).to receive(:revenue).and_return(100)
      allow(customer).to receive(:rate).and_return(rate)
      allow(rate).to receive(:min).and_return(80)
      allow(rate).to receive(:max).and_return(90)
    end

    it "should return the sum of the estimated min and max projections" do
      expect(subject.projection).to eq [
        customer.revenue * customer.rate.min,
        customer.revenue * customer.rate.max
      ]
    end
  end
end
{% endhighlight %}

Stubbing all of those methods is painful. And worse, if any of the methods being
called change, we'll have to change the stubs. That's not very good. And yet, I
will argue that this pain is a good thing. This pain is alerting us to the
presence of bad design.

If we look at those tests, what is that we are stubbing? First we have to stub
the customers method to return our own Customer double. Then we have to stub not
one but two methods on customer to return specific values.

So what is this test telling us? I would argue that this test is telling us that
Estimator knows too much about Customer. This is where we should step back and
figure out exactly what it is we want from Customer. Why does Estimator need to
know the customer's revenue and the customer's rate? What is it we are doing
here?

Our original feature description wanted us to sum the customer's projected
revenue. We are doing this, but we implemented it so that all the work is in
Estimator. Estimator really just cares about what the customer's projected
revenue is, and yet we have implemented it so that Estimator knows how to
calculate a Customer's projected revenue. That feels like the responsibility of
a Customer, so let's make it one.

Here's the test for the new method on Customer:

{% highlight ruby linenos %}
RSpec.describe Customer do
  subject { Customer.new(revenue: 100) }
  let(:rate) { double }
  let(:min) { 80 }
  let(:max) { 90 }

  before do
    allow(subject).to receive(:rate).and_return(rate)
    allow(rate).to receive(:min).and_return(min)
    allow(rate).to receive(:max).and_return(max)
  end

  it "should return the min and max projection" do
    expect(subject.projection).to eq [
      subject.revenue * rate.min,
      subject.revenue * rate.max
    ]
  end
end
{% endhighlight %}

Then we can implement this method on Customer like so:

{% highlight ruby linenos %}
class Customer < ActiveRecord::Base
  belongs_to :rate
  belongs_to :estimator

  def projection
    [
      revenue * rate.min,
      revenue * rate.max
    ]
  end
end
{% endhighlight %}

Now we can revisit our Estimator tests and update them to look like this:

{% highlight ruby linenos %}
RSpec.describe Estimator do
  subject { Estimator.new }

  describe "#projection" do
    let(:customer) { double }
    let(:projection) { [80,90] }

    before do
      allow(subject).to receive(:customers).and_return([customer])
      allow(customer).to receive(:projection).and_return(projection)
    end

    it "should return the sum of the estimated min and max projections" do
      expect(subject.projection).to eq [
        projection.min,
        projection.max
      ]
    end
  end
end
{% endhighlight %}

And the updated Estimator code:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.inject([0,0]) do |(min, max), customer|
      min += customer.projection.min
      max += customer.projection.min
      [min, max]
    end
  end
end
{% endhighlight %}

I think we've definitely made an improvement here. Estimator know only knows
one method on Customer. But is there more we could do? Let's leave
Estimator for a second and go back to our current tests for Customer.

{% highlight ruby linenos %}
RSpec.describe Customer do
  subject { Customer.new }

  describe "#projection" do
    let(:rate) { double }

    before do
      allow(subject).to receive(:rate).and_return(rate)
      allow(rate).to receive(:min).and_return(min)
      allow(rate).to receive(:max).and_return(max)
    end

    it "should return the min and max projection" do
      expect(subject.projection).to eq [
        customer.revenue * rate.min,
        customer.revenue * rate.max
      ]
    end
  end
end
{% endhighlight %}

To me, this looks like Customer knows too much about Rate. Why does Customer
care that Rate has a min and a max? What is it that we actually want to get
from customer? When you really think about it, we want to multiply a customer's
revenue by a customer's rate. The fact that Rate happens to be represented with
a minimum and maximum value shouldn't matter to us. This is the code we want:

{% highlight ruby linenos %}
def projection
  rate * revenue
end
{% endhighlight %}

So just how do we go about doing this? It's important to remember here that `*`
is just another method. `1 * 2` is just syntax that calls the method `*` on `1`
and passes `2` as the argument. There is special syntax that allows us to
leave off the `.` which makes it look different. If we imagined that `*` was
really called `times`, it would look like this: `1.times(2)`.

With that knowledge, we can go ahead and implement the `*` method on Rate.
Here's our test:

{% highlight ruby linenos %}
RSpec.describe Rate do
  subject { Rate.new(min: min, max: max) }
  let(:min) { 80 }
  let(:max) { 90 }

  describe "#*" do
    let(:multiplier) { 100 }

    it "should return the two possible rates" do
      expect(subject * multiplier).to eq [
        min * multiplier,
        max * multiplier
      ]
    end
  end
end
{% endhighlight %}

And the implementation:

{% highlight ruby linenos %}
class Rate < ActiveRecord::Base
  def min
    self[:min] / 100.to_f
  end

  def max
    self[:max] / 100.to_f
  end

  def * other
    [
      min * other,
      max * other
    ]
  end
end
{% endhighlight %}

Now we can multiply a Rate by some other number and get an Array back that
contains the minimum and maximum values as applied to that other number.

This now simplifies our code in Customer:

{% highlight ruby linenos %}
class Customer < ActiveRecord::Base
  belongs_to :rate
  belongs_to :estimator

  def projection
    rate * revenue
  end
end
{% endhighlight %}

But what should we test now? One of the points of this was for Customer to know
less about Rate. Right now however, our tests still know about Rate so we can
check the return values. But maybe we shouldn't be testing the return values at
all? Rate already has a test that asserts what happens when you call the `*`
method on it. Therefore, all we want to test here is that we called that method
properly, and with the correct arguments. Now our Customer test looks like this:

{% highlight ruby linenos %}
RSpec.describe Customer do
  subject { Customer.new(revenue: revenue) }
  let(:revenue) { 100 }

  describe "#projection" do
    let(:rate) { double }

    before do
      allow(subject).to receive(:rate).and_return(rate)
    end

  	it "should send the * message to rate" do
  	  expect(rate).to receive(:*).with(revenue)
  	  subject.projection
  	end
  end
end
{% endhighlight %}

Now Customer looks simpler, and all it knows is that Rate has a `*` method. But
what happens when we go back out to Estimator? What does it know still?

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.inject([0,0]) do |(min, max), customer|
      min += customer.projection.min
      max += customer.projection.max
      [min, max]
    end
  end
end
{% endhighlight %}

Estimator still knows what it is expection from the customer's projection
method. Conceptually all we want to do is get the sum of the customer's
projections. It would be nice if we could do something more like this:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.inject([0,0]) do |sum, customer|
      sum + customer.projection
    end
  end
end
{% endhighlight %}

So, let's make our code work like that! First, we're going to need an object
that takes a min and a max itself and knows how to add other mins and maxes to
it. For lack of a better name, we'll call it MinMax. Here's the test:

{% highlight ruby linenos %}
RSpec.describe MinMax do
  subject { MinMax.new(min, max) }
  let(:min) { 80 }
  let(:max) { 90 }

  describe "#+" do
    let(:other) { [min, max] }

    it "should return a new object that responds to min and max" do
      new_min_max = subject + other
      expect(new_min_max.min).to eq subject.min + other.min
      expect(new_min_max.max).to eq subject.max + other.max
    end
  end
end
{% endhighlight %}

And here's the implementation:

{% highlight ruby linenos %}
class MinMax
  attr_reader :min, :max

  def initialize(min, max)
    @min, @max = min, max
  end

  def + other
    new_min = min + other.min
    new_max = max + other.max
    self.class.new(new_min, new_max)
  end

  def self.zero
    new(0, 0)
  end
end
{% endhighlight %}

We can use this new class like this:

{% highlight ruby linenos %}
MinMax.new(1, 10)
#=> #<MinMax:0x007ff19604e2e8 @min=1, @max=10>

MinMax.new(2, 10)
#=> #<MinMax:0x007ff193045650 @min=2, @max=10>

MinMax.new(1, 10) + MinMax.new(2, 10)
#=> #<MinMax:0x007ff1950009b8 @min=3, @max=20>

MinMax.new(1, 10) + [2, 10]
#=> #<MinMax:0x007ff1950009b8 @min=3, @max=20>
{% endhighlight %}

Now we can refactor Estimator like so:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.inject(MinMax.zero) do |minmax, customer|
      minmax + customer.projection
    end
  end
end
{% endhighlight %}

Which can now be written even more succinctly as:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.map(&:projection).inject(MinMax.zero, :+)
  end
end
{% endhighlight %}

And we're done! Now our final implementation code looks like this:

{% highlight ruby linenos %}
class Estimator < ActiveRecord::Base
  has_many :customers

  def projection
    customers.map(&:projection).inject(MinMax.zero, :+)
  end
end

class Customer < ActiveRecord::Base
  belongs_to :rate
  belongs_to :estimator

  def projection
    rate * revenue
  end
end

class Rate < ActiveRecord::Base
  def min
    self[:min] / 100.to_f
  end

  def max
    self[:max] / 100.to_f
  end

  def * other
    [
      min * other,
      max * other
    ]
  end
end

class MinMax
  attr_reader :min, :max

  def initialize(min, max)
    @min, @max = min, max
  end

  def + other
    new_min = min + other.min
    new_max = max + other.max
    self.class.new(new_min, new_max)
  end

  def self.zero
    new(0, 0)
  end
end
{% endhighlight %}

We have a bit more code than before, but the complexity of individual pieces
has gone down. One objection I sometimes run into when proposing refactorings
like this is that the complexity of the system as a whole has gone up. This is
certainly true. In fact, we can use tools to assess what has changed. I like to
use the Ruby tool [Flog](https://github.com/seattlerb/flog).

Here's the scores before the refactoring:

{% highlight bash linenos %}
35.4: flog total
 5.9: flog/method average

20.7: Estimator#projection             lib/estimator.rb:6
 4.1: Rate#min                         lib/rate.rb:4
{% endhighlight %}

And after:

{% highlight bash linenos %}
46.1: flog total
 3.8: flog/method average

 9.2: MinMax#+                         lib/min_max.rb:8
 7.5: Estimator#projection             lib/estimator.rb:7
 4.8: Rate#*                           lib/rate.rb:12
 4.4: main#none
 4.1: Rate#min                         lib/rate.rb:4
{% endhighlight %}

As you can see, the overall complexity of the system went up, as expected. But
now the most complex method is a little less than half of what it was before.
I prefer for the complexity of my pieces to go down even if the total
complexity goes up, because there is a point in every software project where
you can no longer understand the whole thing anyway. When that happens, I would
like to have small pieces I can easily understand and work with.

## So what's wrong with this kind of testing?

If you've been paying attention, or you're familiar with this kind of testing,
you may have had some hesitancy about using stubs and mocks to test. They have
problems themselves. Whenever you stub a method and make it return a value, you
are stating that you expect that method to exist and to return a value like the
one you are returning. If that code ever changes, you won't know, your test with
the stubbed value will continue to work.

Thankfully, there are ways to help mitigate this problem. RSpec 3 adds
`instance_double` and `class_double` which won't allow you to stub methods
that don't exist. This gets us part of the way there, but it won't ensure that
those methods return anything like what we say they return in our stubs.

So the problem remains. It is possible to write tests this way, change
the behaviour of collaborators, and have your tests still pass. This is not
good. The test we started with doesn't have this problem. It will throw errors
when methods on collaborating objects change because we are dealing with real
collaborators.

There's a place for both kinds of tests. It's not that you should always test
like this, or always test like that. It seems to me that the best tests for
ensuring your system works (like our first test), are not the same as the best
tests for getting the most design benefit out of TDD. Personally, I use
factories less often than I did in the past, and use this technique more often.
I supplement this with more integration tests so I can maintain confidence that
everything is still working. This is what's working for me right now.

If you have any questions or comments about this post or the code discussed,
I would love to hear them! You can comment here, on the
[repository](https://github.com/ericroberts/factories), or tweet at me at
[@eroberts](https://twitter.com/eroberts).

Good luck with your testing!

<h2 id="references">References</h2>

<ol>
  <li>
    <a
  href="http://blog.testdouble.com/posts/2014-01-25-the-failures-of-intro-to-tdd.html">
      The Failures of "Intro to TDD"
    </a>
  </li>
</ol>