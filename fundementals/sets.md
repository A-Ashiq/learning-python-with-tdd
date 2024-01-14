# Sets

A set in Python is an unordered array-like data structure. The most interesting and useful thing to note about sets is that they de-duplicate items and therefore only contain unique items. Under certain scenarios this can be a very useful property if we don't need to care about ordering.

Let's create ourselves a test file so that we can prove some of these concepts to ourselves.

```
touch src/sets.py tests/test_sets.py
```

***

## Deduplication

In our test file, we are going to write a test to prove the de-deduplication concept to ourselves:

```python
class TestSets:
    def test_items_are_deduplicated(self):
        """
        Given multiple identical strings
        When they are added to a set
        Then the set only contains the 1 unique item
        """
        # Given
        items = set()
        duplicated_item = "a"

        # When
        for _ in range(5):
            items = add_item_to_set(items=items, item=duplicated_item)

        # Then
        assert len(items) == 1
        assert duplicated_item in items

```

In this test we have a `set` along with an item, in this case the string `"a"` that we want to add to our `set`. We repeatedly call our function 5 times to add our item to our `set`.

However, with no `add_item_to_set()` function defined. Our test is going to throw a `NameError` exception:

```python
FAILED               [100%]
test_sets.py:4 (TestSets.test_items_are_deduplicated)
self = <test_sets.TestSets object at 0x1039fb710>

    def test_items_are_deduplicated(self):
        """
        Given multiple identical strings
        When they are added to a set
        Then the set only contains the 1 unique item
        """
        # Given
        items = set()
        duplicated_item = "a"
    
        # When
        for _ in range(5):
>           items = add_item_to_set(items=items, item=duplicated_item)
E           NameError: name 'add_item_to_set' is not defined

test_sets.py:17: NameError
```

With this, lets go and define our `add_item_to_set()` function:

```python
def add_item_to_set(items: set, item: str) -> set:
    items.add(item)
    return items
```

In this function, we take our `set` given as the `items` arg and call the `add()` method with the given `item` arg to add the item to the `set`.

Note that with this we expect the `set` to be deduplicated on write. In other words, if we try to add an item which is already present in the `set` then it will automatically not be added to the `set`

Importing our new function into place for our test:

```python
from src.tuples import add_item_to_set


class TestSets:
    def test_items_are_deduplicated(self):
        """
        Given multiple identical strings
        When they are added to a set
        Then the set only contains the 1 unique item
        """
        # Given
        items = set()
        duplicated_item = "a"

        # When
        for _ in range(5):
            items = add_item_to_set(items=items, item=duplicated_item)

        # Then
        assert len(items) == 1
        assert duplicated_item in items

```

Running this and our test now passes.

***

## Difference

There are a number of operations specific to the `set` data structure which are grounded in the core mathematics world.

The first one we are going to look at is the `difference` operation. The `difference` operation allows us to find all the items in 1 `set` when compared to another `set`.

Let's write a test for this:

```python
from src.tuples import get_difference

    ...
    
    def test_get_difference(self):
        """
        Given 2 sets
        When `get_difference()` is called
        Then the returned set contains 
            the difference of set A against set B
        """
        # Given
        main = {"a", "b"}
        secondary = set("b")

        # When
        difference: set = get_difference(main=main, secondary=secondary)

        # Then
        assert difference == {"a"}
```

You know the drill by now. Our `NameError` will tell us to go and write our `get_difference()` function:

```python
def get_difference(main: set, secondary: set) -> set:
    return set(main).difference(secondary)
```

{% hint style="info" %}
Note that `main - difference` would be shorthand for `set(main).difference(secondary)`.
{% endhint %}

With our function in place, the last thing we need to do is run our now passing test.

***

## Summary

In this section, we learned how to use tuples and what they are. For brevity, we skipped over a few of the more interesting operations. So it might be worth checking them out if you get the time.

{% hint style="info" %}
As always, you can find the source code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-course-materials)
{% endhint %}
