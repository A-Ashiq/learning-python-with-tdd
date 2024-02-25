# Dependency Injection

Dependency injection sounds a lot fancier and complicated than what it actually is. \
But it is an incredibly powerful and useful technique that we should include in our repertoire as it is something we will need later on.

Dependency injection is the idea of providing an object or function the things that it needs and will later use as opposed to letting that object or function simply create those things within itself.&#x20;

***

## What does no dependency injection look like?

Lets take the following snippet:

<pre class="language-python"><code class="lang-python"><strong>class UserInterface:
</strong>    def __init__(self):
        self.repository = UserRepository()

    def get_user(self, user_id: int) -> User:
        user = self.repository.get_user(user_id=user_id)
        
        # do some extra stuff
</code></pre>

In this example, the `UserInterface` object initializes an instance of the `UserRepository`.\
It then calls out to the `UserRepository` instance in the `get_user()` method.

Now lets make the assumption that the `UserRepository` represents `User` records in a database. And as such we can assume that `UserRepository` will need a connection to a database.

See where this is going?&#x20;

As things stand the `UserInterface` creates a `UserRepository` object internally within its `__init__()` method. So the `UserInterface` object has an implicit dependency on the `UserRepository` which in turn depends on a database. So we can't initialize the `UserInterface` without access to a database. Straight out of the gate, the idea of us writing effective unit tests against the `UserInterface` class is becoming a faint possibility.

Now lets say we wanted to swap the `UserRepository` out for a repository which implements its persistence differently from a database. Our current `UserInterface` does not easily allow us to do this.

***

## Applying dependency injection

Taking the `UserInterface` class from above. Lets see what this looks like if we implemented the class with dependency injection:

<pre class="language-python" data-line-numbers><code class="lang-python"><strong>class UserInterface:
</strong>    def __init__(self, respository: UserRepository):
        self.repository = respository

    def get_user(self, user_id: int) -> User:
        user = self.repository.get_user(user_id=user_id)
        
        # do some extra stuff
</code></pre>

The difference is subtle but it has a dramatic impact in terms of how we can make use of the `UserInterface`.

This time around, we have declared the `repository` argument on the `__init__()` constructor of the `UserInterface` class on line number 2. So whenever the `UserInterface` class is created we have to provide it with the `UserRepository`.

See how we've added the type hint of  `UserRepository` to the `repository` argument on line number 2? Well, as long as the object that we pass to the `repository` argument implements a `get_user()` method which takes a `user_id` argument as per line number 6, then that object will be valid.

Which is a dynamic programming concept whereby the runtime only **cares about the behaviour of an object as opposed to its type**. Taking the above example, we could easily provide an object of another class to the `repository` argument, so long as it implements the same behaviour i.e.  a `get_user()` method with a keyword argument of `user_id`.

This is known as **duck-typing**_._ The name is derived from the saying _"If it looks like a duck and quacks like a duck, it's a duck"._

Now that we've decoupled the `UserInterface` from the database by virtue of forcing the caller to explicitly provide the `UserRepository` ahead of time, we have added an important control point to our `UserInterface` class. If we want to write tests against the `UserInterface` class at a lower gear, then we can do so much easier than before.

***

## Swapping out the dependency (for tests)







##

***

## References

* [Dependency Injection in Python](https://www.afaanashiq.com/python/dependency-injection-in-python/)
* [Test Doubles](https://www.afaanashiq.com/code/test-doubles/)
* [Drawing Test Boundaries](https://www.afaanashiq.com/code/drawing-test-boundaries/)
