# Lists

The first of the data structures we will cover is the list.

Lists are an ordered, indexed built-in data structure of variable capacity. You might have heard them referred to as arrays from people coming from other languages and for all intents and purposes they are the same.&#x20;

Lists can be instantiated with a pair of square brackets, `[]` or via the contsructor `list()`.

To start of with we should create a new file in which we can write our tests. So in your terminal lets use the following command:

```
touch test_lists.py
```

This command will create a new file called `test_lists.py` . With this in place we can start going over a number of concepts around lists, using our tests to _see_ these concepts in action.

## Mutability

Unlike some of the other data structures we can **add or remove items** from them **after the point of creation**. This behaviour of being able to change a data strcuture after it is created is known as being **mutable**.

### Adding items to a list

But don't take my word for it. Lets write some tests and verify this claim. Lets start of with setting up our first test, we'll use this to check our previous claim that lists are mutable:

```
class TestLists:
    def test_are_mutable(self):
        """
        Given a list of integers
        When an integer is added to the list
        Then the item can be found in the list
        """
        # Given
        new_number = 4
        numbers = [1, 2, 3]

        # When
        numbers.append(new_number)

        # Then
        assert numbers == [1, 2, 3]
```

> This test will fail when ran

Here we are using the `append()` method on the `list` object.&#x20;

As you might have guessed, the `append()` method takes an object and adds it to the end of the list. In our case we took a list which contained the integers 1, 2 and 3, and added the integer 4 to the end of the list.

Our test is in the red state, we've done this on purpose so that we can see the differences between an invalid state compared to what we expect from this.&#x20;

```
test_lists.py:1 (TestLists.test_are_mutable)
[1, 2, 3, 4] != [1, 2, 3]

Expected :[1, 2, 3]
Actual   :[1, 2, 3, 4]
```

Remember that we are trying to prove that lists are mutable. So with that in mind, let's refactor our test to catch our expected behaviour:

```
class TestLists:    
    def test_item_can_be_added(self):
        """
        Given a list of integers
        When an integer is added to the list
        Then the item can be found in the list
        """
        # Given
        new_number = 4
        numbers = [1, 2, 3]

        # When
        numbers.append(new_number)

        # Then
        assert numbers == [1, 2, 3, new_number]
```

Our test now passes, and we've proved the concept of mutability to ourselves.&#x20;

### Removing items from a list

Let's try this again, but this time let's remove an item:

```
    def test_item_can_be_removed(self):
        """
        Given a list of integers
        When an integer is removed from the list
        Then the item cannot be found in the list
        """
        # Given
        number_to_remove = 3
        numbers = [1, 2, number_to_remove]

        # When
        numbers.remove(number_to_remove)

        # Then
        assert numbers == [1, 2, number_to_remove]
```

> From here on out we will be writing each test as a method on our test class. The rest of the test class will be omitted for brevity.

```
test_lists.py:17 (TestLists.test_item_can_be_removed)
[1, 2] != [1, 2, 3]

Expected :[1, 2, 3]
Actual   :[1, 2]
```

Again, our test will fail. That's because our assertion claims that the `numbers` list will still contain the item that we just removed.

But from this we can determine that the `remove()` method takes an object and removes the first occurence of that item from the list. In our case we took a list which contained the integers 1, 2 and 3, and removed the first instance of the number 3. For our list, we only had the 1 instance of the integer 3 in our list.

If we refactor our test with the correct assertion, we can see the difference:

```python
    def test_item_can_be_removed(self):
        """
        Given a list of integers
        When an integer is removed from the list
        Then the item cannot be found in the list
        """
        # Given
        number_to_remove = 3
        numbers = [1, 2, number_to_remove]

        # When
        numbers.remove(number_to_remove)

        # Then
        assert numbers == [1, 2]
```

With this test passing, we've confirmed the basic idea of **mutability** to ourselves. We have also found out how to use the `append()` and `remove()` methods on the `list` object to change the `list` after it has been created.

> We're kinda stretching the idea/purpose of TDD here, but this chapter is primarily about us gaining an understanding of the core language concepts and of course writing tests to verify the behaviours we expect from our code.

### Adding iterables to a list

So we've found that we can add individual items to a list. But we also have the ability to add other iterables to a list, including another list.&#x20;

So let's write a test for this:

```python
    def test_another_list_can_be_added(self):
        """
        Given a list of integers
        When another list is added to the original list
        Then all the items in the 2nd list will be added
        """
        # Given
        main_list = [1, 2]
        items_to_be_added = [3, 4]

        # When
        main_list.extend(items_to_be_added)

        # Then
        assert main_list == [1, 2]
```

As you might have guessed, this test is going to fail:

```
FAILED          [100%]
test_lists.py:33 (TestLists.test_another_list_can_be_added)
[1, 2, 3, 4] != [1, 2]

Expected :[1, 2]
Actual   :[1, 2, 3, 4]
<Click to see difference>
```

Our test is telling us what we expect i.e. the items from the 2nd list have now been added to the first.&#x20;

> With `extend()` we have modified the list in-place.

So let's refactor our test to prove our theory:

```python
    def test_another_list_can_be_added(self):
        """
        Given a list of integers
        When another list is added to the original list
        Then all the items in the 2nd list will be added
        """
        # Given
        main_list = [1, 2]
        items_to_be_added = [3, 4]

        # When
        main_list.extend(items_to_be_added)

        # Then
        assert main_list == [1, 2, 3, 4]
```

And now the test passes. Great!

***

## Indexing

Another really useful property about lists, is that they are indexed. This means that every item in a list is assigned a position. Whereby the first item in the list is denoted with the index of `0`

<figure><img src="../.gitbook/assets/Screenshot 2023-11-06 at 18.49.47.png" alt=""><figcaption><p>In this list of the 3 strings, each item is assigned an index starting from 0</p></figcaption></figure>

The fact that lists are indexed provide us with the ability to manipulate lists in situations when we don't know ahead of time what items are in the list, or we don't care.

### Retrieving the index of an item

Getting the index of an item is pretty straight forward. We can call `index()` on the list object with the first argument being the item itself:

```python
    def test_index_returns_correct_value(self):
        """
        Given a list of strings
        When `index()` is called for a given string
        Then the correct index is returned
        """
        # Given
        items = ["a", "b", "c"]

        # When
        index = items.index("b")

        # Then
        assert index == "b"

```

Once again, this test is going to fail:

```
FAILED                              [100%]
test_lists.py:49 (TestLists.test_index)
1 != 'b'

Expected :'b'
Actual   :1
<Click to see difference>
```

Listening to our test and we can see that the returned index is 1. So let's refactor our test

```python
    def test_index_returns_correct_value(self):
        """
        Given a list of strings
        When `index()` is called for a given string
        Then the correct index is returned
        """
        # Given
        items = ["a", "b", "c"]

        # When
        index = items.index("b")

        # Then
        assert index == 1
```

And now that our test passes, we've confirmed our theory.

##

## References

* [Lists | Official Python Documentation](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
