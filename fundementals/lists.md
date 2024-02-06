# Lists

The first of the data structures we will cover is the list.

Lists are an ordered, indexed built-in data structure of variable capacity. You might have heard them referred to as arrays from people coming from other languages and for all intents and purposes they are the same.&#x20;

Lists can be instantiated with a pair of square brackets, `[]` or via the contsructor `list()`.

To start of with we should create a new file in which we can write our tests. So in your terminal lets use the following command:

```
touch tests/test_lists.py
```

This command will create a new file called `test_lists.py` in the `tests/` directory. With this in place we can start going over a number of concepts around lists, using our tests to _see_ these concepts in action.

## Mutability

Unlike some of the other data structures we can **add or remove items** from them **after the point of creation**. This behaviour of being able to change a data strcuture after it is created is known as being **mutable**.

### Adding items to a list

But don't take my word for it. Lets write some tests and verify this claim. Lets start of with setting up our first test, we'll use this to check our previous claim that lists are mutable:

```python
from src.lists import add_item_to_list


class TestLists:
    def test_add_item_to_list(self):
        """
        Given a list of integers and a new number to be added
        When `add_item_to_list()` is called
        Then the new number can be found in the list
        """
        # Given
        new_number = 4
        numbers = [1, 2, 3]

        # When
        new_numbers = add_item_to_list(items=numbers, item=new_number)

        # Then
        assert new_numbers == [1, 2, 3, 4]
```

> This test will fail when ran because we have not yet defined `add_item_to_list()`

For this first test case, we are going to apply TDD to the letter. Now remember, that means we adhere to the red-green-refactor cycle.&#x20;

So if we run this test as things stand we will fall at the first hurdle with an import error:

```python
test_lists.py:1: in <module>
    from src.lists import add_item_to_list
E   ImportError: cannot import name 'add_item_to_list' from 'src.lists' (/Users/afaanashiq/projects/learn-python-with-tdd/2-fundementals/src/lists.py)
```

Okay, so we're in the red. Our test is failing because we have tried to import something which does not exist. Also remember that we should listen to our tests and let them guide the way for us.

With this in mind we should start by creating the file:

```
touch src/lists.py
```

Within that file we should create the function that our test wanted us to create:

```python
def add_item_to_list():
    ...
```

Note that the ellipsis `...` here acts as a placeholder. This is akin to the `pass` keyword.

Running our test again and we will get another failure. This time we will get a `TypeError`:

```python
FAILED                   [100%]
test_lists.py:4 (TestLists.test_add_item_to_list)
self = <test_lists.TestLists object at 0x1045b89b0>

    def test_add_item_to_list(self):
        """
        Given a list of integers and a new number to be added
        When `add_item_to_list()` is called
        Then the new number can be found in the list
        """
        # Given
        new_number = 4
        numbers = [1, 2, 3]
    
        # When
>       new_numbers = add_item_to_list(items=numbers, item=new_number)
E       TypeError: add_item_to_list() got an unexpected keyword argument 'items'

test_lists.py:16: TypeError
```

Our test is telling us that we tried to pass something into our function that isn't allowed. That's kinda obvious because we didn't define too much in our function.

Right so we need to get out of the red. Being true to the letter of TDD would push us to get back to green as quick as possible with the smallest amount of change and then refactor from safe ground.&#x20;

So with that in mind, let's modify our `add_item_to_list()` function:

```python
def add_item_to_list(items: list[int], item: int) -> list[int]:
    return [1, 2, 3, 4]
```

Now we have defined 2 arguments to the function along with type hints to say that we expect to be provided with a list of integers and another integer.

If you think this function looks terrible you'd be correct, `add_item_to_list()` currently only holds true for the 1 one case of when `items=[1, 2, 3]` and `item=`4.&#x20;

In all other possible test cases, our function will not suffice. But remember our goal was simply to get back to the safe ground of a passing test.

Now that we are in that safe zone, let's refactor our function to finally make use of the `append()` method on the `list` object:

```python
def add_item_to_list(items: list[int], item: int) -> list[int]:
    items.append(item)
    return items

```

As you might have guessed, the `append()` method takes an object and adds it to the end of the list. In our case we took a list which contained the integers 1, 2 and 3, and added the integer 4 to the end of the list.

We could write more test cases against this function. And maybe that might be worth doing in your own time!

But our test now passes, and we've proved the concept of mutability to ourselves. So let's move on.

### Removing items from a list

Let's try this again, but this time let's remove an item:

```python
from src.lists import add_item_to_list, remove_item_from_list

    ...

    def test_remove_item_from_list(self):
        """
        Given a list of integers
        When `remove_item_from_list()` is called
        Then the item cannot be found in the list
        """
        # Given
        number_to_remove = 3
        numbers = [1, 2, number_to_remove]
    
        # When
        new_numbers = remove_item_from_list(items=numbers, item=number_to_remove)
    
        # Then
        assert new_numbers == [1, 2]
```

> From here on out we will be writing each test as a method on our test class. The rest of the test class will be omitted for brevity.

Again, this test is going to fail because we have not defined the `remove_item_from_list` function.

So let's head over to our `src/lists.py` file and define that function for ourselves:

```python
def remove_item_from_list(items: list[int], item: int) -> list[int]:
    return [1, 2]
```

Once again, our test will pass. But it doesn't really mean much functionally. We just want to get to the green zone so we can think in safety about our implementation:

```python
def remove_item_from_list(items: list[int], item: int) -> list[int]:
    items.remove(item)
    return items
```

Now we can see our test passes. Again, this would be a good opportunity to write some more tests cases against this function.

From this we can determine that the `remove()` method takes an object and removes the first occurence of that item from the list. In our case we took a list which contained the integers 1, 2 and 3, and removed the first instance of the number 3. For our list, we only had the 1 instance of the integer 3 in our list.

With this test passing, we've confirmed the basic idea of **mutability** to ourselves. We have also found out how to use the `append()` and `remove()` methods on the `list` object to change the `list` after it has been created.

> This chapter is primarily about us gaining an understanding of the core language concepts and of course writing tests to verify the behaviours we expect from our code. As we go further along you'll see more of the benefits of TDD.

### Adding iterables to a list

So we've found that we can add individual items to a list. But we also have the ability to add other iterables to a list, including another list.&#x20;

So let's write a test for this:

```python
from src.lists import add_item_to_list, remove_item_from_list, add_list_to_list

    ...
    
    def test_add_list_to_list(self):
        """
        Given 2 list of integers and
        When `add_list_to_list()` is called
        Then all the items in the 2nd list will be added
        """
        # Given
        main_list = [1, 2]
        items_to_be_added = [3, 4]

        # When
        new_numbers = add_list_to_list(items=main_list, items_to_be_added=items_to_be_added)

        # Then
        assert new_numbers == [1, 2, 3, 4]
```

As you might have guessed, this test is going to fail.

Our test is telling us what we expect so we should go and define our function:

<pre class="language-python"><code class="lang-python"><strong>def add_list_to_list(items: list[int], items_to_be_added: list[int]) -> list[int]:
</strong>    items.extend(items_to_be_added)
    return items
</code></pre>

> With `extend()` we have modified the list in-place.

We've skipped a couple of steps to get to our proof a little quicker. So feel free to write some more test cases against this function.

***

## Indexing

Another really useful property about lists, is that they are indexed. This means that every item in a list is assigned a position. Whereby the first item in the list is denoted with the index of `0`

<figure><img src="../.gitbook/assets/Screenshot 2023-11-06 at 18.49.47.png" alt=""><figcaption><p>In this list of the 3 strings, each item is assigned an index starting from 0</p></figcaption></figure>

The fact that lists are indexed provide us with the ability to manipulate lists in situations when we don't know ahead of time what items are in the list, or we don't care.

### Retrieving the index of an item

Getting the index of an item is pretty straight forward. We can call `index()` on the list object with the first argument being the item itself:

```python
from src.lists import (
    add_item_to_list,
    remove_item_from_list,
    add_list_to_list,
    get_index_of_item,
)

    ...
    
    def test_get_index_of_item(self):
        """
        Given a list of strings
        When `get_index_of_item()` is called for a given string
        Then the correct index is returned
        """
        # Given
        items = ["a", "b", "c"]

        # When
        index = get_index_of_item(items=items, item="b")

        # Then
        assert index == 1
```

Once again, this test is going to fail. We get an `ImportError` so lets go and define it:

```python
def get_index_of_item(items: list[str], item: str) -> int:
    return items.index(item)
```

And now that our test passes, we've confirmed our theory!

### Inserting items at a given index

Because lists are indexed, we can also perform interesting operations like slice a list based on a provided index or insert an item into a list at a given position.

Let's write a test to prove that idea:

```python
from src.lists import (
    add_item_to_list,
    remove_item_from_list,
    add_list_to_list,
    get_index_of_item,
    insert_item_at_index,
)

    ...
    
    def test_insert_item_at_index(self):
        """
        Given a list of items
        When `test_insert_item_at_index()` is called
            for a given item and index
        Then the item is added to the list at the given index
        """
        # Given
        items = ["a", "c", "d"]

        # When
        insert_item_at_index(items=items, index=1, item="b")

        # Then
        assert items == ["a", "b", "c", "d"]

```

Once again, this test is going to fail as things stand, because we haven't defined our function. So lets do just that:

```python
def insert_item_at_index(items: list[str], index: int, item: str) -> list[str]:
    return items.insert(index, item)
```

Now our test passes, we can see that we've taken our item, in this case the string `"b"` and inserted it at the position of `1`.&#x20;

> Remember, that we have 0-based indexing. Meaning we start counting from 0 instead of 1.

***

## Additional operations

There are a number of other operations which we can use on lists, although some of these also fall under the bracket of mutability.

### Sorting

We can sort items within a list and mutate the list in place. So lets write a test that tries to negate this theory first:

```python
  from src.lists import (
    add_item_to_list,
    remove_item_from_list,
    add_list_to_list,
    get_index_of_item,
    insert_item_at_index,
    sort_items_in_list,
)
    
    ...
   
    def test_sort_items_in_list(self):
      """
      Given a list of items
      When `sort_items_in_list()` is called on the list
      Then the items within the list are sorted
      """
      # Given
      items = ["b", "c", "a"]
    
      # When
      sort_items_in_list(items=items)
    
      # Then
      assert items == ["a", "b", "c"]
    
```

So with our test in place lets head over to our source code:

```python
 def sort_items_in_list(items: list[str]) -> list[str]:
    return items.sort()
```

And with that in place we have a passing test!

We have barely scratched the surface with how useful `sort()` is here. We can sort by a given attribute, reverse the sorting operation i.e. in descending order too.&#x20;

We can also use the `sorted()` built-in function, which operates in a pretty similar way to the `sort()` method. The key difference being that the `sorted()` function returns a new list instead of mutating the existing list.

For brevity we are not going to go over all of these possibilities. But it would be worth exploring them for yourself, using tests as guardrails to prove these concepts to yourself.

### Counting list length

Another very common and useful operation for lists is the ability to get a count of all the items within a list. This can be acheived via the `len()` call.

We know the drill pretty well at this point, so lets write the test to try and disprove this claim first:

```python
from src.lists import (
    add_item_to_list,
    remove_item_from_list,
    add_list_to_list,
    get_index_of_item,
    insert_item_at_index,
    sort_items_in_list,
    count_items,
)
  
    ...
   
    def test_count_items(self):
        """
        Given a list of items
        When `count_items()` is called with the list
        Then an integer is returned representing the number of items
        """
        # Given
        items = ["b", "c", "a"]

        # When
        number_of_items = count_items(items=items)

        # Then
        assert number_of_items == 3
```

With this in place, we know what to expect from our test. So let's write the source code to match:

```python
def count_items(items: list[str]) -> int:
    return len(items)
```

***

## Summary

In this section we covered a number of key operations and useful characteristics of lists. We drove our learning with tests, discrediting and proving theories to ourselves along the way in what might have felt like a scientific approach at times.

There are a number of things we didn't get round to covering in this section, and it might have been a little foolish to have attempted to do so! With the framework we've used, you can explore other properties and characteristics for yourself.&#x20;

{% hint style="info" %}
You can find the source code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-course-materials)
{% endhint %}

***

## References

* [Lists | Official Python Documentation](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
* [Examples of source code](https://github.com/A-Ashiq/learning-python-with-tdd-fundementals/tree/master/lists)
