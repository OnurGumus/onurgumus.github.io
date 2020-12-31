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
As I have just mentioned about ACID compatiblity, usually the databases offer some sort of isolation among their transactions. 
Let's assume two request come at the same time such that they are trying to transfer money from **Account1** to **Account2**
