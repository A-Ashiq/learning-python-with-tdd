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



### Inserting items at a given index

Because lists are indexed, we can also perform interesting operations like slice a list based on a provided index or insert an item into a list at a given position.



Let's write a test to prove that idea:

```python
    def test_item_can_be_inserted_at_index(self):
        """
        Given a list of items
        When `insert()` is called for a given item and index
        Then the item is added to the list at the given index
        """
        # Given
        items = ["a", "c", "d"]

        # When
        items.insert(1, "b")

        # Then
        assert items == ["a", "c", "d"]

```

Once again, this test is going to fail as things stand, because our assertion suggests we don't expect the item to be insterted at the position of 1.&#x20;

```
FAILED      [100%]
test_lists.py:64 (TestLists.test_item_can_be_inserted_at_index)
['a', 'b', 'c', 'd'] != ['a', 'c', 'd']

Expected :['a', 'c', 'd']
Actual   :['a', 'b', 'c', 'd']
<Click to see difference>
```

Lets correct our assertion to match up with our theory:

```python
    def test_item_can_be_inserted_at_index(self):
        """
        Given a list of items
        When `insert()` is called for a given item and index
        Then the item is added to the list at the given index
        """
        # Given
        items = ["a", "c", "d"]

        # When
        items.insert(1, "b")

        # Then
        assert items == ["a", "b", "c", "d"]
```

Now our test passes, we can see that we've taken our item, in this case the string `"b"` and inserted it at the position of `1`.&#x20;

> Remember, that we have 0-based indexing. Meaning we start counting from 0 instead of 1.

***

## Additional operations

There are a number of other operations which we can use on lists, although some of these also fall under the bracket of mutability.

### Sorting

We can sort items within a list and mutate the list in place. So lets write a test that tries to negate this theory first:

```python
    def test_can_be_sorted(self):
        """
        Given a list of items
        When `sort()` is called on the list
        Then the items within the list are sorted
        """
        # Given
        items = ["b", "c", "a"]

        # When
        items.sort()

        # Then
        assert items == ["b", "c", "a"]
```

This test is going to fail because we're trying to say that the list remains as is even after calling `sort()` in the list:

```
FAILED                      [100%]
test_lists.py:79 (TestLists.test_can_be_sorted)
['a', 'b', 'c'] != ['b', 'c', 'a']

Expected :['b', 'c', 'a']
Actual   :['a', 'b', 'c']
<Click to see difference>
```

We can see quite clearly that the items within the list were sorted for us. Refactoring our test slightly should get us in to the green:

```python
    def test_can_be_sorted(self):
        """
        Given a list of items
        When `sort()` is called on the list
        Then the items within the list are sorted
        """
        # Given
        items = ["b", "c", "a"]

        # When
        items.sort()

        # Then
        assert items == ["a", "b", "c"]
```

We have barely scratched the surface with how useful `sort()` is here. We can sort by a given attribute, reverse the sorting operation i.e. in descending order too.&#x20;

We can also use the `sorted()` built-in function, which operates in a pretty similar way to the `sort()` method. The key difference being that the `sorted()` function returns a new list instead of mutating the existing list.

For brevity we are not going to go over all of these possibilities. But it would be worth exploring them for yourself, using tests as guardrails to prove these concepts to yourself.

### Counting list length

Another very common and useful operation for lists is the ability to get a count of all the items within a list. This can be acheived via the `len()` call.

We know the drill pretty well at this point, so lets write the test to try and disprove this claim first:

```python
    def test_can_be_counted(self):
        """
        Given a list of items
        When `len()` is called on the list
        Then an integer is returned representing the number of items
        """
        # Given
        items = ["b", "c", "a"]

        # When
        number_of_items = len(items)

        # Then
        assert number_of_items == 1
```

This test is going to fail, because we actually have 3 items in the list not 1:

```
FAILED                     [100%]
test_lists.py:94 (TestLists.test_can_be_counted)
3 != 1

Expected :1
Actual   :3
<Click to see difference>
```

Refactoring this test to get us in the green:

```python
    def test_can_be_counted(self):
        """
        Given a list of items
        When `len()` is called on the list
        Then an integer is returned representing the number of items
        """
        # Given
        items = ["b", "c", "a"]

        # When
        number_of_items = len(items)

        # Then
        assert number_of_items == 3
```

***

## Summary

In this section we covered a number of key operations and useful characteristics of lists. We drove our learning with tests, discrediting and proving theories to ourselves along the way in what might have felt like a scientific approach at times.

There are a number of things we didn't get round to covering in this section, and it might have been a little foolish to have attempted to do so! With the framework we've used, you can explore other properties and characteristics for yourself.&#x20;

{% hint style="info" %}
You can find the source code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-fundementals/tree/master/lists)
{% endhint %}

***

## References

* [Lists | Official Python Documentation](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
* [Examples of source code](https://github.com/A-Ashiq/learning-python-with-tdd-fundementals/tree/master/lists)
