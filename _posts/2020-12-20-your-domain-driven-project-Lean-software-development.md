---

layout: default

title: "Your domain driven project:Lean software development"

date: 2022-12-21-00:00:00 -0000

comments: true

published: true

image: posts/2020-12-20-your-domain-driven-project-Lean-software-development/lean-1.png

excerpt_separator: <!--more-->

---

# Your domain driven project: Lean software development

In the previous post we have seen how to kick-start the project by discovering our domain. Once we have established our main stories and the relevant acceptance criterias, 
the next step would be splitting our features/stories to their respective bounded contexts. The concept of bounded context is heavily discussed in any domain driven book, 
usually typical examples given as a customer concept in one domain meaning one thing, such as an actor buying something, whereas in another context it may refer to an owner of an or target of a delivery.
I myself, have a bit more technical and perhaps more vague interpretation of the bounded contexts:

![BoundedContext-1](/assets/posts/2020-12-20-your-domain-driven-project-Lean-software-development/lean-1.png)
 <!--more-->

For example if we have that banking process earlier, probably we want users to be authenticated. Or there could be another sub system managing 
the communication such as sending emails or SMS to the user or managing their personal details. I know this kind of separation is not aligned with the 
classical definition of bounded contexts. In my case, within the main bounded context the same customer entity is affiliated with a bank account, their balance etc, 
and in another bounded context we manage customers' addresses and personal details. And we observe such details like address and personal details are usually not interesting to accounting and transaction bounded context. 
The main goal of proper software development via domain driven design is to tackle the complexity. The more hard bounded divisions we have the easier the tackling process since the problem is splitted into smaller chunks.




This simple division actually helps us a lot. Let's see why? Firstly if we have a team of developers to work on the project, we have to decide who works on what. 
You might have heard [Brook's law](https://en.wikipedia.org/wiki/Brooks%27s_law) stating that: 

<< Adding more people to a highly divisible task, such as cleaning rooms in a hotel, decreases the overall task duration 
(up to the point where additional workers get in each other's way). However, other tasks including many specialties in software projects are less divisible;
Brooks points out this limited divisibility with another example: while it takes one woman nine months to make one baby, "nine women can't make a baby in one month". >>


So we have to be able to split the things in such a way our developers should be able to do their job without stepping on each other toes. 

Another major reason is to determine what is important and give the focus on that important slice. After 3 months of development.
You don't want to tell your manager you have a perfect implementation for the authentication slice, but you haven't started the main thing. That would be disastrous. 
Sometimes you have too few resources or team members may not be familiar with a particular task, then you can opt-in to outsource or buy a ready-made solution, 
say for the authentication and your fellow developers could focus on what is important. Whereas you sometimes you have sometimes too many resources, then you could assign 
some developers with fine-tuning the purchased solution or assist to the outsource team. Thus, we try to [eliminate the waste](https://en.wikipedia.org/wiki/Lean_software_development#Eliminate_waste)



Furthermore, those vertical slices can be increased within a single slice to achieve something like micro-front ends. Whatever strategy you choose,
Your primary objective should be focus and deliver what is important aligned with the [lean software development](https://en.wikipedia.org/wiki/Lean_software_development) process and make a demo on the first iteration by [delivering it as fast as possible](https://en.wikipedia.org/wiki/Lean_software_development#Deliver_as_fast_as_possible) and gather feedback and [amplify learning](https://en.wikipedia.org/wiki/Lean_software_development#Amplify_learning)


One thing you have to focus is once you have picked a vertical slice to go you have to build it from end to end so that you achieve [Build integrity in](https://en.wikipedia.org/wiki/Lean_software_development#Build_integrity_in). Failing to work that way, you will end up non integrating pieces floating around, and I can guarantee you, things **will** go wrong on the day of integration. So don't take any risks, but keep things integrated from sprint 1. Well, you can ask how am I going to that in such
a short amount of time. Here's my answer:

## API First and Mocking

So you can see that red line on the above figure. That's where our API lands. And designing and agreeing on the API should be the very first thing you should do in the actual coding process. Do not dwell on the architecture. A good architecture would indeed allow you [delay the decisions as much as possible](https://en.wikipedia.org/wiki/Lean_software_development#Decide_as_late_as_possible) to focus on the API first even before writing your tests. Since when writing your tests
even if you prefer TDD, you would want your tests to compile but fail at runtime. How are you going to write your API? Luckily if you follow the previous post, you already have your discovery done. You know your nouns and verbs. You can immediately start having an API function called **Withdraw** taking an **Account** and an **Amount** as the input and return **Success** or **Failure**.

Once the contract is established you can start writing your tests and front end and backend people can start working their respective areas.

The second important aspect is **mocking**. You are expected to finish the entire withdrawal process in sprint 1 but that might be too big to chew in such a short amount of time. So instead just mock the results. For example hard code such a logic if the amount is greater than $1000 always fail and otherwise always succeed without doing anything.
Whereas front-end people can just start consuming the API and build a very primitive but functional UI. Yes that's probably what the end product should be, and you might need the developer's assistance when presenting such UI. Management should not refrain involving the developers from demoing directly to the customers. Since such demos will
create a sense of achievement within the team, and it will further [empower](https://en.wikipedia.org/wiki/Lean_software_development#Empower_the_team) them. After all we 
are writing software and this is how software grows. 

