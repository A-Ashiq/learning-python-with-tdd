# Type hints

When we assign something to a variable. That _thing_ has a specific type. It could be an integer, a string, a boolean, a data structure or some other kind of object. In statically-typed languages we have to explicitly declare this type alongside the variables, arguments or return values.

Python is a **dynamically-typed** language. Which means that we are not required to declare the type associated with our variables, arguments or return values. This has its pros and cons. This reduces the burden on newcomers to the language and means its easier to get up and running. However, because Python allows you to pass anything through a given parameter, this does also allow you to provide different types to those parameters. As well as this, when we assign something to a variable, we can also change its type after the fact. As you can imagine, these characteristics can cause problems and can lead to bugs.

Imagine we had ths function, without type hints

```python
def add(x, y):
    return x + y
```

Python allows us to pass anything into the `x` and `y` arguments. So either of these would be valid:

```python
>>> add(x=1, y=2)
3
```

In this case we are passing `1` and `2` into the arguments of our `add()` function.&#x20;

We can also concatenate the 2 provided arguments.

```python
>>> add(x="abc", y="def")
'abcdef'
```

So what would happen if we provided 2 arguments of different types?

```python
>>> add(x=1, y="def")
Traceback (most recent call last):
    coro = func()
           ^^^^^^
  File "<input>", line 1, in <module>
  File "<input>", line 2, in add
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

See the problem? Because Python allows us to pass any kinds of types we want around, we can easily run into problems just like this.

[PEP-484](https://peps.python.org/pep-0484/) brought about the proposal for type hints in Python. It was included in version 3.5.

This type annotation system is entirely optional in Python, but is worth adopting for anything non-trivial. Especially, given the benefits they provide in the context of working within a team on the same codebase.

{% hint style="info" %}
Even if you add type hints, Python will **not** enforce type hints. Although 3rd party libraries like [mypy](https://www.mypy-lang.org/) can introduce static type checking for us.
{% endhint %}



## References

* [Typing | Python official documentation](https://peps.python.org/pep-0484/)
* [PEP 484 | Type hints](https://peps.python.org/pep-0484/)
* [PEP 483 | The theory of type hints](https://peps.python.org/pep-0483/)
