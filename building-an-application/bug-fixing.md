# Bug fixing

Bugs are all but inevitable despite our best efforts.

And at the end of the last section we found our first one. So how do we go about fixing bugs? And more importantly how do we ensure that once we fix bugs, they can't come back to bite us again? As you might have guessed, the answer starts and ends with tests!&#x20;

When it comes to fixing bugs, ideally we should be writing tests at the edge of our system. Taking our endpoint for example, the test which targets the bug should be written against the domain logic if possible i.e. the `calculate_income_tax_owed()` as opposed to the API endpoint itself. Remember the API endpoint might necessitate passing through various unrelated components like authentication, validation and serialization. This of course assumes that the bug is not located in 1 of those areas.

Our test suite should double up as a specification for how we want our systems to operate. This should include how we expect it to function under normal conditions, under failure conditions and also to cement into place adjustments to behaviour. These adjustments being bug fixes.

***

## Writing the test to catch the bug

