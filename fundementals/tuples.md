# Tuples

The next data structure we will cover is the tuple.

Tuples are a similar sequence data type to lists. However, tuples are an immutable, built-in data structure of a fixed capacity. This means that once we create them, we cannot then modify them in-place like we saw with lists in the last section.

Because of this immutability, tuples use less memory than lists and tend to be more performant.

Tuples are denoted by comma seperated items. These are often wrapped with round brackets, `()`. But it is the `,` characters not the round brackets which instantiate tuples for us.

To prove some of the core characteristics of tuples, we can create a new test file and another file for the source code. In your terminal:

```
touch tests/test_tuples.py
```

***

## Immutability

Tuples are immutable. Which means we cannot make changes to tuples after they have been created.

### Assigning items to a tuple

We can prove this by writing a test which checks what happens when we try to assign a new item to a tuple:

{% code lineNumbers="true" %}
```python
class TestTuples:
    def test_raise_error_when_item_is_assigned_after_instantiation(self):
        """
        Given a tuple of items
        When another item is assigned to the tuple
        Then a `TypeError` is raised
        """
        # Given
        items = "a", "b", "c"

        # When / Then
        items[3] = "d"
```
{% endcode %}

Now we have an interesting case here that we have not come across yet. In this test, we are checking for and expect an error to be thrown.

Running this test and we will see the following:

```python
FAILED [100%]
test_tuples.py:1 (TestTuples.test_raise_error_when_item_is_assigned_after_instantiation)
self = <test_tuples.TestTuples object at 0x106593c50>

    def test_raise_error_when_item_is_assigned_after_instantiation(self):
        """
        Given a tuple of items
        When another item is assigned to the tuple
        Then a `TypeError` is raised
        """
        # Given
        items = "a", "b", "c"
    
        # When / Then
>       items[3] = "d"
E       TypeError: 'tuple' object does not support item assignment

test_tuples.py:12: TypeError
```

So this makes sense, and it does prove our theory.

But running this as part of our test suite would result in an overall failure, indicating that an unexpected failure occured. But that is not strictly true here.

We can refactor our test to assert for the expected error:

{% code lineNumbers="true" %}
```python
import pytest


class TestTuples:
    def test_raise_error_when_item_is_assigned_after_instantiation(self):
        """
        Given a tuple of items
        When another item is assigned to the tuple
        Then a `TypeError` is raised
        """
        # Given
        items = "a", "b", "c"

        # When / Then
        with pytest.raises(expected_exception=TypeError):
            items[3] = "d"
```
{% endcode %}

In our test, we are now using the `with` keyword to open a context manager. In essence we are saying to the test runner that we are expecting a `TypeError` when the code block within the context manager is executed.

This might feel a little alien, but its important for us to be able to test for failure cases as well as the _happy path_. To manage control flow we need to be sure that the specific errors we expect to be raised are done in the places we expect.&#x20;

***

## Summary

In this section we covered key characteristics of tuples.

{% hint style="info" %}
You can find the source code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-course-materials)
{% endhint %}

