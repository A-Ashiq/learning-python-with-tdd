# Generators

Generators are somewhat different to data structures like lists or tuples which do all the work up front. In those data structures we store all the given data in memory right from the word go.

Generators allows us to _stream_ data through item by item. They don't save all the items in memory. Instead they will give us each item one by one as and when we ask for them.\
This is a really useful property, particularly when we are dealing with large collections of data.\
However, it does mean we lose some of useful properties that we would otherwise have had with list or tuples, such as being able to call `len()` or acces items at a given index. Because the generator gives us each item individually it would have no way of knowing or understanding how many items it holds ahead of time.

So when we are using these objects, how do we know when we have ran out of items?\
When we are running through a generator, it will return items to us one at a time. Once we reach the end, it can be said that we have _exhausted_ the generator. At this point, the object will raise a `StopIteration` error.

Enough with the theory, lets write some tests and verify this behaviour for ourselves.\
To get started lets create the files we need. In your terminal:

```
touch src/generators.py tests/test_generators.py
```



