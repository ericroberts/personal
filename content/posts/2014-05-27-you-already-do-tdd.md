---
layout: post
title: You're Already Doing TDD
---

![You're Already Doing TDD](/images/posts/already-doing-tdd.png)

Originally posted on the [Boltmade Blog](http://www.boltmade.com/blog/youre-already-doing-tdd)

I recently watched Uncle Bob's talk [Architecture the Lost Years](https://www.youtube.com/watch?v=WpkDN78P884) on YouTube. At one point he stops and asks the audience who practices Test Driven Development (TDD). By his reaction, it's apparent that not many people have their hands up.

Everyone without their hand up is wrong. They all do TDD. In fact, they have TDD'd every line of code they've ever written, and you have too.

In my post [Red, Green, Refactor](http://www.boltmade.com/blog/red-green-refactor) we defined Test Driven Development as:

> 1. <span>Write a failing test</span>
2. <span>Write some code that passes the test</span>
3. <span>Refactor</span>

So does everyone really do this? Let's take a look from the perspective of a bug being reported by a user in our system:

- <span>A user reports that they can no longer purchase something from your site.</span>
- <span>You go to the purchasing system, and try various paths through until you find one that causes the error the user reported.</span>
- <span>You write some code, then try the permutation that had previously failed to see if it now works as expected.</span>

You've just used tests to drive your development. Every time you open your browser and check to see the behaviour you expected to happen, that's a test. Every time you open your console and check the result of calling some method, that's a test. You've tested your code. How else would you know that it works?

You may have noticed I skipped a step. In our original definition of TDD, step three is refactor. We've really only covered two steps here. In practice, I find that people who are not writing tests for their code often do not do the refactor step. It's just too exhausting. In the simplest case, testing that something works involves switching from your code to the browser, refreshing the page, and verifying that what you see is what you expect. But most of the complex parts of our applications involve much more than that. In order to test that the issue reported above is resolved we'd likely follow these steps:

1. <span>Find a product</span>
2. <span>Add it to your cart</span>
3. <span>Fill in your shipping details</span>
4. <span>Fill in your credit card information</span>
5. <span>Hit purchase button</span>
6. <span>Verify that the purchase was completed successfully</span>
7. <span>Repeat steps 1-6 with different parameters until we're confident we didn't break anything</span>

When you have to go through all of that every time you make a change, are you really going to want to refactor? Even if you are willing to do that now, I wouldn't be willing to bet that future programmers who come across the code are going to want to touch any more than they absolutely have to.

While we have TDD'd our code, we haven't written [Self-Testing Code](http://www.martinfowler.com/bliki/SelfTestingCode.html). Martin Fowler says of Self-Testing Code that:

> You have self-testing code when you can run a series of automated tests against the code base and be confident that, should the tests pass, your code is free of any substantial defects.

You certainly don't have that in the scenario we described above. It sounds pretty great though, doesn't it?

So why don't people do it? Well, it's hard. It's another thing to learn. We are all busy, and in our field we spend a lot of time just keeping up with what is going on. But testing through the UI manually is slow. Testing through the console may be slightly faster, but it's still a lot of typing and manually setting up conditions every time you want to know if something works.

I can promise you that if you devote the time to learning how to test properly, it will pay off. It will pay off when you almost never have to open a browser to be confident that your application is still working. It will pay off when you don't have to manually enter the same conditions over and over to see if something gives you the results you expect.

I don't write perfect code, and I don't write perfect tests. Right now, when I run my test suite, I'm not 100% sure that every part of my application works. But the more I do it, the more my confidence rises. The more I do it, the faster I get at writing tests, and the less time I spend testing things manually. If you've never had the experience of writing an entire feature without opening the browser, and then the first time you test it manually it works as you expected it to, then you're missing out.

So remember, you already do TDD, but you could be doing it so much better.


