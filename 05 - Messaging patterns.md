# Messaging Patterns

## Dealing with our of order messages

- it's relatively new technology for instance amazon dynamodb

### Sequential update message problem

- unlikely to happen in a synchronous pattern as the first update will send a response that the sender could understand that update was successful
- use version/sequence number at db so perhaps we could use this with messages
- the problem is that if a message is poisoned then how does the messaging system know?
- this cannot be solved by the queuing infrastructure as it will need to have deep understanding of the messages themselves. This is why it's a really an application/business concern 
- only a subset of the domain needs this (deletes for instance)

## Request/Response

### Return Address Pattern

- if a request wants a response then it should include a `return address` where the response should be sent
  - in a http header
  - the response is really just a `normal http request` sent to the return address
  - all the the normal http infrastructure will be as normal
- in addition to return address, there needs to be a `correlation` to match up this response to original request when the response is received
  -  the simplest thing is to return the `original request's message id` as the correlation in the response
- there could be `multiple responses` (think of status changes for instance)
- stop thinking of 1:1 with request/response 

## Publish/Subscribe

- `pub/sub` is really `subscribe/publish` as this is the order that things happen
- this maps to `request` = `subscribe` and `response` = `publish` 
- a subscriber needs to hold the subscriptions it has active in `durable storage` incase of restart
- the publisher should store return address of it's subscribers in durable storage in case of restart
- if a subscriber crashes then the `normal one way messaging semantics` will still operate and the responses messages for the subscribers will sit in queues waiting for them to wake up again
- there is `no natural correlation` as each published event stands on it's own
- pub/sub is a logical decoupling of temporal coupling so when you have scaled out subscribers, then the return addresses need to be load balancer addresses and events need to be routed correctly and not all machines
- if the event requires a cache update, use a distributed cache like redis, and still route the message to a single scaled out subscriber
- for front end notifications when there is back end eventing going on, think of a backplane service like SignalR to to the unicast or multicast to the front ends

## Publish/Subscribe Topics

- logical grouping of class of events (e.g. 'Products' topic)
- problems is how to have contract for these topics
- nservicebus uses message type as topic to make is code friendly
- topics could be organised in hierarchies
- message type can be modelled using interfaces as opposed to concrete classes
- as the business concerns grow, interfaces allow new aspects to added to existing functionality
- using interfaces needs serialisers that can handle interfaces (not all can)
- nservicebus creates special proxy classes to handle this
- allows spatial decoupling in that new functionality can be added with new interfaces/topics
- in an in process event then the publisher can kniw when all the subscribers have received the message, but for in a distributed system this is not possible. - you can't assume, or should have a coupling (such as shared state)  between the publisher and the subscriber

## Exercise - Dealing with out of order messages

- 
