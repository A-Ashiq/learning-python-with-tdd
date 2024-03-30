# Code styling

Okay so I'll admit I can be pretty lazy sometimes.

I also don't like spending valuable time debating coding styles. I'm not talking about functionality here, I'm really talking about styles. Whether to use break lines in certain places or add a comma here or there.

And we're all different. As humans we also all prefer slightly different styles. So without an agreed baseline of coding styles, we could otherwise fall into the trap of wasting time discussing styles.

Fortunately there are some pretty good tools out there which solve these problems. When it comes to addressing code styles, these can generally be broken down into 2 main subgroups:

* **Linters** can analyse our code for smells and patterns which are deemed low quality and highlight them to us.
* **Formatters** will take our code and re-write them to conform to an adjustable set of rules. They tidy up our code to an opinionated set of guidelines whilst maintaining the logic and functionality.

***

## An example code snippet

To start off with lets make a new file. In your terminal:

```
touch src/code_styling.py
```

And now in our newly created file, lets add some code that we want to put through some of our static analysis tooling:

{% code lineNumbers="true" %}
```python
def some_operation(first_number, second_number, third_number, fourth_number, fifth_number, sixth_number, seventh_number):

    denominator = [fourth_number, fifth_number, sixth_number, seventh_number]
```
{% endcode %}

Lets take this new `some_operation()` function which does a whole lot of nothing. For this we don't really care about what the function does. But we do care about writing it in a consistent style. The function takes 7 arguments and creates a list of some of those arguments as a variable called `denominator` on line 3.

Putting functionality and logic to the side, there are a ton of differerent ways in which we could have written this function.

Some languages come bundled with code formatting tools, for example `Go` gives us the `fmt` package which can be used straight out of the box. Unfortunately Python does not have an equivalent in the standard library. But there a couple of widely-used 3rd party libraries that we can install pretty quickly:

* `isort` - Reorganises and formats imports in our code.
* `black` - Takes an opinionated view and formats our code for us.
* `ruff` - A combined linter and formatter which has become increasingly popular in recent years.

For now, we are just gonna try out using `black`. So lets drop into our terminal and install the library:

```shell
pip install black
```

Once that is complete, we can run `black` over the code we just wrote. Once again, in your terminal:

```shell
black src/code_formatting.py
```

In your terminal you will see a message which looks something like this:

```sh
reformatted src/code_styling.py

All done! ✨ 🍰 ✨
1 file reformatted.
```

And finally we can take a look at our new function:

{% code lineNumbers="true" %}
```python
def some_function(
    first_number,
    second_number,
    third_number,
    fourth_number,
    fifth_number,
    sixth_number,
    seventh_number,
):

    denominator = [fourth_number, fifth_number, sixth_number, seventh_number]
```
{% endcode %}

See the difference?

The code formatter `black` took our code and applied its set of rules against our code and rewrote it for us to confirm to its set of rules. Functionally, our code is no different but because it conforms to a given set of rules that means we can apply this across entire codebases to make it look like it was written by the 1 person.&#x20;

Not only does this save time, it can also reduce cognitive load for readers of our code (often ourselves at some point in the future!). By applying the same styling across the board, our brains know what to expect when we are reading through, we don't have to decipher different styling patterns as well as logic.

This does take some getting used to, but crucially the consistency that code formatters bring are worth the change.

***

## References

* [isort formatter documentation](https://pycqa.github.io/isort/)
* [Black code formatter documentation](https://black.readthedocs.io/en/stable/index.html)
* [Ruff documentation](https://docs.astral.sh/ruff/)
