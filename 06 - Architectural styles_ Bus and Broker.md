# Architectural Styles - Bus and Broker

## Architectural Styles

- an architectural style is `not the architecture` rather they are the dos and don'ts that constrain your architecture
- there are many styles e.g.
  - MVC
  - DDD
  - SOA
  - REST
  - Layering
  - Pipes
  - Pub/Sub
  - bus
  - broker
  etc.
- they are styles, even if they sometimes have architecture in the same (e.g. SOA)
- you can have many styles in your architecture, it's not restricted. e.g. not everything in the system needs to be pub/sub.
- SOA is most likely to be founded upon messaging, and there other one such as buses and brokers that are two
- `buses` and `brokers` are an attempt at `reducing spatial complexity`

## Broker

- aka `hub & spoke`, or `mediator`
- came to solve the problem of `Enterprise Application Integration`
- simple way to integrate many things, usually that already exist
- new code just attaches to the broker
- handles routing, fail over
- single point of failure, so needs to have high availability 
- an example of `conway's law`, as IT functions grew out as central functions, the broker came to handle software for many business functions
- provides central management for many integrations
- allows intelligent routing, transformation and orchestration  
- doesn't compel changes to surrounding apps
- embodies the 11th fallacy - business should be centralised
- large amount of functionality was sitting in the broker as opposed to apps which prevents apps from gaining autonomy due to xml usage in brokers (dif/merge is hard) so hard to scale teams
- eventually the disadvantages of brokers overcame the advantages. 
- SOA was the perceived solution

## Bus

- allows event sources to connect to event sinks - basically pub/sub
- modeled on the concept of the the hardware bus
- allows you build systems with optional components
- need to think about ownership of messages
- need to have form of uniquely addressing things
- you have logical address that map to physical addresses
- there really isn't a physical bus everywhere rather it's a logical concept that connects things logically
- hardware bus controllers map to things like intelligent logical routing, e.g. load balancers routing between many machines
- works opposite to brokers in that the expectation bus doesn't change, rather the apps do. So logic is not in the bus, rather in the apps
- you should think of the network to be dumb compared to the apps
- ESB are brokers packaged as buses