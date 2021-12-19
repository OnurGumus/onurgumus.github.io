---

layout: default

title: The snapshot of a memory snapshot
description : "Understanding chrome/edge's memory snapshot-concepts"
date: 2021-12-19-00:00:00 -0000

comments: true

published: false

image: posts/2021-02-19-The-tail-of-a-recursion/y-comb.png

excerpt_separator: <!--more-->

---

#  The snapshot of a memory snapshot for Chrome & Edge

While the web apps slowly replacing the desktop apps, they are growing more and more complex which in turn they are becoming more memory intensive. 
Perhaps a decade ago a front end developer wouldn't much concerned about possible memory leaks but considering the complexity, analyzing memory leaks is becoming 
a necessary skill. For apps that run inside the browser in particular Chrome/Edge, your first line of defence (and perhaps last unless you attempt to debug v8 engine itself)
is the memory snapshot tool. And it is surprisingly easy to use. You open your developer tools go to memoryt tab and take a snapshot.
![take-snapshot](/assets/posts/2021-12-19-The-snapshot-of-a-memory-snapshot./take-snapshot.png)
<!--more-->

## The story of shallow size and retained size.
There are gazillion of tutorials on how to do memory analysis or things to avoid for memory leaks. In this post however, I'd like to discuss the simple looking but 
one of the most confusing aspect of a memory snapshot. That is the columns of snapshot table namely 'Distance', 'Shallow Size' and 'Retained Size'. While Distance and
![columns](/assets/posts/2021-12-19-The-snapshot-of-a-memory-snapshot/columns.png)

Shallow Size is fairly easy to grasp, the most critical one **Retained Size** is a different story. So in this post we will be doing some experiments to understand what these are and how could we utilize these columns. For this experiment, I'll be using Chrome Canary build, Version 99.0.4775.0 (Official Build) canary (x86_64)m to have the least bug free (ironic, I know) experience. If you are going to follow me along, I suggest you do the same and make sure there are no extensions installed or at least they are disabled as browser extensions can interfere with the analysis.


To have a controlled environment I'll be using **about:blank** url. Let's fire up our console from dev tools and create two classes. We type the following to the console:


```js
class FooA{}
class FooB{}
window.fooA1 = new FooA();
window.fooA2 = new FooA();
window.fooA1.fooB = new FooB();
```
![foo-bar](/2021-12-19-The-snapshot-of-a-memory-snapshot/foo-bar.png)


We have created to classes **FooA** and **FooB** and then we attached 2 instances of FooA to the window and then we attach an instance of FooB to a FooA. Here
we use **window** because window is a direct child of a GC root which will permanently hold these objects until we clean them. We don't want the objects to be garbage collected right after we closed the console.

The next thing we do is to clear the console, switch to memory tab, close dev tools and reopen it. Id o this as a general practice  to prevent the console itself not to interfere with my analysis but note that each time you close dev tools, you will be losing the previous snapshots if you have any unless yous saved them. So be careful if you do snasphot comparison.


Once the dev tools is reopen, click to takesnapshot button and once the snapshit is done search foo in the summary pane. Once we expand FooA and FooB then should look similar to below:

![foo-bar](/2021-12-19-The-snapshot-of-a-memory-snapshot/snapshot-1.png)

We can see our 2 FooA instances and a FooB instance. So let's try to understand what's going on here. 

## Distance
The distance value for FooA instances are 2. That's very natural because the distance refers to the shortest distance to the GC root. Earlier we mentioned the window object is a direct child of GC Root so window's distance is 1. Both FooA's are direct child of the window so their distances are 2. What about FooB, well it is the child of one of the FooA instance so the distance of it is 3. 

This is as in:

```js
GC Root ---1---> window ---2---> FooA ---3---> FooB

GC Root ---1---> window ---2---> FooA
```


A memory snapshot is like a graph and distance helps us to identify how far that object is distant from the GC root. Generally closer objects are more significant. But on the overall distance may not be a very interesting property.


## Shallow size
Shallow size of memory that is held by the object itself. It is rather hard to etimate this size because it is implementation specific and usually used for object's own description. For common objects, classes it's size is typically less than 100 bytes per object. In this particular example we see they are are 12, but you may see different values such as 28 or 52 and it can even change during the life time of the same object. Since the size is usally small Shallow size 
isn't usually a concern. But there are two main exceptions to this rule, for strings and array like objects they have their shallow size counted based on their lengths and it is failry common to see arrays and strings with large Shallow sizes. Also some dom elements also behave like an array internally and they can also 
have significant shallow sizes. Finally even the shallow size is small for regular objects, if you leak thousands of these objects, theses mall shallow size would add up to a big value. 


## Retained size

Now we have arrived the most important part of our story. Please read the definition carefully even read it multiple times because it may not be the thing
you were expecting: Retained size is the size of memory that is freed once the object itself is deleted along with its dependent objects that were made unreachable from GC roots.

In otherwords, if we ask GC the following question: How much memory we would gain at this point if GC runs now if this object wasn't not held by any other objects. If that particular object wasn't held by any other objects this object and all dependents which are solely relying this particular object would be cleaned and we retain that much amount of memory back and this is precisly the Retained size for our object.


In our example for the Foo B instance we see the Retained Size is 12. Obviously retained size can't be smaller than the Shallow size since that would be the minimal we would gain if the object is cleaned up and that's prececisly what happens here. 

![fooB](/2021-12-19-The-snapshot-of-a-memory-snapshot/FooB.png)

If we look at the properties of FooB as in above we see two particlar items. **__proto__** and **map**. __proto__ represents the prototype of this class and **map** is contains description and offsets of the properties (more on that later). Both of these properties are actually affiliated with the FooB class/type itself not with the instance. So in otherwords even if the FooB instance is garbage collector, the FooB type is still there since we declared it as class and we could create subsequent instances. Hence that's why __proto__ and map for this type is not retained and not included in the retains size. Therefore for FooB our 
retained size is the shallow size, 12.


If we look at the FooA instances however we see something a bit peculiar. The first FooA instance having retained size is 128 and second one's is 12. Well
I think you can see why the second FooA's retained size is 12 since it is the same case as above. Let's look at the first FooA:


![fooB](/2021-12-19-The-snapshot-of-a-memory-snapshot/FooA1.png)

For FooA we have a the retained size of 128. How where does the this number come? Well obviously we have 12 bytes for the shallow size. The thing is since we have executed the following line ```window.fooA1.fooB = new FooB();``` this caused some disturbance in object's description mapping. Because we attached a property to FooA 
