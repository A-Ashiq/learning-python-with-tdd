# Error handling

Towards the end of the [previous chapter](dictionaries.md#accessing-key-value-pairs) we discussed how the `.get()` method on a `dict` object wraps around a `KeyError`, which is thrown when trying to access a key which does not exist in a dictionary. This is an example of error handling.

Things don't always go to plan and we need to design for less than ideal outcomes. This is why error handling is an important skill for us to pick up.

Error handling allows us to better control the flow of our programs, to provide resilience and to ensure that our applications can continue to operate for those unwanted scenarios.

In this chapter, we will learn how to catch errors and how to treat them. As well as how to handle multiple errors and treat them seperately or differently. We will also go over how and why we might want to raise our own errors too.

***

## Catching errors

Catching and handling errors in Python is pretty simple. It goes something like this:

```python
try:                          # try clause
    do_something_that_fails() # try block
except KeyError:              # except clause
    do_something_else()       # except block
```

In this case we call out to our function `do_something_that_fails()`, and we catch any `KeyError` which is thrown during the execution of our `try` block, treating it with the call out to the `do_something_else()` function.

{% hint style="info" %}
In contrast, if the `try` block does **not raise an error** then the defined `except` blocks will **not** be executed.
{% endhint %}

The `except` clause catches the **entire** `try` block. In our case, we only had the 1 call to the `do_something_that_fails()` function.&#x20;

### Writing a test for simple error handling

Okay so now that we've got some of the initial theory down, let's setup the approriate files:

```
touch src/error_handling.py tests/test_error_handling.py
```

With these files in place, let's write the test:

```python
from src.error_handling import divide_numbers


class TestErrorHandling:
    def test_divide_returns_fallback_value_for_invalid_string_input(self):
        """
        Given an integer and a string
        When `divide_numbers()` is called
        Then the expected fallback value is be returned
        """
        # Given
        x = 1
        invalid_input = "some-string"

        # When
        value = divide_numbers(x=x, y=invalid_input)

        # Then
        assert value == "N/A"

```

### Writing the function to fulfil our test

And now let's write the corresponding `divide_numbers()` function:

```python
def divide_numbers(x: int, y: int) -> int | str:
    try:
        return x / y
    except TypeError:
        return "N/A"
```

Now just a heads up, what we've written for our `divide_numbers()` function is not particularly good. But we'll come to that later in this section. For now, we can see that if we run our test. With an input of a string for 1 of the arguments to our function. Then a `TypeError` will be raised. In our case, the `TypeError` will be caught and treated with the fallback value of `"N/A"`.

### Seeing the errors in action

To verify this for yourself, comment out the `try/except` clauses within the `divide_numbers()` function and run the test again:

```python
def divide_numbers(x: int, y: int) -> int | str:
    return x / y
```

With this we are removing the `try/except` catch for the `TypeError` and allowing the exception to bubble up to the surface. If we run the test we should see the traceback associated with the `TypeError`:

```python
FAILED [100%]
test_error_handling.py:4 (TestErrorHandling.test_divide_returns_fallback_value_for_invalid_string_input)
self = <test_error_handling.TestErrorHandling object at 0x104b5f590>

    def test_divide_returns_fallback_value_for_invalid_string_input(self):
        """
        Given an integer and a string
        When `divide_numbers()` is called
        Then the expected fallback value is be returned
        """
        # Given
        x = 1
        invalid_input = "some-string"
    
        # When
>       value = divide_numbers(x=x, y=invalid_input)

test_error_handling.py:16: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

x = 1, y = 'some-string'

    def divide_numbers(x: int, y: int) -> int | str:
        # try:
>       return x / y
E       TypeError: unsupported operand type(s) for /: 'int' and 'str'

../src/error_handling.py:3: TypeError
```

### Focused try blocks

Ideally, our `try` blocks should be small and focused. The primary driver behind this is that we should not want to catch errors that we do not know about.&#x20;

For exampe, in this scenario:

```python
try:
    do_something_first()
    do_something_that_fails()
except KeyError:
    do_something_else()
```

We call the `do_something_first()` function within the same `try` block. Now before we expected the `do_something_that_fails()` call to throw a `KeyError`, so we treat that with the `do_somethin_else()` call in the `except` block.&#x20;

But what if the call to `do_something_first()` happened to throw a `KeyError`? In that case, we handled the error with the same treatment as we would have done if the error had been thrown by the `do_something_that_fails()` call.&#x20;

Confusing right?

This is why we should aim to keep our `try` blocks as small as possible. Ideally 1 liners. This reduces the blast errors of our error catching to just the things we know about. We do not want to be in a position where we are **burying errors or catching errors unexpectedly**. This feels opaque and can **often result in more bugs**. In this case, we should be more inclined to allow the error to bubble up so that it can be seen and logged.

***

## Handling multiple errors the same way

Let's say that our `do_something_that_fails()` function call will throw a `KeyError` as well as a `TypeError` and in this scenario we want to treat both errors in the same way with the call to `do_something_else()` as we were doing previously.

This is pretty straight forward for us to do:

```python
try:
    do_something_that_fails()
except (KeyError, TypeError):
    do_something_else()
```

Now we wrap our expected exception types within the same `except` clause as a tuple.

When the program is executed, the first `KeyError` or `TypeError` will be caught and treated with our defined `except` block.

### Writing a test for handling multiple errors&#x20;

So let's write a test to capture this:

```python
class TestErrorHandling:
    ...    
    def test_zero_division_error_returns_fallback_value(self):
        """
        Given 2 integers where the denominator is 0
        When `divide_numbers()` is called
        Then the expected fallback value is be returned
        """
        # Given
        x = 1
        y = 0

        # When
        value = divide_numbers(x=x, y=y)

        # Then
        assert value == "N/A"
```

Running this test will fail with a `ZeroDivisionError`. This error is thrown by division operations when the denominator is `0`. This is of course an impossible calculation and as such Python communicates this clearly to us:

```python
FAILED [100%]
test_error_handling.py:20 (TestErrorHandling.test_zero_division_error_returns_fallback_value)
self = <test_error_handling.TestErrorHandling object at 0x1023eec00>

    def test_zero_division_error_returns_fallback_value(self):
        """
        Given 2 integers where the denominator is 0
        When `divide_numbers()` is called
        Then the expected fallback value is be returned
        """
        # Given
        x = 1
        y = 0
    
        # When
>       value = divide_numbers(x=x, y=y)

test_error_handling.py:32: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

x = 1, y = 0

    def divide_numbers(x: int, y: int) -> int | str:
        try:
>           return x / y
E           ZeroDivisionError: division by zero

../src/error_handling.py:3: ZeroDivisionError
```

So now that we know we can enact a different error from the original error that we have guarded against. Lets go ahead and make use of this concept of treating multiple exceptions with the same `except` block:

```python
def divide_numbers(x: int, y: int) -> int | str:
    try:
        return x / y
    except (TypeError, ZeroDivisionError):
        return "N/A"
```

Now if we run our test file again, we can see that both of our tests pass.

***

## Handling multiple errors differently

Okay so what if we want to handle our errors differently?

This is also pretty straight forward. Instead of us wrapping the exception types within the 1 tuple on a single `except` clause, we can chain together multiple `except` clauses against our single `try` block:

```python
try:
    do_something_that_fails()
except KeyError:
    do_something_else()
except TypeError:
    fallback_to_this()
```

With this in place we call out to `fallback_to_this()` if a `TypeError` is thrown as part of the execution of our `try` block.

The crucial difference here is that our `except` clauses will be checked and the corresponding `except` block will be executed if matched in descending order, going down the chain.

For a more concrete example, we are going to take the `get_item_from_dict()` function that we looked at in the previous chapter. So lets commit the cardinal sin of copying our own code and drop this function into our `src/error_handling.py` file.

<pre class="language-python"><code class="lang-python">def get_item_from_dict(items: dict, key: str) -> int:
<strong>    return items[key]
</strong></code></pre>

So we know from the previous chapter that if we provide `items` as a `dict` with a `key` which does not exist then this function will raise a `KeyError`. We can also do something which would never make sense and pass say a `list` instead of a `dict` to the `items` arg and incur a `TypeError`.

### Writing a test for handling multiple errors differently

Armed with this knowledge we should write our tests:

```python
from src.error_handling import divide_numbers, get_item_from_dict


class TestErrorHandling:    
    ...
    def test_key_error_raised_will_return_fallback_value(self):
        """
        Given a dict and a key which does not exist in the dict
        When `get_item_from_dict()` is called
        Then the expected fallback value is be returned
        """
        # Given
        items = {}
        key = "abc"

        # When
        returned_item: str = get_item_from_dict(items=items, key=key)

        # Then
        assert returned_item == "N/A"
        
    def test_type_error_raised_will_return_alternative_value(self):
        """
        Given an incompatible argument of a list and a key
        When `get_item_from_dict()` is called
        Then the expected alternative value is returned
        """
        # Given
        items = []
        key = "abc"

        # When
        returned_item: str = get_item_from_dict(items=items, key=key)

        # Then
        assert returned_item == "Invalid"

```

You might be noticing a pattern emerge now. As we build our systems, we let our tests take the drivers seat and let them guide us. The result is that our tests describe the behaviours that we expect from our code, including how we expect our systems to react when things don't quite go to plan.

{% hint style="info" %}
Most forms of engineering & manufacturing processes are bound by law to provide specifications for their products.

In our world, our test suites double up as a form of specification. It shows the reader exactly what we expect from our system.
{% endhint %}

### Seeing the errors in action

With these tests in place, we are trying to build our `get_item_from_dict()` function so that we can treat the `KeyError` and `TypeError` seperately. If we run the tests as is we will get failed tests as follows:

```python
FAILED [ 75%]
test_error_handling.py:36 (TestErrorHandling.test_key_error_raised_will_return_fallback_value)
self = <test_error_handling.TestErrorHandling object at 0x10656d2b0>

    def test_key_error_raised_will_return_fallback_value(self):
        """
        Given a dict and a key which does not exist in the dict
        When `get_item_from_dict()` is called
        Then the expected fallback value is be returned
        """
        # Given
        items = {}
        key = "abc"
    
        # When
>       returned_item: str = get_item_from_dict(items=items, key=key)

test_error_handling.py:48: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

items = {}, key = 'abc'

    def get_item_from_dict(items: dict, key: str) -> str:
>       return items[key]
E       KeyError: 'abc'

../src/error_handling.py:9: KeyError
```

```python
FAILED [100%]
test_error_handling.py:52 (TestErrorHandling.test_type_error_raised_will_return_alternative_value)
self = <test_error_handling.TestErrorHandling object at 0x10656dbb0>

    def test_type_error_raised_will_return_alternative_value(self):
        """
        Given an incompatible argument of a list and a key
        When `get_item_from_dict()` is called
        Then the expected alternative value is returned
        """
        # Given
        items = []
        key = "abc"
    
        # When
>       returned_item: str = get_item_from_dict(items=items, key=key)

test_error_handling.py:64: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

items = [], key = 'abc'

    def get_item_from_dict(items: dict, key: str) -> str:
>       return items[key]
E       TypeError: list indices must be integers or slices, not str

../src/error_handling.py:9: TypeError
```

### Writing the function to satisfy our tests

Armed with this, lets go ahead and re-write our `get_item_from_dict()` function to apply the concept of handling the errors seperately:

```python
def get_item_from_dict(items: dict, key: str) -> str:
    try:
        return items[key]
    except KeyError:
        return "N/A"
    except TypeError:
        return "Invalid"
```

If we run our test file again we can see they all pass.

***

## Else clause

Coming to the more rarely used available features within error handling, brings us to the `else` clause.

The `else` clause can be used to define a block of code which is to be executed if the `try` block ran successfully without throwing an error. This can be useful when we want to keep our `try` blocks small and focused, so that we are not blindly catching exceptions:

{% code fullWidth="true" %}
```python
try:
    do_something_that_passes()
except KeyError:
    do_something_else()
else:
    do_next_thing()             # this runs after the `try` block executes successfully
```
{% endcode %}

So you might be wondering. Why would I bother with this clause?

You might be tempted to re-write the above as follows:

```python
try:
    do_something_that_passes()
except KeyError:
    do_something_else()

do_next_thing()
```

However, these are **not** the same.&#x20;

In the 2nd version, where we moved `do_next_thing()` to be outside of the error handling, then `do_next_thing()` will be executed after the error handling. So in theory if a `KeyError` is raised from the execution of the `try` block, we wil then call `do_something_else()`, after which `do_next_thing()` will be called, assuming `do_something_else()` does not stop the execution of the program by say raising another error inside of it.

A pattern that you will likely see a lot of is _returning early_ to control the flow of execution:

```python
try:
    do_something_that_passes()
except KeyError:
    return do_something_else()

do_next_thing()
```

In this case, if a `KeyError` is thrown then we will return out of this block and `do_next_thing()` will not be called. You might want to consider returning early as a sort of turning off the main path.

***

## Finally clause

And that brings us to the final optional clause if you'll excuse the dad joke!

The `finally` clause can be used as a sort of teardown step. Something that we always want to execute at the end, regardless of whether an error was raised or the `try` block ran successfully without any errors.

The key thing to be aware of here, is that the `finally` block is executed as the **last thing before the try block completes.**

```python
try:
    do_something()
except KeyError:
    do_something_else()
finally:
    always_do_this()             
```

The `finally` clause can be handy for teardown type operations, perhaps to close and release a connection to an external resource like a database or a file.

***

## Surfacing errors

So we have been commiting a number of cardinal sins in our example code snippets in this chapter so far. If you scroll up you will come across a function which looks something like this:

```python
def divide_numbers(x: int, y: int) -> int | str:
    try:
        return x / y
    except (TypeError, ZeroDivisionError):
        return "N/A"
```

Imagine you were calling this function with your `x` and `y` arguments. Now lets say when we called out to this `divide_numbers()` function we hit either a `TypeError` or a `ZeroDivisionError`, in either case we would receive a return value of `"N/A"`.

Lets also imagine we passed the return value of this to some other piece of logic. See what the problem with this is? This is a pretty obvious way of embedding bugs into our systems.

The way this function is currently structured is very unkind to the callers of our code. We are being incredibly opaque and we are forcing callers to inspect the return value before deciding what to do.

{% hint style="info" %}
Building good quality software revolves around communicating intent as clearly as possible.
{% endhint %}

From the caller’s perspective, the only way they can tell whether the operation was unsuccessful is to check the type of the return value. This seems like a surefire way of imposing more cognitive load on the caller.&#x20;

You can bet your team members won't thank you for this! And we can safely say that our function was unclear and ambigious.

In most scenarios, we should allow the error to bubble up to the calling code. That way we are being explicit and clear as to the fact that there was an issue with the call.

***

## Custom exceptions

Building on the point we just made, we want to write software that is clear and explicit.&#x20;

But built-in exceptions might not always communicate our intent to the letter. There may also be situations in which we want to raise an error to describe a condition which has or has not been met. If this doesn't make sense yet, then don't worry we'll cover this with some examples soon enough.

### Writing the test for a custom exception

Before we get started, lets write a test to capture our theory:

```python
import pytest

from src.error_handling import (
    divide_numbers,
    get_item_from_dict,
    FoodNotAvailableForBreedError,
    get_food_type_for_breed,
)

class TestErrorHandling:
    ...
    def test_custom_exception_is_raised(self):
        """
        Given an unsupported dog breed
        When `get_food_type_for_breed()` is called
        Then a `FoodNotAvailableForBreedError` is raised
        """
        # Given
        unsupported_dog_breed = "Poodle"

        # When / Then
        with pytest.raises(FoodNotAvailableForBreedError):
            get_food_type_for_breed(breed=unsupported_dog_breed)
```

Here we have our new `get_food_type_for_breed()` function. With an unsupported input argument, we expect a `FoodNotAvailableForBreedError` is raised.

With this in place lets go ahead and implement the function:

```python
def get_food_type_for_breed(breed: str) -> str:
     if breed == "Poodle":
         raise FoodNotAvailableForBreedError
```

Seems simple enough right? Now to implement our new custom exceptions, we need to extend from the base exception class like so:

```python
class FoodNotAvailableForBreedError(Exception):
    ...
```

With all of these pieces in place, our test will pass.

You might be wondering, if we are creating and implementing custom exceptions as part of systems then surely we will end up with quite a few of these?&#x20;

The answer to that question is yes. But we should be prepared to accept this as a trade off for building a system which is clear about its intention and about its failure scenarios.

***

## Summary

In this chapter, we went into some detail around how to handle errors as well as how to manipulate them to our advantage.

Knowing how to handle errors is incredibly important to being able to control the flow of logic through our applications. And we will definitely see more and more of this as we progress through this course.

{% hint style="info" %}
As always, you can find the source code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-course-materials)
{% endhint %}

***

## References

* [Errors and Exceptions | Python Official Documentation](https://docs.python.org/3/tutorial/errors.html)
