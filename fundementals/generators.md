# Generators

Generators are somewhat different to data structures like lists or tuples which do all the work up front. In those data structures we store all the given data in memory right from the word go.

Generators allows us to _stream_ data through item by item. They don't save all the items in memory. Instead they will give us each item one by one as and when we ask for them.\
This is a really useful property, particularly when we are dealing with large collections of data.\
However, it does mean we lose some of useful properties that we would otherwise have had with list or tuples, such as being able to call `len()` or acces items at a given index. Because the generator gives us each item individually it would have no way of knowing or understanding how many items it holds ahead of time.

So when we are using these objects, how do we know when we have ran out of items?\
When we are running through a generator, it will return items to us one at a time. Once we reach the end, it can be said that we have _exhausted_ the generator. At this point, the object will raise a `StopIteration` error.

Enough with the theory, lets write some tests and verify this behaviour for ourselves.\
To get started lets create the file we need. In your terminal:

```
touch tests/test_generators.py
```

Drop into our new test file and lets write the test:

{% code lineNumbers="true" %}
```python
import pytest


class TestGenerators:
    def test_items_are_yielded_until_exhausted(self):
        """
        Given an iterator of integers
        When `next()` is called continuously
        Then the next integers are returned
            until the iterator is exhausted
        """
        # Given
        iterator = (n for n in range(3))

        # When
        first_value = next(iterator)
        assert first_value == 0

        second_value = next(iterator)
        assert second_value == 1

        third_value = next(iterator)
        assert third_value == 2

        # Then
        with pytest.raises(StopIteration):
            next(iterator)

```
{% endcode %}

On line 13, we create an iterator object which will yield 3 integers starting from 0.

We then call `next()` on the iterator on lines 16, 19 and 22. The `next()` keyword is how we can ask for the next remaining item from the iterator.

Line 26 contains a piece of syntax we have not come across just yet. The line `with pytest.raises(StopIteration)` says when the encapsulated bit of code throws a `StopIteration` error catch it and bury it because this is expected. The _encapsulated code_ is the indented block contained on line 27. If we had not included line 26, the execution of the test would have thrown a `StopIteration` error and the test would have failed. But in scenarios like this we are actively looking for the error to be thrown.
