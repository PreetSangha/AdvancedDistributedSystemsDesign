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
- if there is only functionality and no data then that's a function not a service
- if there is only data, then it's a database not a service
- even if you put a rest/wsdl layer over it, it's still not a service
- if you think of a service (in the development view) to be a source code repo
  - then the contract can be published as an external package (nuget, npm, etc.)

  ## a service example and Q&A

- business functionality lives within services
- enterprise functionality is an emergent property that aries from the interaction of these services
  - just like business functionality lives within services, so does data. This means that there shouldn't be a single place for all data. Rather it lives with the services
- services publish and sink events
- many services could be deployed to the same box
- many services could be deployed in the same app
- many services could be deployed in the same web url
- this leads to the idea that a page is just the emergent property of many services exposed in independant widgets that are composed into a page
- services are not responsible for all the data in the apps
- think about the user getting their data from multiple services
- try not to share data, but if you have to, then you must rely on eventual consistency
- to prevent performance problems with issuing multiple requests to multiple services, you can use more intelligent infrastructure that could batch service requests,  and then unbatch them

  
