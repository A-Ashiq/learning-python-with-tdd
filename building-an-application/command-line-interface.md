# Command line interface

So our product owner has come to us with a new feature request. They want us to add a command line interface (CLI) to our application. This should expose the functionality we've already built in the HTTP API in the form of a CLI, allowing developers to interact with the application without needing to touch the HTTP API itself.

***

## Choosing the library

For the purposes of building the CLI, we can build on top of a library which has done most of the heavy lifting for us. In a similar way in which we saw the `FastAPI` framework did for us when it came to the API.

There are lots of options to choose from when it comes to selecting the library we want to use for the CLI:

* argparse - Python's standard library offering for building CLIs.
* Click - A more modern and widely used library for building CLIs.
* Place - Another library for parsing the command line.
* Typer - A modern CLI library which builds on top of `click` and `argparse`.

The `typer` library provides a similar API to that of `FastAPI` so we're going to use `typer` moving forward. &#x20;

***

## Installing the dependency

We've been around this block before. So lets install the `typer` library within our project:

```sh
pip install typer
```

And finally, to pin it within our project dependencies:

```sh
pip freeze > requirements.txt
```

***

## Writing the test first

To begin with we'll need our test folders and files. So lets tee up the correct folder:

```sh
mkdir -p tests/integration/interfaces/cli/modules
```

And lets add the test files we need:

```sh
touch tests/integration/interfaces/cli/modules/test_taxes.py tests/integration/interfaces/cli/modules/__init__.py
```

And now we're ready to start writing the test in the new file `tests/integration/interfaces/cli/modules/test_taxes.py`:

{% code lineNumbers="true" %}
```python
from click.testing import Result
from typer.testing import CliRunner

from cli import app

runner = CliRunner()


class TestCalculateIncomeTaxed:
    def test_returns_correct_calculation(self):
        """
        Given a salary of £10,000
        When the `taxes calculate-income-taxes` command is called
        Then the correct calculation is returned in the standard output
        """
        # Given
        salary = 10_000
        main_module_name = "taxes"
        sub_module_name = "calculate-income-taxes"
        cli_runner = CliRunner()

        # When
        result: Result = cli_runner.invoke(app=app, args=[main_module_name, sub_module_name, str(salary)])

        # Then
        assert result.exit_code == 0
        assert "£0" in result.stdout

```
{% endcode %}

In this test we describe how we want to call our CLI with the command:

```
taxes calculate-income-taxes 10000
```

We initialize the `CliRunner` from the `typer` library and provide the main `app` instance to be consumed by it.

On line 26, we check that the exit code of the result is 0. This exit code indicates that everything went just fine, consider it to be the happy path. A non-zero exit code normally indicates that something went wrong.

On line 27, we check `stdout` which is the stream in which programs write output to the main environment. This is how we can interact to and from the running program via our CLI.

Note that right now, the import on line 4 which brings in the main `app` instance will throw an error because we have not yet defined that `app` instance or that file.

***

## Creating the CLI application instance

We'll need to create the object which represents the CLI application.

Lets make a file at the root level of our project. We'll put it at the root level of the codebase primarily so we can get to it easily when issuing commands from the terminal:

```renpy
touch cli.py
```

And within that file we should instantiate the CLI application:

{% code lineNumbers="true" %}
```python
from typer import Typer

app = Typer()


if __name__ == "__main__":
    app()

```
{% endcode %}

Take note of line 6, which means only execute the code block below if the file, in this case `cli.py` is being called directly. If it is being imported then only execute the code outside of this block.&#x20;

This allows us to define what should happen if we call the file and differentiate this from when other files simply import code from within this file.

***

## Implement the solution

Now that we have the main CLI application ready, we are in a position to wire up the new component into this application object. This component will represent a kind of route but for the CLI application instead.

So lets tee up the files that we need:

```sh
touch interfaces/cli/modules/taxes.py interfaces/cli/modules/__init__.py interfaces/cli/__init__.py
```

And now lets drop into the new `interfaces/cli/modules/taxes.py` file:

{% code lineNumbers="true" %}
```python
from typer import Typer

from domain.taxes import calculate_income_tax_owed

app = Typer()


@app.command()
def calculate_income_taxes(salary: float) -> None:
    calculated_tax: float = calculate_income_tax_owed(salary=salary)
    print(f"£{calculated_tax}")

```
{% endcode %}

On line 1, we import the `Typer` class from the `typer` library and instantiate it on line 5. If this feels like deja vu and looks familiar then your instincts would be correct. The API to the `typer` library is very similar to that of `FastAPI`.

On line 9, we define a new function and decorate it with `app.command()`. The `typer` library consumes these functions in the same way `FastAPI` does, the parameters we define are used as the arguments to the CLI command along with associated type hints.&#x20;

{% hint style="info" %}
Docstrings are also consumed and displayed but we've not provided any yet.
{% endhint %}

On line 10, we adopt the approach we've already agreed on in terms of taking the input and passing it to the domain layer for the calculation to be performed.

And finally on line 11, we print the result to the terminal. Remember that we are building a CLI here, so we do want our results to be printed to `stdout`. Also note that we are simply printing to `stdout` currently. The `typer` library comes bundled with `rich`,  which allows us to add formatting and styling to the content which is published to `stdout`, including markup, tables and colours.

***

## Wiring the route up to the CLI app

And now the last thing for us to do is to wire up our `taxes` command into the CLI application.

{% code lineNumbers="true" %}
```python
from typer import Typer
from interfaces.cli.modules import taxes

app = Typer()
app.add_typer(typer_instance=taxes.app, name="taxes")


if __name__ == "__main__":
    app()

```
{% endcode %}

On line 2 we import our `taxes` module into the main `cli.py` file.

On line 5 we wire up that `Typer` instance into the main `Typer` CLI application instance.\
The `add_typer()` method is similar to the `register_endpoint()` method we saw when wiring `Router` objects up to the `FastAPI` application instance.\
The `name` parameter to the `add_typer()` method represents the main module name of the command.

{% hint style="info" %}
You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-five).
{% endhint %}

***

## References

* [Argparse | Python official documentation](https://docs.python.org/3/library/argparse.html)
* [Click official documentation](https://click.palletsprojects.com/en/)
* [Place official documentation](https://plac.readthedocs.io/en/latest/)
* [Rich official documentation](https://rich.readthedocs.io/en/stable/introduction.html)
