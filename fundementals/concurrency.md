# Concurrency

You might have heard the terms concurrency and [parallelism](parallelism.md) used interchangeably. But they describe different methodologies when it comes to speeding up the execution of a given collection of workloads. In this section we will focus primarily on concurrency.

## When do we need concurrency?

In todays world we often, if not always find ourselves building distributed systems. Taking a very simple architecture with an application which needs to talk to a database:

<figure><img src="../.gitbook/assets/Screenshot 2024-03-12 at 19.45.21.png" alt=""><figcaption></figcaption></figure>

When the application needs to pull data from the database it will need to make a query. But once it asks for that data from the database, it would then have to wait until the database responded with the data it asked for. But that time spent waiting is effectively dead time as far as the appliation is concerned. Wouldn't it be great if we could tell the application to go and fulfill other tasks whilst waiting for the database to respond? And then resume whatever it needed to do in the first place when the database responds.&#x20;

This is the problem that concurrency tries to solve.

This is referred to as being an I/O bound problem. It is called I/O bound because the time it takes to complete the operation is dependant on some I/O subsystem. Where an I/O subsystem just means some other component. That component might be a database or an external API which would result in having to make requests over network. Or that component could even be writing some data to disk.

<figure><img src="../.gitbook/assets/Screenshot 2024-03-12 at 20.33.38.png" alt=""><figcaption></figcaption></figure>

In the diagram shown, the grey box which highlights the time spent waiting for the external component represents the I/O bound constraint.

This is important because it also tells us that throwing extra resources in the form of CPU and memory at the application won't buy us any significant increase in performance since the bottleneck lies with the external component.

So the main thing to note is that if the execution of the operation requires leaving the immediate boundary of the application, then we can categorise that portion of the operation as being I/O bound.

***

## Threading







***

## Write the test for multiple threads

So lets write the test to prove this concept to ourselves:

{% code lineNumbers="true" %}
```python
import time

from src.concurrency import (
    io_bound_operation,
    run_with_multiple_threads,
    SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION,
)


class TestConcurrency:
    def test_multi_threading_can_run_multiple_io_bound_operations(self):
        """
        Given a callable which emulates a long I/O operation
        When `run_with_multiple_threads()` is called
            to run the callable with 10 threads
        Then the threads are executed concurrently
        And the total elapsed time is slightly greater
            than the time taken for 1 thread to complete
        """
        # Given
        start_time = time.time()
        number_of_threads = 10

        # When
        run_with_multiple_threads(
            func=io_bound_operation, number_of_threads=number_of_threads
        )

        # Then
        end_time = time.time()
        elapsed_time: float = round(end_time - start_time, 2)

        time_taken_for_sequential_operations: int = (
            number_of_threads * SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION
        )
        assert (
            SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION
            <= elapsed_time
            < time_taken_for_sequential_operations
        )
```
{% endcode %}

In this test, we are going to wrap the main `When` block with timers so that we can check the threads get executed conurrrently instead of sequentially.

In lines 25-27, we are referencing a `run_with_multiple_threads()` function which will act as our interface to the _threading_ functionality.

And finally we'll do some rough assertions on lines 36-40 that the total elapsed time will be at least equal to the time we're gonna use to emulate the execution of 1 operation, but less than the time which would have been taken if the operations were executed sequentially i.e. 1 after the other.

***

## Implement the functionality

Now it is time to implement the functionality to verify our hypothesis:

{% code lineNumbers="true" %}
```python
import logging
from multiprocessing.dummy import Pool as ThreadPool
import time
from typing import Callable

logger = logging.getLogger(__name__)


SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION = 5


def io_bound_operation(index: int) -> None:
    print(f"\nStarting io bound operation for thread number {index}")
    time.sleep(SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION)
    print(f"\nFinished io bound operation for thread number {index} in {SECONDS_TAKEN_FOR_SINGLE_IO_OPERATION}s")


def run_with_multiple_threads(func: Callable, number_of_threads: int) -> None:
    print(f"Creating pool of {number_of_threads} threads")
    indexes = [i for i in range(number_of_threads)]
    with ThreadPool(processes=number_of_threads) as pool:
        pool.map(func, indexes)

```
{% endcode %}

There is a lot to unpack here, so buckle in!

We've defined a function called `io_bound_operation()` on line 12. This function is a bit of a sham but the whole point of it is to emulate a long running IO bound operation for our purposes here. In the real world this might be a long running database query or a call being made to some external 3rd party API. Either way it is out of the control of the current application. To simulate this behaviour we've made the function wait for 5 seconds before completing. We've extracted that 5 seconds into a constant on line 9 so that we can use it in our tests for expected time calculations.

On line 18, we define the `run_with_multiple_threads()` function which takes a callable and an integer, whereby the integer represents the number of threads to spin up.

On line 20, we create a list of numbers counting from 0 up to the aforementioned number of threads. So if that number is 10, then this list will look like `[0, 1, 2, ..., 10]`.

##

##

##

***

## References

* [Scalability the basics](https://www.afaanashiq.com/architecture/scalability-the-basics/)
