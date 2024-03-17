# Parallelism

Parallelism is more likely to be what we might think of when we talk about concurrent programming. Whereas [concurrency](concurrency.md) does not actually lead to N number of tasks being executed at the same time, parallelism does mean that in the truest form.

So you're probably thinking, well if concurrency means our program only executes instructions from 1 task at any 1 given time then why would we ever use it? Why not just always use parallelism?

***

## When do we need parallelism?

By nature, parallelism incurs additional overhead. It takes time to create multiple sub-processes, this overhead is particularly stark when compared to the amount of time taken to spin up a pool of threads, which is significantly less. So depending on the operations being ran, parallelism could end up putting us in a worse position. In fact, depending on the task we might even have been better off running them sequentially!

The other thing with parallelism is we are consuming more hardware resources. Each process is assigned to a CPU core along with its own memory allocation. This is needed if the task entails some kind of intense calculation which is happening within the process. This is known as a _CPU bound or memory bound task._ But if the work is happening outside of the process like a request being made to an external API or a database query, then the additional resources don't do anything for us. In fact the additonal overhead associated with creating the multiple processes becomes a net negative factor for us.

***
