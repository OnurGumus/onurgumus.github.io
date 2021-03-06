---

layout: default

title: Demystifying the volatile keyword
description : "A demonstration of how Out of Order Execution causes problems"
date: 2021-02-07-00:00:00 -0000

comments: true

published: true

image: posts/2021-02-07-Demystifying-the-volatile-keyword/latency.png

excerpt_separator: <!--more-->

---

# Demystifying the volatile keyword


After reading Albahar's excellent free chapter for his book on [Threading](http://www.albahari.com/threading/part4.aspx) I have decided to dig the concept of out of order execution further.

Please take a look at the below code and try to guess the output:

<!--more-->

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

namespace MemoryBarriers
{
    class Program
    {
        static volatile int x, y, a, b;
        static void Main()
        {
            while (true)
            {
                var t1 = Task.Run(Test1);
                var t2 = Task.Run(Test2);
                Task.WaitAll(t1, t2);
                if (a == 0 && b == 0)
                {
                    Console.WriteLine("{0}, {1}", a, b);
                }
                x = y = a = b = 0;
            }
        }

        static void Test1()
        {
            x = 1;
           // Interlocked.MemoryBarrierProcessWide();
            a = y;
        }

        static void Test2()
        {
            y = 1;
            b = x;
        }
    }
}
```
In the above code, we have defined 4 fields x, y, a and b which are initialized to 0, don't mind the volatile keyword now as we will see **volatile** keyword makes no difference here. Then we create 2 tasks calling Test1 and Test2 respectively and wait for both tasks to complete. Once both tasks are complete we check if **a** and **b** are still 0 and if so we print their values. Finally, we reset everything back to 0 and re-run the same loop over and over again.

If you run the above code, preferably in the release mode, you will observe the output as being many **0, 0**'s are printed. But that is rather surprising.  Let's see why.

In Test1 we set x to 1 and a to y and Test2 sets y to 1 and b to x and so we have four statements racing in 2 threads.Here are the possible scenarios:

### Test1 then Test2:
```
x = 1
a = y
y = 1
b = x
```

In this scenario we assumed Test1 finished before Test2, then the final values will be

x = 1, a = 0 , y = 1, b = 1

### Test2 then Test1:
```
y = 1 
b = x
x = 1
a = y
```
then

x = 1, a = 1, y = 1, b = 0


### Test2 between Test1:
```
x = 1
y = 1
b = x
a = y
```

x = 1, a = 1, y = 1 and b = 1

### Test1 between Test2
```
y = 1
x = 1
a = y
b = x
```


x = 1, a = 1, y = 1 and b = 1

### Test1 interleaving Test2

```
x = 1
y = 1
a = y
b = x
```
x = 1, a = 1, y = 1 and b = 1

### Test2 interleaving Test1
```
y = 1
x = 1
b = x
a = y
```

x = 1, a = 1, y = 1 and b = 1


I think we have covered all possible scenarios yet whichever race condition scenario occurs, it looks that it is impossible to have a and b
both simultaneously being zero once both tasks are completed, yet miraculously our if condition still triggers. How is that so?

## Explanation

The answer lies within [Out Of Order Execution](https://en.wikipedia.org/wiki/Out-of-order_execution). Let's have a look at the disassembly codes for Test1 and Test2. And if you are not familiar with x86
assembly, don't worry, it is actually very simple and I have added comments to the relevant parts.

```assembly
#MemoryBarriers.Program.Test1()
    #function prolog ommitted
    L0015: mov dword ptr [rax+8], 1  # upload 1 to memory location of 'x'
    L001c: mov edx, [rax+0xc]        # download from memory location of 'y' to edx
    L001f: mov [rax+0x10], edx.      # upload from edx to memory location of 'a'
    L0022: add rsp, 0x28.           
    L0026: ret

#MemoryBarriers.Program.Test2()
    #function prolog
    L0015: mov dword ptr [rax+0xc], 1 # upload 1 to memory location of 'y'
    L001c: mov edx, [rax+8].          # download from memory location of 'x' to edx
    L001f: mov [rax+0x14], edx.       # upload from edx to memory location of 'b'
    L0022: add rsp, 0x28
    L0026: ret
```

Please note that I have chosen to use "upload" and "download" words in the comments
instead of the conventional read/write terminology. For the unfamiliar ones, in order to read a value from a variable and assign it to another memory location we must read it
to the CPU registers, in this case, **edx** being used for that purpose only then we can assign it to the target variable. The CPU operations are very very fast, such that reading or writing to the memory  appears to be really slow compared to what we do in CPU. And this is precisely why I have used the words "upload" and "download". These days, reading from and writing to the memory behaves almost as if we are uploading to or downloading from a remote web service. It is really that slow comparatively.

Here are some latency numbers for 2020 (ns being nano-seconds)

```
L1 cache reference: 1 ns
L2 cache reference: 4 ns
Branch mispredict: 3 ns
Mutex lock/unlock: 17 ns
Main memory reference: 100 ns
Compress 1K bytes with Zippy: 2000 ns
Send 2K bytes over commodity network: 44 ns
Read 1 MB sequentially from memory: 3000 ns
Round trip within same datacenter: 500,000 ns
Disk seek: 2,000,000 ns
Read 1 MB sequentially from disk: 825,000 ns
Read 1 MB sequentially from SSD: 49000 ns
```
[source](https://colin-scott.github.io/personal_website/research/interactive_latency.html)

So accessing the main memory is 100 times slower than accessing something within the CPU cache, which behaves similarly to web proxy servers. 

As a developer  you are developing an app and let's say if you have some independent upload
download operations to some web service. How would you design such calls? You would parallelize them to save time! And that's precisely what the CPU does. Our beloved CPU
is smart enough to figure out that these upload and download operations do not affect each other **per thread** and in order to save from time, it parallelizes them meaning the execution order of these instructions can be changed depending on which one will finish first. Hence we have **out-of-order** execution. 

However, we already know that our code is not as working as we wanted. Because the assumption our CPU made was wrong. That assumption was only made based on per-thread basis dependency checks. Unfortunately, the CPU cannot take multiple threads into consideration when deciding instruction independence and for such cases, we have to help it manually.


## Why volatile does not help

Perhaps one of the most confusing concepts is the **volatile** keyword which is also available in C++ and Java. It is known roughly to prevent race conditions like above. There is also a separate discussion for the volatile keyword being redundant on Intel x86 and Intel x64 due to [strong memory model](https://en.wikipedia.org/wiki/Memory_ordering) these CPUs offer. But that's not true and volatile does make a difference to some applications on .NET even if the program runs under those CPUs and we will briefly see why.

If we go back to our example above we observe despite the fact that we have marked our fields as volatile, it didn't help. 
Why is that so?

If we look at what volatile keyword does we would discover, assuming we have a pair of consecutive upload and download operations,
volatile instructs the CPU that:

1- Make sure the download operations from a volatile variable will be completed before executing the next upload/download instructions.

2- Make sure the preceding upload/download instructions will be completed before the current upload operation to a volatile variable is executed.



But volatile does not forbid a download operation to a volatile variable to be completed before the preceding upload instruction is finished. The CPU is free to execute both
in parallel and continue with whichever finishes first. And that's precisely what's happening here as volatile keyword does not prevent:
```assembly
    mov dword ptr [rax+0xc], 1 # upload 1 to memory location of 'y'
    mov edx, [rax+8].          # download from memory location of 'x' to edx
```
to turn into this

```assembly
    mov edx, [rax+8].          # download from memory location of 'x' to edx
    mov dword ptr [rax+0xc], 1 # upload 1 to memory location of 'y'
```

Hence x is read before y is updated since the CPU thinks these instructions are independent (which is an incorrect assumption due to other threads).

Having that said volatile keyword isn't totally useless in other cases. Firstly, it still prevents re-ordering of downloads with downloads and downloads with uploads when they occur consecutively . Also, not all CPUs have strong memory guarantees like Intel. At least, we have to consider AMD and ARM CPUs. Finally, again a sample is taken from 
[Albahari](http://www.albahari.com/threading/part4.aspx), slightly modified version:

```csharp
using System;
using System.Threading;
public class C {
    bool completed;
    static void Main()
    {
      C c = new C();
      var t = new Thread (() =>
      {
        bool toggle = false;
        while (!c.completed) toggle = !toggle;
      });
      t.Start();
      Thread.Sleep (1000);
      c.completed = true;
      t.Join();        // Blocks indefinitely
    }
}

```

If you run the above code with a release build, it will block indefinitely even with Intel CPUs. This time CPU is not guilty but the culprit is the JIT optimization. If
you convert:

```csharp
bool completed;
```

to 
```csharp
volatile bool completed;
```

Then it will work expectedly. Let's see the disassembly for non-volatile and volatile cases:

```assembly
#non volatile

    L0000: xor eax, eax
    L0002: mov rdx, [rcx+8]
    L0006: movzx edx, byte ptr [rdx+8]
    L000a: test edx, edx
    L000c: jne short L001a
    L000e: test eax, eax 
    L0010: sete al
    L0013: movzx eax, al
    L0016: test edx, edx # <-- Pay attention to here
    L0018: je short L000e
    L001a: ret
 ```
 
 and 
 ```assembly
 #volatile
 
    L0000: xor eax, eax
    L0002: mov rdx, [rcx+8]
    L0006: cmp byte ptr [rdx+8], 0
    L000a: jne short L001e
    L000c: mov rdx, [rcx+8]
    L0010: test eax, eax
    L0012: sete al
    L0015: movzx eax, al
    L0018: cmp byte ptr [rdx+8], 0  <-- Pay attention to here
    L001c: je short L0010
    L001e: ret
```

See the lines stating "Pay attention to here". Those lines are actually where we do the check for completed:

```csharp
        while (!c.completed)
```

When volatile is not used, JIT caches the completed value to **edx** and then just uses the edx register to test if completed is changed. 
But when we use volatile we force JIT not to cache and then every time we need to read the value it access directly to memory
as in **cmp byte ptr [rdx+8], 0**

JIT does this since we have seen memory access is 100+ times slower and just like CPU, JIT with good intentions but also naively caches our variable
such that it cannot detect that we consume the same variables from different threads. And volatile cures the problem here, forcing JIT not to cache. I also like the volatile keyword
for documentation purposes. It communicates to the developer that this variable is meant to be used from different threads.


## Memory barriers to the rescue

Let's go back to our original case. We have seen those half "fences" from **volatile** are not helpful. So what can we do? Enter **Memory Barriers**
A memory barrier is a special **lock** instruction to CPU that forbids instructions from re-ordering across the barrier. Hence the program would
behave expectedly but will be dozens of nano-seconds slower as a drawback. 

In our sample I have commented a line:

```csharp
   //Interlocked.MemoryBarrierProcessWide();
```
If you uncomment the line the program will work expectedly. But this is rather a peculiar call. The actual memory barrier in .NET is issued with:
```csharp
   Interlocked.MemoryBarrier();
```
But if you just use a MemoryBarrier you will still see **0, 0**'s coming out but at a slower rate! The reason for that is since we have two threads, for the actual solution we have to put two memory barriers into both of these functions. Only then the program will behave properly. 
However as a developer, in a real-life project, could you be sure a third method like this doesn't exist? Such a code using the same variables could be hiding in a different library and we may not be aware of it. So  **Interlocked.MemoryBarrierProcessWide()** is your **nuke from the orbit** button. It makes sure such reorderings for those variables will never be a problem "process-wide". Having that said extra care is required. I would expect, this to be super slow. Still, I'd take the slow code than the wrong code. Still if you have full control over your code, individual memory barriers rather than a process-wide one (or a lock keyword essentially does the same thing) is preferred. 


## Final words

We made a brief journey to the mystical world of memory barriers and the volatile keyword. I admit these concepts do not have much place in day-to-day development but still could be handy if you are dealing with high-performance multi-threading scenarios. Finally, this entire post is based on one of the 50 topics in my video course:

[![50 Things You've Been Doing Wrong in C# and .NET Core](https://static.packt-cdn.com/products/9781789804683/cover/smaller)](https://www.udemy.com/course/50-things-youve-been-doing-wrong-in-c-and-net-core/)
