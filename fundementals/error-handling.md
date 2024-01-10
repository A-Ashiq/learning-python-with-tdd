# Error handling

Towards the end of the [previous chapter](dictionaries.md#accessing-key-value-pairs) we discussed how the `.get()` method on a `dict` object wraps around a `KeyError`, which is thrown when trying to access a key which does not exist in a dictionary. This is an example of error handling.

Things don't always go to plan and we need to design for less than ideal outcomes. This is why error handling is an important skill for us to pick up.

Error handling allows us to better control the flow of our programs, to provide resilience and to ensure that our applications can continue to operate for those unwanted scenarios.

In this chapter, we will learn how to catch errors and how to treat them. As well as how to handle multiple errors and treat them seperately or differently. We will also go over how and why we might want to raise our own errors too.
