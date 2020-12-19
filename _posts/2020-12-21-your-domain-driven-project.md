# Your domain driven project : The next steps

In the previous post we have seen how to kickstart the project by discovering our domain. Once we have establihed our main stories and acceptance criteria, 
the next step would be splitting our features/stories to their respective bounded contexts. The concept of bounded context is heavily discussed in any domain driven book, 
usually typical examples a customer concept in one domain meaning one thing, such actor buying something, in another context it may refer to an accounting process or the delivery.
I myself have a bit more technical and perhaps more vague interpretation of the bounded contexts. 

For example if we have that banking process earlier, probably we want users to be authenticated. Or there could be another sub system managing 
the communication such as sending emails or sms to the user or managing their personal details. I know this kind of seperation is not aligned with the 
classical definiton of boundled contexts, but in the main bounded context the same customer entity is affilated with a bank account, their balance etc, 
in another bounded context we manage users address and personal details. Such details are usually not interesting to accounting and transaction domain. 
The main goal of proper software development via domain driven design is to tackle with the complexity. The more hard bounded divisons we have the easier the tackling process.

So for starters we can have such a schematic

[!BoundedContext-1](/asssets/posts/bounded-context-1.png)

This simple divison actually helps us a lot. Let's see why? Firstly if we have a team of developers to work on the project, we have to decide who works on what. 
You might have heard [Brook's law](https://en.wikipedia.org/wiki/Brooks%27s_law) stating that: 

<< Adding more people to a highly divisible task, such as cleaning rooms in a hotel, decreases the overall task duration 
(up to the point where additional workers get in each other's way). However, other tasks including many specialties in software projects are less divisible;
Brooks points out this limited divisibility with another example: while it takes one woman nine months to make one baby, "nine women can't make a baby in one month". >>


So we have to be able to split the things in such a way our developers should be able to do their job without stepping on each other toes. 

Another major reason is to determine what is important and give the focus on that important slice. After 3 months of development.
You don't want to tell your manager you have a perfect implementation for the authenticaiton slice but you haven't started the main thing. That would be disasterous. 
Sometimes you have too few resources or team members may not be familiar with a particular task, then you can opt-in to outsource or buy a ready made solution, 
say for the autentication and your fellow developers could focus on what is important. Whereas you sometimes you have sometimes too many resources, then you could assign 
some of the developers with fine tuning the purchased solution or asist to the outsource team. 



Furthermore, those vertica slices can be increased within a single slice to achieve something like micro-front ends. Whatever strategy you choose,
Your primary objective should be focus and deliver what is important aligned with the an agile process and quickly make a demo on the first iteration and gather feedback.
