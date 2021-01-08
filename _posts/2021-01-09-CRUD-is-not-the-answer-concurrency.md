---

layout: default

title: "CRUD Is Not The Answer: Concurrency"

date: 2021-01-09-00:00:00 -0000

comments: true

published: false

image: posts/2021-01-09-CRUD-is-not-the-answer-concurrency/transactions.png

excerpt_separator: <!--more-->

---


# CRUD Is Not The Answer: Concurrency

Although typical CRUD applications are simple, they have one major important caveat, especially if they are web applications. They involve concurrency! Suppose that we have a banking
application that allows us to transfer some amount of money from one account to another. 

[img]


<!--more-->

At the top, we have a presentation layer but it could be some sort of web service interface which receives a transfer request. And let's say request is delivered to the business layer. A typical implementation of a business layer would be:

1- Go to the database and check if **Acount1** has enough balance. 
2- If it is so, decrement that amount from **Account1**, 
3- Add that amount to **Acount2** 
[img]


Of course, speaking of this, we have to identify our transaction boundary. Assuming we have an ACID-compliant database that gives atomicity, we
would like these two operations, deducing some amount from **Account1** and adding it to the **Account2**, to happen within a single transaction. Since without transaction atomicity if an error happens in between, you would end up with an inconsistent state.

Building a transaction boundary like that doesn't actually solve the concurrency problem. Indeed, it creates the problem itself. 
As I have just mentioned about ACID compatibility, usually databases offer some sort of isolation, which stands for the I of ACID, among their transactions. 
Let's assume there are two requests coming at the same time such that they are trying to transfer some money from **Account1** to **Account2**. Both transactions will simultaneously check if **Account1** has enough balance. Suppose that **Account1** has $1000, both transactions will first make a select query and both will see that $1000 is available whereas in pratice only one of these transactions can occur. Subsequently, both of them will reduce $1000 from **Account1** and they would add $1000 to **Account2** accordingly.

[img]

Since those transactions are not aware of each other and they are isolated, you will end up with **Account1** having a $0 balance
whereas Account2 would be only added $1000 and confusingly, both transactions will succeed. In other words,
the net effect of the second transaction will disappear, since both transactions will attempt to set **Account1** balance to $0. 

Generally, such concurrency problems are solved by introducing either an optimistic or pessimistic concurrency check.
[img]

In the case of pessimistic concurrency check, you basically acquire some lock on the relevant database rows such that no other transaction can select those rows. In the above scenario, the first transaction would acquire a
lock on the **Account1** row and the second transaction would just wait for that lock to be resolved which happens only
after the first transaction commits. Yes, such a pesimistic locking mechanism solves our problem, however, from a performance point of view it may not be acceptable, and in addition to that, since we have locked some rows, eventually it is likely we will end up with a database deadlock at some point in time.

The other alternative is to use optimistic locking. For optimistic locking, we usually use a 
version or a timestamp column and each update statement which contains a "where" statement against
the version/timestamp checks the number of affected rows to determine the concurrency problem. 
Affected rows 0 would denote a concurrency problem and we have to restart the transaction from scratch. Optimistic locking also solves our problem but with two big caveats. 

1- If we have a very busy system retrying the transactions over and over again would create a performance problem.

2- If we have any side-effects during the transaction, such as calling an external service, sending an email, etc, rerunning the same transaction would put us into a dilemma as in 
we are to recreate those side-effects.

Actually, in proper DDD, **Account1** and **Account2** would be domain aggregates where each of which should have its own transactions.
[img]


But if we use 2 transactions we break the atomicity. That is it would be possible the transaction
responsible for updating **Account1** is successful but the one associated with **Account2** might fail. In order to solve this problem, we could come up with a transaction coordinator that can track the state of each transaction individually. However even if we use such a transaction 
coordinator, at some point time we could observe, the transaction associated with **Account1** 
has completed however the one associated with Account2 is not yet done. The actual consistency
will only happen eventually, hence the term **eventual consistency** is used for such cases.
For many developers, the database being in an inconsistent state is somewhat worrisome. 
However, if you check with other business stakeholders, they may not be worried as much as the
developers indeed chances are high that they will welcome the idea.

How would the actual sequence diagram look like? Perhaps a simplified version would
be as below:

[img]

If you follow the numbers you can track the process. The red arrows are denoting the commands
whereas the blue ones are the events. An aggregate (in this case account) will always accept a 
command and return/publish an event. Whereas a process or our transaction coordinator
will listen to events and dispatch commands. In case the system crashes at any point in time when the system restarts the coordinator will recover its last state, and reissue the necessary commands to resume where it was. 

Of course from an implementation point of view, this is much more complicated compared to using a single transaction. There are many CQRS event sourcing libraries available. However in my opinion,
Actors are perfect for representing aggregates and process managers. In a future post, I will talk about a possible implementation of it.

Finally, you may end up with a false conclusion that you have only a few users, concurrency won't be a problem. Indeed concurrency problems usually are not a major concern among web devs, however, the fact is, a concurrency problem could occur just by a single user making
a double click to a button or could be generated for malicious purposes programmatically to break the consistency of database state. 
