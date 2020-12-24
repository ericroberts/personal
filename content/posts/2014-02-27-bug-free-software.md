---
layout: post
title: Bug Free Software - Is it Possible?
---

Recently the company I work at, [Boltmade](http://www.boltmade.com/), was undergoing a rebranding. Naturally we looked at the websites of other software development companies to see what they were saying about themselves. To me, one site stood out. [8th Light](http://8thlight.com/) says in their [company principles](http://www.8thlight.com/principles) that, "we take responsibility for the correctness of our code by testing it thoroughly. We do not tolerate preventable defects". Going further, the [blog post](http://blog.8thlight.com/eric-smith/2013/04/08/we-are-principled-6.html) that accompanies this says, "if we make a mistake, we'll fix it quickly—for free. It's that simple".

I think what strikes me most about this is that it's surprising at all. Why shouldn't this just be how software development is done? Why do we just accept that all software will have bugs? It is true that is technically impossible to remove all possible bugs from a piece of software. But I think that an over acceptance of this is leading us to create software with far more defects than should be acceptable.

It may be impossible to remove every final last bug, but I can definitely guarantee that everything I know about works correctly if I'm doing my job. An article I read recently ([Bug Free Software? Dream on](http://www.infoworld.com/d/developer-world/bug-free-software-dream-554)) stresses the impossibility of getting rid of all bugs. In particular one phrase bothered me. The article says that, "developers play whack-a-mole" with bugs. This bothers me because in whack-a-mole, the moles come back out of the same hole some undetermined time after you knocked them down in the first place. In a way, it's a perfect description of the experience of many developers. They fix one bug over here, rush to put out a fire over there, and when they come back they find that somehow the first bug has come back again. However, there is no reason developers should ever play whack-a-mole with bugs. Proper testing should ensure that when you fix a bug it stays fixed.

The only way someone can deliver on their promise of bug free software is to test it thoroughly. I don't really care if that means you clicked every path through the system, and tried every use case. If you'd rather do that over writing tests, be my guest. Just remember you'll be doing it every time you change anything. As for me, I'll write the tests once and have them catch things for me from then on.

So why don't more people do this? Well, testing is hard. It takes time to learn how to test, it takes time to setup the tools to test, and it takes time to run the tests (much less time than it would to click through every path in the system, although I would argue that almost nobody actually does this when they say they tested it manually). But that doesn't mean it's not worth it. Investing the time in this area will pay off. But if you don't make testing a priority, figure out how to set it up, and ensure that everyone is running the tests, you won't see any benefit. It takes buy in from everyone. If one person is over in their corner diligently writing tests and nobody else does, you will still have a fragile application.

AirBnB just today [posted a great article](http://nerds.airbnb.com/testing-at-airbnb/) about how they went from, in their words, "a handful of brittle tests to a large, resilient suite". There are some good pointers in that post on how to achieve the same thing. To me, there were three important things that stood out:

1. <span>Change your culture to "make testing a first-class citizen". As I mentioned, if nobody believes in it, it's not going to happen. It's also important to avoid the pattern of "we know tests are important, but we're too busy to do it right now". If it's important, it's important. Do it.</span>
2. <span>The second important thing is to make "the bar to writing tests...so low you can trip over it". Make things easy and people will do them.</span>
3. <span>And finally: "The important thing is to test all the f***ing time. Even when it’s hard. Especially when it’s hard."</span>

So how do you know if you should be thinking about tests? To me, the test for if you need tests (or more tests), is how comfortable you feel pushing a new build to production. Does it make you hesitate? Do you cross your fingers hoping things will be ok? Do you always do it early in the day so you can be around in case something goes wrong? If so, then maybe considering getting some more tests in there. Deploying shouldn't be a scray process. Tests can help.

I'm happy to hear your thoughts, please leave comments below! I could ramble on this topic for a while but I don't want to write a huge wall of text. I'm happy to continue the discussion in the comments or write a follow up post.






