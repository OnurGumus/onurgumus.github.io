---

layout: default

title: "DDD-with-Akka-actors"

date: 2021-01-16-00:00:00 -0000

comments: true

published: false

image: posts/2021-01-16-CRUD-is-not-the-answer-concurrency/Account-0-100.png

excerpt_separator: <!--more-->

---

Let's say you are writing a todo application, where users of a company would create tasks where each task has a title and description. And one of the requirements is that the task titles should be unique among all the tasks created regardless of who created them.

How would you design your app to fulfill such a constraint? Before reading on I would like you to think about it. Let's assume the application is being used by a
very large company and there is a lot of concurrent traffic going on. 

If you store the data within an ACID-compliant database, you could add a unique constraint to the title column, but aren't you feeling uncomfortable that we have to rely on an infrastructural entity like the database to fulfill our business need?

Still perhaps it is the best solution you can get with the CRUD mentality.
The very problem is actually the source of truth, being the database.
As if the entire application behaves like a dummy mock just to satisfy the database itself.  Can we move the source of truth from the database to our application?

In order to make such a move for the source of truth to our code perhaps you would suggest using ORMS. But then I'd say Objects in OOP  won't help you. Because what we call an object is just a data structure living on a pile of allocated memory along with a virtual method table for its methods. It's fixed and bound to some physical memory location. Indeed, the objects in OOP are quite low-level constructs and not good candidates to represent rich domain entities.
And we developers deceive ourselves by using ORMs, hydrating them, and throwing them out when the request is done. I don't know you, but I feel like it's just not the real thing. They are not truly "alive"
objects. What I would like to have instead "objects" that are alive.

That's where the actors come into play. By definition of an object in OOP is something that encapsulates state and behavior. Objects expose the behavior via their methods. Similarly, actors also encapsulate state and behavior. But rather than using methods, you send messages to them to invoke their behaviors.
And unlike regular objects, they are not really bound to a memory location (excluding the illusion created by garbage collectors moving objects around). Indeed, virtual actors in Orleans or entities in Akka cluster sharding make the actors be transparent through the entire cluster such that they are even not bound to a physical machine anymore. When you use such an actor, you really don't care where the "object" is physically located. Actors also offer built-in thread-safety. So in other words, actors are better objects to model the aggregates than traditional objects in OOP. If you could grasp this much, I think you are already enlightened and the rest are technical details.

You can still use the traditional Objects, but they are meant to live inside our aggregate actors as an implementation detail.


Since we have opt-in for actors, we are to use messages to communicate with them. We could categorize our messages into two. Commands and Events.
A Command representing a request a demand, which should be validated. And an event is something representing what happened.
So Command -> Actor -> Event + Actor's new State formula applies.

## Now why do we use CQRS?

One of the primary reasons to use CQRS is to evolve the query side and command side independently. When you develop your application with a CRUD mentality you don't know what reports are needed in advance. You have to be a fortune teller.
You probably have felt that dilemma... Sometimes we think like "Hmm which columns should I add to my table to satisfy the reports?". And while you are trying to play this guessing game, one day your boss asks, if you could give him or her the users who had created a task then deleted it within 5 minutes. If you have not added the relevant columns to store about the deletion of an along with their time stamps, you may not be able to present this report.

Worse than that, in CRUD, when you start adding/changing columns, then those changes need to propagate back all the way to your domain, and such changes mean more code changes in the core logic, new deployments, and potentially new bugs and new headaches.

As stated, the primary motivation of CQRS is to segregate your command and query sides such that the implementation of these incoming new report requests will not break anything on the core command part.

With CQRS, you could just add these new columns to the reported side without changing a single line of code on the core/command.
Now you have another challenge though, in order to address all these potential future report queries, you need to record everything that happened in the system.
You could not afford to lose a single bit of info/happening in your system.
And that's precisely where the event sourcing comes in.
If you delete the associated reservation record when a customer deletes a reservation, then that info is gone. And don't get me started on soft deletes, as it is a terrible option either.

Your only bet is to rely on Event Sourcing in order not to lose any data and more than that Event Sourcing is perfectly aligned with actors.

As my implementation, you would have an actor for each task, but also an actor for each title where the key of the title actor could be the hash of the title string. And each task creation managed by a Saga/Process Manager. First, a task creation command is issued to the task actor, then the task actor publishes an event like "TaskCreationRequested" which causes a Saga/PM to start and that Saga sends a command to Task Title and if reservation successful, notifying the Task actor to 
continue.

Aggregates and Services receive commands and publish events And Saga/PM listeners events and dispatches commands

The following sequence happens in the actors when a command is received

1. Validate Command
2. Create an Event
3. Persist the event
4. Apply the event
5. Publish the event

Another important thing is probably called virtual actors in Orleans but in Akka, it's called entities in cluster sharding
which allow you to send a message with an entity id even if that actor doesn't exist at all, and the actor will be automatically created 

As a result, you don't care about where that actor is located.

Another challenge is if we represent all our aggregates as an Actor, eventually our memory will be full of thousands of actors. We can't keep them in memory
So what do we do?
As for Akka, it offers a setting called passivation. The actor system in Akka tracks sharded entities and if they don't receive a message in say 2 minutes for eg. the system gracefully terminates them. However, if those terminated actors receive a message the state will be a replay and the state will be restored.

And as for the process manager, there is another setting called Remember Me in Akka, which would restart those sagas/PMs automatically on system restart even entire cluster crashes so you don't have to "remember" which sagas were running before the crash.

Finally, you might fight to implement a simple application as above overwhelming perhaps overly complex. That's certainly fine. In that case, if remember our vertical slices concept I have mentioned in the [lean development post](https://onurgumus.github.io/2020/12/21/your-domain-driven-project-Lean-software-development.html) and you have the freedom to choose different approaches for different layers. The most important thing is not about going with CQRS but have your bounded contexts segregated so that if you choose wrongly you could reimplement that part without 
ruining the entire development process.
