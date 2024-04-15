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

## References

* [Argparse | Python official documentation](https://docs.python.org/3/library/argparse.html)
* [Click official documentation](https://click.palletsprojects.com/en/)
* [Place official documentation](https://plac.readthedocs.io/en/latest/)
