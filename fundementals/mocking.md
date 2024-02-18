# Mocking

If you cast your mind back to the [definition of unit tests from the Types of tests section](../testing/types-of-tests.md#unit-tests), we said unit tests should target small isolated pieces of functionality. We want the scope of our unit tests to be isolated from the rest of the system.

Our tests should be as determinstic as possible. But the further up the testing pyramid we go, the more moving parts and components that have to be within the scope of each test. And as such the more variables are in play. But when we are writing unit tests we want to adhere to the rules we have set for ourselves already.

{% hint style="info" %}
We want our tests to be as deterministic as possible. But as soon as we introduce aspects with such variability like making calls over network, then we inherently cede control.
{% endhint %}

So how do we take pieces of code which glue different parts of our system together? How do we verify the agreed contract between multiple parts of our system without having to run them for real? Remember that we will already be doing the latter further up the test pyramid. How do we simulate the behaviour of surrounding components in place of the real thing? How do we verify that we are calling other components correctly? This is where we can introduce test doubles.&#x20;

Mocks are dynamic components which are a form of test double. They can be used to emuate real pieces of functionality or objects. And we can also use them to verify calls are being made from 1 component to another in the way we expect.

Before we get started, lets create the files that we will need for this section. In your terminal:

```
touch src/mocking.py tests/test_mocking.py
```

***

## A ~~sort of~~ real world scenario

To help us out, we are going to tee up a fake scenario in the `src/mocking.py`  file.

On line 8, we have a simple `User` class which holds on to an `email_address` string that we have to provide to the initialization of a `User`.

On line 13 we have defined a `notify_user()` function which calls out to another function called `send_email()`. In order to simulate the unpredictability of making a network call, we have hardcoded a 10 second wait into the `send_email()` function.

On line 15 we have declared the aforementioned `send_email()` function, which takes an `email_address` argument as a string. This is important because it forms the contract between the `notify_user()` function and the `send_email()` function.

{% code lineNumbers="true" %}
```python
import time


class User:
    def __init__(self, email_address: str):
        self.email_address = email_address


def notify_user(user: User) -> None:
    email_address: str = user.email_address
    send_email(email_address=email_address)
    # do some other stuff


def send_email(email_address: str) -> None:
    time.sleep(10)
```
{% endcode %}

***

## How do we test this?

So lets say we want to write unit tests against what we have above. How do we go about this?\
We know that if we simply allow `notify_user()` function calls to run as is then they will call out to the `send_email()` function which will incur the penalty of a request leaving the boundaries of the running process. In our contrived example, we've tried to simulate this with the `time.sleep(10)` call to make the test wait 10 seconds.

This is where mocking becomes useful for us. Mocking is a way for us to substitute components in our code with a dynamic object, this will also swap out the functionality of the target component entirely. We can then check how this mock object was interacted with by the surrounding code.

When we are testing the `notify_user()` function, we can mock out the `send_email()` function and also verify that the `notify_user()` function calls out to the `send_email()` function with the correct arguments.&#x20;

In this case, we want to check that the `notify_user()` peels off the `email_address` attribute from the given `User` instance and provides the `email_address` to the `send_email()` function. We can call this interaction between these 2 functions the _contract_. So we can write a test to verify this contract is in place.&#x20;

This might seem quite alien at first. But its important to note that we want our unit tests to be small and focused. Ideally they should be testing 1 major aspect of the functionality. So its acceptable for us to delegate each of these major aspects to different tests. Mocking can help us draw those boundaries around our tests.

***

## Writing a test to verify the contract

So lets go ahead and write the test to verify the contract between the caller function, `notify_user()` and the callee function, `send_email()`:

{% code lineNumbers="true" %}
```python
from unittest import mock

from src.mocking import User, notify_user


class TestMocking:
    @mock.patch(target="src.mocking.send_email")
    def test_send_email_is_called_with_correct_arg(
        self, spy_send_email: mock.MagicMock
    ):
        """
        Given a `User` object
        When `notify_user()` is called
        Then the call is delegated
            to the `send_email()` function
        """
        # Given
        email_address = "jane.doe@gmail.com"
        user = User(email_address=email_address)

        # When
        notify_user(user=user)

        # Then
        spy_send_email.assert_called_once_with(email_address=email_address)
```
{% endcode %}

On line 1, we import the `mock` module from the `unittest` library. Note that the `unittest` is built-in and we do not need to `pip install` it as an additional dependency.

On line 7, we create a mock `patch` with a target of  `"src.mocking.send_email"`. This path is the path **where the target function is being called not where it is defined.** In our case that just happens to be the same thing but it is worth keeping this in mind as it is a gotchya that a lot of people trip up on. Note that we could also easily have used the `patch` function as a [context manager](https://peps.python.org/pep-0343/) by using the `with` keyword instead of a decorator.

On line 9, we have to assign an argument for the result of this `patch`. We define the name of this argument as we see fit. In this case, we have called it `spy_send_email`. This is because we will _spying_ on the function, where the 2nd part of the argument name happens to be named after the function name.

On line 19, we instantiate a `User` class as is. Our `User` class is very light with no external dependencies so its fine to instantiate an object directly from the `User` class for the purposes of our test. But in other more complicated scenarios we might want to pass in a fake equivalent of the `User`. More on that later!

On line 22, we call out to the main function, `notify_user()`.

On line 25, we call the `assert_called_once_with()` method on the `spy_send_email` mock object. As part of this, we redeclare how we expected the `send_email` function to have been called. Which was with a single keyword argument of `email_address`.&#x20;

If we run this test, we can see that it runs and passes in under 1 ms. Now see what happens if you removed the `patch` and allow the `notify_user()` function to call out to the real `send_email()` function.

***

## Things to note

If we find ourselves having to patch over multiple targets within the scope of our test, this should feel like a [code smell](https://martinfowler.com/bliki/CodeSmell.html). In cases like this, we should opt to ensure that we have created the right levels of abstraction in our code first.

Lets say we have the following function, which calls a number of functions within:

```python
def do_something_first() -> None:
    ...


def do_something_following() -> None:
    ...


def do_something_next() -> None:
    ...


def do_something_last() -> None:
    ...


def some_func() -> None:
    do_something_first()
    do_something_following()
    do_something_next()
    do_something_last()
    # Do other stuff
```

When we want to mock the internals of `some_func()` we would be forced to write something like the following:

```python
class TestSomeFunc:
    @mock.patch(target="src.mocking.do_something_first")
    @mock.patch(target="src.mocking.do_something_following")
    @mock.patch(target="src.mocking.do_something_next")
    @mock.patch(target="src.mocking.do_something_last")
    def test_code_smell_patch_stack(
        self,
        mocked_do_something_first: mock.MagicMock,
        mocked_do_something_following: mock.MagicMock,
        mocked_do_something_next: mock.MagicMock,
        mocked_do_something_last: mock.MagicMock,
    ):
        
        some_func()
```

We've been forced to mock each of these individually. But if we had wrapped these into another layer of abstraction, then we would have made things much easier for ourselves to not only test but also to reason about.

So lets condense our main function as follows:

```python
def do_first_steps():
    do_something_first()
    do_something_following()


def do_last_steps():
    do_something_next()
    do_something_last()


def some_func_revised() -> None:
    do_first_steps()
    do_last_steps()
    # Do other stuff
```

We've condensed the inner function calls into 2 seperate upper level functions. We could also have wrapped them into the 1 function, and really this is more of a judgement call for us to make. Perhaps the functionality makes sense to be encapsulated into the 1 function or maybe there are 2 main discrete parts to care about.&#x20;

```python
class TestSomeFuncRevised:
    @mock.patch(target="src.mocking.do_first_steps")
    @mock.patch(target="src.mocking.do_last_steps")
    def test_code_smell_patch_stack(
        self,
        mocked_do_first_steps: mock.MagicMock,
        mocked_do_last_steps: mock.MagicMock,
    ):

        some_func_revised()
```

Either way, the level of mocking required for the `some_func_revised()` function is mitigated.&#x20;

{% hint style="info" %}
Tests can often tell us things that we might not otherwise have seen ourselves.
{% endhint %}

We've listened to the things our test was telling us and adjusted accordingly. This is another powerful and useful characteristic of letting tests drive our development.

##

***

## References

* [Test Doubles](https://www.afaanashiq.com/code/test-doubles)
* [Drawing Test Boundaries](https://www.afaanashiq.com/code/drawing-test-boundaries/)
* [Mock object library | Python official documentation](https://docs.python.org/3/library/unittest.mock.html)
* [Monkeypatch fixture | Pytest documentation](https://docs.pytest.org/en/6.2.x/monkeypatch.html)
