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

2- If





