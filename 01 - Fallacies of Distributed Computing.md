# Introduction

- an application is an executable running on one machine
- a system is many applications running across many machines
- a system has connectivity concerns


# The 10 Fallacies of Distributed Systems

## 1. The Network is reliable

- there are many reasons why a network connection can fail
- your message could have been received but no reply returned
- your message may never have been received
- you could introduce some abstraction for reliability such as store/forward queuing etc
- if you introduce this then you have to consider that this must be asynchronous in nature, since you can't guarantee when the message will be delivered.You can only assume that it will be. 
- there you cannot rely on doing request/response synchronous behaviour in distributed systems on an unreliable network

## 2. Latency isn't a problem

- if a cpu cycle is 1s then memory is the order of minutes, local disk is the order of days, and call across the US (real term 40ms) is in the order of a number of years when scaled up. 
- don't cross the network unless you have to
- if you have to cross the network, then take all your data with you

## 3. Bandwidth isn't a problem

- though bandwidth is increasing, data transfer is slowing down due to congestion
- a common example is eager fetching in ORMs
- the more traffic there is, the more chance of collision, this implies latency increases
- comparatively speaking there is less bandwidth now than a decade ago due to traffic increases
- don't underestimate the bandwidth of truck full of hard drives
- move time critical data to separate networks with different traffic priorities
- we can't eagerly fetch/lazy load everything
- decompose to having more than one domain where each is designed to handle a subset of the data, so that each can have local optimisations such as eager fetch etc.
- make the design not priority centric, rather make the design entity centric

## 4.The network is secure

- nothing is 100% safe, even if isolated, as people will do dumb stuff, using portable media or other things
- devs often run with admin privileges, owning this will could compromise anything you connect to
- attack vectors are more and more complex, and people in the organisation are vectors too
- social media has made is easy to determine who is who and what they might have access to
- people could be easily be compromise (using force, or just accidentally)
- it's **not if** you're going to be hacked, it's **when** you're going to be hooked
- determine the risk and costs by performing a threat analysis
- talk to the business about having a plan B when it happens, and make sure to  include PR and Legal teams in this discussion
- newer hardware will always be able to crack much faster, and you can guarantee this.

## 5. The topology will not change

- servers will go down, addresses might change and holding onto the address of the client could be a problem
- synchronous calls could tie up resources (also this could be used to attack your system)
- don't hard code ip address, domain names etc. 
- consider using more resilient protocols (e.g. multicast)
- self discovery mechanisms are fragile and not scale. consider if one service is restarted - will it rediscover
- will the system be able to retain performance characteristics if the topology changes. 
- performance test early

## 6. The admin will know what to do

- until they get promoted/move on, so assume that no one knows everything about the system
- if you have to choose something, then make something configurable though this will inevitably lead to too much for someone to understand
- the more moving parts that you have, the more likely that they will get out of sync with each other
- high availability is not just an admin function, consider when upgrades happen, will the system remain resilient
- you need to deploy so often that it's not a concern on availability as large changes will be more likely to cause issues
- changes need to be backwards compatible, so multiple versions remain in production at the same time
- enable the admins to bring down parts of the system down for maintenance without bring the whole system down. often queuing architectures work well here
- too much or too little logging could be a problem 
- focus on the needles not the haystacks, and develop better magnets to find the needles

## 7. Transport cost is not a problem

- serialisation has a cpu cost, and more data results in higher cost
- often this cost is not usually in performance testing as it's 'invisible'
- in the cloud this is major design factor
- better logical decoupling results in more options as you can always choose what to distribute and what to not, later. queuing/asynchronous architectures lead to this very well. The logical and the physical need not be the 1:1
- hardware and network infrastructure has both upfront **and ongoing** cost
- not all network boundaries are the same. geographically distributed systems means that transport costs increase with distance.
- different networks could have greatly different costs, for instance cellular, satellite are very expensive

## 8. The network is homogeneous

- in the past we had homogeneous networks, but the internet changes all that
- there are many platforms, languages, types of systems, types of networks, protocols, database types, stuff people have hacked over http for instance
- what about serialisation incompatibilities
- semantic interoperability is hard and needs to be budgeted for. Remember these problems occur in the real world, and usually not in dev/test. Consider the example of the patient admission where age is not available, and medicine dispensing where age is a requirement.

# The fallacies of Enterprise systems


## 9. The system is atomic

- maintenance is hard  in *big balls of mud* as a change could effect other parts
- people don't start out trying to build *big balls of mud*
- integration through the db creates coupling even more so if store xml/json/etc in there. schemas are hard, nested schemas are really hard
- if the system wasn't designed to scale out to many machines, do this could result in hurting performance
- the solution is loose coupling and modularisation. which domain sits on which side of which boundary
- design for scale out in advance or you'll be stuck with scale up

## 10. The system is finished

- maintenance costs over the lifetime are far far greater than development costs
- changes will always come in later and therefore the system is never "finished". this is when the real work starts
- if it's harder to fix bugs and do new features in maintenance so why do not design the system to make this cheaper? 
- we should design the things for less skilled people than us, so why do we not?
- we shouldn't consider a maintenance period in a project, we should consider the term product not project
- a product has a long life, perhaps for ever
- we should consider that software should be designed as though it's a product that lives for ever
- something that lives forever should have teams that may have changing people, but work with it for a long time

### 10.1 A better development process

- BA doesn't dictate, they help you understand the why
- architects/principals help with estimates 
- estimates should be of the form ***"Given a well formed teams of size S, that is not working on anything else, then I'm C% confident that the work will take between T1 & T2"***
- estimates are communication tools for communication with business stakeholders. these can be used to enable the business to make better decisions
- the decision is the requirement


## 11 Business logic can and should be centralised

- where to enforce the business rules, everywhere?
- we should be concerned as to what happens when the rules change and we forget a somewhere
- the more we create reusable components, the more we introduces dependencies where they are used, and the more likelihood that change will introduce problems due to the is tight coupling
- change invariable comes later rather than earlier, so the designing for flexibility could be a curse
- the solution 
  1. logic **will** be distributed 
  2. we can centralise the development view using the 4+1 views of software
  3. **tag** source code with feature implemented, so that we could find all the code that's gone into a feature, even if it's multiple files

  by tagging the files (e.g. issue connected to PR connected to files) we can get 'one place' the logic exists - rather than it being in 'one shared' component that is 'reused'. hence the logic is reused not the artefact. this is the centralisation of the development view. duplication is not evil as we have tools that mitigate it's issues. **DON'T BE DRY, BE WET!**

# Summary

- best practices haven't yet caught up with "best thinking"
- technology can't solve all the problems
- adding hardware doesn't necessarily help

*don't fight the laws of physics!* 

 










