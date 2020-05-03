---
layout: default
title: "BDD: a swiss army knife for DDD"
date: 2020-05-03-00:00:00 -0000
comments: true
excerpt_separator: <!--more-->
---

# BDD: a swiss army knife for DDD

BDD, standing for behavior driven development, can bridge the gap between all stake holders and allows us to discover the domain more throughly . Although sort of test driven development, it's focus on the business behaviour rather than the actual implementation. This offers some benefits to conventional unit testing. BDD allows

- to build your ubiquitous domain language.
- all stake holders to be part of the development process. 
- people who can't code to write tests.
- to drive the UX.
- to discover the domain and events.
- to write acceptance criteria for your stories.

<!--more-->

Usualy for BDD, Gherkin Sytax is used. And it is in general in the following format

```gherkin
Given inital state
When some event happens
Then assertion for final state.
```

So in a sense Gherkin syntax offer a step in state machine like applications. The good news is most UI apps are state machines.
Now we will try to model our sample application FBlazorShop in Gherkin syntax and we will eventually see how it maps to code.


<img src="/_posts/images/pizzaconfig.png" width="70%"/>

```gherkin

Feature: Pizza configuration

Scenario: Show pizza configs
Given there are some pizza configurations
When I visit the main page
Then all configurations should be displayed.

Scenario: Choose a pizza config
Given some configurations displayed
When I choose the one of them,
Then details of that configuration should be shown
```

<img src="/_posts/images/config_details.png" width="70%"/>


<img src="/_posts/images/shopping_cart.png" width="30%"/>


```gherkin
Feature: Shopping Cart

Scenario: Add a pizza
Given the details of a pizza config shown 
And there are N pizzas in the order already
When I add the configuration
Then a new pizza should be added to orders with configured specs
And the top item in the orders should be the new added item
And there should be N + 1 items in the orders.

Scenario: Remove a pizza
Given N > 0
And there are N pizzas in the order already
When I remove an item
Then there should be N - 1 pizzas in the order 
And removed item should not be in the orders
```

<img src="/_posts/images/address_empty.png" width="70%"/>

```gherkin
Feature: Place holder

Scenario: Initiate place order
Given at least one pizza added to order list
When I select Order
Then I should be at address selecting page

Scenario: Go back from place holder
Given I am at address selecting page
When I select to go back
Then I should at least see some orders in the box

Scenario: Address validation 
Given I am at address selecting page
And address fields are empty
When I press place order
Then I should still be at address selecting page
And some errors should be shown to fill the address page

Scenario: Enforce login when placing order
Given I am at address selecting page
And address fields are not empty
And I am not logged in
When I press place order
Then login screen should appear

Scenario: Place Order
Given I am at address selecting page
And address fields are not empty
And I am signed in
When I press order
Then I should still be at order following page
When I select my orders
Then I should see my order on the screen
```

<img src="/_posts/images/Login.png" width="70%"/>

```gherkin
Feature: Login

Scenario: Login
Given login screen appeared
When I entered correct credentials
Then I should be at signed in status
```

<img src="/_posts/images/Order_Placed.png" width="70%"/>

```gherkin
Feature: Tracking

Scenario: Track details
Given there are some order displayed on the screen
When I click track for one of these orders
Then I should see its details
```
