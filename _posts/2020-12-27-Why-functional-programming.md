---

layout: default

title: "Why functional programming : Purity"

date: 2022-12-27-00:00:00 -0000

comments: true

published: false

image: posts/2020-12-20-your-domain-driven-project-Lean-software-development/lean-1.png

excerpt_separator: <!--more-->

---

# Why functional programming?

This is a question that has been answered many times. A quick google search on "Why functional programming" or "why functional programming matters" will yield quite 
insightful results. Having that said I still beleive there is more to add this topic. And what would be my answer to this qustion? One word: **purity** (or Statelessness). 
Before delving into purity let's dissect the **functional** in functional programming.

When I heard the term functional programming, I was very confused. I said, "I have been using C, we already have functions there. JavaScript also has functions, may OOP languages
have functions disguised as methods of objects. Why this new term?" Well, my first mistake was it wasn't a new term. The conceptual origin of functional programming
**lambda calculus** is developed as early as 1930 by Alonzo Church and the first functional programming language LISP was available by late 50's. So functional programming wasn't
something new. My second mistake was the confusion of the term "functional". The word function here does not represent the procedures which we call functions in the programming 
languages but rather they refer to mathematical functions just like Sine and Cosine functions. And it is **purity** that makes these mathematical functions different 
than functions in the programming programming languages. That is given the same input the function will always yield the same output without any side effects. 

For example sin(90) will always yield 1 as a result, no matter when or how many times you call. It also has no side affects, that is, it is not altering anything you care.
So that's a good example on for a pure "function" hence the term "function"al programming (Ok, perhaps a more formal description would involve lambda calculus or category theory
but this approach I have given is simpler to take and it is correct).

So we have seen how functions in functional programming are different than procedureal programming which won't constrain your functions to be pure. One may argue 
functional programming is good for mathematics and science or finance but how about real world line of business applications? Indeed that's precisely 
where functional programming shines. But before that let's review some misconceptions about functional programming.

## Misconceptions about functional programming

When we ask developers, what are the characteritics of good code or a good programming language, it is likely you would hear good code (or language) is related to

- Good code is more maintainable.
- Good code leads to better readability.
- Good code tends to be shorter and concise.
- Good code is testable and dependencies are isolatable.
- Good code is extendable.
- Good code is less buggy.

Functional programming is not code nor it is a language but a paradigm. You could probably do functional programming with any language. However some languages embrace it 
as a first class citizen (or even dictate it) wheras some languages tend to challenge you or too verbose to go with functional programming. So above items about good code
are not relevant to functional programming paradigm. I would like to emphesize these once more as in the following:

- Functional programming **does not** necessarly lead to more maintainable code nor does it aim that.
- Functional programming **is not** necessarly more readable.
- Functional programming can easily turn to spaghetti if one is not careful.


Wow, if functional programming doesn't give me those aspects I care about my code why do I care? That's a good question.

## Why should I care about functional programming?

We have discussed functional programming does not necessarily lead to better code. So why bother? The answer is, functional programming keeps the developer sane!
Let's see this with an example. Suppose that we have a Car class with a public property Color and two public methods Run() and Stop() which does what ever internally necessary to 
run the car

![Car](/posts/.../class-diagram.png)

Now we do:
```C# 
var car = new Car();
car.Run();
... //do other things.
SomeOtherFunction(car);
//Did SomeOtherFunction stopped ther car? I don't know I have to track it.
... //other things

SomeOtherFunction(Car car){
  //hmm have I run the car before? I have to track it.
  car.Run();
  //now I have how run the car perhaps more than one time. What's gonna happen?
}
```


We could see how stateful programming burdens the developer. We have 3 problems here:

1-) As an author of SomeOtherfunction how will I know if the Run method is called? I need to look up the caller's code thus **extra burden**. 
One might argue, we could add an IsRunnig property, however nothing forces me at compile time to check that. I could easily forget such a check. And not always
the property changed by the method be that much obvious and it would **burden** me to read the API docs for the car. 

