# Lists

The first of the data structures we will cover is the list.

Lists are an ordered built-in data structure of variable capacity. You might have heard them referred to as arrays from people coming from other languages and for all intents and purposes they are the same.&#x20;

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
        assert numbers == [1, 2]
```

With this test passing, we've confirmed the basic idea of **mutability** to ourselves. We have also found out how to use the `append()` and `remove()` methods on the `list` object to change the `list` after it has been created.

> We're kinda stretching the idea/purpose of TDD here, but this chapter is primarily about us gaining an understanding of the core language concepts and of course writing tests to verify the behaviours we expect from our code.

***

## References

* [Lists | Official Python Documentation](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
