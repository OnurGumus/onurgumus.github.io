---

layout: default

title: "How to start your domain driven project?"

date: 2020-12-15-00:00:00 -0000

comments: false
published: false
excerpt_separator: <!--more-->

---

So you have recieved 100 pages of the requirements doc and a presentation has been made about your new project. Now as a developer your job is to translate the business requirements
to your code. But where do we start? Architecture you say? System diagrams? Project structure? How about starting from discovering and tranlating the domain? I assume you are familiar
or at least or the term Domain Driven Design.

## Ubiquotous Language


Domain driven design is rolled around the concept called Ubiquotous language. Unbiqtoutous language is the common language we share, we we talk among other stake holders. 

![Ubiq-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-1.png)

<!--more-->
Sharing a common language is extremly important to mitigate misunderstandings. To better understand it let's briefly jump to aviation and the worst air disaster ever happened.

## Tenerife disaster. 

![tenerife-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-1.png)

The primary reason causing this unfortunate disaster was communication error. The communication with the control tower and pilot has involved non aviation related terms like "OK"
on top of that the weather was bad, the radio communication was problematic and the Pilot was in hurry. 


![tenerife-2](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-cvr.png)

Aviation industry has learnt from these mistakes and that's why they are strictly sticking to their own ubiqoutous language. 

In the case of development, a misunderstanding of requirements in a long term project may surface many months later causing irreversable problems especially. Perhaps we
developers should also learn from aviation industry and embrace our ubiqoutous language.

The key point of ubiqoutous language is that not only it is shared among different stake holders but also it is part of the source code. 
We may have the requirements but requiremnts, but without proper digestion and translation of requirments they are just pages with text. Furthermore, you will often see, 
there are many gaps in the requirements only to be discovered at a later phase. Unless you are Kasparov like business analyst, it is quite difficult to see what is going to 
come up after 20 moves later. So there we have the discovery process. 

## Discovery 

The discovery process allows us to create stories and it brings up our fundemental parts of our very unquitous language. Nouns and Verbs! 

![ubiq-3](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-3.png)

But how do we perform the discovery? One of the ways is event storming. In event storming, we have an actor such as a user or a customer, but it is optional. 
Although typıcally event storming is performed with throwing events out, I prefer conveying our event storming session with the BDD way that is using gherking syntax.

In gherkin syntax we do the following:

```gherkin
Feature: Specify feature name
And some description

Scenario: Scenario name
Given some initial state
When some action/command happens
Then Some assertion about final state
And and optional assertion about if an event is fired
```


For example:

```gherkin

Feature: Money withdraw
As customer I would like to with draw many from my bank account

Scenario: Widthdraw less than balance
Given I have 1000$ in my bank account
When I withdraw 300$ 
Then the withdraw operation should be successful
And my final balance should be $700

Scenario: Widthdraw more than balance
Given I have 1000$ in my bank account
When I withdraw 1300$ 
Then the withdraw operation should be failed
And my final balance should be $1000

```

Notice how we discovered the commands (verbs) **Withdraw**, we have discovered the events **Withdraw Successful** and **Withdraw Failed** and we have discovered the nouns
**Account** and **Balance** where balance is also being a state, a candidate for a property of the **Account**. There we go we are building our ubiquitos language.
We can continue our event storming session. A good question is "What happens if the **Withdraw has been failed**?. Perhaps no one has asked this question before, forgotten in the requirements, so your Business Analyst stops and he or she replies "An email should be sent to the customer about his failed Withdrawals. Aha we have discoverd another  action called **Send an email** . And the process goes on. I highly recommend you to try this with your project's stakeholders. You will see it's a relatively quick and focused effort and it will make the audience, developers more engaged and familiar with the concepts.

Now assuming we have build our Gherkin based features. How do we use them? By generating our scenarios actually we have accomplished a lot. 

- We have created our acceptence tests
- We have created our ubiqoutous language
- We have discovered the corner cases otherwise not documented
- We could create Behavior-driven tests for our development.
- Those BDD tests could be created by a non programmer
- Serves as documentation by example


Now how to create BDD tests from above is matter of your choice for the language and framework. If you use C# you could use SpecFlow or TickSpec(my favorite) but BDD frameworks
are available for almost any language and platform.

