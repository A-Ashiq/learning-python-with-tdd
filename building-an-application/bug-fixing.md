# Bug fixing

Bugs are all but inevitable despite our best efforts.

And at the end of the last section we found our first one. So how do we go about fixing bugs? And more importantly how do we ensure that once we fix bugs, they can't come back to bite us again? As you might have guessed, the answer starts and ends with tests!&#x20;

Ideally we should be writing tests at the edge of our system. Taking our endpoint for example, the test which targets the bug should be written against the domain logic i.e. the `calculate_income_tax_owed()` as opposed to the API endpoint itself.\


