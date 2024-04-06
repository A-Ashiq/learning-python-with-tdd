# Project structure

So far we've not paid a whole lot of attention to our project structure. We've placed all our domain logic, API layer functionality and the application instance itself in the 1 `main.py` file. Although this has been fine so far it's about to become an issue for us.

> _Premature optimization is the root of all evil._ Donald E. Knuth

As a bit of a spoiler alert for the next section, we've heard it from the grapevine that our product owner is going to ask us to build a command line interface (CLI) for our application.

Although we could quite easily just continue adding to our `main.py` file, we would be creating a bit of mess from a structural perspective. If we keep going down this road, our project is going to become unwieldy and difficult to extend and add more things to.

In this section we are going to redesign our project structure so that we have clearer boundaries between the different parts of our system. This might feel like additional work which doesn't bring about any new functionality but this will pay dividends for us later down the line.

***

## Designing our system

For us to properly structure our codebase we need to take a step back and look at our project as a whole. We'll need to figure out which parts are going to need to talk to each other, we'll need to figure out the direction which that communication will be going. And we will also need to understand who needs to interact with each component.

Including our upcoming CLI addition, we know we will have the following components:

* HTTP API endpoint to serve our end users.
* Our CLI which will also serve a group of end users, albeit a different set of users that our HTTP API will service.
* A calculation which feeds our HTTP API endpoint.
* And finally, the main `FastAPI` application instance itself, which consumes our defined HTTP API endpoints and provides all the free goodies that we get from using a framework.

We've talked about this briefly before, but we've already set some rough rules for ourselves that our API endpoint should not directly contain the code needed to perform any calculations.\
The API endpoint should not be aware of the implementation details of the calculations. The endpoint should simply take the input i.e. the HTTP request from end users, perhaps perform some validation and serialization and then ask the calculation for an answer. With this in mind, we can say that our **HTTP API should sit at the edge of our system.**

We can **say the same of our CLI**, which should behave in a similar way. Take some input from the command line and pass it over to the calculation for the correct answer. So we can safely say that our CLI should also sit at the edge of our system too.&#x20;

Our calculations are the only thing so far that actually need to be shared between multiple components. Both our HTTP API and our CLI will depend on and find themselves calling out to our calculations. Our calculations are also the only things which could be easily understood by a non-techinical stakeholder. As such our calculations have an element of subject matter expertise to them. With this in mind we can wrap our calculations in the centre of our system.

And we also have our `FastAPI` application instance which only needs to know about the endpoints.

So lets sketch this conceptual model out for ourselves:



***

## References

* [Structured Programming with go to Statements | Donald E. Knuth](https://dl.acm.org/doi/10.1145/356635.356640)
