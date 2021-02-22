# What are the push backs you'll get

## People are important

- people get often take things as a personal attack
- avoid referring to higher authorities unless there is are experts that you both agree upon
- sometimes even the authority can be contradictory if you don't understand the nuances deeply
- organisational inertia is often very strong so small benefits might not be easily overcome

## When you have peak loads like say a sale

- the public cloud was designed to scale h/w like this, why would I need to change my architecture when aws/azure/google will elastically scale the resources for me?
- so you only have to pay for what you use and they scale back an forth as needed

## Cloud providers have a delay while they scale

- the delays are usually in the order of minutes, while the peaks are usually over periods of hours
- say we send a special sale alert to our customers, there's usually a slow build up load as opposed to immediately going to peak (strong step function), and the cloud will scale in minutes
- it's in the cloud providers' interests to scale faster etc, and thus is this really a problem that we want to solve or should we allow them to do this as they do it so well

## We could process things more asynchronously when at load

- we've done async before - batch jobs are a perfect example of this
- the batch jobs are well understood, durable and reliable

## Cloud based solutions are more cost effective when using messaging

- this requires the business processing is asynchronous but this needs to signed of by the business
- this is a business processing change, and we don't need messaging to leverage the cost savings
- there is a certain degree log acceptable to the business, and at load you may get time shifts of load with asynchronous processes, but the later load still has to be handled. The cloud already scales for this

## Messaging allows the loads to dispersed through the network as opposed to places like the db

- everything is backed up by resources like storage and so unless there is lack of contention at that level, the load is still going be there
- we can only avoid access to the db if we transport more data (context), and often the processes need more data and this could be joins etc. You're just putting the higher tax on the queuing system as opposed to in the db
- you get higher logical coupling as senders have to have more data than they need, and will have to change as the requirements for the receiver change. If you have to have some logical coupling then why not just use the database as keep it lower

## This new technology solves all the problems

- you said this before with soap, wcf etc. Historically it has never lived up it promises

## Server/db contention can be relieved

- the public cloud has dbs that scale like that so we only have to pay for them, and while we are making more money we can afford that
- we don't want postpone processes (like in messaging) we can just ask for x new servers. We can do this for all kinds of servers

## We can independantly bring down, version and upgrade the backend  

- there is a limit to how much delay we'll accept
- we ideally want no down time and we can do blue/green with just load balancers and juggling servers. It's much harder to make changes that are backwardly compatible  while the load balancer changes are minor in comparison. 

## We won't end up with a mess of logical complexity (services calling services)
 
- people who've created the mess in the first place are very likely to create a mess with messaging. The technology isn't a panacea.

## Using an event driven architecture has decoupling benefits. This has had benefits in decoupling code (physically)  and http doesn't have this

- we can already do this with things like wcf callbacks and we can create pub/sub easily with this, why need another new technology like nservicebus
- webhooks are an established technology


# there is no one winning reason 
- you need to make the argument on many fronts
- if you can rebut a lot common arguments then that inspires a higher degree of confidence
- the biggest fear is that you're going to mess up, so don't over promise, rather start small
- look for processes that are already naturally asynchronous
  - for instance sending emails is a naturally asynchronous process
  - find something that could fail right now due to external services and add some resiliency using queues
  - use feature flags to allow admins to choose turn it on slowly/safety net
  - if the external service fails then you can show them that the queuing system is alerting them, and they can replay messages.
  - another naturally asynchronous service is something like PDF generation, or perhaps batch jobs that could be made more real time
  









