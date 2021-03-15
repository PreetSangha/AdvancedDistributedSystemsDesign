# Introduction to Service Oriented Architecture

## SOA Tenets

1. Services are autonomous
2. Services have explicit boundaries
3. Services share contract and schema and not class or type
4. Service interaction is controlled through policy

- in other words services have something more meaningful than just data - encapsulation
- though SOA and Web Services were separate technologies, they became conflated

## What is a Service

- a technical authority for a specific business capability
  - all data and business rules reside within the service 
  - ergo everything is in a service (there is nothing left over)
- if there is only functionality and no data then that's a `library` not a service
- if there is only data, then it's a `database` not a service
- if you put a rest/wsdl layer over it, it's still not a service
- if you think of a service (in the development view) to be a source code repo
  - then the contract can be published as an external package (nuget, npm, etc.)

## A service example and Q&A

- business functionality lives within services
- enterprise functionality is an emergent property that aries from the interaction of services
- just like business functionality lives within services, so does data. This means that there shouldn't be a single place for all data. Rather it lives with the services
- services publish and sink events
- many services could be deployed to the same box
- many services could be deployed in the same app
- many services could be deployed in the same web url
- this leads to the idea that a page is just the emergent property of many services exposed in independant widgets that are composed
- services are not responsible for other services' data in the app
- think about the user getting their data from multiple services
- try not to share data, but if you have to, then you must rely on eventual consistency
- though eventual consistency is an indicator of a good service boundary, it doesn't imply it
- don't share too much about the data, even in events. Allow services to talk about data without having to know everything about it (e.g. `entity id`)
- who is accountable for the correctness of the data - that's the service. It shouldn't just share that around generally.
  - if you end up with multiple services where the same data is essentially 'owned' then you need to rethink the architecture.
- to prevent performance problems with issuing multiple requests to multiple services, you can use more intelligent infrastructure that could batch service requests, and then unbatch them.  
- batching/unbatching is a good pattern for mobile devices where you need to reduce the number of round trips

## Modelling workflows, boundaries and business capabilities
- if a service is supposed to 'own' it's onw data the it follows that a component of the service resides on all of the machines where that service physically needs to be present
- this leads to the concept of the business capability vertical, as opposed to different boundaries than, say, mobile, web portal and back end web services. What you need to think about is the mobile part of the X service and the web part of the X service and the backend parts of the X service as a logical service
- the service span the systems as opposed to the other way round
- for example  when entering shipping information into an shopping site then you should think of it as you typing the address into the shipping service's ui, as opposed 'the system and which collects the information and passes it to the shipping services'  
- a naive design would consider an `order` record that has say a `shipping id` and say a `billing id`. The problem is that this couples the order to that shipping or billing concept. What if the we wanted a different type of shipping (say electronic as opposed to postal), or a different payment method (say COD as opposed to CC). You then have to consider how to `munge` your data to cover this new capability
- rather than having the order as the parent, you could just consider something like the order (from the order service), and passing it's id to the shipping service or billing service. 
  - changes are easier as you can pass the `order id` to the shipping service which can handle change in isolation, and similarly for the billing service getting passed the order id. 
  - in essence the order doesn't care about the other services. 
  - whereas the shipping service does want to know about the order
- so when the order id created? In the above scenario it becomes a concern for the `order service `and` when it logically thinks that an order is to be made` (for instance: `proceed to checkout`) it could create it. 
  - the other services could then get it when the order service makes it available as a shared datum, for instance in session state of the UI
- overall this compositional approach leads to less data being shared and higher autonomy for services (**this is the killer feature of the SOA to reduce coupling**)

## Uncomposition and Branding
- certain concerns aren't often the business capability's concern. For instance layout, colours etc. 
- use view models to move decouple these concerns from the data
- initially we wanted each service to have it's own UI code (a widget). However now we need to consider the case where there is a shared UI concern (e.g. branding service) that wants to influence the UI
- use dictionaries of data (view models) and bind to these. Then each service can add their own data to the shared master view model. 
- there is still services' code on the client, but it's not overly concerned with presentation, rather it could be raw/basic html without styling and the view and branding service can work together to sort out the presentation 

## IT Ops Service
- responsible for keeping info flowing and secure
- focused on the deployment and physical views, such as web servers, DBs etc and owns shared concerns such as connection strings, queue names etc.
- users' permissions
- authentications
- shared and infrastructure considerations
- we try to choose design choices where conventions help the stuff to just work, and things don't need to couple with the information on IT Ops
- business rules are not IT Ops (user can do function X, however whether user can access a service is IT Ops)
- think of this as the green lego board service
- rarely subs to other services except perhaps for instance for automated provisioning
- often IT Ops integrates with data push/pull to third parties and a this emerges as a hub/spoke pattern. 
- this doesn't necessarily imply remote calls as their are patterns where the the IT Ops service could be in process (e.g. logging type stuff)
  - **remember that a service could be running in many apps and thus in the same process as the `third party adapter`**
  - this could be as simple as nuget packages of interfaces together with bootstrappers that inject components into the startup/DI so that dependencies are correctly set up
  - consider Dahan's example of the billing service needing to talking to a Biller that is actually an actual integration adapter (e.g. biztalk) to the system that does billing
  - the billing service gets an injection dependency of ItOps.Billing and all the billing service needs to do it call the it's interface to enact a third party integration
  - however the concerns of the third party adapter are not the concerns of the billing service and all the IT Ops (adapter) has to do is call to all it's concerns (providers) to actually get the data it needs. This could e from many services
  - To decouple the IT Ops service from the Order and Billing services all it needs to do is specify interfaces that  the billing and orders services implement in some way (in the same way as the first decoupled integration)
  - good use of DI will help tremendously in make sure that these things could decoupled and much more SOA oriented 
