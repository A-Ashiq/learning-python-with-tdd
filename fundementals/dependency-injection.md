# Dependency Injection

Dependency injection sounds a lot fancier and complicated than what it actually is. \
But it is an incredibly powerful and useful technique that we should include in our repertoire as it is something we will need later on.

Dependency injection is the idea of providing an object or function the things that it needs and will later use as opposed to letting that object or function simply create those things within itself.&#x20;

***

## What does no dependency injection look like?

Lets take the following snippet:

<pre class="language-python" data-line-numbers><code class="lang-python">import time


class User:
    def __init__(self, user_id: int):
        self.user_id = user_id


class UserRepository:
    @classmethod
    def get_user(cls, user_id: int) -> User:
        time.sleep(5)  # Wait for 5s to emulate db query
        return User(user_id=user_id)


<strong>class UserInterface:
</strong>    def __init__(self):
        self.repository = UserRepository()

    def get_user(self, user_id: int) -> User:
        user = self.repository.get_user(user_id=user_id)
        
        # do some extra stuff
        return user
</code></pre>

In this example, the `UserInterface` object initializes an instance of the `UserRepository`.\
It then calls out to the `UserRepository` instance in the `get_user()` method. For the purposes of this exercise the `get_user()` method on the `UserRepository` class has a hardcoded delay of  5 seconds.

Now lets make the assumption that the `UserRepository` represents `User` records in a database. And as such we can assume that `UserRepository` will need a connection to a database.&#x20;

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
        return user
</code></pre>

The difference is subtle but it has a dramatic impact in terms of how we can make use of the `UserInterface`.

This time around, we have declared the `repository` argument on the `__init__()` constructor of the `UserInterface` class on line number 2. So whenever the `UserInterface` class is created we have to provide it with the `UserRepository`.

See how we've added the type hint of  `UserRepository` to the `repository` argument on line number 2? Well, as long as the object that we pass to the `repository` argument implements a `get_user()` method which takes a `user_id` argument as per line number 6, then that object will be valid.

Which is a dynamic programming concept whereby the runtime only **cares about the behaviour of an object as opposed to its type**. Taking the above example, we could easily provide an object of another class to the `repository` argument, so long as it implements the same behaviour i.e.  a `get_user()` method with a keyword argument of `user_id`.

This is known as **duck-typing**_._ The name is derived from the saying _"If it looks like a duck and quacks like a duck, it's a duck"._

Now that we've decoupled the `UserInterface` from the database by virtue of forcing the caller to explicitly provide the `UserRepository` ahead of time, we have added an important control point to our `UserInterface` class. If we want to write tests against the `UserInterface` class at a lower gear, then we can do so much easier than before.

***

## Swapping out the dependency (for tests)

On that note lets take a look at how we might take advantage of dependency injection when we are writing tests. Say we wanted to write unit tests against some of the logic in the `get_user()` method on the `UserInterface` class, but we don't want or need to include the database in our test. In this case, we'd be better off injecting a fake version of the `UserRepository` class into the instance of the `UserInterface` which we want to test

Lets go ahead and set this up:

{% code lineNumbers="true" %}
```python
from src.dependency_injection import User, UserInterface


class FakeUserRepository:
    def __init__(self, users: list[User]):
        self._users = users

    def get_user(self, user_id: int) -> User:
        return next(user for user in self._users if user.user_id == user_id)


class TestUserInterface:
    def test_get_user(self):
        """
        Given a `User` object
        When `get_user()` is called from an instance of `UserInterface`
        Then the correct `User` object is returned
        """
        # Given
        user_id = 123
        user = User(user_id=user_id)
        fake_user_repository = FakeUserRepository(users=[user])
        user_interface = UserInterface(respository=fake_user_repository)

        # When
        retrieved_user: User = user_interface.get_user(user_id=user_id)

        # Then
        assert retrieved_user == user
```
{% endcode %}

The test itself is relatively straight forward.&#x20;

On line 22 we create an instance of the `FakeUserRepository`, this is the dependency that we have replaced with an object which we have modified so that it does not interact with the database.&#x20;

On line 23, when we create the main `UserInterface` class, we pass the `FakeUserRepository` object into the initialization of the `UserInterface` class. This in itself is quite interesting because the `UserInterface` does not need to know or care about the fact that we've given it a different type of object than it would normally expect. This is the duck-typing paradigm we went over earlier.

On line 5 of the `__init__()` method on the `FakeUserRepository`, we provide a list of `User` objects.  The purpose of this is to mimic the peristence aspect of the real `UserRepository` class. This is how we emulate the fact that we otherwise would have stored those `User` objects in a database.

On line 8 we re-implement the `get_user()` method on the `FakeUserRepository.` As a minimum, the `get_user()` method takes the same arguments as the concrete `UserRepository` class.

On line 9 we reimplement the functionality of the `get_user()` method on `FakeUserRepository` class. This reimplements the how the class retrieves a `User` object with the given `user_id` by grabbing the relevant `User` object within its own peristence i.e. the `_users` instance attribute. This mimics the database query which would have otherwise happened with the concrete `UserRepository` class.&#x20;

***

## Swapping out the dependency (for something else)

The other interesting property of applying dependency injection in this way is that we can re-use the `UserInterface` class with a different implementation of the `UserRepository`. More specifically, we could replace the `UserRepository` altogether with a different object which implements the `get_user()` method. Lets say for example we wanted to swap the current `UserRepository` out for another object that interacts with a different database or form of peristence.

If you'll excuse the cliche, the only thing we know for sure is that things will change.

{% hint style="info" %}
So as engineers it is important for us to build systems that are modular and easy to make changes to.
{% endhint %}

***

## References

* [Dependency Injection in Python](https://www.afaanashiq.com/python/dependency-injection-in-python/)
* [Test Doubles](https://www.afaanashiq.com/code/test-doubles/)
* [Drawing Test Boundaries](https://www.afaanashiq.com/code/drawing-test-boundaries/)
