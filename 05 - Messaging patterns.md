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
  - the simplest thing is to return the `original request's message id` as the correlation in the response
- there could be `multiple responses` (think of status changes for instance)
- stop thinking of 1:1 with request/response 

## Publish/Subscribe

- `pub/sub` is really `subscribe/publish` as this is the order that things happen
- this maps to `request` = `subscribe` and `response` = `publish` 
- a subscriber needs to hold the subscriptions it has active in `durable storage` in case of restart
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
- in an in process event then the publisher can know when all the subscribers have received the message, but for in a distributed system this is not possible. - you can't assume, or should have a coupling (such as shared state)  between the publisher and the subscriber

## Dealing with out of order messages

- don't build services that rely on messages to be received in order if they don't preserve the message which received in the wrong logical order
- nservicebus allows you push the message back to the queue for later processing, but this could result in a loop if the required preceding message never arrives
- if you throw an exception then your implementation (e.g. nservicebus) should log more effectively such that a first fail (in a series of retries) is not logged as an error. Only the last one one, which sends the message to the poison message queue,  should be logged as an error. The other attempts should be warning, with the first one as simply info (Preet: I don't understand that)
- the error/poison queue is the actual place that tells you if the system is healthy, not the log  
- if the out of order messages are caused by bugs in other services, then the error queue filling up will give indications that something is wrong. This is very telling when releasing new versions of services  
- retrain devs to escalate errors, not just log them. A good way is stop checking for nulls etc., though this requires your exception handling infrastructure to be me messaging sensitive
- have a conversation within the organisation as to what exceptions mean so that preconceptions can be addressed there

## Visulisations

- difficult for people grok complex pub/sub implementations with lots of loose coupling. You can't just debug with step, step, step.
- an audit log can be mined to generate visualisations (e.g) [What does your particular system look like?](https://particular.net/blog/what-does-your-particular-system-look-like)
- think of how you can improve cognition with colours/shapes etc.
- consider this as a tool for helping everyone with understanding

## Summary

- orgs don't like change, so don't introduce too much change too quickly. (e.g.) could you use a SQL transport for messaging at the start?
- messages are simple dtos 
- message driven code is not harder
- be sure to identify service/message boundaries as this is the hard part











