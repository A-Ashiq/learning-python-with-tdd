# Dependency Injection

Dependency injection sounds a lot fancier and complicated than what it actually is. \
But it is an incredibly powerful and useful technique that we should include in our repertoire as it is something we will need later on.

Dependency injection is the idea of providing an object or function the things that it needs and will later use as opposed to letting that object or function simply create those things within itself.&#x20;

***

## An example of dependency injection

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



***

## References

* [Dependency Injection in Python](https://www.afaanashiq.com/python/dependency-injection-in-python/)
* [Test Doubles](https://www.afaanashiq.com/code/test-doubles/)
* [Drawing Test Boundaries](https://www.afaanashiq.com/code/drawing-test-boundaries/)
