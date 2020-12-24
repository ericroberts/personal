---
layout: post
title: Red, Green, Refactor
---

![Red, Green, Refactor](/images/posts/red-green-refactor.png)

Ever since DHH's keynote at Railsconf 2014 and his subsequent blog post [TDD is dead. Long live testing.](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html), the development world (or at least the Ruby community) has been abuzz with thoughts on Test Driven Development (TDD). Uncle Bob, as well as a number of other influential members of the community, have jumped in to give their thoughts on the topic. I've listed some of these other posts at the bottom for further reading. I figured I may as well jump on the bandwagon myself, because hey, bandwagons are fun right?

One thing I've noticed about all of these posts, forum discussions, and tweets, is that there is a lot of focus on the speed of testing and the importance of decoupling. I must admit to being confused by this focus. TDD, as it was explained to me, is pretty simple:

1. <span>Write a failing test</span>
2. <span>Write some code that passes the test</span>
3. <span>Refactor</span>

Red, green, refactor. Notice how it doesn't say anything about speed? Or whether the tests end up hitting the database? It doesn't talk about loading Rails in order to run your tests, or the difference between stubs, fakes, and mocks. It just says three simple things: write a test for some new functionality, write some code to pass that test, and then refactor that code.

While many discussions have revolved around speed, DHH seems to also not agree with the test-first approach in general, because in [Test induced design damage](http://david.heinemeierhansson.com/2014/test-induced-design-damage.html) he criticizes BDD for being such an approach:

> At this point BDD proponents might well argue that, yes, testing units is not what we should be doing. We should be going outside-in. But as long as that's also done under the test-first regime, I don't think it generally help matters much. It still leads down a road of excessive mocking and artificial boundary installations.

I don't agree that the simple act of testing first needs to lead to excessive mocking. By itself, test-first says nothing about *how* I write my test, just *when* I write my test.

The point of testing first is to focus on the task at hand. What functionality am I implementing? Perhaps more importantly, what am I *not* implementing? Once I've figured out what I'm trying to do, I get there the quickest way I know how, and finally, refactor that code to something I'm happy with. It's not unlike writing. Create an outline, write your first draft, and then polish it until you're happy with it.

So where did all this preoccupation with speed and decoupling come from? Nothing in the steps I've talked about so far says whether 4.5 minutes for your test suite is too long or if times over one second are too slow.

However, Wikipedia does have a fuller outline of the steps than what I've presented in their [Test-driven development](http://en.wikipedia.org/wiki/Test-driven_development) article. Let's take a look (emphasis mine):

> 1. <span>Add a test</span>
2. <span>Run *all* tests and see if the new one fails</span>
3. <span>Write some code</span>
4. <span>Run *all* tests</span>
5. <span>Refactor code</span>

If you want to run all your tests every time you add a new one, you want them to be fast, otherwise they will just slow you down. But the fixation on what is the *right* amount of time is damaging the discussion. If DHH is fine with waiting 4.5 minutes for his test suite to run, who cares? I think it's pretty awesome that he has thousands of assertions; most projects I see don't have anything close to that.

DHH also makes a point in his post [Slow database test fallacy](http://david.heinemeierhansson.com/2014/slow-database-test-fallacy.html) about not running all your tests everytime you add a new one, and it is an approach I find reasonable:

> I still wouldn't want to wait 80 seconds every time I make a single change to my model, and want to test that. Of course not! Why on earth would you run your entire test harness for every single line change in a particular model? If you have so little confidence in the locality of your changes, the tests are indeed telling you that the system has overly high coupling.

Speed is important. If your tests are too slow to be useful, what's the point of having them? What entails "fast enough" though, is something that is a matter of individual preference. It is also dependent on whether or not you agree with DHH about not running all of your tests all of the time. Faster is better, but I don't believe in pursuing that at the expense of more important concerns, like writing the code that passes the tests.

Red, green, refactor is, to me, the core of TDD. Decoupled logic in my application is also good idea. If TDD helps me to get there, and decoupling also increases the speed of my tests, those are just bonuses on top of a process I already follow.

So why all the fighting and confusion over TDD? Well, Test Driven Development is just a name. Names are pretty useful things; they help us have discussions about rather complicated topics. If you know what Object Oriented Programming is, and I know what Object Oriented Programming is, we can have a discussion about it without having to stop to define every detail along the way*.

But names can also be harmful. If you "know" what Object Oriented Programming is, and I "know" what Object Oriented Programming is, but our understanding of those two things differs greatly, our discussion will be rather difficult and confusing to follow. We're both assuming that the other has the same ideas in their head, which makes it hard to communicate effectively.

While reading [Sandi Metz](http://www.sandimetz.com/)'s book [Practical Objected Oriented Design in Ruby](http://www.sandimetz.com/poodr/), I noticed something interesting about the way she writes. She doesn't tell you what things are called until after she's already shown you how to do it. You don't have a chance to let your previous knowledge of those concepts get in the way. It's a great way of explaining ideas that ensures everyone is on the same page.

So what does this have to do with TDD? Well, Test Driven Development is a name that means a lot of things to a lot of people. To me, it means red, green, refactor. Everything else just helps me do that better.

Maybe when we have discussions about TDD, we should be stating what our definition of TDD is. If we are all arguing about different ideas, perhaps we could find some common understanding and begin our discussion there. Our first test might fail, but we can figure out how to make it pass and refactor from there.

<small>*Growing a language can be fun. If you haven't already watched [Growing a Language](https://www.youtube.com/watch?v=_ahvzDzKdB0) by Guy Steele, you should! It's a great talk.</small>

### Related Reading
- [TDD is dead. Long live testing.](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html) - DHH
- [Test-induced design damage](http://david.heinemeierhansson.com/2014/test-induced-design-damage.html) - DHH
- [Slow database test fallacy](http://david.heinemeierhansson.com/2014/slow-database-test-fallacy.html) - DHH
- [Monogamous TDD](http://blog.8thlight.com/uncle-bob/2014/04/25/MonogamousTDD.html) - Uncle Bob
- [Test Induced Design Damage?](http://blog.8thlight.com/uncle-bob/2014/05/01/Design-Damage.html) - Uncle Bob
- [When TDD doesn't work.](http://blog.8thlight.com/uncle-bob/2014/04/30/When-tdd-does-not-work.html) - Uncle Bob
- [Professionalism and TDD](http://blog.8thlight.com/uncle-bob/2014/05/02/ProfessionalismAndTDD.html) - Uncle Bob
