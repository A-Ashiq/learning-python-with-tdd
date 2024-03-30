# Framework

So we've got our directory setup and the virtual environment is ready for us. You would be forgiven for thinking it is time for us to start writing code.

***

## What is a framework?

Our product owner wants us to build an HTTP REST API. So how do we get started? We'd have to build an HTTP server for our API. But that is going to be a lot of work before we get anywhere near writing the business logic to solve the problem statement put forward to us by the product owner. Wouldn't it be great if there was a kind of template that we could use that gave us the HTTP server for free and we could then build the things that specifically matter to us on top of that?

This is where frameworks can be a very worthwhile investment. They take away a lot of work required up front from us and allow us to spend more time building the things that matter specifically to us.&#x20;

That being said, they do come with their own learning curve. And depending on the type of framework we've chosen we might be provided with a lot more functionality than we actually need, leading to bloat throughout our codebase.

These frameworks can give us easy ways to build endpoints, provide [Open API](https://www.openapis.org/what-is-openapi) documentation and even [Object Relational Mappings](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one) (ORMs). If you don't know what these mean, don't worry we'll be covering them in this chapter.&#x20;

There are commonly used frameworks in each language. For example, Java has [Sprint Boot](https://spring.io/projects/spring-boot), Node.js has [Express](http://expressjs.com/) and PHP has [Laravel](https://laravel.com/).

***

## Python frameworks

In the Python spaces there are many different web frameworks available to us, each of which have things that they are better at. There are 3 main frameworks that are most commonly used today in the Python world:

* [Django](https://www.djangoproject.com/) - Often described as being _batteries included._ Django gives us a very powerful ORM, complete with a robust database migration system and a lot of the bells and whistles needed to build a web app.
* [Flask](https://flask.palletsprojects.com/en/3.0.x/) - Considered the polar opposite of Django by some. Flask is quite minimalist by design. It doesn't give you a lot out of the box, but it does allow you to pick and choose the bits you need.&#x20;
* [FastAPI](https://fastapi.tiangolo.com/) - Probably more comparable to Flask than Django. FastAPI also makes use of the modern Python type system to provide enhanced OpenAPI documentation and performs well as a Python framework. It also emphasises developer productivity and focuses on some of the concepts we've already covered like [dependency injection](../fundementals/dependency-injection.md). &#x20;

***

## Choosing our framework

Your framework choice is a very important decision to make. It has wide ranging impact on how you build your application. It can be pretty difficult to migrate from 1 framework to another and more often than not, we are tied into the selected framework. So with this in mind it is important for us to be confident that we can commit to a given framework.

As welll as the technical features of each framework we often need to consider how much community support there is around a given framework and how well known it is. If it is a framework that no one has ever heard of, then there will be a slightly steeper learning curve associated with it, which makes hiring engineers more of a challenge. There is however a valid argument to be made that good engineers should be able to pick up frameworks with relative ease. After all, frameworks just solve similar problems in slightly different ways.&#x20;

For the purposes of our application, we are going to add the various pieces as and when we need them. As I've aluded to earlier, we could pick any framework and eventually meet our goals, but we're going to use **FastAPI**.&#x20;

***

## Install the framework

So to get started, we will need to install our framework. Most frameworks should provide documentation on how to get up and running. In this case we need to drop into a terminal instance and use the following command:

```sh
pip install fastapi
```

{% hint style="info" %}
This assumes you are in our new project directory and your virtual environment is activated. To do the latter, make sure you run `source .venv/bin/activate` **before** pip installing anything.&#x20;
{% endhint %}

Once this is complete, we will then need to install an ASGI server:

```sh
pip install "uvicorn[standard]"
```

And that's about it for installing the framework. We have the libraries we need installed within our virtual environment.&#x20;

***

## Updating project dependencies

But before we move on we need to declare our project dependencies and commit them to version control.

If you cast your mind back to the section on [project dependencies](../getting-started/environment-setup.md) you'll realise we have done this before.

So once again drop into your terminal with the following command:

```sh
pip freeze > requirements.txt
```

This will take all the dependencies which are currently installed in our virtual environment and create a list of dependencies. This will include the name of each library and the version which has been installed.

Now that we have our project dependencies set up, we will need to add them to our version control.&#x20;

Lets drop into our terminal and add them to our Git staging area:

```sh
git add requirements.txt
```

And now lets record the changes to our local Git repository:

```sh
git commit -m "Add FastAPI and Uvicorn to project dependencies"
```

The `-m` here stands for _message_. This is the message that we want to be recorded along with our code change. This message should summarise what our code change represents to the code repository. In our case we are simply adding the `FastAPI` and `Uvicorn` dependencies

And finally we can _push_ this commit from our local repository to the remote repository:

```
git push 
```

And with that we have pushed our first code change to our repository, which includes a list of the 3rd party libraries required for our selected framework, **FastAPI**.

You can find the code for this chapter at the [Github repo](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application-part-one).

***

## References

* [What is Open API | OpenAPI Initiative](https://www.openapis.org/what-is-openapi)
* [What is an ORM, how does it work, and how should I use one? | Stack overflow](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one)
* [Spring Boot | Java framework](https://spring.io/projects/spring-boot)
* [Express | Node.js framework](http://expressjs.com/)
* [Laravel | PHP framework](https://laravel.com/)
* [Django | Python framework](https://www.djangoproject.com/)
* [Flask | Python framework](https://flask.palletsprojects.com/en/3.0.x/)
* [FastAPI | Python framework](https://fastapi.tiangolo.com/)
