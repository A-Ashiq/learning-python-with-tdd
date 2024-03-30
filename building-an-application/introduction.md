# Introduction

Now that we've got a set of principles laid out for ourselves, we can move a step forward into our first project together.

We are going to start off with a set of requirements. We are going to build towards the solution and then we'll adapt to a new set of requirements. The idea is to get us to building under real-world scenarios, or at least emulate changes in direction that we can often find ourselves. This often happens in real-world scenarios when product owners come to us with new requirements.

Our product owner wants us to build an HTTP REST API which will allow users to calculate and determine how much tax users of our application owe.

We will build our application with modularity in mind and let our tests take the lead and drive our development.

To get started, we should create a new directory for ourselves. Lets call it `money-calcs`:

```
mkdir money-calcs
```

And now lets navigate into the new directory:

```
cd money-calcs
```

And finally lets set up the virtual environment:

```
python3 -m venv .venv
```

If you cast your mind back to the [environment setup chapter](../getting-started/environment-setup.md), you will know that the command we just sent will create a new virtual environment for us in a new folder called `.venv/` located in the current directory.

{% hint style="info" %}
Convention dictates that we name this folder `venv/` or `.venv/`. Either will do but the `.` character in file or folder names also indicates hidden configuration type files. Hence why we'll be using that moving forward.
{% endhint %}

***

You will be able to reference the source code for this chapter at the [Github repository](https://github.com/A-Ashiq/learning-python-with-tdd-building-an-application).
