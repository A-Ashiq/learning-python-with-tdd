# Additional functionality

Now that we've got the interfaces to our system set up and working we're in a pretty good position. We know that both the HTTP API and the CLI are both hooked up to our core domain logic.&#x20;

The great thing about this is that if we make improvements to or add additonal functionality or even fix bugs in that core domain logic, then our interface components reap the benefits.&#x20;

This is one of the big bonus points that we get for free when we design our systems in this way. With the correct abstractions in place, we only have to change things in 1 place to bring the entire system in line as to where we want it to be. And each component keeps its scope small so that we know exactly what it is responsible for doing.&#x20;
