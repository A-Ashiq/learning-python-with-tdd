# Dictionaries

A dictionary is akin to a hash map or associative arrays in other languages. Don't worry just yet if you don't know what any of that means either!

The data structures that we've covered so far, lists, tuples and sets can be said to be _sequence-based_ data structures. Dictionaries are the outlier, as they are indexed by keys, each of which map to a corresponding value.

It is best to think of dictionaries as a set of key-value pairs. Whereby the uniqueness of sets applies and as such the keys within a dictionary are unique.

The keys of dictionaries are hashed. This allows for drastically faster lookup of keys in a dictionary compared to equivalent lookups in sequence-based data structures like lists.

***

## Mutability

Dictionaries are also mutable. In a similar vein to lists as we've seen before, this means we can add or remove items from them after the point of creation.

### Adding a key-value pair

We can add a key value pair by assigning it to an existing dictionary. Before we dive in, we should create the files we need:

```python
touch src/dicts.py tests/test_dicts.py
```

Let's write the test first and then we can implement the function:

{% code lineNumbers="true" %}
```python
from src.dicts import add_item_to_dict


class TestDicts:
    def test_item_can_be_added(self):
        """
        Given an empty dictionary
        And a new key value pair to be added
        When `add_item_to_dict()` is called
        Then the returned dictionary contains the key-value pair
        """
        # Given
        items = {}
        new_key = "abc"
        new_value = 123
        
        # When
        items = add_item_to_dict(items=items, key=new_key, value=new_value)
        
        # Then
        assert new_key in items
        assert items[new_key] == new_value
```
{% endcode %}

To start of with we'll get the usual `ImportError`. So lets now head over to the `dicts.py` file and define our `add_item_to_dict()` function:

{% code lineNumbers="true" %}
```python
def add_item_to_dict(items: dict, key: str, value: int) -> dict:
    items[key] = value
    return items
```
{% endcode %}

Here we take the given dict `items` and assign the provided `key` with the `value`.

{% hint style="info" %}
Note that by assigning the key to the dict `items`we mutate the dict in-place.
{% endhint %}

Running the test now and we can see it passes.

### De-duplicated key value pairs

Keys are deduplicated on write to dictionaries. This means if we have a dictionary which contains a key-value pair and we then try to add another key-value pair which has the same key. Then the value will simply be overwritten.

Let's write a test:

{% code lineNumbers="true" %}
```python
  ...  
    
    def test_items_are_deduplicated_when_added(self):
        """
        Given a dictionary which contains a key-value pair
        And a new key value pair of the same key
        When `add_item_to_dict()` is called
            for both key-value pairs
        Then the returned dictionary contains the 2nd value
        """
        # Given
        items = {}
        key = "abc"
        original_value = 123
        new_value = 456

        # When
        items = add_item_to_dict(items=items, key=key, value=original_value)
        assert items == {key: original_value}
        items = add_item_to_dict(items=items, key=key, value=new_value)

        # Then
        assert items == {key: new_value}
```
{% endcode %}

In this test, we add the value of `123` for the key `"abc"`. We then add a different value of `456` with the same key of `"abc"`.&#x20;

This will overwrite the original value of key with our new value.

***

## Looking up items

As mentioned previously, dictionaries are a key-value pair data structure from which we look up each pair via their key.

Let's say we have the following dictionary:

```python
items = {"abc": 123}
```

In this case, `"abc"` is the only key in the dictionary. Its corresponding value is `123`.

We can check the key is in the dictionary with the followng:

```python
"abc" in items
```

We cannot however check that the value of `123` is in the dictionary with this approach. Remember that the keys are hashed and this is how we access items in dictionaries.

### Accessing key value pairs

Lets say we have a dictionary and we try to access a key which does not exist in that dictionary. What do you think should happen?

Well the answer is. It depends on how we tried to access the key. With dictionaries we have 2 primary methods of accessing keys.&#x20;

We can access keys with the square bracket notation or with the `get()` method.  If we try to access a key which does not exist via the square bracket notation, then a `KeyError` will be raised.

Note that the `get()` method is simply a try/except catch whereby we can return a default value instead of allowing the `KeyError` to be raised.

{% code lineNumbers="true" %}
```python
import pytest

from src.dicts import add_item_to_dict, get_item_from_dict

class TestDicts:
    
  ...  
    
    def test_looking_up_non_existent_key_raises_error(self):
        """
        Given a dictionary and a non-existent key
        When `get_item_from_dict()` is called
        Then a `KeyError` is raised
        """
        # Given
        items = {}
        key = "abc"
        
        # When / Then
        with pytest.raises(expected_exception=KeyError):
            get_item_from_dict(items=items, key=key)
        
```
{% endcode %}

Heading over to our source code file we can implement our new function:

{% code lineNumbers="true" %}
```python
def get_item_from_dict(items: dict, key: str) -> int:
    return items[key]
```
{% endcode %}

Running the test and we can see it passes.

Trying to look up a key which does not exist will throw a `KeyError`. This tells us explicitly that our operation failed because we tried to look up a key which was not in the dictionary at the point in which we enacted the lookup.

This is important because in this scenario we can _catch_ this error and decide how we want to treat the failure scenario. This is known as control flow and is incredibly useful for us to be able to manipulate.

***

## Summary

Dictionaries are useful due to their flexibility and performance benefits. We have the ability to get a sequence based data structure containing just the keys or the values by themselves in addition to the `items()` call that we saw earlier, which can be helpful for iteration type operations.

We can also `pop()` items from a dictionary in a similar way in which we can do with lists.

***

## References

* [Python data structures | Dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
