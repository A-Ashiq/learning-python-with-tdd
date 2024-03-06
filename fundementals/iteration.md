# Iteration

Iteration is the act of taking each individual item from a sequence of items. It is an incredibly powerful and useful concept. And one that we are likely to come across often. People often refer to iteration as _looping over_ or _looping through_ a collection of items. All these things refer to the same concept.

We can refer to objects which produce some sequence of items that can be iterated over as _iterables._ So in this sense [lists](lists.md), [tuples](tuples.md), [sets](sets.md), [generators](generators.md) and [dictionaries](dictionaries.md) are all different kinds of iterables because they ultimately produce a sequence of items.

But in Python, any object which implements an `__iter__()` method is an iterable. So in theory we have the freedom to build our own iterables if we wanted.

For now, lets focus on getting the concept of iteration set in our minds. To do this, we'll need a couple of files. In your terminal:

```
touch src/iteration.py tests/test_iteration.py
```

***

## Write the test first

To start with lets write the test:

```python
from src.iteration import print_all_items


class TestIteration:
    def test_loops_over_list(self):
        """
        Given a list of strings
        When `print_all_items()` is called
        Then each item is printed
        """
        # Given
        items = ["red", "blue", "green"]

        # When / Then
        print_all_items(items=items)
```

If we run this, we will get our usual `ImportError` because we've tried to import the `print_all_items()` function which does not yet exist.

So lets head over to the source file and write the function:

{% code title="" lineNumbers="true" %}
```python
def print_all_items(items):
    for item in items:
        print(item)
```
{% endcode %}

Line 2 is the key part here. We use the built-in `for` keyword to create the iteration. We then assign`item` as the variable name. Note that it is up to us decide what this variable name is. Once we drop into the iteration, that thing can be referred to as `item`. \
We then use the `in` built in keyword followed by the name of the iterable. This would look something like the following in formulaic terms.

```python
for <variable assigned to each item> in <name of iterable>
```

On line 3, we have dropped into the iteration. And at this point we have access to the individual item, so we can perform the action that we want. In this case we simply `print()` the item.

If we run our test now we can see something that resembles the following:

```python
============================= test session starts ==============================
collecting ... collected 1 item

test_iteration.py::TestIteration::test_loops_over_list PASSED            [100%]
red
blue
green


============================== 1 passed in 0.00s ===============================

Process finished with exit code 0
```

***

## References

* [Iterators | Python official documentation](https://docs.python.org/3/tutorial/classes.html#iterators)
