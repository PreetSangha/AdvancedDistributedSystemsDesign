# Introduction to Messaging

# Why messaging?

- provides `temporal decoupling` between services
- interoperability through AMQP the common queuing standard

# One way, fire and forget

- basic building block is `email`
- everything is is built on the this `one way fire and forget` messaging
- single addressee 
- every message has an id (though not all technologies enforce this)
- often the id is used to ensure that store and forward works and increases resiliency
- outgoing queues on sender are used so sender knows that message is in transit.
- this call is usually synchronous (ie. sender wait) but could fail due to queues being full
- on receiver, id can be used to solve duplicate receipts 
- a message could be duplicated due to network problems
- we don't always care about messages having durability (application concern)
- try and use the functionality built into your queuing technology as opposed to building your own
- messaging (asynchronicity) scales over RPC (synchronicity) as the latter degrades under load (resource locks on sender/receiver e.g. garbage collection, threads, db locks etc are held for much longer)
- consider the problem of gen2 memory not being cleaned up often so you end up with memory pressure. The GC, when cleaning gen2, will suspends threads while collecting 
- memory pressure correspondingly increases contention in other resources. And will bottleneck across the whole system eventually with timeouts becoming more common, ergo performance degrades
- a good web server will recycle processes when it feels processes are in trouble, and this increases pressure further
- a good queuing system will generally be more available than your application services and thus should be ready before you in the case of server restarts

## Service interfaces and strongly typed messages

- there is a difference between service interfaces and messaging interfaces
- service layers grow large over time
- get difficult as more and more people interact with the code and for them to collaborate
- monolithic apis have problems with scaling in terms of prioritisation
- use strongly types messages to get the scalability
- represent the service methods as messages instead with the DTO being the message
- allows us to have independant logical routing for different message types
- this breaks up monolithic service surfaces into independantly managed/versioned smaller services and decreases the `platform coupling` of the apis 
- nservicebus : [Full Duplex sample](https://docs.particular.net/samples/fullduplex/) from particular.net
   - review [ConfigureAwait(false)](https://devblogs.microsoft.com/dotnet/configureawait-faq/) to understand the code better
   - remember to share message schema, not classes in the real world

## Fault tolerance

- infrastructure will fail (see fallacy 1) and messaging handles that
- write messages to the incoming queue when a message arrives, but before the client is informed. This reduces the scope of the http message to just this. 
- later in the client you can use, for instance, distributed transaction semantics to pull the message and perform the action (e.g. write to db), and if this fails then the message removal is also rolled back
- queuing systems invariably have things like retry built in, so helping us extend the time we can handle the message
- we should organise the system to retry intelligently
- you can use `poison letter` queues to store messages for later troubleshooting in case all the retries fail.
  - should alert something
  - should be in monitoring
  - nservicebus calls this the error queue, and has a ui to iew this and the issues that caused it
