# Test driven development

## Introduction

Traditionally, one would write their software first and after this they would write the tests to support their source code. Test Driven Development (TDD) flips this on its head.

In a nutshell, TDD is the approach of writing tests before writing your source code and using the tests to guide you throughout the development process.&#x20;

Kent Beck is often credited with bringing TDD to the forefront in the early 2000s. And TDD is a core component of the [Extreme Programming (XP)](https://agilealliance.org/glossary/xp/) movement. But we won't delve into too much history. For now let's get into some of the more details around TDD.

## Why doesn't everyone practice TDD?

Now this is a loaded question.&#x20;

Software engineering is an incredibly broad field and it perhaps lacks the structure that other professions do, nevermind other engineering paths. Anyone can step into the world of software engineering with just a computer and an internet connection. That's really it! But most learning materials, particularly the ones aimed at beginners often skip past testing as it can feel like too much to learn.

But **learning to write tests and TDD are 2 quite seperate things**.&#x20;

**TDD requires a change in thinking**. It is also most likely a reverse in approach compared to any way in which you might have tried to solve a problem before.

Some people argue that writing software via TDD slows them down and makes them less productive. I have personally found that TDD slows me down in the short term. I spend **more time thinking about the interface of my code**, **how I want my code to be used** and **how I want it to behave**. In the medium to long term, I have found that TDD provides some pretty substantial benefits that mean it's quite often the right approach for me to take.&#x20;

I have tried a number of methods over the years but I have found that taking a TDD approach when learning new languages is one of the most effective ways to learn for me.

And then there is the fact that it is also simply not for everyone. We are all different and unique in our own ways. The biggest thing to remember is we learn in different ways. And **figuring out the best learning approach which works for you** is in itself an incredibly useful skill to have.

And with that in mind, I am not going to tell you which approach you should adopt.

## Red-green-refactor

TDD has a cyclic approach with 3 main phases of the cycle. It is often referred to as the _red blue green_ refactor cycle.

The 3 phases are intended to force us into the following:

* Red - We write a test which we know will fail. We should also run the test to ensure that we see the _red_ i.e. the test failing.
* Green - We write a small piece of source code and run our test to ensure that we see the _green_ i.e. the test now passing. In this phase we just care about writing enough to get us to the _green_ state. Being in the red is not safe ground, so the general principle is that we want to get back to _green_ as quick as possible.
* Refactor - Now that we know our test passes with a pretty minimal implementation, we can spend this phase improving our implementation. This is the phase we use to care about optimizations.
