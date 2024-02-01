# Classes

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

***

## Instance attributes

An instance attribute is only available to instances of that class. Generally we want them to provide state that is specific to that object.&#x20;

Taking our `Circle` example above, we consider `pi` as a kind of global variable when it comes to the calculation of circles, `pi` is constant and applies to all circles with no exceptions. But the `diameter` or the `radius` of any given circle is unique to each circle.

So lets write a test to capture this:

```python
import math

from src.classes import Circle


class TestCircle:
    ...
    def test_radius_as_instance_attribute(self):
        """
        Given the `Circle` class
        When the `radius` attribute is accessed
        Then the correct value is returned
        """
        # Given
        radius = 2.5

        # When
        circle = Circle(radius=radius)

        # Then
        assert circle.radius == radius
```

If we run this test as is, we'll get a new error that we haven't seen yet:

```python
FAILED  [100%]
test_classes.py:18 (TestCircle.test_radius_as_instance_attribute)
self = <test_classes.TestCircle object at 0x1064a5220>

    def test_radius_as_instance_attribute(self):
        """
        Given the `Circle` class
        When the `radius` attribute is accessed
        Then the correct value is returned
        """
        # Given
        radius = 2.5
    
        # When
>       circle = Circle(radius=radius)
E       TypeError: Circle() takes no arguments

test_classes.py:29: TypeError

```

In our test we instantiated an object from the `Circle` and pointed the variable `circle` to that new object. The issue of course, is that our `Circle` class does not take any arguments.

Guided by this, lets head over to our `Circle` class and make it accept our `radius` argument:

{% code lineNumbers="true" %}
```python
import math


class Circle:
    pi = math.pi

    def __init__(self, radius: float):
        self.radius = radius
```
{% endcode %}

When an object is instantiated from a class, first the `__new__()` method is called, followed by the `__init__()`. The `__new__()` method is responsible for creating the instance. The `__init__()` method is responsible for curating the instance.&#x20;

So for us, the `__init__()` method on line 7 is exactly what we need to provide any kind of data to the object. Its the perfect place for our `radius` argument.

On line 8, we defined `self.radius = radius` , this tells our class to create an **instance** attribute on the class i.e. `self.radius` and point it to the argument we passed in via the `__init__()` method.

<table><thead><tr><th width="124">Attibute type</th><th width="418">Use cases</th><th>Accessible by</th></tr></thead><tbody><tr><td>Class</td><td><ul><li><em>Global</em> type constants applicable to all objects of that class</li><li>Storing class-level state. E.g. a counter or even holding all created objects</li><li>Holding default values for ease of reference</li></ul></td><td>The class and all instances</td></tr><tr><td>Instance</td><td><ul><li>State related to the instance which would be different from 1 object to the next</li></ul></td><td>The instances only</td></tr></tbody></table>

## Methods

Okay so now lets say we start to need something more than just storing state on our class.

This is where methods come into play. By and large there a few different types of methods which are available for use:

* Instance methods - A method which has access to the instance. These are the most commonly used type of method.
* Static methods - A method which can be called without having created an object, as such it does not have access to the instance itself.&#x20;
* Class methods - Similar to static methods, except class methods have access to the class and not the instance itself.
* Property methods - Behave similar to instance methods, except they are called without `()` and as such they are called in the same way instance or class attributes are called.&#x20;

### Instance methods

Instance methods are the most common type of method that we might use on a class. Instance methods have access to the internal state of the class i.e. the class and instance attributes. The instance methods can be used to perform calculations or alter state on the class.

With instance methods we pass the keyword `self` into the method as the first argument.&#x20;

{% hint style="info" %}
It should be noted that the `self` keyword is actually just a very strong convention, not a reserved keyword.
{% endhint %}

Python takes this `self` as the object itself. This is the glue which provides the current object and all of its state and makes it available to the instance method.

Going back to our `Circle` class lets write a test to capture a new instance method:

```python
class TestCircle:
    ...
    def test_calculate_area_as_instance_method(self):
        """
        Given a radius of 2.5
        When `calculate_area()` is called
            from an instance of a `Circle`
        Then the correct value of 19.63 is returned
        """
        # Given
        radius = 2.5
        circle = Circle(radius=radius)

        # When
        area: float = circle.calculate_area()

        # Then
        assert area == math.pi * radius ** 2
```

Running this test is going to give us our old faithful friend, the `AttributeError`:

```python
FAILED [100%]
test_classes.py:49 (TestCircle.test_calculate_area_as_instance_method)
self = <test_classes.TestCircle object at 0x106853230>

    def test_calculate_area_as_instance_method(self):
        """
        Given a radius of 2.5
        When `calculate_area()` is called
            from an instance of a `Circle`
        Then the correct value of 19.63 is returned
        """
        # Given
        radius = 2.5
        circle = Circle(radius=radius)
    
        # When
>       area = circle.calculate_area()
E       AttributeError: 'Circle' object has no attribute 'calculate_area'

test_classes.py:62: AttributeError
```

With that in mind, lets implement the `calculate_area()` method on our `Circle` class:

```python
class Circle:
    pi = math.pi

    def __init__(self, radius: float):
        self.radius = radius

    def calculate_area(self) -> float:
        return self.pi * self.radius ** 2
```

We define the instance method in a similar way to the way in which we would a function. The `self` argument is the first argument to be passed into the `calculate_area()` method. With the `self` argument, we have access to both the `pi` class attribute and the `radius` instance attribute, which we use for the implementation.

### Static methods

For all intents and purposes, static methods are the same as functions. The difference being that static methods live on the class itself.

Static methods don't take the `self` or the `cls` argument as the 1st argument and as such static methods are available to the class and not just the object itself as is the case with instance methods. Because static methods don't have access to the state of the object they cannot alter state or perform computation with any input from the object state.

So you might be wondering, if all this holds true then why even bother with them? Surely it would be simpler to implement that piece of logic as a module-level function which sits alongside the class.

And there are primarily a couple of reasons we might want to use static methods:

* To group logically similar pieces of functionality together with the class and keep a cleaner namespace.
* To leverage class inheritance should the need arise.

{% hint style="info" %}
Guido Van Rossum, the creator of Python has previously admitted to static methods being a bit of a mistake in [this note](https://mail.python.org/pipermail/python-ideas/2012-May/014969.html) and [this one too.](https://mail.python.org/pipermail/python-ideas/2016-July/041189.html)
{% endhint %}

To demonstrate the use of static methods, we're going to make up a new class for rectangles. If you're thinking these examples are overly simplistic, that's the point! In the next chapter we'll start to build something much more tangible than rectangles and circles.

But for now, lets write a test for a static method that we'll call from our new `Rectangle` class:

```python
class TestRectangle:
    def test_calculate_perimeter(self):
        """
        Given a length of 2.5 and width 1
        When `calculate_perimeter()` is called
            from the `Rectangle` class
        Then the correct value is returned
        """
        # Given
        length = 2.5
        width = 1.0
        
        # When
        perimeter = Rectangle.calculate_perimeter(
            length=length, 
            width=width,
        )
        
        # Then
        assert perimeter == 2 * (length + width)
```

See the difference between this and the instance or property methods? In the `# When` section of our test, we call the `calculate_perimiter()` method from the Rectangle class itself **not an instance** of the `Rectangle` class.&#x20;

If we run this test as is, we will get our familiar friend the `NameError`:

```python
FAILED          [100%]
test_classes.py:68 (TestRectangle.test_calculate_perimeter)
self = <test_classes.TestRectangle object at 0x103e37770>

    def test_calculate_perimeter(self):
        """
        Given a length of 2.5 and width 1
        When `calculate_perimeter()` is called
            from the `Rectangle` class
        Then the correct value is returned
        """
        # Given
        length = 2.5
        width = 1.0
    
        # When
>       perimeter = Rectangle.calculate_perimeter(
            length=length,
            width=width,
        )
E       NameError: name 'Rectangle' is not defined

test_classes.py:81: NameError
```

With this in mind, lets go and implement the `calculate_perimeter()` on a new `Rectangle` class:

```python
class Rectangle:
    @staticmethod
    def calculate_perimeter(length: float, width: float) -> float:
        return 2 * (length + width)
```

We define the method as a function which lives on the `Rectangle` class, wrapping the method with the `@staticmethod` decorator. The `@staticmethod` decorator is built-in and we don't need to explicitly import it.

Also see how we don't have to pass `self` into the method as the 1st argument? This is the _static_ nature of the method. The method is completely independent from the state of the class or any relevant instance of that class.

Now make sure you import the `Rectangle` class into the test file:

```python
import math

from src.classes import Circle, Rectangle


class TestCircle:
    ...
    

class TestRectangle:
    def test_calculate_perimeter(self):
        """
        Given a length of 2.5 and width 1
        When `calculate_perimeter()` is called
            from the `Rectangle` class
        Then the correct value is returned
        """
        # Given
        length = 2.5
        width = 1.0

        # When
        perimeter = Rectangle.calculate_perimeter(
            length=length,
            width=width,
        )

        # Then
        assert perimeter == 2 * (length + width)

```

With the import in place we can run the test and see the test pass.

{% hint style="info" %}
It is worth writing another test and instantiating a `Rectangle` object and trying out the same static method again.
{% endhint %}

### Class methods

Class methods take the `cls` keyword as the implicit 1st argument. As you might have guessed this represents the class itself and as such it means class methods have access to the class but cannot alter state associated with the instance.

A common pattern is to use class methods as factory methods or alternative constructors. In other words, as ways to instantiate objects with particular state.

### Property methods

Going back our circle class, we might want to implement functionality on the `Circle` class to allow us to easily calculate the circumference of the circle.

We might want to implement this as a property of the class. Because the circumference is a feature of a circle.

So lets write a test:

```python
class TestCircle:
    ...
    def test_circumference_as_property(self):
        """
        Given a radius of 2.5
        When the `circumference` property is accessed
            from an instance of a `Circle`
        Then the correct value is returned
        """
        # Given
        radius = 2.5
        
        # When
        circle = Circle(radius=radius)
        
        # Then
        assert circle.circumference == 2 * math.pi * radius
```

See the difference? We interface with the `circumference` property in the same way we would as if it were an instance attribute, by calling it without the trailing parenthesis `()`.

Lets head over and implement the property:

```python
import math


class Circle:
    pi = math.pi

    def __init__(self, radius: float):
        self.radius = radius

    @property
    def circumference(self) -> float:
        return 2 * self.pi * self.radius
```

And its as simple as that, we define an instance method and wrap it with the built-in `@property` decorator.

***

## Inheritance

\<Coming soon>
