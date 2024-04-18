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

When we want to write tests to verify the contract between 2 components we can simply mock 1 of them and cast assertions on how the collaborating component is being called.

{% hint style="info" %}
You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-six).
{% endhint %}
