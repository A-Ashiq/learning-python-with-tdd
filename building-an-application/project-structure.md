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

So lets sketch a conceptual model out for ourselves:

<figure><img src="../.gitbook/assets/Screenshot 2024-04-06 at 13.04.03.png" alt=""><figcaption><p>Conceptual sketch of our system components</p></figcaption></figure>

Our API and our CLI are just a kind of interface to our application. Really they just provide a means of interacting with our system. The domain logic can sit right in the heart of our system and serve the upstream components as and when needed. Ideally we should keep the components at the edge of our system, in our case the API and the CLI free of domain logic. Those components should only reach downwards into the domain core to get what they need to service their own users.

{% hint style="info" %}
If you imagine a busy restaurant, as customers we interact with the waiters. We give them our orders and they take that order to the kitchen so that the chefs can prepare our food. Our API and CLI are simply different waiters in this analogy. The kitchen represents the domain logic, whilst the prepared food is equivalent to the calculated answers we get back from the domain logic.&#x20;
{% endhint %}

***

## Directory structure

Taking the conceptual model we've just sketched out for ourselves. Lets now apply this to how we think our new folder structure should look:

```
|- domain/
    |- taxes.py
|- interfaces/
    |- api/
        |- __init__.py
        |- main.py
        |- routers/
            |- __init__.py
            |- taxes.py
    |- cli/
        ...
|- tests/
    ... 
```

Our `api/` and `cli/` directories can be placed within an `interfaces/` directory. The `main.py` of the `api/` folder can hold our application instance, whereas the `interfaces/api/routers/taxes.py` file can house the `GET` `income-taxes` endpoint that we've already written. Note that the `routers/` setup is a `FastAPI` specific way of doing things.

We don't know exactly what the `cli` component will look like just yet. But it's fine to assume that we can keep it contained within that folder.

***

## Restructuring the project

To begin with lets create the new folders that we'll need:

```sh
mkdir -p domain interfaces/api/routers interfaces/cli
```

The `-p` flag of the `mkdir` command creates folders recursively for us. So the above command will create the following:

* `domain/`
* `interfaces/`
* `interfaces/api/`
* `interfaces/api/routers/`
* `interfaces/cli/`

This is the same as:

```
|- domain/
|- interfaces/
    |- api/
        |- routers/
    |- cli/
```

***

## Setting up the domain layer

Our function `calculate_income_tax_owed()` is currently in the `main.py` file. It is also our only real bit of domain logic right now.

To set up our domain layer, we will need to make a new file and move that function into that file.

To begin with, lets make the new file:

```sh
touch domain/taxes.py
```

Most modern IDEs like Pycharm or VS Code have really useful refactoring tools, including tools to move functions and classes from 1 file to another.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 20.55.22.png" alt=""><figcaption><p>Screenshot of <code>Move</code> refactoring tool in Pycharm</p></figcaption></figure>

In doing so, they will redo all imports for us too, which is worth its weight in gold.

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 21.00.42.png" alt=""><figcaption></figcaption></figure>

And now lets also move the tests into the correct place. Lets make the directories we need:

```sh
mkdir -p tests/unit/domain
```

And then lets make the test file that we need:

```sh
touch tests/unit/domain/test_taxes.py
```

With that in place, we can use the `Move` tool to move the existing test class `TestCalculateIncomeTaxOwed` to the new `tests/unit/domain/test_taxes.py` file.

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 21.13.46.png" alt=""><figcaption></figcaption></figure>

***

## Creating a home for the main app

We also know that we need to make a new home for our main `FastAPI` application instance.

So lets go ahead and create the files we need:

```sh
touch interfaces/api/main.py interfaces/api/__init__.py
```

And finally, lets use the `Move` tool again, to move the `app` which was previously at `main.py` to instead be located in `interfaces.api/main.py`

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 22.09.44.png" alt=""><figcaption></figcaption></figure>

***

## References

* [Structured Programming with go to Statements | Donald E. Knuth](https://dl.acm.org/doi/10.1145/356635.356640)
* [The Clean Architecture | Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
