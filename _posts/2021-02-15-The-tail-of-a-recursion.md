---

layout: default

title: The tail of a recursion
description : "An journey into recursion"
date: 2021-02-15-00:00:00 -0000

comments: true

published: false

image: posts/2021-02-15-The-tail-of-a-recursion/latency.png

excerpt_separator: <!--more-->

---

# The tail of a recursion

When I was younger I used to be scare of recursion. Reading them was fine, but writing a recursive algorithm from scratch was rather complicated for me as
I'd always fallback to the impertive counter-parts. But after sharpening my skills a bit and doing reading, now I have the opposite impression. Now 
recursion is rather easy and imperative ones look more difficult. So if you are challenged by the recursive algorithms like me, read on. I might change your idea.



## Base case and sub problem

The simple fact it most  typical recursive algorithms has two parts. A base case, acts like an exit condition and a part that solves only sub part of the problem. Let's see an example with a simple sorting algorithm. Please note that our goal is not to write most efficient code here and the code is pseudo

Step 1: Write the base case

```pseudocode
procedure sort(array)
  if array.Lenght = 1 
    return array

```

Here we have defined our base case, the exit condition. If the array is with 1 element there is nothing else required we could just return 
the  array itself. 

Step2: Assume a solution function already exists but only for a subset of the problem

```pseudocode
procedure sort(array)
  if array.Lenght = 1 
    return array
    
  first_element, rest = split_first_element(array)
  result = sort_already_works(rest)

```

So we assumed there is already a working sort function called **sorting_already_works** and we pass the *tail* part of our array. Since that function is 
only working for smaller arrays than what we have. Now since the element is sorted, all we have to do is to insert the first element to correct location.
This is rather easy you can check rest until a greater value is found and just insert **first_element** one before


```pseudocode
procedure sort(array)
  if array.Lenght = 1 
    return array
    
  first_element, rest = split_first_element(array)
  result = sort_already_works(rest)
  
  insert_sorted(first_element,result)
  return result
 
```

and finally we replace **sort_already_works** with **sort**


```pseudocode
procedure sort(array)
  if array.Lenght = 1 
    return array
    
  first_element, rest = split_first_element(array)
  result = sort(rest)
  
  insert_sorted(first_element,result)
  return result
 
```


Let's look at another example where we would like to find out all permutations of given array:

```pseudocode
procedure find_permutations(array)
  result = new List()
  if array.Lenght == 1 then
    result.Add(array)
    return result
    
  first_element, rest = split_first_element(array)
  result = find_permutations(rest)
  
```


You can see we have followed the exact same pattern as before. We just accumulate results into a List. Now the only-thing required to insert **first_element** to every position of all returned lists:

```pseudocode
procedure find_permutations(array)
  result = new List()
  if array.Lenght == 1 then
    result.Add(array)
    return result
    
  first_element, rest = split_first_element(array)
  result = find_permutations(rest)
  
  for each result_item in result do
     for i = 1 to array.Length do
        insert_at_index(first_element,result_item, i)
     
  return result
```

It's easy peasy. 

## The story of stack and heap

Before diving further, let's make some brief statements into stack and heap.

Typically when you start a process, the OS creates some threads for you to execute your code. Each thread is given some contiguous memory usually between
1 to 4 MB but that is entirely configurable. In the stack area we are allowed pop and push things. When we push things the stack grows and we pop things, the stack becomes smaller. The end of the stack is bookmarked by a stack pointer in the CPU. If we want to clean the stack, all we 
have to do is to move the stack pointer to an earlier location and we assume anything beyond the stack pointer is garbage. This way we don't have to clean
the local variables. 

Whereas if we want some sort of data to remain even after we return from a function call stack won't help us since, the stack pointer will rewind and anything
that is on the stack after the function returns will be considered as garbage. So for such cases, we use the heap area which is fairly large usually up to Terrabytes in a x64 system with virtual memory support. Typically we as the runtime to create a 
new object and the runtime puts it to some known location and return us a reference. Then either a Garbage Collector tracks those items in the heap or it becomes our duty to track them.

When we make a function call, we have to to tell some details to the callee, such as the parameters passed and the return address which denotes where the callee
should return when it is done. So we have to have a common protocol between the caller and callee. As a convention typically these parameters and return address are put into CPU registers and stack. For example, in x64, return address is pushed the stack and the first 4 parameters are sent to rcx,rdx,r8 and r9 registers
(assuming they are not floating point) and if we have more than 4 parameters those extra parameters are also pushed to stack. And depending on the agreement between the caller and callee , either caller and callee cleans the stack when the call is done. In general this is called creating a stack frame.


## The problems with recursion

So by following 2 simple rules, by specifying a base case and assuming a solution function for the sub problem exists we can solve most recursive problems.
Having that said there couple of problems with recursion. 

1- Every recursive call is a function call: So as we have covered above, there is a lot of ceremony going on at each function call. That means
function calls are significantly slower than regular loops, which affects recursive functions in a negative way. 

2- The danger of overflowing stack: Unlike heap by default we are only given few MB stack space. So if your recursive call is too deep, then you might overflow your stack which typically crashes your application. Many operating systems and platforms allow you to define the stack size beforehand but usually it is not 
a good idea to mess with these default values unless you really know what you are doing.



## The tail of a recursion

So we have seen there are some severe problems with recursion. Yet all hope is not lost. Many compilers are smart enough to figure out your intend and can
provide you some optimizations which are very handy. One of those optimizations is **tail call optimization**. If you call another function as a last statement
before the return and you do not depend on any local variables in the current function then the compiler can skip above ceremony and make the call as a direct continuation of the current function and can skip creating a stack frame and get-away with a jump statement. If the call is recursive then we have a specialized form of tail call optimization called tail recursion.

More interestingly some functional programming languages like F# doesn't have a **break** keyword to exit from a loop. So it is not possible to exit in the middle of a loop.

```fsharp
let printTillN (n:int) =
  let rec loop x = 
    if x <= n then 
        printf "%i" x
        loop (x + 1)
  loop 0
  
printTillN 10
```

So we are trying to print the numbers till N and since we don't have a break keyword in F#, we decided to use a loop with recursion. Now two questions:
1-) Isn't this in efficient, just as we are calling another function for everytime we loop hence creating a stack frame
2-) Are we in the danger of stack overflowing?

Well appearantly the answer is No.
If we convert this code to C# by using (sharplab.io)[https://sharplab.io/#v2:EYLgxg9gTgpgtADwGwBYA0AbEAzAzgHwxgBcACABygEsA7YgFSowwDlSAKGkW4gSlIC8AWABQpUkTKwwEiBHKkEg0qPHiq2RaQA8A0jVLEAFjAOq1Fyj00AiAKRUbi8xfEY5C9koDUpAIy85u7ypAAM5qJWdIzMbH7hIkA==]

We see it is actuall compiled to the following code:

```csharp
 while (x <= n)
        {
            PrintfFormat<FSharpFunc<int, Unit>, TextWriter, Unit, Unit> format = new PrintfFormat<FSharpFunc<int, Unit>, TextWriter, Unit, Unit, int>("%i");
            PrintfModule.PrintFormatToTextWriter(Console.Out, format).Invoke(x);
            int num = n;
            x++;
            n = num;
        }
```


So the compiler magically turned out recursion into a while loop hence no stack overflows.



## Continuation Passing Style


## Functional birds


