---

layout: default

title: "Why event sourcing"

date: 2021-01-16-00:00:00 -0000

comments: true

published: false

image: posts/2021-01-16-CRUD-is-not-the-answer-concurrency/Account-0-100.png

excerpt_separator: <!--more-->

---
Let me challenge you. So you might understand the value of Event Sourcing, but please be patient it will be step 
You are writing a TODO app.
And let's say you have a requirement that each task should have a unique title.
How would you design your app to fulfill such a constraint?

That's also what I would do. And how would db behave then if you send same title with a concurrent requets
Ok that's correct. And there is nothing else you cand o.
But don't you think this solution is annoying?
First you rely a database feature, then you are to handle a unique constraint exception
Looks like a terrible solution to me.
Tue 6:44 PM

But it is still the best solution you can get with CRUD.
The problem is the source of truth, is being the database.
As if entire application is just a dummy mock to satisfy DB
Can we move the source of truth from DB to the application /

Objects in OOP  won't help you. Because what we call object is just a pile of memory with a virtual method table.
And people decieve themselves by using ORM's hydrating objects and throwing them out for each request. It's like a child faking a parent. It's not the real thing.
We need "objects" that are alive.
That are not bound to memory.
That's where actors come into play.
The definition of an object in OOP is something that encapsulate state and the behavior. Objects expose the behavior with their methods.
similarly actors also encapsulate state and behavior. But rather than you use methods, you send messages to thme.
Thumbs up
1
Tue 6:49 PM
That was real clear
Tue 6:49 PM
And unlike regular objects they are not bound to a memory location. In deed, virtual actors in Orleans or akka cluster sharding makes the actors be transparent through entire cluster.
You don't care where the "object" is.
And it offers builtin thread safety
So in other words, actors are better objects to model the aggregates than Objects in OOP.
IF you grasp this much , I think you are already enlightened and the rest technical details.
Tue 6:51 PM


You can still use Objects, but they are meant to live in our aggregate actors as an implementation detail

I mean if you have an Order aggregate then it's fine as OrderDetails and Order objects in the Order aggregate
which are encapsulated in the actor.
Before coming to ES, let's talk about CQRS

Since we have opt in for actors, we are to use messages to  communicate with them
We could categorize our messages into 2. Commands and Events.
A Command representing a request a demand, which should be validated.
And Event is something representing what happend
so. Command -> Actor -> Event + Actor's new State formula applies
Now why do we use CQRS ?
Tue 6:55 PM

which would require to poll each actor, not practical at all
but the primary reason for CQRS is to evolve the query side and command side independently. This is very important
That is when you develop your application in crud manner you don't know what reports are needed
You have to be a fortune teller
You probably have felt that dilmmea... Hmm which cs olumns should I add to my table. And you try to guess what is needed
Thumbs up
1
Tue 6:58 PM
then your boss asks, give me the users who created a task then deleted it in 5 minutes
then you are like err...
worse than that in CRUD, you start adding/changing columns it needs to propagate back all the way to your domain , means more core code change in the core logic
new deployments, new bugs new headaches
The primary motivation of CQRS is to segregate your command and query sides so that , these incoming new reports will not break anything on the core command part
you can just add these to the report side without changing a single line of code on the core/command
Now you have another challenge though, in order to address all these potential future report queries, you need tor record everything happened in the system
You should not lose a single bit of info / happening in your system
that's where the event sourcing comes in
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

