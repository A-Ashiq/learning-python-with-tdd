# Bug fixing

Bugs are all but inevitable despite our best efforts.

And at the end of the last section we found our first one. So how do we go about fixing bugs? And more importantly how do we ensure that once we fix bugs, they can't come back to bite us again? As you might have guessed, the answer starts and ends with tests!&#x20;

When it comes to fixing bugs, ideally we should be writing tests at the edge of our system. Taking our endpoint for example, the test which targets the bug should be written against the domain logic if possible i.e. the `calculate_income_tax_owed()` as opposed to the API endpoint itself. Remember the API endpoint might necessitate passing through various unrelated components like authentication, validation and serialization. This of course assumes that the bug is not located in 1 of those areas.

Our test suite should double up as a specification for how we want our systems to operate. This should include how we expect it to function under normal conditions, under failure conditions and also to cement into place adjustments to behaviour. These adjustments being bug fixes.

***

## Writing the test to catch the bug

So lets write another test to capture the behaviour we want to add to our function:

```python
class TestCalculateIncomeTaxOwed:
    ...
     
    @pytest.mark.parametrize(
        "salary",
        (0, 10, 100, 200, 1_000, 5_000, 12_568, 12_569),
    )
    def test_calculates_no_tax_when_salary_is_lower_than_personal_allowance(
        self, salary: int
    ):
        """
        Given a salary which lower than the personal allowance
        When `calculate_income_tax_owed()` is called
        Then the correct calculated tax owed of 0 is returned
        """
        # Given
        input_salary = salary

        # When
        calculated_tax_owed: int = calculate_income_tax_owed(salary=input_salary)

        # Then
        assert calculated_tax_owed == 0
```

Once again, we've parametrized a number of values within the contextual boundary that makes sense here i.e between £0 and £12,569. In any of these cases we expect the calculated tax owed to be £0.&#x20;

If we run this test as is, it will fail due to the incorrect assumptions we'd already made in our implementation.

***

## Implement the fix

Now that we have specified the behavior we want. We can now safely go and implement the solution. Having the test in place sets up our finish line, we don't have to worry about when we feel like the functional part of the solution is ready because the test will tell us.

{% code lineNumbers="true" %}
```python
def calculate_income_tax_owed(salary: float) -> float:
    tax_free_allowance = 12_570
    basic_rate_threshold = 50_270
    
    if salary <= tax_free_allowance:
        return 0

    if salary <= basic_rate_threshold:
        return (salary - tax_free_allowance) * 0.20

    return (basic_rate_threshold - tax_free_allowance) * 0.20 + (
        salary - basic_rate_threshold
    ) * 0.40
```
{% endcode %}

For now lets be really crude and just add another `if` statement before any of the calculations can be performed one line 5.

This function feels like it is starting to get out of hand. But that's okay for now.&#x20;

The beauty of layering in all these tests into place means that if we want to go back and refactor things later on then we can do so easily and safely in the knowledge that our refactoring has not inadvertently broken functionality that we wanted.

Our tests are starting to feel like a safety net, they are now there to catch us from making any silly mistakes and in this case also to make sure that bugs are not repeated.

If we run the test now, we'll see it passes:

```python
============================= test session starts ==============================
collecting ... collected 8 items

test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[0] PASSED [ 12%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[10] PASSED [ 25%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[100] PASSED [ 37%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[200] PASSED [ 50%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[1000] PASSED [ 62%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[5000] PASSED [ 75%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[12568] PASSED [ 87%]
test_main.py::TestCalculateIncomeTaxOwed::test_calculates_no_tax_when_salary_is_lower_than_personal_allowance[12569] PASSED [100%]

============================== 8 passed in 0.24s ===============================

Process finished with exit code 0
```
