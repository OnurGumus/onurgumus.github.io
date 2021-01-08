Now let's look at 

Although CRUD applications are simple, they have one major important caveat, especially if they are web applications. They involve concurrency! Suppose that we have a banking
application that allows us to tranfer some amount of money from one account to the other. At the top we have a presentation layer but it could be some sort of 
web service interface which recieves a transfer request. And that request is delivered to the business layer. A typcal implementation of business layer would be:

1- Go to database and check if **Acount1** has enough balance. 
2- If it is so, decrement that amount from **Account1**, 
3 - Add that amount to **Acount2** 

Of course speaking of this, we have to identify our transaction boundary. Assuming we have an ACID complient database which gives us some atomicity, typically we
would like these two operations, deducing amount from **Account1** and adding it to the **Account2**, to happen in a single transaction. Because otherwise 
if an error happens in between, you would end up with an inconsistent state.

Building a transaction boundary like that, doesn't solve the concurrency problem. In deed, it creates the problem itself. 
As I have just mentioned about ACID compatiblity, usually databases offer some sort of isolation among their transactions. 
Let's assume two requests come at the same time such that they are trying to transfer money from **Account1** to **Account2**. Both trnsactions will simultanously check if **Account1** has enough balance. Suppose that **Account1** has $1000 and both transactions will first make a select query and both wills see that $1000 is available. Then both of them will reduce $1000 from **Account1** and they would add $1000 to **Account2**.
[img]
Since those transactions are not aware of each other, you wills see **Account1** with $0 balance
whereas Account2 would be only added $1000. But both transactions will succeed. In other words
the net effect of the second transaction will disappear. The reason for that is both transactions
will attempt to set the **Account1** balance to $0. 

Typically this problem is solved by introducing either an optimistic or pesimistic concurrency check.
[img]

In pesismitic concurrency check you basically aquire some lock on the relevant database rows such that no other transaction can select that row. In above case, the first transaction would aquire a
lock and the second one would wait for that lock to be resolved which happens only after the
after the first transaction commits. On the overall this solves our problem, however, from performance point of view it may not be acceptable, and in addition to that, since we have locked 
some rows, eventntually it is likely we will have a dead lock at some point of time.

The other alternative is to use optimistic locking. For optimistic locking, we usually use a 
version or a timestamp column and each update statement which contains a "where" statement against
the version/timestamp checks the number of affected rows to determine the concurrency problem. 
Affected rows 0 would denote a concurrency problem and we have to restart the transaction from 
scratch. Optimistic locking also solves our problem but with two big caveats. 

1- If we have a very busy system retrying the transactions over and over again would create a performance problem.

2- If we have any side-effects during the transaction, such as calling an external service, sending an email etc, rerunning the the same transaction would put us into a dillema as in 
we are to recreate those side-effects.

Actually in proper DDD, **Account1** and **Account2** would be domain aggregates where each of which should have their own transactions.
[img]


But if we use 2 transactions we break the atomicity. That is it would be possible the transaction
responsible for updating **Account1** is successful but the one associated with **Account2** might fail. In order to solve this problem we could come up with a transaction coordinator which can 
track the state of each transaction individually. However even if we use such a transaction 
coordinators, at some point time we could observe, the transaction associated with **Account1** 
has completed however the one associated with Account2 is not yet done. The actual consistency
will only happen eventually, hence the term **eventual consistency** is used for such cases.
For many developers the database being in an inconsistent state is somewhat worrysome. 
However if you check with other business stakeholders, they may not be worried as much as the
developers in deed chances are high that they will welcome the idea.

How would the actuall sequence diagram would look like? Perhaps a simplified version would
be as below:

[img]

If you follow the numbers you can track the process. The red arrows are denoting the commands
whereas the blue ones are the events. An aggregate (in this case account) will always accept a 
command and return/publish an event. Whereas a process or our transaction corrdinator
will listen to events and dispatch commands. In case the system crashes at any point of time, when the system restarts the coordinator will recover it's last state, and reissue the necessary commands to resume where it was. 

Of course from an implementation point of view this is much more complicated compared to using a single transaction. There are many CQRS event sourcing libraries available. However in my opinon,
Actors are perfect much for representing aggregates and process managers. In a future post I will talk about a possible implementation of it.

Finally you may end up with a false conclustion that you have only few users an concurrency 
won't be a problem. Indeed concurrency problems usually is not a major concern among webd devs, however, the fact is, a concurrency problem could occur just by a single user making
a double click to a button or could be generated for malicous purposes programatically to break the consistency of database state. 


