---



layout: default



title: "How to start your domain driven project?"



date: 2020-12-15-00:00:00 -0000


comments: true

published: true

excerpt_separator: <!--more-->

image: posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-2.png

---

# How to start your domain driven project

So you have received your 100 pages of the requirements doc and a presentation has been made about your new project. Now as a developer your job is to translate the business requirements to your code. But where do we start? Architecture you say? System diagrams? Project structure? How about starting from discovering and translating the domain? I assume you are familiar or at least or the term Domain Driven Design. 

Domain driven design is not about the layers, is not about your architecture, not about your objects or entities (although entity is fundamental concept in DDD). Domain driven design is about deriving your design from the business domain. We try to build such an _aligment_ between the source code and the other stakeholders, we invent our domain language, that is Ubiquitous Language.



## Ubiquitous Language


Domain driven design is rolled around the concept called ubiquitous language. Ubiquitous language is the common language we share, we we talk among other stakeholders. 


![Ubiq-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-1.png)



<!--more-->

Sharing a common language is extremely vital in order to mitigate misunderstandings. To better understand why it is important let's briefly jump to aviation and  see the worst and most unfortunate air disaster ever happened.



## Tenerife disaster. 



![tenerife-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-1.png)



One of the main reasons causing this unfortunate disaster was communication error. The communication between the control tower and the pilot has involved some non-aviation related terms like "OK" which is quite ambigous. On top of that the weather was bad, the radio communication was problematic and the Pilot was in hurry. 





![tenerife-2](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-cvr.png)


When the pilot heard the term OK, he decided the runaway is empty and collided with another plane during the take off. Aviation industry has learnt from these mistakes and that's why they are adhere to their own ubiquitous language, e.g. "Your aircraft, my aircraft".

In the case of software development, a misunderstanding of requirements may not be obvious initially, however in the long term, even many months later some major problems may surface and cause irreversible damage. Perhaps, we developers should also learn from  the aviation industry and embrace our ubiquitous language.



The key point of  the ubiquitous language is that not only it is shared among different stakeholders but also it is part of the source code. 



![Ubiq-2](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-2.png)



We may have the requirements but without proper digestion and translation of requirements they are just pages with text. Furthermore, you will often see, 
there would be many missing points in the requirements only to be discovered at a later phase. Unless you are Kasparov like business analyst, it is quite difficult to see what is going to come up after 20 moves later. To better illustrate the problem and soluton let's talk about the discovery process.



## Discovery 

The discovery process is where we discover our domain, and create our initial stories. We should also use the discovery process to bring up and compose our very ubiquitous language.


But how do we perform the discovery? One of the ways is event storming. In event storming, we can start via an actor such as a user or a customer, but it is optional. Traditionally people throw out the events and commands they can think of on the fly and use stickers to be posted to a whiteboard, hence the term event storming.

However I prefer a more systematic approach when conveying the event storming session. I coincide it with the [Behavior-driven-development](https://en.wikipedia.org/wiki/Behavior-driven_development) way by using [gherkin](https://cucumber.io/docs/gherkin/) syntax. What is gherkin syntax again? As an example,



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



Basically we start from  a state then some action is taken  and system moves to another state and optionally it generates and event which can be handled by another component.





![gherkin-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/state-event-command.png)







A more concrete example for a banking domain would be:



```gherkin
Feature: Money withdraw
As customer I would like to withdraw money from my bank account

Scenario: Widthdraw less than balance
Given I have 1000$ in my bank account
When I withdraw 300$ 
Then the withdraw is successful
And my final balance should be $700

Scenario: Widthdraw more than balance
Given I have 1000$ in my bank account
When I withdraw 1300$ 
Then the withdraw failed
And my final balance should be $1000
```

![gherkin-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/gherkin-1.png)


Notice how we discovered the commands (verbs) **Withdraw**, we have discovered the events **Withdraw Successful** and **Withdraw Failed** and we have discovered the nouns

**Account** and **Balance** where balance is also being a state and a candidate for a property of the **Account**. There we go we are building our ubiquitous language.

We can continue our event storming session. A good question is "What happens if the **Withdraw has been failed**?. Perhaps no one has asked this question before, maybe forgotten in the requirements doc, so your Business Analyst stops and he or she replies "An email should be sent to the customer about his or her failed Withdrawals. Aha we have discovered another action called **Send an email** . And the process goes on. I highly recommend you to try this with your project's stakeholders. You will see it's a relatively quick and focused effort and it will make the audience, developers more engaged and familiar with the concepts.



Now assuming we have build our Gherkin based features. How do we use them? Actually by generating our scenarios actually we have accomplished a lot:



- We have created our stories with acceptance tests

- We have created our ubiquitous language for the domain

- We have discovered the corner cases otherwise not documented

- We could create Behavior-driven tests for our development.

- New BDD scenarios for the existing features could be created by a non programmer by using the same syntax.

- Serves as documentation by example

You will also see trying to document a feature with plain english can be quite challenging without a proper systematic approach. Gherkin syntax gives you one of these systematic approaches and allow you think about corner cases that would otherwise difficult to discover or describe.

Now how to create BDD tests from above is matter of your choice for the language and framework. If you use .NET you could use [SpecFlow](https://specflow.org/) or [TickSpec](https://github.com/fsprojects/TickSpec)(my favorite) but many BDD frameworks are available for almost any language and platform. 

Basically by using the same BDD feature set, you can use it to create multiple tests:
- Tests for your backend API
- Tests for your front end code
- Tests the browser automation or full integration.

All these thests can be bound to set of central gherkin feature documents. If you can make deriving your features via behaviors, you will have great confidence in your code and 
documentation simultenously. 

So this is how we bound our domain model, discovery process, tests and story all at once. I personally really like this approach and recommend it for any kind of project.
Please feel free to share any comments or objectsions if you have. 



