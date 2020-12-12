---

layout: default

title: "How to start your domain driven project?"

date: 2020-12-15-00:00:00 -0000

comments: false

excerpt_separator: <!--more-->

---

So you have recieved 100 pages of the requirements doc and a presentation has been made about your new project. Now as a developer your job is to translate the business requirements
to your code. But where do we start? Architecture you say? System diagrams? Project structure? How about starting from discovering and tranlating the domain? I assume you are familiar
or at least or the term Domain Driven Design.

#Ubiquotous? 

Domain driven design is rolled around the concept called Ubiquotous language. Unbiqtoutous language is the common language we share, we we talk among other stake holders. 

[Ubiq-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/ubiq-1.png
Sharing a common language is extremly important to mitigate misunderstandings. To better understand it let's briefly jump to aviation and the worst air disaster ever happened.

##Tenerife disaster. 


[tenerife-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-1.png

The primary reason causing this unfortunate disaster was communication error. The communication with the control tower and pilot has involved non aviation related terms like "OK"
on top of that the weather was bad, the radio communication was problematic and the Pilot was in hurry. 


[tenerife-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-2.png

Aviation industry has learnt from these mistakes and that's why they are strictly sticking to their own ubiqoutous language. 

In the case of development, a misunderstanding of requirements in a long term project may surface many months later causing irreversable problems especially. Perhaps we
developers should also learn from aviation industry and embrace our ubiqoutous language.

The key point of ubiqoutous language is that not only it is shared among different stake holders but also it is part of the source code. 
We may have the requirements but requiremnts, but without proper digestion and translation of requirments they are just pages with text. Furthermore, you will often see, 
there are many gaps in the requirements only to be discovered at a later phase. Unless you are Kasparov like business analyst, it is quite difficult to see what is going to 
come up after 20 moves later. So there we have the discovery process. 

##Discovery 
The discovery process allows us to create stories and it brings up our fundemental parts of our very unquitous language. Nouns and Verbs! 

[tenerife-1](/assets/posts/2020-12-15-How-to-start-your-domain-driven-project/tenerife-3.png
But how do we perform the discovery? One of the ways is event storming. In event storming, we have an actor such as a user or a customer, but it is optional. 
We can, however use the following methodology to convey our event storming session. 

At first we have some state
then some action is taken
and finally an event is fired and we land in another state.
