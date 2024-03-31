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

{% code lineNumbers="true" %}
```python
from http import HTTPStatus

from starlette.testclient import TestClient

from main import app


class TestIncomeTaxesEndpoint:
    def test_calculates_correct_tax(self):
        """
        Given a salary of £33,000
        When a GET request is made to the `income-taxes/` endpoint
        Then the calculated tax owed is £4,100
        """
        # Given
        test_client = TestClient(app=app)
        salary = 33_000

        # When
        response = test_client.get("/income-taxes", params={"salary": salary})

        # Then
        assert response.status_code == HTTPStatus.OK
        assert response.json() == {"tax owed": 4_100}
```
{% endcode %}

On line 3, we import the `TestClient` class from the `starlette` library which was installed within our virtual environment when installed `fastapi`. Most modern frameworks come bundled with a client that we can use to interact with our own API endpoints under test conditions. For this particular client, the `TestCient` class takes an argument of the `FastAPI` application instance itself, which we have not yet defined.

On line 20, we are saying that we want to use our `TestClient` to make a `GET` request to the `income-taxes/` endpoint. Which has also not been defined yet. We are also saying that we are including the `"salary"` query parameter in the request. So the URL path of this request would look like the following:

```
/income-taxes?salary=33000
```

On line number 23, we verify that we received an HTTP 200 OK response. Note that the enum `HTTPStatus.OK` is equivalent in this case to `200`. The use of the enum is purely for readability purposes.

And finally on line 24, we check the output calculation provided by our endpoint.

{% hint style="info" %}
In writing the test first, we focus on the design of the endpoint. The interface to our code and how we want to interact with it. This frees us from thinking about the technical details until later.
{% endhint %}

If we run this test as is, we'd be expecting to see our old friend the `ImportError` since the `app` has not been defined yet. But that is not the case here:

{% code lineNumbers="true" %}
```python

test_main.py:None (test_main.py)
../../.venv/lib/python3.12/site-packages/starlette/testclient.py:33: in <module>
    import httpx
E   ModuleNotFoundError: No module named 'httpx'

During handling of the above exception, another exception occurred:
test_main.py:3: in <module>
    from starlette.testclient import TestClient
../../.venv/lib/python3.12/site-packages/starlette/testclient.py:35: in <module>
    raise RuntimeError(
E   RuntimeError: The starlette.testclient module requires the httpx package to be installed.
E   You can install this with:
E       $ pip install httpx
```
{% endcode %}

On line number 12 of the stacktrace we can see the error message that tells us exactly what the problem is. The `TestClient` class depends on the `httpx` package which we don't yet have.

So lets run the command as dictated to us by the stracktrace to get the `httpx` package:

```sh
pip install httpx
```

Once this is installed, we should freeze our pip dependencies again and commit them to version control.

***

## Getting to green as quick as possible



