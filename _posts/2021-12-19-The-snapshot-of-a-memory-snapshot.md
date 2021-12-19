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


To have a controlled environment I'll be using **about:blank** url. Let's fire up our console from dev tools and create two classes:


```js
class Foo{}
class Bar{}
window.foo = new Foo();
Foo {}
window.foo.bar = new Bar();
```
![foo-bar](/2021-12-19-The-snapshot-of-a-memory-snapshot/foo-bar.png)

Then clear the console, switch to memory tab, close dev tools and reopen it. Id o this as a general practice  to prevent the console itself not to interfere with my analysis but note that each time you close dev tools, you will be losing the previous snapshots if you have any unless yous saved them. So be careful if you do snasphot comparison.


Once the dev tools is reopen, click to takesnapshot button.
