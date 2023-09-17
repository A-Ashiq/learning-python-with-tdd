# Test template

## Introduction

To start off with, we are going to set out our test template. We will use this template pretty consistently throughout this course as it will provide a framework for us to write tests with.

We may find there to be some situtations in which the framework does not apply. But that's okay.&#x20;

## Test naming

Generally speaking, we should follow a consistent template when writing our tests. This becomes especially important when working within a team. If everyone is writing tests in roughly the same format then our test suite is much easier to read.&#x20;

And for the most part, we tend to be **reading tests when we are fixing bugs and therefore under pressure**. We should always be considering our reader first and foremost.&#x20;

We should ensure that the **name of our test references the thing we are testing**, and probably also a **brief explanation of what we are testing.**&#x20;

The following template in pseudo-code can help us get started:

```python
def test_<name of the thing>_<what this test does>():
    ...
```

## Test structure

Typically there are 3 parts of a unit test:

1. The part in which we **arrange** our test. This is where get things set up for ourselves and bring the state of the system in line with what we expect.
2. Where we carry out the **act**. This is when we call the thing we want to test, along with any parameters which we have set up in the previous step.&#x20;
3. The final part is where we **assert** for the results which we expect from our test.&#x20;

This is commonly known as the 3 As and it provides a framework for us to write our unit tests with.

You may have also come across this in the form of `Given, When, Then`. In other words:

* `Given` some state and input
* `When` we call this piece of our system
* `Then` the result is X.

The 3 A's and the `Given, When, Then` frameworks provide us with the similar structures and are often used interchangeably.&#x20;

For our purposes, we will use the `Given, When, Then` approach, as this can encourage us to write test specification in the form of documentation alongside our test code in a more _human-readable_ format.

## Our initial test template

Putting all these things together, and we can come up with something which will form the backbone of our unit test template:

```
def test_<name of the thing>_<what this test does>():
    # Given section
    
    # When section
    
    # Then section
```

## Improving our test template

Right so what happens when we want to write multiple tests for the 1 piece of logic? The `<name of the thing>` part is going to become really repetitive.&#x20;

To reduce duplication as well as minimise our own chances of making a mistake. We can wrap all our tests for that part of the system which we are testing within a test class:

```
class Test<name of the thing>:
    def test_<what this first test does>():
        ...
    
    def test_<what this second test does>():
        ...
```

With this we only need to describe the purpose of the test on each method on the overarching test class.

## Template test specification

As we are adopting the `Given, When, Then` approach we also want to ensure that we have a framework in place for writing our specification. This is the piece of documentation that we will write for each test:

```python
class Test<name of the thing>:
    def test_<what this first test does>():
        """
        Given <some state and input>
        When <this piece of our system is called>
        Then <the result is X>
        """
        # Given
        <>
        
        # When
        <>
        
        # Then
        <>
```

We can include our specification as a docstring on the test method.&#x20;

And then we can signifiy the start of each of the 3 main sections of the test with a comment containing the keyword as a kind of signpost to our readers to say 'hey this is the given section, you should expect to find setup type stuff here'. &#x20;

## Summary

This test template can give us a really handy framework to use when we are writing unit tests. You might be thinking that this is incredibly verbose and long-winded. And you would be correct. It is verbose. But it is also explicit and clear.&#x20;

There are plenty of sign posts for the reader which are inherently built into place by virtue of our template. In general, we should favour our test code to be descriptive and clear at the expense of being verbose.&#x20;

## References

* [Given When Then definition - Martin Fowler](https://martinfowler.com/bliki/GivenWhenThen.html)
