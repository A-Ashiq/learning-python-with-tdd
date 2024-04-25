# Refactoring

You might have heard of the word _refactoring_ before. But now we're gonna put that into practice. Refactoring is the process **changing how a piece of code is implemented** and structured. The crucial part of this process is that the **funcionality must remain the same.**&#x20;

In other words, we can tidy up some piece of code but it must still produce the same results. If the results change then we've strayed away and further afield than just refactoring.

This is where having tests in place can really shine. We can lean on our tests and rely on them as a kind of safety harness to catch us if we stray too far with our changes.

***

## Planning the changes

Our `calculate_income_tax_owed()` function has gotten quite unruly as we've been bolting on pieces of functionality with incremental approach we adopted. And that is absolutely fine. But now we can focus on tidying it up without having to worry too much about bringing about certain functionaluty, because our tests will inform us.

So let's take a look at the function and see if there are any clear lines we can draw for ourselves.

<figure><img src="../.gitbook/assets/Screenshot 2024-04-25 at 20.45.45.png" alt=""><figcaption></figcaption></figure>

We've kinda crowbarred in a few different calculation into the same place. But really the `calculate_income_tax_owed()` function shouldn't have to be aware of the implementation details or the nitty gritty of how to do these different calculations.

If anything the `calculate_income_tax_owed()` function should just take the input `salary` and decide which calculation to perform based on the thresholds. We can encapsulate the calculations as seperate private functions:

<figure><img src="../.gitbook/assets/Screenshot 2024-04-25 at 20.58.02.png" alt=""><figcaption></figcaption></figure>

***

## Refactoring the function&#x20;

For now, lets ignore the `return 0` statement which handles `salary` figures under the tax free allowance threshold and extract the 3 highlighted blocks into seperate private functions:

{% code lineNumbers="true" %}
```python
PERSONAL_ALLOWANCE_THRESHOLD = 12_570
HIGHER_RATE_THRESHOLD = 50_270
ADDITIONAL_RATE_THRESHOLD = 125_140


def calculate_income_tax_owed(salary: float) -> float:
    if salary <= PERSONAL_ALLOWANCE_THRESHOLD:
        return 0

    if salary <= HIGHER_RATE_THRESHOLD:
        return _calculate_basic_rate_tax_owed(salary=salary)

    if salary <= ADDITIONAL_RATE_THRESHOLD:
        return _calculate_higher_rate_tax_owed(salary=salary)

    return _calculate_additional_rate_tax_owed(salary=salary)


def _calculate_basic_rate_tax_owed(salary) -> float:
    return (salary - PERSONAL_ALLOWANCE_THRESHOLD) * 0.20


def _calculate_higher_rate_tax_owed(salary) -> float:
    return (HIGHER_RATE_THRESHOLD - PERSONAL_ALLOWANCE_THRESHOLD) * 0.20 + (
        salary - HIGHER_RATE_THRESHOLD
    ) * 0.40


def _calculate_additional_rate_tax_owed(salary) -> float:
    return (
        (HIGHER_RATE_THRESHOLD - PERSONAL_ALLOWANCE_THRESHOLD) * 0.20
        + (ADDITIONAL_RATE_THRESHOLD - HIGHER_RATE_THRESHOLD) * 0.40
        + (salary - ADDITIONAL_RATE_THRESHOLD) * 0.45
    )

```
{% endcode %}

See the difference now? The `calculate_income_tax_owed()` has become a lot simpler and easier to read. It delegates the responsibility of performing the calculation to our new private functions according to thresholds.&#x20;

We've moved the thresholds to the upper scope of the file because they are shared between the calculations.

{% hint style="info" %}
The thresholds could really do with being injected into the calculation functions, which would allow us to do the calculations based on different thresholds since we know they can change from year to year.
{% endhint %}

In other languages, there is the concept of **constants**. Constants are similar to variables the key difference being that they cannot be changed after being initialized. Python does not support constants but it does have a convention to indicate a constant. Variables defined in capital letters are to be considered as constants. We've made use of this convention on lines 1-3.

If we run our tests again, they will still pass.

***

## A note about private functions

Private functions in Python are defined as functions or methods with a leading `_` . As in the name of the function must start with `_`. As you might have guessed private functions in Python are also not quite enforced in the same way as other languages. There is nothing to stop you from defining it as is and importing it directly elsewhere.

{% hint style="info" %}
You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-seven/).
{% endhint %}
