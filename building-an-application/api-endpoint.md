# API Endpoint

To begin with we are going to need an HTTP API endpoint that our users can send a request to. This endpoint should return the calculated income tax that they owe based on the information they provided in their request to the endpoint.

For now we're going to keep things nice and simple and assume that there are no other sources of income to be considered. So this will be a pretty simple calculation of, given a salary figure how much of that figure will be owed in income tax.&#x20;

Note that this will be for UK income tax calculations. If you are following along from a different jurisdiction you can just adjust the calculation to suit your needs.

***

## Install the test runner

So before we go any further, we are going to need to install the test runner. If you cast your mind back to the [environment setup](../getting-started/environment-setup.md#installating-test-runner) section, we have done this before.&#x20;

So lets install the `pytest` library:

```sh
pip install pytest
```

And then update the dependencies again:

```sh
pip freeze > requirements.txt
```

Now we're ready to start writing our first test.

Note that we've actually missed another dependency that we're gonna need. But we're gonna let our first test guide us and tell us exactly what we need.&#x20;

***

## Writing our first test

We currently have source code to test. A blank slate if you will. So an [integration test ](../testing/types-of-tests.md#integration-tests)is gonna give us the most bang for our buck. Given that we're looking to write an endpoint for our API, it makes sense to write a test against this endpoint.&#x20;

So lets create a new directory to hold all of our tests:

```
mkdir -p tests/integration
```

And then lets create our test file in our new `integration` folder:

```
touch tests/integration/test_main.py 
```

In our new test file we can begin to write our very first test for our application.





