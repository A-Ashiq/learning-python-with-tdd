# Context

Okay so you've done your first couple of online courses and you've written a hello world app or maybe something a little more exciting.

And straight off the bat I'm going to tell you that testing is probably one of the most important things to understand and appreciate.&#x20;

I'm also going to tell you that writing test code is a skill which you will need if you want to build robust software that can stand the test of time. So I guess you could say it's pretty important.

***

## Why write tests?

Writing robust, maintable software is a difficult thing. It's all but impossible to do without tests in place.

Tests provide us with a few things:

* They **verify our assumptions of our code** and tell us whether or not the source code is behaving in the way we want. You could say that tests can act as a really useful guiderail for us.
* They **double up as specification**. Each test can act as a clause. Whereby each test/clause describes a subset of behaviour. This is really important as this **describes how we intend our software to operate**_._
* After rectifying a bug, we can write tests to catch the bug. By doing this we can add to our _specification._ So we can always ensure from that **point onwards that our software does not exhibit the faulty behaviour** which lead to the bug in the first place.&#x20;

***

## When to write tests?

Now this is has been up for debate for years. You're initial thought might well be, I write my actual code first and then I write the tests to verify the things I expect from the code.

Now what if I told you that there is a large proportion of engineers who write their tests before writing the source code. Seems alien and very uncomfortable at best right?

I'm not going to tell you which approach you should adopt. The only thing **I will tell you for certain is you should be writing tests**.

Generally speaking, the only time I personally don't write tests is for proof of concept (POC) type work. In other words, if I am just trying to build something quickly to prove that something can work.

Throughout this course we will generally adhere to the principle of writing tests first. In case you're wondering. Yes, this has a designated name, Test Driven Development (TDD). We'll expore this in a little more detail next.
