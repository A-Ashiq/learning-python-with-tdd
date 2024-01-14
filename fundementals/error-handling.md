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

And now let's write the corresponding `divide_numbers()` function:

```python
def divide_numbers(x: int, y: int) -> int | str:
    try:
        return x / y
    except TypeError:
        return "N/A"
```

Now just a heads up, what we've written for our `divide_numbers()` function is not particularly good. But we'll come to that later in this section. For now, we can see that if we run our test. With an input of a string for 1 of the arguments to our function. Then a `TypeError` will be raised. In our case, the `TypeError` will be caught and treated with the fallback value of `"N/A"`.

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

***

## Else clause

Coming to the more rarely used available features within error handling, brings us to the `else` clause.

The `else` clause can be used to define a block of code which is to be executed if the `try` block ran successfully without throwing an error. This can be useful when we want to keep our `try` blocks small and focused, so that we are not blindly catching exceptions:

```python
try:
    do_something_that_passes()
except KeyError:
    do_something_else()
else:
    do_next_thing()             # this runs after the `try` block executes successfully
```

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

In this case, if a `KeyError` is thrown then we will return out of this block and `do_next_thing()` will not be called. Returning early can be considered as turnings off the main path.

***

## Finally clause

And that brings us to the final optional clause if you'll excuse the dad joke!

The `finally` clause can be used as a sort of teardown step. Something that we always want to execute at the end, regardless of whether an error was raised or the `try` block ran successfully without any errors.

The key thing to be aware of here, is that the `finally` block is executed as the **last before the try block completes.**

The `finally` clause can be handy for teardown type operations, perhaps to close and release a connection to an external resource like a database or a file.

***

## Custom exceptions





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
