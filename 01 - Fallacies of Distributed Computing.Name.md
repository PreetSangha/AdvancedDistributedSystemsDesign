# The 10 Fallacies of Distributed Systems

## 1. The Network is reliable

- there are many reasons why a network connection can fail
- your message could have been received but no reply returned
- your message may never have been received
- you could introduce some abstraction for reliability such as store/forward queuing etc
- if you introduce this then you have to consider that this must be asynchronous in nature, since you can't guarantee when the message will be delivered.You can only assme that it will be. 
- there you cannot rely on doing request/response synchronous behaviour in distributed systems on an unreliable network

##  Latency isn't a problem

- if a cpu cycle is 1s then memory is the order of minutes, local disk is the order of days, and call across the US (real term 40ms) is in the order of a nuber of years when scaled up. 
- don't cross the network unless you have to
- if you have to cross the network, then take all your data with you

## Bandwidth isn't a problem

- though bandwidth is increasing, data transpfer is slowing down due to congestion
- a common example is eager fetching in ORMs
- the more traffic there is, the more chance of collision, this implies latency increases
- comparitively speaking there is less bandwidth now than a decade ago due to traffic increases
- don't underestimate the bandwidth of truck full of hard drives
- move time critical data to seperate networks with different traffic priorities
- we can't eagerly fetch/lazy load everything
- decompose to having more than one domain where each is designed to handle a subset of the data, so that each can have local optimisations such as eager fetch etc.
- make the design not priority centric, rather make the design entity centric

## The network is secure

- nothing is 100% safe, even if isolated, as people will do dumb stuff, using portable media or other things
- devs often run with admin privileges, owning this will could compromise anything you connect to
- attack vectors are more and more complex, and people in the organisation are vectors too
- social media has made is easy to determine who is who and what they might have access to
- people could be easily compromise (using force, or just accidently)
- it's **not if** you're going to be hacked, it's **when** you're going to be hooked
- determine the risk and costs by performing a threat analysis
- talk to the business about having a plan B when it happens, and make sure to  include PR and Legal teams in this discussion
- newer hardware will alaways be able to crack much faster, and you can guaratee this.

## The topology will not change

- servers will go down, addersses might change and holding onto the address of the client could be a problem
- synchronous calls could tie up resources (also this could be used to attack your system)
- don't hard code ip address, domain names etc. 
- consider using more resilient protocols (e.g. multicast)
- self discovery mechanisms are fragile and not scale. consider if one service is restarted - will it rediscover
- will the system be able to retain performance characteristics if the topology changes. 
- performance test early

## The admin will know what to do

- until they get promoted/move on, so assume that no one knows everything about the system
- if you have to choose something, then make something configurable though this will inevitably lead to too much for someone to understand
- the more moving parts that you have, the more likely that they will get out of sync with each other
- high availability is not just an admin function, consider when upgrades happen, will the system remain resilient
- you need to deploy so often that it's not a concern on availibility as large changes will be more likely to cause issues
- changes need to be backwards compatible, so multiple versions remain in production at the same time
- enable the admins to bring down parts of the system down for maintenance without bring the whole system down. often queuing architectures work well here
- too much or too little logging could be a problem 
- focus on the needles not the haystacks, and develop better magnets to find the needles

## Transport cost is not a problem

- serialisation has a cpu cost, and more data results in higher cost
- often this cost is not usually in performance testing as it's 'invisible'
- in the cloud this is major design factor
- better logical decoupling results in more options as you can always choose what to distribute and what to not, later. queuing/asychrnonous architectures lead to this very well. The logical and the physical need not be the 1:1
- hardware and network infrastructure has both upfront **and ongoing** cost
- not all netowork boundaries are the same. geographically distributed systems means that transport costs increase with distance.
- different networks could have greatly different costs, for instance cellular, satellite are very expensive









