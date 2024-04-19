# Additional functionality

Now that we've got the interfaces to our system set up and working we're in a pretty good position. We know that both the HTTP API and the CLI are both hooked up to our core domain logic.&#x20;

The great thing about this is that if we make improvements to or add additonal functionality or even fix bugs in that core domain logic, then our interface components reap the benefits.&#x20;

This is one of the big bonus points that we get for free when we design our systems in this way. With the correct abstractions in place, we only have to change things in 1 place to bring the entire system in line as to where we want it to be. And each component keeps its scope small so that we know exactly what it is responsible for doing.&#x20;

This can make our system easier to reason about and more extensible.

***

## Write the test first

Taking our subject matter expertise, we can translate this into a test for the additional tax rate banding, which is the last banding not yet covered:

{% code lineNumbers="true" %}
```python
class TestCalculateIncomeTaxOwed:

    ...
    
    @pytest.mark.parametrize(
        "salary, expected_tax_owed",
        (
            [150_000, 48_675],
            [175_000, 59_925],
            [200_000, 71_175],
        ),
    )
    def test_calculates_for_additional_rate_banding(
        self, salary: int, expected_tax_owed: float
    ):
        """
        Given a salary which is in the additional rate banding
        When `calculate_income_tax_owed()` is called
        Then the correct calculated tax owed is returned
        """
        # Given
        input_salary = salary

        # When
        calculated_tax_owed: float = calculate_income_tax_owed(salary=input_salary)

        # Then
        assert calculated_tax_owed == expected_tax_owed

```
{% endcode %}

Take note of the `pytest` import which is already at the top of the file from the previous tests we've already written.

***

## Implement the solution

And now lets go ahead and write a solution to wrap the logic we need to satisfy our tests.

{% code lineNumbers="true" %}
```python
def calculate_income_tax_owed(salary: float) -> float:
    tax_free_allowance = 12_570
    basic_rate_threshold = 50_270
    higher_rate_threshold = 125_140

    if salary <= tax_free_allowance:
        return 0

    if salary <= basic_rate_threshold:
        return (salary - tax_free_allowance) * 0.20

    if salary <= higher_rate_threshold:
        return (basic_rate_threshold - tax_free_allowance) * 0.20 + (
            salary - basic_rate_threshold
        ) * 0.40

    return (
        (basic_rate_threshold - tax_free_allowance) * 0.20
        + (higher_rate_threshold - basic_rate_threshold) * 0.40
        + (salary - higher_rate_threshold) * 0.45
    )

```
{% endcode %}

This is admittedly getting a little rough around the edges, but that is okay for now. We can always come back and refactor once the safety harness of our tests can catch us.

And now if we run the tests they will now pass.&#x20;

Our solution is still missing the [personal allowance taper logic](api-endpoint.md#subject-matter-expertise) so it is still not where we might want it to be. But the crucial thing is we've moved another step forward in the right direction.

***

## Tying everything together

We've now estabilshed our design, the API and CLI components simply call out to the domain logic which lives further downstream to get the things they need.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2024-04-18 at 21.08.35.png" alt="" width="301"><figcaption></figcaption></figure>

When it comes to testing these components we shouldn't need to repeat the same tests across both the interface layers for say a given list of inputs. This would be repeating the same tests over and over.&#x20;

So how do write tests which can tie together these components and verify that the contract between them is holding up as we expect it to?

Casting our minds back to the [mocking chapter](../fundementals/mocking.md) and we already have the tools to do this.

***

## Contract tests

When we want to write tests to verify the contract between 2 components we can simply mock 1 of them and cast assertions on how the collaborating component is being called.

### Contract test for the CLI

To begin with we can take our CLI component and write a contract test for it. In this test we will want to verify that the input `salary` figure which is received from the user input from the command line is passed to the call to the domain function, `calculate_income_tax_owed()`. We can also check to see if the returned value from the function is used in the output of the CLI.

So to get started with this we will need a new package within the tests folder. This time you can add the folders and files for yourself:

```
|- tests/                            # already exists
    |- unit/                         # already exists
        |- interfaces/               # to be added
            |- __init__.py           # to be added
            |- cli/                  # to be added
                |- __init__.py       # to be added
                |- modules/          # to be added
                    |- __init__.py   # to be added
                    |- test_taxes.py # to be added
```

&#x20;Once this is done, we can drop into our new test file at `tests/unit/interfaces/cli/modules/test_taxes.py`:

{% code lineNumbers="true" %}
```python
from unittest import mock

from cli import app
from click.testing import Result
from typer.testing import CliRunner

MODULE_PATH = "interfaces.cli.modules.taxes"


class TestCalculateIncomeTaxes:
    @mock.patch(f"{MODULE_PATH}.calculate_income_tax_owed")
    def test_delegates_call_to_domain_logic(
        self, spy_calculate_income_tax_owed: mock.MagicMock
    ):
        """
        Given a salary of £10,000
        When the `taxes calculate-income-taxes` command is called
        Then the call is delegated to `calculate_income_tax_owed()`
            to perform the calculation
        """
        # Given
        salary = 10_000
        cli_runner = CliRunner()

        # When
        result: Result = cli_runner.invoke(
            app=app, args=["taxes", "calculate-income-taxes", str(salary)]
        )

        # Then
        spy_calculate_income_tax_owed.assert_called_once_with(salary=salary)

        returned_calculation = spy_calculate_income_tax_owed.return_value
        assert f"£{returned_calculation}" in result.stdout

```
{% endcode %}

On line 7 we define a constant called `MODULE_PATH` which is a string which represents the path to the file in which the thing we are about to test lives. The crucial thing to note is that because we are testing the CLI component, the target path of our mock should there at the entrypoint as opposed to where the function was orginally defined.

On line 26-28 we invoke the CLI in the same way as we've seen with a [previous test that we've already written.](command-line-interface.md#writing-the-test-first) The difference here is that the underlying domain function, `calculate_income_tax_owed()` has been mocked out.

On line 31 we verify the contract between the CLI component and the `calculate_income_tax_owed()` by checking the arguments which were passed from the caller (the CLI component) to the callee (the domain function). Take note of the `assert_called_once_with()` method which is simply a convenience method available to us on the mock object.

And on line 33, we capture the return value of the mocked function. It should be noted that because we've mocked it out, this is not the actual computed value. Because the calculation has not taken place due to the fact that we mocked it out. This can be a little confusing at first, so spend some time mulling this over. Drop into a debugger and see for yourself.

And finally on line 34 we use the returned value from the mock to verify it was consumed correctly by the thing we are testing i.e. CLI component.

And with all that in place we have a half decent test in place which is testing the contract between 1 of our interface components and the domain layer.

### Contract test for the API

The next step for us would be to write another contract test, this time we want to verify the contract between our API component and the domain logic that it depends on.

Similar to what we've just done, in this test we'll want to verify that the input `salary` figure which is received from the HTTP request is passed to the call to the domain function, `calculate_income_tax_owed()`. Again, we can also check to see if the returned value from the function is used in the HTTP response.

And the same as before, we'll need a new package within the tests folder. So go ahead and add the following files and folders:

```
|- tests/                            # already exists
    |- unit/                         # already exists
        |- interfaces/               # already exists
            |- __init__.py           # already exists
            |- api/                  # to be added
                |- __init__.py       # to be added
                |- routers/          # to be added
                    |- __init__.py   # to be added
                    |- test_taxes.py # to be added
```

And with that structure in place, we can head over to our new `tests/unit/interfaces/api/routers/test_taxes.py` file:

{% code lineNumbers="true" %}
```python
from unittest import mock

from starlette.testclient import TestClient

from interfaces.api.main import app


MODULE_PATH = "interfaces.api.routers.taxes"


class TestCalculateIncomeTaxes:
    @mock.patch(f"{MODULE_PATH}.calculate_income_tax_owed")
    def test_delegates_call_to_domain_logic(
        self, spy_calculate_income_tax_owed: mock.MagicMock
    ):
        """
        Given a salary of £10,000
        When a GET request is made to the `income-taxes/` endpoint
        Then the call is delegated to `calculate_income_tax_owed()`
            to perform the calculation
        """
        # Given
        test_client = TestClient(app=app)
        salary = 10_000
        spy_calculate_income_tax_owed.return_value = 123

        # When
        response = test_client.get("/income-taxes", params={"salary": salary})

        # Then
        spy_calculate_income_tax_owed.assert_called_once_with(salary=salary)

        returned_calculation = spy_calculate_income_tax_owed.return_value
        assert response.json() == {"tax owed": returned_calculation}

```
{% endcode %}

By and large this test looks pretty similar to the one we wrote for the CLI component, the main exception being on line 34 where we verify the use of the mocked function in the output.

And that's about it, now that we've got our contract tests in place we can sleep better at night!

But we've started to repeat things in some of our tests, particularly around the `TestClient` and the `CliRunner` for example. Later on, we'll look at some techniques for how we can address this duplication and encourage reuseability in our test suite.

{% hint style="info" %}
You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-six).
{% endhint %}
