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

## A sort of real world scenario

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

This is where mocking becomes useful for us.&#x20;

When we are testing the `notify_user()` function, we can mock out the `send_email()` function and also verify that the `notify_user()` function calls out to the `send_email()` function with the correct arguments.&#x20;

In this case, we want to check that the `notify_user()` peels off the `email_address` attribute from the given `User` instance and provides the `email_address` to the `send_email()` function. We can call this interaction between these 2 functions the _contract_. So we can write a test to verify this contract is in place.&#x20;

This might seem quite alien at first. But its important to note that we want our unit tests to be small and focused. Ideally they should be testing 1 major aspect of the functionality. So its acceptable for us to delegate each of these major aspects to different tests. Mocking can help us draw those boundaries around our tests.



***

## References

* [Test Doubles](https://www.afaanashiq.com/code/test-doubles)
* [Drawing Test Boundaries](https://www.afaanashiq.com/code/drawing-test-boundaries/)
* [Mock object library | Python official documentation](https://docs.python.org/3/library/unittest.mock.html)
* [Monkeypatch fixture | Pytest documentation](https://docs.pytest.org/en/6.2.x/monkeypatch.html)
