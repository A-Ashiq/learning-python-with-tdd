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

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 21.00.42.png" alt=""><figcaption><p>Screenshot from Pycharm for moving the <code>calculate_income_tax_owed()</code> into the interfaces layer</p></figcaption></figure>

And now lets also move the tests into the correct place. Lets make the directories we need:

```sh
mkdir -p tests/unit/domain
```

And then lets make the test file that we need:

```sh
touch tests/unit/domain/test_taxes.py
```

With that in place, we can use the `Move` tool to move the existing test class `TestCalculateIncomeTaxOwed` to the new `tests/unit/domain/test_taxes.py` file.

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 21.13.46.png" alt=""><figcaption><p>Screenshot from Pycharm for moving the <code>TestCalculateIncomeTaxOwed</code> into the unit tests file</p></figcaption></figure>

***

## Creating a home for the main app

We also know that we need to make a new home for our main `FastAPI` application instance.

So lets go ahead and create the files we need:

```sh
touch interfaces/api/main.py interfaces/api/__init__.py
```

And finally, lets use the `Move` tool again, to move the `app` which was previously at `main.py` to instead be located in `interfaces.api/main.py`

<figure><img src="../.gitbook/assets/Screenshot 2024-04-08 at 22.09.44.png" alt=""><figcaption><p>Screenshot from Pycharm for moving the main <code>FastAPI</code> app instance</p></figcaption></figure>

***

## Splitting the API by routes

When dealing with larger applications, we often want to split our API into groups. [Flask uses blueprints](https://flask.palletsprojects.com/en/2.3.x/blueprints/) to acheive this. [FastAPI uses the concept of an `APIRouter`](https://fastapi.tiangolo.com/tutorial/bigger-applications/#apirouter). They are pretty similar just dressed up a little different.

The idea being we register our endpoints on an `APIRouter` instance. And then we can register the `APIRouter` on the main `FastAPI` app instance to wire it up to the application.

Lets add the files we need:

```sh
touch interfaces/api/routers/__init__.py interfaces/api/routers/taxes.py
```

Once this is done, we can go and set up our first `APIRouter` and wire the endpoint into it:

{% code lineNumbers="true" %}
```python
from fastapi import APIRouter

from domain.taxes import calculate_income_tax_owed

router = APIRouter()


@router.get(path="/income-taxes")
def calculate_income_taxes(salary: float) -> dict[str, float]:
    tax_owed: float = calculate_income_tax_owed(salary=salary)
    return {"tax owed": tax_owed}
    
```
{% endcode %}

The way in which we interact with the `APIRouter` is exactly the same as when we were previously wiring the endpoints straight into main `FastAPI` app instance, which happens to be a great design approach which has made our lives easier!

Once this is in place, we can get rid of the original `main.py` file as we no longer need it:

```sh
rm main.py
```

And the last thing we need to do is to register the `APIRouter` into the main `FastAPI` app instance:

{% code lineNumbers="true" %}
```python
from fastapi import FastAPI
from .routers import taxes

app = FastAPI()
app.include_router(router=taxes.router)

```
{% endcode %}

***

## Updating the tests directory to reflect the source code

We have 1 more little task left. We need to make sure that the `tests/` directory reflects the changes we've made to the project structure of the source code.

Currently our `tests/` directory looks like the following:

```
|- tests/
    |- integration/
        |- test_main.py
    |- unit/
        |- domain/
            |- test_taxes.py
```

The `test_taxes.py` file is in the correct position but the `test_main.py` in the `integration/` subdirectory does not reflect what we have now.&#x20;

We know that the tests in the `test_main.py` file hit the `GET /income-taxes` endpoint. This is now brought about via the `APIRouter` we just created and wired up.

So lets tee up the directories that we need in the correct place:

```sh
mkdir -p tests/integration/interfaces/api/routers
```

And then let's use the `mv` command to move the original test file into the correct place and rename it in 1 fell swoop:

```sh
mv tests/integration/test_main.py tests/integration/interfaces/api/routers/test_taxes.py
```

Now our `tests/` directory should look something like:

```
|- tests/
    |- integration/
        |- interfaces/
            |- api/
                |- routers/
                    |- test_taxes.py
    |- unit/
        |- domain/
            |- test_taxes.py
```

And whilst we're at it we will want to add `__init__.py` files throughout the `tests/` directory.

{% hint style="info" %}
You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-four).
{% endhint %}

***

## References

* [Structured Programming with go to Statements | Donald E. Knuth](https://dl.acm.org/doi/10.1145/356635.356640)
* [The Clean Architecture | Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
