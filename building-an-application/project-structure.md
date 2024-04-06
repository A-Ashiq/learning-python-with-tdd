# Project structure

So far we've not paid a whole lot of attention to our project structure. We've placed all our domain logic, API layer functionality and the application instance itself in the 1 `main.py` file. Although this has been fine so far it's about to become an issue for us.

> _Premature optimization is the root of all evil._ Donald E. Knuth

As a bit of a spoiler alert for the next section, we've heard it from the grapevine that our product owner is going to ask us to build a command line interface (CLI) for our application.

Although we could quite easily just continue adding to our `main.py` file, we would be creating a bit of mess from a structural perspective. If we keep going down this road, our project is going to become unwieldy and difficult to extend and add more things to.

In this section we are going to redesign our project structure so that we have clearer boundaries between the different parts of our system. This might feel like additional work which doesn't bring about any new functionality but this will pay dividends for us later down the line.

***









***

## References

* [Structured Programming with go to Statements | Donald E. Knuth](https://dl.acm.org/doi/10.1145/356635.356640)
