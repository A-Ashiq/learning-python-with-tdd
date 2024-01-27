# Classes

Okay so now things get really interesting.&#x20;

If you've heard of the phrase Object-Orientated Programming (OOP), then by the end of this chapter you will understand why we can now consider classes to be the centre of OOP in Python.&#x20;

Imagine you are an architect, the kind that designs buildings. In order for your work to result in a building you have to come up with a design for that building. Those design deliverables would be a pack of drawings (blueprints in US money) and specifications. These documents describe what the building should look like, where all the doors and windows and walls should be located. The builder takes all of this information and constructs the building according to that information. Classes represent the design and a kind of blueprint. The objects which we create by insantiating those classes represent the buildings themselves, the end product so to speak.

{% hint style="info" %}
Classes act as a specification, here we define how our objects should behave and what they should do. We can instantiate (create) as many objects from our class as we want to.
{% endhint %}

If this doesn't make sense yet. Don't worry, as always we'll guide our learning with tests to get a practical sense of what is going on.

So before we get started lets create the files that we will be using in this chapter:

```
touch src/classes.py tests/test_classes.py
```

***

## Class attributes

Its important to draw the distinction between the class and instances of that class.

A class attribute is an attribute that we can define on the class itself, and as such it will be available to the class as well as all instances of that class.

So lets write a test to capture this

```python
import math

from src.classes import Circle


class TestCircle:
    def test_pi_as_class_attribute(self):
        """
        Given the `Circle` class
        When the `pi` attribute is accessed
        Then the correct value is returned
        """
        # Given / When
        pi = Circle.pi

        # Then
        assert pi == math.pi
```

In this test we don't instantiate an object from the `Circle` class but instead we reference the class itself.

So lets head over to write the class itself:

```python
import math


class Circle:
    pi = math.pi
```

The class attribute `pi` is defined on the class as shown above.

## Instance attributes

An instance attribute is only available to instances of that class. Generally we want them to provide state that is specific to that object.&#x20;

Taking our `Circle` example above, we consider `pi` as a kind of global variable when it comes to the calculation of circles, `pi` is constant and applies to all circles with no exceptions. But the `diameter` or the `radius` of any given circle is unique to each circle.

So lets write a test to capture this:

```python
import math

from src.classes import Circle


class TestCircle:
    ...
    def test_diameter_as_instance_attribute(self):
        """
        Given the `Circle` class
        When the `diameter` attribute is accessed
        Then the correct value is returned
        """
        # Given
        diameter = 2.5

        # When
        circle = Circle(diameter=diameter)

        # Then
        assert circle.diameter == diameter
```

If we run this test as is, we'll get a new error that we haven't seen yet:

```python
FAILED  [100%]
test_classes.py:18 (TestCircle.test_diameter_as_instance_attribute)
self = <test_classes.TestCircle object at 0x1064a5220>

    def test_diameter_as_instance_attribute(self):
        """
        Given the `Circle` class
        When the `diameter` attribute is accessed
        Then the correct value is returned
        """
        # Given
        diameter = 2.5
    
        # When
>       circle = Circle(diameter=diameter)
E       TypeError: Circle() takes no arguments

test_classes.py:29: TypeError

```

In our test we instantiated an object from the `Circle` and pointed the variable `circle` to that new object. The issue of course, is that our `Circle` class does not take any arguments.

Guided by this, lets head over to our `Circle` class and make it accept our diameter argument:

{% code lineNumbers="true" %}
```python
import math


class Circle:
    pi = math.pi

    def __init__(self, diameter: float):
        self.diameter = diameter
```
{% endcode %}

When an object is instantiated from a class, first the `__new__()` method is called, followed by the `__init__()`. The `__new__()` method is responsible for creating the instance. The `__init__()` method is responsible for curating the instance.&#x20;

So for us, the `__init__()` method on line 7 is exactly what we need to provide any kind of data to the object. Its the perfect place for our `diameter` argument.

On line 8, we defined `self.diameter = diameter` , this tells our class to create an **instance** attribute on the class i.e. `self.diamater` and point it to the argument we passed in via the `__init__()` method.



<table><thead><tr><th width="124"></th><th width="418">Use cases</th><th>Accessible by</th></tr></thead><tbody><tr><td>Class attributes</td><td><ul><li><em>Global</em> type constants applicable to all objects of that class</li><li>Storing class-level state. E.g. a counter or even holding all created objects</li><li>Holding default values for ease of reference</li></ul></td><td>The class and all instances</td></tr><tr><td>Instance attributes</td><td><ul><li>State related to the instance which would be different from 1 object to the next</li></ul></td><td>The instances only</td></tr></tbody></table>

***



