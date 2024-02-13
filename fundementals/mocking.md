# Mocking

If you cast your mind back to the [definition of unit tests from the Types of tests section](../testing/types-of-tests.md#unit-tests), we said unit tests should target small isolated pieces of functionality. We want the scope of our unit tests to be isolated from the rest of the system.

Our tests should be as determinstic as possible. But the further up the testing pyramid we go, the more moving parts and components that have to be within the scope of each test. And as such the more variables are in play. But when we are writing unit tests we want to adhere to the rules we have set for ourselves already.&#x20;

So how do we take pieces of code which glue different parts of our system together? How do we verify the agreed contract between multiple parts of our system without having to run them for real? Remember that we will already be doing the latter further up the test pyramid. How do we simulate the behaviour of surrounding components in place of the real thing? How do we verify that we are calling other components correctly? This is where we can introduce test doubles.&#x20;

Mocks are dynamic components which are a form of test double. They can be used to emuate real pieces of functionality or objects. And we can also use them to verify calls are being made from 1 component to another in the way we expect.

Before we get started, lets create the files that we will need for this section. In your terminal:

```
touch src/mocking.py tests/test_mocking.py
```





## References

* [Test Doubles](https://www.afaanashiq.com/code/test-doubles)
* [Mock object library | Python official documentation](https://docs.python.org/3/library/unittest.mock.html)
* [Monkeypatch fixture | Pytest documentation](https://docs.pytest.org/en/6.2.x/monkeypatch.html)
