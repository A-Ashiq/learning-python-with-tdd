# Iteration

Iteration is the act of taking each individual item from a sequence of items. It is an incredibly powerful and useful concept. And one that we are likely to come across often. People often refer to iteration as _looping over_ or _looping through_ a collection of items. All these things refer to the same concept.

We can refer to objects which produce some sequence of items that can be iterated over as _iterables._ So in this sense [lists](lists.md), [tuples](tuples.md), [sets](sets.md), [generators](generators.md) and [dictionaries](dictionaries.md) are all different kinds of iterables because they ultimately produce a sequence of items.

But in Python, any object which implements an `__iter__()` method is an iterable. So in theory we have the freedom to build our own iterables if we wanted.





***

## References

* [Iterators | Python official documentation](https://docs.python.org/3/tutorial/classes.html#iterators)
