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
![y-comb](/assets/posts/2021-02-19-The-tail-of-a-recursion/y-comb.png)
<!--more-->

## The story of shallow size and retained size.
There are gazillion of tutorials on how to do memory analysis or things to avoid for memory leaks. In this post however, I'd like to discuss the simple looking but 
one of the most confusing aspect of a memory snapshot. That is the columns of snapshot table namely 'Distance', 'Shallow Size' and 'Retained Size'. While Distance and
Shallow Size is fairly easy to grasp, the most critical one **Retained Size** is a different story
![stack1](/assets/posts/2021-02-19-The-tail-of-a-recursion/stack-frame1.png)
## Base case and subproblem
