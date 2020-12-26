---

layout: default

title: "Functional programming: Enemy of the state"

date: 2022-12-27-00:00:00 -0000

comments: true

published: false

image: posts/2020-12-20-your-domain-driven-project-Lean-software-development/lean-1.png

excerpt_separator: <!--more-->

---

# Why functional programming?

This is a question that has been answered many times. A quick google search on "Why functional programming" or "why functional programming matters" will yield quite insightful results. Having that said I still believe there is more to add to this topic. And what would be my answer to this question? One word: **purity** (or Statelessness). 
Before delving into purity let's dissect the **functional** in functional programming.

When I heard the term functional programming, I was very confused. I said, "I have been using C, we already have functions there. JavaScript also has functions, may OOP languages
have functions disguised as methods of objects. Why this new term?" Well, my first mistake was it wasn't a new term. The conceptual origin of functional programming
**lambda calculus** is developed as early as 1930 by Alonzo Church and the first functional programming language LISP was available by the late '50s. So functional programming wasn't
something new. My second mistake was the confusion of the term "functional". The word function here does not represent the procedures which we call functions in the programming languages but rather they refer to mathematical functions just like Sine and Cosine functions. And it is **purity** that makes these mathematical functions different than functions in the programming languages. That is given the same input the function will always yield the same output without any side effects. 

For example, sin(90) will always yield 1 as a result, no matter when or how many times you call. It also has no side effects, that is, it is not altering anything you care about.
So that's a good example for a pure "function" hence the term "function"al programming (Ok, perhaps a more formal description would involve lambda calculus or category theory
but this approach I have given is simpler to take and it is correct).

So we have seen how functions in functional programming are different than procedural programming which won't constrain your functions to be pure. One may argue functional programming is good for mathematics and science or finance but how about the real-world line of business applications? Indeed that's precisely where functional programming shines. But before that let's review some misconceptions about functional programming.

## Misconceptions about functional programming

When we ask developers, what are the characteristics of good code or a good programming language, it is likely you would hear good code (or language) is related to

- Good code is more maintainable.
- Good code leads to better readability.
- Good code tends to be shorter and concise.
- Good code is testable and dependencies are isolatable.
- Good code is extendable.
- Good code is less buggy.

Functional programming is not code nor it is a language but a paradigm. You could probably do functional programming with any language. However, some languages embrace it as a first class citizen (or even dictate it) whereas some languages tend to challenge you or too verbose to go with functional programming. So above items about good code
are not relevant to the functional programming paradigm. I would like to emphasize these once more as in the following:

- Functional programming **does not** necessarily lead to more maintainable code nor does it aim that.
- Functional programming **is not** necessarily more readable.
- Functional programming can easily turn to spaghetti if one is not careful.


Wow, if functional programming doesn't give me those aspects I care about my code why do I care? That's a good question.

## Why should I care about functional programming?

We have discussed functional programming does not necessarily lead to better code. So why bother? The answer is, functional programming keeps the developer sane!
Let's see this with an example. Suppose that we have a Car class with a public property Color and two public methods Run() and Stop() which does whatever internally necessary to 
run the car

![Car](/posts/.../class-diagram.png)

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


We could see how stateful programming burdens the developer. We have 3 problems here:

1- As an author of SomeOtherfunction how will I know if the Run method is called? I need to look up the caller's code thus it's an **extra burden** to the developer to find it out. 
One might argue, we could add an IsRunnig property, however, nothing forces me at compile time to check that. I could easily forget such a check. And not always
the property changed by the method be that much obvious and it would **burden** me to read the API docs for the car. 

2- What happens if I call Run twice. Will it throw an exception? Will it ignore? The only way to find out is either read the API or read the source. 
Hence we have another **burden** here. 

3- After SomeOtherFunction returns how do I know if the car is stopped or not.  Again we have to read the docs or the source and yet another **burden** here.

So you can see such a simple code brings a lot of responsibility to the developer that he has to track the state at various stages of the coding process.
How do we solve this problem? Well, the problem is about **state tracking** and if we didn't have the state in the first place, we wouldn't have that problem.

## The functional solution

So we want to get rid of the state. Perhaps a way to do so is to represent the state by using the types so instead of a single Car type. We could utilize two different types. **StoppedCar** and a **RunningCar**. Then we have a **run** function that is taking a **StoppedCar** and **RunningCar**

![Run-function](/posts/.../run-function.png)

Once we define such a run function, solve all three problems. First, we always know if the car is running:

```csharp
SomeOtherFunction(RunningCar car){
 //we know for sure the car is running
 //you cannot call run again since the code won't compile
}
```

And obviously, we cannot call the run function again since the code won't compile. And depending on the return type the caller will know what type has been returned.
Now as developers we have fewer things to track, fewer things to worry about. Now life is so easy or is it? 



## If functional programming is so good why it is not popular?

Well, the first thing to acknowledge is functional programming is not about syntax. It's a paradigm. Recently C# 9 has attained the record syntax, a record is a functional concept
and if you are not familiar with the functional paradigm, you might be confused about why we need records. You will only see unconvincing answers like records are good because they are immutable (the real answer is value semantics and referential transparency, whereas immutability is only a vehicle to achieve these). 
What I am trying to say is to get started with functional programming, you have to forget about most things you know about imperative programming. 
So it is literally baby steps again and it would take quite a while to master it. And that is one of the major reasons people shy away from functional programming.

In the imperative world we have statements that tell the compiler what to do:

- do this;
- do that;

Whereas functional programming is more like movie frames. Each frame is immutable and unchangeable but if we roll 24 frames per second. It creates the illusion of a movie.


Functional programming does the same. We never change the data but every time we need a change things we create a new instance of that type. And obviously, that's a lot of CPU cycles and memory allocation compared to the imperative approach and 30 years ago that really mattered. 30 years ago, your colleagues would say, "Whoa! Are you allocating 200 bytes just to change the status of a car?" But these days, it is **engineers are expensive and servers are not**. Now, your users are unlikely to notice a few hundred nanoseconds delay. But you could utilize the hardware power for the sake of programmer productivity. Indeed, immutability, functional paradigm even scales better when you have a  distributed multi-threaded environment. But these historical problems with functional programming allowed the imperative paradigm to flourish whereas people were skeptical with the functional programming.

Combined with the relearning process described, functional programmers are a minority. Having that said, today's challenges require complicated solutions, reactive programming
is going more and more popular. Probably one of the most famous and demanded javascript library is React and it's a brilliant example demonstrating how functional and reactive programming shines. Single-page applications are way more stateful than the backend apps since usually backend only serves the data relaying in and out from the database without holding the state. And many developers appreciated how React (and redux like solutions) helped them to scale their complicated applications. 

So there are challenges for functional programming but sit tight the future might be different.
