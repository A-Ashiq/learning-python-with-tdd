# Error handling

Towards the end of the [previous chapter](dictionaries.md#accessing-key-value-pairs) we discussed how the `.get()` method on a `dict` object wraps around a `KeyError`, which is thrown when trying to access a key which does not exist in a dictionary. This is an example of error handling.

Things don't always go to plan and we need to design for less than ideal outcomes. This is why error handling is an important skill for us to pick up.

Error handling allows us to better control the flow of our programs, to provide resilience and to ensure that our applications can continue to operate for those unwanted scenarios.

In this chapter, we will learn how to catch errors and how to treat them. As well as how to handle multiple errors and treat them seperately or differently. We will also go over how and why we might want to raise our own errors too.

***

## Catching errors

Catching and handling errors in Python is pretty simple. It goes something like this:

```python
try:
    do_something_that_fails()
except KeyError:
    do_something_else()
```

In this case we call out to our function `do_something_that_fails()`, and we catch any `KeyError` which is thrown during the execution of our `try` block, treating it with the call out to the `do_something_else()` function.

The `except` clause catches the entire `try` clause. In our case, we only had the 1 call to the `do_something_that_fails()` function.&#x20;

### Focused try blocks

Ideally, our `try` blocks should be small and focused. The primary driver behind this is that we should not want to catch errors that we don't know about.&#x20;

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

This is why we should aim to keep our `try` blocks as small as possible. Ideally 1 liners. This reduces the blast errors of our error catching to just the things we know about. We do not want to be in a position where we are burying errors or catching errors unexpectedly. This feels opaque and can often result in more bugs. In this case, we should be more inclined to allow the error to bubble up so that it can be seen and logged.

***

## Handling multiple errors the same way





***

## Handling multiple errors differently





***

## Summary



