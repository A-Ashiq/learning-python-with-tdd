# Type hints

When we assign something to a variable. That _thing_ has a specific type. It could be an integer, a string, a boolean, a data structure or some other kind of object. In statically-typed languages we have to explicitly declare this type alongside the variables, arguments or return values.

Python is a **dynamically-typed** language. Which means that we are not required to declare the type associated with our variables, arguments or return values. This has its pros and cons. This reduces the burden on newcomers to the language and means its easier to get up and running. However, because Python allows you to pass anything through a given parameter, this does also allow you to pass through different types.





{% hint style="info" %}
Python does **not** enforce type hints. Although 3rd party libraries like [mypy](https://www.mypy-lang.org/) can introduce static type checking for us
{% endhint %}
