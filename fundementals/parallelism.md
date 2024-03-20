# Parallelism

Parallelism is more likely to be what we might think of when we talk about concurrent programming. Whereas [concurrency](concurrency.md) does not actually lead to N number of tasks being executed at the same time, parallelism does mean that in the truest form.

So you're probably thinking, well if concurrency means our program only executes instructions from 1 task at any 1 given time then why would we ever use it? Why not just always use parallelism?

***

## When do we need parallelism?

By nature, parallelism incurs additional overhead. It takes time to create multiple sub-processes, this overhead is particularly stark when compared to the amount of time taken to spin up a pool of threads, which is significantly less. So depending on the operations being ran, parallelism could end up putting us in a worse position. In fact, depending on the task we might even have been better off running them sequentially!

The other thing with parallelism is we are consuming more hardware resources. Each process is assigned to a CPU core along with its own memory allocation. This is needed if the task entails some kind of intense calculation which is happening within the process. This is known as a _CPU bound or memory bound task._ But if the work is happening outside of the process like a request being made to an external API or a database query, then the additional resources don't do anything for us. In fact the additonal overhead associated with creating the multiple processes becomes a net negative factor for us.

***

## An example CPU bound problem

In this case, we're gonna work backwards. We will define the problem first by writing the source code:

{% code lineNumbers="true" %}
```python
import logging
import time
from multiprocessing.pool import Pool
from random import randrange

from typing import Callable

logger = logging.getLogger(__name__)


def run_with_multiple_processes(func: Callable, number_of_processes: int) -> None:
    print(f"Creating pool of {number_of_processes} processes")
    indexes = [i for i in range(number_of_processes)]
    with Pool(processes=number_of_processes) as pool:
        pool.map(func, indexes)


def cpu_bound_operation(index: int) -> None:
    print(f"Starting CPU bound operation for process number {index}")
    number: int = randrange(10_000_000, 100_000_000)
    count(number=number)
    print(f"Finished CPU bound operation for process number {index}")


def count(number: int) -> None:
    start_time = time.time()

    result = 0

    while result < number:
        result += 1

    end_time = time.time()
    elapsed_time: float = round(end_time - start_time, 2)

    print(f"Finished counting to {number} in {elapsed_time}s")
```
{% endcode %}

On lines 25-36 we define a `count()` function which counts up to a given `number`, starting from 0. &#x20;

And then on lines 18-22 we define another function called `cpu_bound_operation()`. On line 20, we select a random number between 10 million and 100 million.

{% hint style="info" %}
The `_` character used in large numbers is for readability purposes only. E.g. `10_000_000` is the same as 10000000.
{% endhint %}

On line 21 we pass the number we just generated into a call to the `count()` function. All of this means the `cpu_bound_operation()` function counts from 0 to a number between 10 milliion and 100 million.

We consider this to be a CPU bound problem because the operation is an intensive calculation being performed by the process which in turn is limited by the speed of the CPU.

***

## IO bound tasks with multi processing?

To begin, lets write a test. It is going to be very similar to a test we wrote in the previous section. This time we want to spin up 10 processes to point against our IO bound operation.

Lets create the files and get started:

```
touch src/multi_processing.py tests/test_multi_processing.py
```

Note that we've named this file `multi_processing.py` and not `multiprocessing` because that would clash with the built-in library `multiprocessing` that we'll be using.\
These files are not particularly well named, but that's okay for now. The sole purpose of the code we've written together so far is to help us learn and grasp new concepts.

Now lets head over to our new test file:

{% code lineNumbers="true" %}
```python
import time

from src.concurrency import (
    io_bound_operation,
    SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION,
)
from src.multi_processing import run_with_multiple_processes


class TestMultiprocessing:
    def test_multiprocessing_can_run_multiple_io_bound_operations(self):
        """
        Given a callable which emulates a long I/O operation
        When `run_with_multiple_processes()` is called
            to run the callable with 10 processes
        Then the total elapsed time is slightly greater
            than the time taken for 1 process to complete
        """
        # Given
        start_time = time.time()
        number_of_processes = 10

        # When
        run_with_multiple_processes(
            func=io_bound_operation, number_of_processes=number_of_processes
        )

        # Then
        end_time = time.time()
        elapsed_time: float = round(end_time - start_time, 2)

        time_taken_for_sequential_operations: int = (
            number_of_processes * SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION
        )
        assert (
            SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION
            <= elapsed_time
            < time_taken_for_sequential_operations
        )
```
{% endcode %}

***

## Implement the functionality

And once again, we should go and implement our new `run_with_multiple_processes()` function in the source file:

{% code lineNumbers="true" %}
```python
from multiprocessing.pool import Pool

from typing import Callable


def run_with_multiple_processes(func: Callable, number_of_processes: int) -> None:
    print(f"Creating pool of {number_of_processes} processes")
    indexes = [i for i in range(number_of_processes)]
    with Pool(processes=number_of_processes) as pool:
        pool.map(func, indexes)
```
{% endcode %}

This should look pretty familiar if you followed the [concurrency section](concurrency.md#implement-the-functionality).

In fact the main difference is on the very first line. We import the `Pool` class from the `multiprocessing` library. This `Pool` class provides the same API as the `ThreadPool` class provided for us to use in the previous section.

The `Pool` object on line 9 provides a pool of worker processes for us to use. The `Pool` object also manages the processes on our behalf. Again this is done in the same way as we saw with the `ThreadPool`. The `Pool` will open and close the processes as required without any additional work from our side of things.

***

## Witnessing the overhead firsthand

So if we run this test as is, it will pass:

```python
============================= test session starts ==============================
collecting ... collected 1 item

test_multi_processing.py::TestMultiprocessing::test_multiprocessing_can_run_multiple_io_bound_operations 

============================== 1 passed in 5.26s ===============================
PASSED [100%]Creating pool of 10 processes
Starting io bound operation for thread number 0
Starting io bound operation for thread number 2
Starting io bound operation for thread number 1
Starting io bound operation for thread number 3
Starting io bound operation for thread number 5
Starting io bound operation for thread number 4
Starting io bound operation for thread number 6
Starting io bound operation for thread number 7
Starting io bound operation for thread number 8
Starting io bound operation for thread number 9
Finished io bound operation for thread number 0 in 5s
Finished io bound operation for thread number 1 in 5s
Finished io bound operation for thread number 2 in 5s
Finished io bound operation for thread number 3 in 5s
Finished io bound operation for thread number 6 in 5s
Finished io bound operation for thread number 4 in 5s
Finished io bound operation for thread number 5 in 5s
Finished io bound operation for thread number 7 in 5s
Finished io bound operation for thread number 8 in 5s
Finished io bound operation for thread number 9 in 5s

Process finished with exit code 0
```

Everything looks as we expect right?

Now take a look at the total amount of time the test took to run. It took 5.26s when we tackled the problem with processes compared to 5.03s with threads. That is a 203ms increase. You might not think that's a big deal, but in computing that is an age! Now imagine you have any kind of scale around this problem and we've introduced serious overhead for no net positive impact, especially when we know threading could have resolved our problem here in less time and with less resources.

***

## References

* [Multiprocessing | Python official documentation](https://docs.python.org/3/library/multiprocessing.html)
