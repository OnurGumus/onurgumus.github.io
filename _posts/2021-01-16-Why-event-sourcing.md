---

layout: default

title: "Why event sourcing"

date: 2021-01-16-00:00:00 -0000

comments: true

published: false

image: posts/2021-01-16-CRUD-is-not-the-answer-concurrency/Account-0-100.png

excerpt_separator: <!--more-->

---
Let me challenge you. So you might understand the value of Event Sourcing, but please be patient, our journey will be step by step.
You are writing a for a movie theate, where people would book seats. And obviously one of ther requirements are one seat can only be booked 
by only one person for a given session. We also have an additional requirement that customers are not allowed to choose seats which leave 1 seat gaps between the selection and seats already purchased, although a 2 seat gap is allowed. So

XXOXX is not allowed but XOOXX is allowed.
How would you design your app to fulfill such a constraint? Before reading on I would like you to think about it. Let's assume this is some very massive theather
and there is a lot of concurrent traffic going on. Lot of people try to reserve seats at the same time, and some people cancel their reserverations.

If you store the data within an ACID compliant the database, you could have  gone with Serializable Isolation Level but that wouldn't work well since our system
is heavily used by lot of concurrent users. DB Constraints? Perhaps but I doubt it is possible to create a constraint does what we want and perform well.

But it is still the best solution you can get with CRUD mentality.
The very problem is the source of truth, which is being the database.
As if entire application behaves like a dummy mock just to satisfy the database.  Can we move the source of truth from the database to our application?

Perhaps you would suggest using ORMS. Objects in OOP  won't help you. Because what we call an object is just a data structure living on  a pile of allocated memory with along a virtual method table for the it's methods. It's fixed and bound to memory. Indeed, the objects in OOP are quite low level constructs and
not good candidates to represent a rich domain entities.
And we developers decieve ourselves by using ORM's hydrating objects and throwing them out for each request. It's not the real thing. They are not truly "alive"
objects. What we need is "objects" that are alive, which are not bound to memory.

That's where actors come into play. By definition of an object in OOP is something that encapsulate state and the behavior. Objects expose the behavior with their methods. Similarly actors also encapsulate state and behavior. But rather than you use methods, you send messages to them to invoke their behaviors.
And unlike regular objects they are not bound to a memory location (excluding the illusion created by garbage collectors moving objects around). Indeed, virtual actors in Orleans or entities in akka cluster sharding make the actors be transparent through entire cluster, they are even not bound to a physical machine anymore. When you use an actor, you don't care where the "object" physically located.Actors also offer builtin thread safety. So in other words, actors are better objects to model the aggregates than traditional objects in OOP. If you could grasp this much, I think you are already enlightened and the rest technical details.

You can still use the traditional Objects, but they are meant to live inside our aggregate actors as an implementation detail.

I mean if you have an Seat aggregate then it's fine as SeatDetails and Seat objects in the Seat aggregate which are encapsulated in the actor.
Before coming to Event Sourcing, let's talk about CQRS

Since we have opt in for actors, we are to use messages to  communicate with them. We could categorize our messages into two. Commands and Events.
A Command representing a request a demand, which should be validated.And an event is something representing what happend.
So Command -> Actor -> Event + Actor's new State formula applies.

## Now why do we use CQRS?

One of the The primary reasons to use CQRS is to be evolve the query side and command side independently. When you develop your application with CRUD mentality you don't know what reports are needed in advance. You have to be a fortune teller
You probably have felt that dilmmea... Sometimes we think like "Hmm which columns should I add to my table?". And you try to play this guessing game.


Then one day your boss asks, give me the customers who created a reservation then deleted it within 5 minutes. If you have not added relevant columns to store
about deletion of the reservations along with their time stamps, you may not be able to present this report.

Worse than that, in CRUD, when you start adding/changing columns, then those changes need to propagate back all the way to your domain, and such changes mean more code changes in the core logic,new deployments, and potentially new bugs and new headaches.

As stated, the primary motivation of CQRS is to segregate your command and query sides such that implementation of these incoming new report requests will not break anything on the core command part.

With CQRS, you could just add these new columns to the report side without changing a single line of code on the core/command.
Now you have another challenge though, in order to address all these potential future report queries, you need to record everything happened in the system.
You could not afford to lose a single bit of info / happening in your system.
And that's precisly where the event sourcing comes in
If you delete the task record when user delete tasks, then that info is gone
and don't get me started on soft deletes, it is a terrible option either

So your only bet is to rely on ES in order not to lose any data
and it perfectly aligned with actors
basically it's match made in heaven
that's all

In my implementation, There is an event listener, and based on events it can create Sagas
And Saga/PM is something listenes events and dispatches commands
unlike to aggrregates listens commands and publishes events
And for publish event you follow the following sequence
1-) Validate Command
2-) Create an Event
3-) Persist the event
4-) Apply the event
5-) Publish the event


Another important thing is probably called virtual actors in Orleans but in akka it's called cluster sharding
that means you can just send a message to anctor with an entity id that doesn't exist at all
and it will be automatically created if it doesn't exist
so you don't care about where that actor is.
Thumbs up

they will be your aggregates
Question: If we represent all our aggregates as an Actor,
Eventually our memory will be full of thousands of actors. We can't keep them in memory
So what do we do?
akka offers a setting called passivation. And tracks sharded entities if they don't receive message in 2 minutes for eg. it gracefully purges them
and if they recieve a msg after it will replay and restore
and for the process manager there is another setting called Remember me , which would restart those sagas automatically even entire cluster crashes
so you don't have to "remember" which sagas were running

