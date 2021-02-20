# Coupling

## Afferent vs Efferent coupling

- afferent : Incoming when things depend upon you
- efferent : Outgoing When you depend on things
- generally less volatile dependencies are better. if you depend on things that are general purpose and probably not likely to change then depending upon them is easily managed. However if they are expected to change like domain calculations then you want to try and avoid the coupling where you can.
- a library like logging is probably ok for higher degrees of afferent coupling as it will probably not change much. 
- though coupling by Interface/contract seems like a good idea to reduce the tightness of the coupling, it doesn't always solve the problem, which is that important behavioural characteristics aren't always able to be captured by the contact. 
  - This is true especially in statically typed languages. Ones that come to mind could be the time it takes to perform some contracted behaviour. 
  - This is temporal coupling
- if something has high efferent coupling, then you have to consider how much it's doing. Perhaps it's violating the Single Responsibility Principle?
- you should measure afferent vs. efferent coupling so you can assert some rules to help you keep coupling from increasing in size over time. Consider tools like ndepend etc.


## Platform, Spatial and Temporal coupling

- there could be hidden coupling, for instance shared data. Try to make the coupling visible so we can manage it. 
- if you must couple on data, then limit the size and scope of the data
- try reduce coupling, but not mechanically as this might be pointing to hidden coupling. An example could be a `DoIt()` method.
- there are three main times of coupling Platform, Spatial and Temporal

 ### Platform coupling
- also called interoperability
- for instance using protocols only available on some platforms
- contract/schema coupling is better than concrete coupling. This is tenet for SOA
- text/xml/json schemas are better then binary schemas
- standard protocols (http, smtp, udp etc...) are have much higher interoperability than a proprietary ones  
- smtp is inherently a queuing protocol with high resiliency 
- be aware of what existing standards are there e.g. soap, wsdl, rest


### Temporal coupling
- when the timing of dependant affects a dependee. 
- synchronicity is where this really shows it's face.
- if resources are held waiting for other things this temporal coupling 
- lower this by using asynchronicity


### Spatial coupling
- how tied to a location am I? 
- for instance this could be machine, address, etc.
- can you communication automatically continue if the dependant goes down?

## Solutions to platform, temporal and spatial coupling

- for data coupling, invert the request/response model by subscribing to changes
- distributed transactions do not scale, and since latency is not zero (see fallacies) you will need caches in your system. Therefore will always be a degree of data staleness in a distributed system. The only question is where the caches are. 
- there must a strong division of responsibility between publishers and subscribers.
  - subscribers must be able to make decisions based on somewhat stale data 
  - Subscribers cannot make accurate decisions if multiple (logical) publishers can publish a given event, consider that events can arrive at different times. Don't violate the `one publisher for any given event` rule.
  - you can have multiple physical publishers in a scaled out system, but not multiple logical publishers
- don't publish events that are commands. Frame events as things that have happened in the past. `SomethingHappened`
- enable decision making by, for instance, giving subscribers context. An example is `validity period` in the `PriceChanged` event.
  - have the business review the degree of staleness. This could lead to better understanding of where pub/sub boundaries should actually be drawn 
- application code `shouldn't need to know` where cooperating services are on the network
- consider the `service agent pattern`
- use load balancers (one logical multiple physical)
- strongly typed messages simplify logical routing. The opposite is Document/Content based routing and forces you look inside messages to determine to whom it should be routed.

## Summary

- loose coupling is more then just a slogan
- 5 dimensions which you need to balance
  - Afferent
  - efferent
  - spatial
  - temporal
  - platform