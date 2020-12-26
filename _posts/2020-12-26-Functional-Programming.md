---

layout: default

title: "Functional programming: Enemy of the state"

date: 2022-12-26-00:00:00 -0000

comments: true

published: true

image: posts/2020-12-26-Functional-Programming/run.png

excerpt_separator: <!--more-->

---

# Functional programming: Enemy of the state

A quick google search on "Why functional programming" or "why functional programming matters" will yield quite insightful results. Having that said, I still believe there is more to add to this topic. And what would be my answer to this question? One word: **purity** (or Statelessness). 
Before delving into purity let's dissect the **function** in the functional programming paradigm.

 <!--more-->

When I heard the term functional programming, I was very confused. I said, "I have been using C, we already have functions there. JavaScript also has functions, many OOP languages
have functions disguised as methods of objects. So Why is this new term?" Well, my first mistake was functional programming wasn't really a new term. The conceptual origin of functional programming
**lambda calculus** is developed as early as 1930 by Alonzo Church and the first functional programming language LISP was available by the late '50s. So functional programming wasn't
something new. My second mistake was the confusion of the term "functional". The word function here does not represent the procedures which we call as functions in the programming languages but rather they refer to the mathematical functions just like Sine and Cosine functions. And it is **purity** that makes these mathematical functions different than functions in these imperative programming languages. That is, given the same input the function will always yield the same output without any side effects. 

For example, sin(90) will always yield 1 as a result, no matter when or how many times you call. It also has no side effects, that is, it is not altering anything you care about.
So that's an example for a pure "function" hence the term "function"al programming (Ok, perhaps a more formal description would involve lambda calculus or category theory stuff but this approach I have given is simpler to take and it is still correct).

We have just seen how functions in functional programming are different than procedural programming counter-parts. Imperative programming languages won't constrain your functions to be pure.

One may argue functional programming is good for mathematics and science or finance but how about the real-world line of business applications? Indeed that's precisely where functional programming shines. But before coming to that let's review some misconceptions about functional programming.

## Misconceptions about functional programming

When we ask developers, what are the characteristics of good code or a good programming language, it is likely you would hear good code (or language) is associated with:

- Good code is more maintainable.
- Good code leads to better readability.
- Good code tends to be shorter and concise.
- Good code is testable and dependencies are isolatable.
- Good code is extendable.
- Good code is less buggy.

Functional programming is not some code nor it is a language but a paradigm. You could probably go with the functional programming with any language. However, some languages embrace it as a first class citizen (or even dictate it) whereas some languages tend to challenge you or they become too verbose to go with functional programming. Thus above items about good code
are not really relevant to the functional programming paradigm. I would like to emphasize once more as in the following:

- Functional programming **does not** necessarily lead to more maintainable code nor does it aim that.
- Functional programming languages **are not** necessarily more readable.
- The practice of functional programming can also turn the code into spaghetti if one is not careful.


Wow, if functional programming doesn't give me those aspects I care about my code, then why do I care? That's a good question.

## Why should I care about functional programming?

We have discussed functional programming does not necessarily lead to better code. So why bother? The answer is, functional programming keeps the developer sane and you will sleep better!
Let's see how with an example. Suppose that we have a Car class with a public property Color and two public methods Run() and Stop(). The Run method does whatever internally necessary to run the car.

![Car](/assets/posts/2020-12-26-Functional-Programming/class-diagram.png)

Now we do:
```C# 
var car = new Car();
car.Run();
... //do other things.
SomeOtherFunction(car);
//Did SomeOtherFunction stopped the car? I don't know I have to track it.
... //other things

SomeOtherFunction(Car car){
  //hmm have I run the car before? I have to track it.
  car.Run();
  //now I have how to run the car perhaps more than one time. What's gonna happen?
}
```


We have 3 problems here:

1- As the author of SomeOtherfunction how will I know if the Run method was called before? I need to look up the caller's code thus it's an **extra burden** to the developer to find it out. 
One might argue, we could add an IsRunning property, however, nothing forces me at compile time to check that. I could easily forget doing such a check. And not always
such properties and  methods are related. So I am **burden**ed with reviewing the caller's code. 

2- What happens if I call Run twice. Will it throw an exception? Will it ignore? The only way to find out is either read the relevant API for the Car class or read the source. 
Hence we have another **burden** here. 

3- After SomeOtherFunction returns how do I know if the car is stopped or not.  Again we have to read the docs or the source for the SomeOtherFunction and yet another **burden** here.

So you can observe such a simple code brings a lot of responsibility to the developer that he has to track and remember the state at various stages of the coding process. 
It could be easy to remember for one object but if you have lot's of objects circulating deep into the code, it may drive you nuts.

How do we solve this problem? Well, the problem is about **state tracking** and if we didn't have the state in the first place, we wouldn't have that problem.

## The functional solution

So we want to get rid of the state. Perhaps a way to do so is to represent the state by using the types so instead of a single Car type. We could utilize two different types. **StoppedCar** and a **RunningCar**. Then we have a **run** function that is taking a **StoppedCar** and **RunningCar**

![Run-function](/assets/posts/2020-12-26-Functional-Programming/run.png)

Once we define such a run function, solve all three problems. First, we always know if the car is running:

```csharp
SomeOtherFunction(RunningCar car){
 //we know for sure the car is running
 //you cannot call run again since the code won't compile
}
```

And obviously, we cannot call the run function again since the code won't compile. And depending on the return type the caller will know what type has been returned.
Now as developers we have fewer things to track, fewer things to worry about. Now life is so easy! Or is it? 



## If functional programming is so good why it is not popular?

Well, the first thing to acknowledge is functional programming is not about syntax. It's a paradigm. For example, recently C# 9 has attained the record syntax, a record is a functional concept
and if you are not familiar with the functional paradigm, you might be confused about why we need records in the first place. You will only see unconvincing answers like records are good because they are immutable (the real answer is they provide value semantics and referential transparency, this don't care about the memory location, whereas immutability is only a vehicle to achieve these). 

What I am trying to say is to get started with functional programming, you have to forget about most things you already know about imperative programming. 
So it is literally baby steps again and it would take quite a while to master it. And that is one of the major reasons people shy away from functional programming.

In the imperative world we have statements that tell the compiler what to do:

- do this;
- do that;

Whereas functional programming is more like movie frames. Each frame is immutable and unchangeable but if we roll 24 frames per second. It creates the illusion of a movie.

![frames](/assets/posts/2020-12-26-Functional-Programming/frames.png)

Functional programming does the same. We never change the data but every time we need a change things we create a new instance of that type. And obviously, that's a lot of CPU cycles and memory allocation compared to the imperative approach and 30 years ago that really mattered. 30 years ago, your colleagues would say, "Whoa! Are you allocating 200 bytes just to change the status of a car?" But these days, it is **engineers are expensive and servers are not**. Now, your users are unlikely to notice a few hundred nanoseconds delay. But you could utilize the hardware power for the sake of programmer productivity. Indeed, immutability, functional paradigm even scales better when you have a  distributed multi-threaded environment. But these historical problems with functional programming allowed the imperative paradigm to flourish whereas people were skeptical with the functional programming.

Having smaller community lead to less examples, less resources, less tooling. You can find lot of articles, books talking about, Functional programming, but try to find a functional sample application for something a simple todo list that persist the data to database. You will be mostly out of luck. You might try to fallback trying to use 
your old tools like ORMs. But then you will be frustrated and ask yourself why bother with the functional way at all. Indeed, CQRS with event sourcing is perhaps one of the correct ways to persist the data in functional programming, instead of ORMS. Aaaand you have another thing to learn!

Combined with the relearning process described, functional programmers are a minority among the developer community. Having that said, today's challenges require complicated solutions, and solutions like reactive programming is going more and more popular. Probably one of the most famous and demanded javascript library is React and it's a good example on demonstrating how functional and reactive programming shines. Single-page applications are way more stateful than the backend apps since usually backend only serves the data by relaying in and out from the database without holding the state. And many developers have appreciated how React (and redux like solutions) helped them to scale their complicated applications. We are more and more interested in declarative solutions.

I personally feel, we are at the edge of madness with respect to OOP and imperative programming and we cannot go longer. Functional programming is the answer but will the 
developers throw away their skills and start the relearning process? Who knows.



So these are the current challenges for functional programming but sit tight the future might be different.
