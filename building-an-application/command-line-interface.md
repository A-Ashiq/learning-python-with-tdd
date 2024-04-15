# Command line interface

So our product owner has come to us with a new feature request. They want us to add a command line interface (CLI) to our application. This should expose the functionality we've already built in the HTTP API in the form of a CLI, allowing developers to interact with the application without needing to touch the HTTP API itself.

***

## Choosing the library

For the purposes of building the CLI, we can build on top of a library which has done most of the heavy lifting for us. In a similar way in which we saw the `FastAPI` framework did for us when it came to the API.

There are lots of options to choose from when it comes to selecting the library we want to use for the CLI.
