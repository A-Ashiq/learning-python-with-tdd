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
        Then the calculated tax owed is £4,086
        """
        # Given
        test_client = TestClient(app=app)
        salary = 33_000

        # When
        response = test_client.get("/income-taxes", params={"salary": salary})

        # Then
        assert response.status_code == HTTPStatus.OK
        assert response.json() == {"tax owed": 4_086}
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

To begin with we should create the file for our source code. For now, we won't too much about project structure. That will come later.

```sh
touch main.py
```

In this file we can set up the `app` instance which is consumed by the `TestClient` as well as the endpoint that we have already written the test against.&#x20;

So our goal here is to get to the _green state_ i.e. where the test is passing as quick as possible. We're not actually too worried about implementation right now. That will also come later. For now we just want the minimal and least path of resistance back to the safe ground of a passing test.

So in our new `main.py`:

{% code lineNumbers="true" %}
```python
from fastapi import FastAPI

app = FastAPI()


@app.get(path="/income-taxes")
def calculate_income_taxes(salary: float):
    return {"tax owed": 4_086}
```
{% endcode %}

On line 1 we import the `FastAPI` class and instantiate it on line number 3. This is the `app` instance which encompasses our server and is the same one that is also used by the `TestClient`.

`FastAPI` has its own specifc way of adding endpoints. On line 6 we use the `app` instance in a decorator with the `get` method. This indicates that this function represents the logic to be used for this `GET` endpoint. The `path` argument as you might have guessed represents the new endpoint path name.&#x20;

`FastAPI` is a little different to other frameworks whereby it uses our function definition as part of the OpenAPI documentation and as part of the endpoint itself. So the arguments to the function form the query parameters to the endpoint.

And finally, on line 7 we do something pretty unusual. We simply always return the answer which would satisfy our test. Of course, if we used any input other than the one declared by the test this endpoint wouldn't be fit for purpose. But remember, we just want to see the test pass first. We'll come back to rethink this implementation once we're on the safe ground of passing tests.&#x20;

If we now run this test as is, we will see it passes!

***

## Subject matter expertise

Now that we're back on safe ground, we can think about how we want to actually implement our calculation.

This is going to require some subject matter expertise. As of March 2024, the income tax bands in the UK look like the following:

| Band               | Taxable income      | Tax rate |
| ------------------ | ------------------- | -------- |
| Personal Allowance | Up to £12,570       | 0%       |
| Basic rate         | £12,571 to £50,270  | 20%      |
| Higher rate        | £50,271 to £125,140 | 40%      |
| Additional rate    | over £125,140       | 45%      |

There are some gotchyas to be aware of when it comes to dealing with personal allowance at the higher income rates, but we'll deal with that later.

For now we can take the information given to us and translate that into some business logic that we can apply.

***

## Refactoring

Armed with our subject matter knowledge we can go back and write something which will calculate the income tax for us for our given input salary of £33,000 instead of the somewhat useless hardcoded value we are currently returning:

{% code lineNumbers="true" %}
```python
from fastapi import FastAPI

app = FastAPI()


@app.get(path="/income-taxes")
def calculate_income_taxes(salary: float):
    tax_free_allowance = 12_570
    tax_owed = (salary - tax_free_allowance) * 0.2

    return {"tax owed": tax_owed}
```
{% endcode %}

This is of course still a little naive but it does provide us with basis to work from.

If we run our test now we will see it passes again.

***

## Seperating responsibilities

Before we start writing more test cases and developing our endpoint further, we should stop and take stock of the hole we've just dug ourselves into.

Our API endpoint is doing too much by itself. Right now we cannot test the business logic of the calculation by itself. To test it we are forced to hit the endpoint. Ideally the API layer of our system should only be handling serialization and validation. It should take some given input, validate it and then pass that input over to some other part of our system for the actual calculation. This way we would be free to make changes to the calculation without having to change things at the API layer.
