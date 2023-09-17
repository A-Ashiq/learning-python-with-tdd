# Writing our first function

## Introduction

We are going to take a very simple task and adopt the TDD approach in solving it. Don't worry, this is purely so that we can gain some early experience with TDD. Later in this course, we will solve more _meaningful_ problems.

This will be our first opportunity to apply the [test template which we came up in the previous section](test-template.md).

## The problem

So we want to write a function which takes 2 numbers and returns the sum of these numbers. This will be our only requirement.&#x20;

Later in the course, once we start building more useful and meaningful things, we will tackle dealing with changing and additional requirements.&#x20;

But for now, let's focus on the task at hand.

## Setup files

Within the project we made earlier, we will want to create 2 files. 1 file for the source code itself and another for the tests. For now, let's not worry too much about folder structure or file placement. Lets just place both files at the root level of our project.

Without further ado, lets go ahead and create the files. In your terminal, run the following command:

```bash
touch calculations.py test_calculations.py
```

> The `touch` command is a Linux command commonly used to create files

Here, we are saying create some new files for us with the following names.

The `pytest` runner will collect files which have filenames which are prefixed with `test_`.&#x20;

## Naming our test

Lets head over to our brand new `test_calculations.py` file.

Within that file, we can create our very first unit test. Following our test template, we will first create the test class and then start writing the first method on the class.&#x20;

Lets say that we are going to call our function `calculate_sum()`:

```python
class TestCalculateSum:
    def test_returns_correct_number(self):
        ...
```

So far so good, we have a test class which explicitly references the thing we will be testing. And the first method on the test class i.e. our first actual test describes what we are testing.

## Structuring our test

Once again, following our template means we should consider our test in 3 discrete parts. For our simple function this will relay to:

1. Creating 2 variables which represent the numbers that we want to pass to our `calculate_sum()` function.
2. Calling our `calculate_sum()` function with the 2 numbers.
3. Checking that the returned number is what we expect it to be.

Translating this with the `Given, When, Then` framework and we can say:

* Given 2 numbers
* When `calculate_sum()` is called
* Then the expected result is returned

With that in place, we can proceed to writing our test. Don't worry, if you think this felt long winded that's okay. With practice this will all become muscle memory.

## Writing our test

So let's write our actual test.

Now there a couple of different ways in which we can approach this. The first is the more _by the book_ approach. We're going to try that to start off with, and then we can discuss why I personally tend not to follow it.

If we are to follow the TDD cycle by the book. Our first _red_ iteration would be the following:

```python
class TestCalculateSum:
    def test_returns_correct_number(self):
        """
        Given 2 numbers
        When `calculate_sum()` is called
        Then the expected result is returned
        """
        # Given
        ...
        
        # When
        calculate_sum()

        # Then
        ...
        
```

To run our test we should call the following command in our terminal:

```sh
pytest test_calculations.py
```

This will give us our first error:

```
test_calculations.py F                                                                                                                                               [100%]

================================================================================= FAILURES =================================================================================
_______________________________________________________________ TestCalculateSum.test_returns_correct_number _______________________________________________________________

self = <test_calculations.TestCalculateSum object at 0x10557eca0>

    def test_returns_correct_number(self):
        """
        Given 2 numbers
        When `calculate_sum()` is called
        Then the expected result is returned
        """
        # Given
    
    
        # When
>       calculate_sum()
E       NameError: name 'calculate_sum' is not defined

test_calculations.py:12: NameError
========================================================================= short test summary info ==========================================================================
FAILED test_calculations.py::TestCalculateSum::test_returns_correct_number - NameError: name 'calculate_sum' is not defined
============================================================================ 1 failed in 0.02s =============================================================================
```

Remember our [red-green-refactor](../testing/test-driven-development.md#red-green-refactor) cycle?&#x20;

The general principle goes, that we want the **errors to guide us** and tell us where to go next whilst we are still in the _red_ phase.

This particular error is a `NameError` which is telling us that we do not have a function called `calculate_sum()` within scope.&#x20;

That is kinda obvious because we haven't written it yet! There are some who adhere to this stringently and more power to them. We might find the first couple of iterations of the cycle to be a little tedious. Particularly when we know ahead of time what the first couple of errors are always going to be i.e. the thing you're testing has not yet been defined.

With this approach, the cycle wants us to define the function, see the test turn green and then write the rest of the test. Rinse and repeat.

***

For our approach, we are going to write the test in its entirety first and then we can take a look at the actual code.

With this in mind, let's write the test:

```python
class TestCalculateSum:
    def test_returns_correct_number(self):
        """
        Given 2 numbers
        When `calculate_sum()` is called
        Then the expected result is returned
        """
        # Given
        x = 1
        y = 2

        # When
        calculated_sum = calculate_sum(x=x, y=y)

        # Then
        assert calculated_sum == 3
```

This first test is going to take 2 numbers of `1` and `2` and provide them to call to our `calculate_sum()` function. The final part of the test is to check that the correct number is returned.

### Running our test - red

If we run this we're going to get that first `NameError` .

Remember, to run our test suite we need the following command from a terminal:

```bash
pytest test_calculations.py
```

Alternatively, if you are using a Pycharm IDE. Make sure you have selected `pytest` as the default test runner for the project:

<figure><img src="../.gitbook/assets/Screenshot 2023-09-17 at 19.48.18.png" alt=""><figcaption></figcaption></figure>

Once you have this selected and applied. You should be able to run the test by selecting the green triangle:

<figure><img src="../.gitbook/assets/Screenshot 2023-09-17 at 19.55.02.png" alt=""><figcaption></figcaption></figure>

Running this test as things stand will fail with the aforementioned `NameError`:&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-09-17 at 19.56.17.png" alt=""><figcaption></figcaption></figure>

### Writing enough to make the test pass

So now that we know we are in the _red_ phase. We want to switch gears and get ourselves in to the _green_ zone. To do this we want to write the smallest amount of code as possible. Once we're back in the safe zone of _green_, then we can think about how to improve things.

So let's write the code that we need to get our test to pass. But remember, the goal here is not to write the fully designed solution, it's just to make the test pass.

With this in mind we will need the following:

* A new function called `calculate_sum()` which takes 2 arguments of `x` and `y`.
* When provided with `x=1` and `y=2`, it should return the number `3`.

Now the easy thing might be to just call `sum()` within `calculate_sum()` and that will be our final answer. But we want to get back to _green_ as quick as possible. Without consideration for our final solution just yet. This is all about just making the test pass:

```python
def calculate_sum(x, y):
    return 3
```

I know what you're thinking. This is clearly wrong. But our test will now pass once we import our `calculate_sum()` function into our test file.

```python
from calculations import calculate_sum


class TestCalculateSum:
    def test_returns_correct_number(self):
        """
        Given 2 numbers
        When `calculate_sum()` is called
        Then the expected result is returned
        """
        # Given
        x = 1
        y = 2

        # When
        calculated_sum = calculate_sum(x=x, y=y)

        # Then
        assert calculated_sum == 3
```

Running our tests will give us something like the following:

```
❯ pytest test_calculations.py
=========================================================================== test session starts ============================================================================
platform darwin -- Python 3.9.13, pytest-7.2.0, pluggy-1.3.0
rootdir: /Users/afaanashiq/projects/tdd-course/1-writing-our-first-function
collected 1 item                                                                                                                                                           

test_calculations.py .                                                                                                                                               [100%]

============================================================================ 1 passed in 0.00s =============================================================================
```

### Refactoring our test

So we already know that our function is kinda rubbish. It would only be useful for the set of scenarios which will return the integer 3. For any other scenarios our function won't be fit for purpose. But now that we are in the safe zone of _green_. We can be sure that it is safe to refactor our function.

Now let's refactor our function to sum the 2 provided arguments:

```python
def calculate_sum(x, y):
    return sum(x, y)
```

### Running our test again

Running our test again in this case will still pass. We are lucky because the implementation of this task was so simple. We can safely say we probably won't get so lucky again!

We could go on to write more tests and verify more of our assumptions, whilst also checking edge cases. But the purpose of this excercise was to get our first taste of TDD. As we move forward, we will use our tests to guide us in the directions we want.&#x20;

## References

* [Linux touch command documentation](https://www.man7.org/linux/man-pages/man1/touch.1.html)
