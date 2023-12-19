# Sets

A set in Python is an unordered array-like data structure. The most interesting and useful thing to note about sets is that they de-duplicate items and therefore only contain unique items. Under certain scenarios this can be a very useful property if we don't need to care about ordering.

Let's create ourselves a test file so that we can prove some of these concepts to ourselves.

```
touch sets.py test_sets.py
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
