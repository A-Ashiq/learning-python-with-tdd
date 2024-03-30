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

## References

* [What is Open API | OpenAPI Initiative](https://www.openapis.org/what-is-openapi)
* [What is an ORM, how does it work, and how should I use one? | Stack overflow](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one)
* [Spring Boot | Java framework](https://spring.io/projects/spring-boot)
* [Express | Node.js framework](http://expressjs.com/)
* [Laravel | PHP framework](https://laravel.com/)
* [Django | Python framework](https://www.djangoproject.com/)
* [Flask | Python framework](https://flask.palletsprojects.com/en/3.0.x/)
* [FastAPI | Python framework](https://fastapi.tiangolo.com/)
