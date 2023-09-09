# Environment setup

## Python installation

To get started we will need to ensure we have Python installed on our machine. You can head over to the [official Python downloads page](https://www.python.org/downloads/) to grab the latest version of Python.

## Virtual environments

We will regularly want to use 3rd party libraries as frameworks to build on top of or for extra pieces of useful functionality. But this comes with a question of how we manage those dependencies. And managing project dependencies can be rough at the best at times.&#x20;

And I won't lie to you, in my experience Python does not do a a great job of this problem particularly well, especially when compared to other languages like Go.

That being said, I'd strongly suggest that we make use of virtual environments from here on out. A virtual environment is an **isolated system** which will allow us to develop as many project as we like at the same time. Because each environment is isolated we can use a **specific version of Python** within that environment as well as an **isolated set of 3rd party libraries**.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-09-09 at 19.32.29.png" alt="" width="375"><figcaption></figcaption></figure>

With a virtual environment we can use a different version of say library X and not run into any troubles of cross-polluting with another project on our machine. With this, we are free to run a different version of libraries and let them be specific to each project.

To get started, in your terminal create the directory of your choice in which we will keep our project.

```
mkdir python_starter_tutorial
```

Now that we have created a directory, we should navigate into it:

```
cd python_starter_tutorial
```

And then we can run the following command to create our first virtual environment:

```
python -m venv venv
```

Here we are saying to our system-level `python` create a `venv` (a virtual environment) at the current directory position in a subfolder called `venv/`.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-09-09 at 21.14.56.png" alt="" width="375"><figcaption></figcaption></figure>

The 1st argument in this command was an instruction to create a virtual environment. The 2nd argument is the name of the folder we want our virtual environment to go in.

Also note that it is a common idiom to expect the virtual environment at the root level of the project within a folder called `venv/` or `.venv/`.

And our final step here is to activate our virtual environment by calling:

```
source venv/bin/activate
```

If we are using an IDE like Pycharm then the integrated terminal will activate the virtual environment for us.

### Project dependencies

We can take a snapshot of the current state of our project dependencies with the following command:

```
pip freeze > requirements.txt
```

Here we are saying to `pip` freeze our project dependencies and write them out to the file named `requirements.txt`. If the `requirements.txt` file does not already exist then it will be created during this process.

And that gives us a new virtual environment to begin developing within.

With all of that in place, we will take a short walkthrough writing our first simple function with TDD.

## References

* [Virtual environments and packages - Python official docs](https://docs.python.org/3/tutorial/venv.html)
