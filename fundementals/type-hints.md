# Type hints

When we assign something to a variable. That _thing_ has a specific type. It could be an integer, a string, a boolean, a data structure or some other kind of object. In statically-typed languages we have to explicitly declare this type alongside the variables, arguments or return values.

Python is a **dynamically-typed** language. Which means that we are not required to declare the type associated with our variables, arguments or return values. This has its pros and cons. This reduces the burden on newcomers to the language and means its easier to get up and running. However, because Python allows you to pass anything through a given parameter, this does also allow you to provide different types to those parameters. As well as this, when we assign something to a variable, we can also change its type after the fact. As you can imagine, these characteristics can cause problems and can lead to bugs.

## Function without type hints

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

***

## Adding type hints&#x20;

Taking our simple `add()` function from earlier, this is how we might want to add type annotations to it:

```python
def add(x: int, y: int) -> int:
    return x + y
```

We are making the assumption here that we only want callers of our function to pass integers into the `add()` function, and not types which can be concatenated instead.

We use the `:` character to indicate the end of the argument name and the start of the type declaration. The `->` , consists of a dash `-`, and the greater than `>` to construct an arrow.  This arrow sign signifies the begining of the declaration of the return type.

***

## Communicating with type hints

We can also communicate our intent with our type hints. We can use them to tell readers what we expect to be provided to our arguments as well as what we might return as we saw earlier.

We can also use type hints to declare if an argument is optional, which is a common pattern you will see a lot of:

```python
def send_request(url: str, query_params: dict[str, str] | None = None) -> None:
    requests.get(url=url, params=query_params)
```

In this case our `url` parameter is type hinted as `str` , whereas the `query_params` argument is type hinted as `dict[str, str] | None` i.e. it could be a dictionary or given as None, whilst this argument will default to `None` if nothing is explicitly provided.

This optional parameter means that callers of our `send_request()` function can call it with or without the `query_params` argument.

{% hint style="info" %}
`Union[..., None]` is equivalent to `Optional[...]`. But as of Python version 3.10, we can now use the `|` union operator instead.
{% endhint %}

***

## References

* [Typing | Python official documentation](https://peps.python.org/pep-0484/)
* [PEP 484 | Type hints](https://peps.python.org/pep-0484/)
* [PEP 483 | The theory of type hints](https://peps.python.org/pep-0483/)
