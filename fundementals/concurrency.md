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

## References

* [Scalability the basics](https://www.afaanashiq.com/architecture/scalability-the-basics/)
