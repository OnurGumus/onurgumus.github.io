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
that is on the stack after the function returns will be considered as garbage. So for such cases, we use the heap area. Typically we as the runtime to create a 
new object and the runtime


## The problems with recursion

So by following 2 simple rules, by specifying a base case and assuming a solution function for the sub problem exists we can solve most recursive problems.
Having that said there couple of problems with recursion. 

1- Every recursive call is a function call:
In many CPU architecture each function call needs some sort ceremony. In general we have to do following
* since the flow has to return to the caller, we have to store the current return address into stack. As you probably know, typically the application memory is 
divided into a stack area for each thread and one or more heap area. 


## The tail of a recursion


## Continuation Passing Style


## Functional birds


