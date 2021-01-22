---

layout: default

title: "Does Your Code Pass The Turkey Test? (JS Edition)"

date: 2021-01-23-00:00:00 -0000

comments: true

published: false

image: posts/2021-01-23-Does-your-code-pass-the-Turkey-test/cqrs.png

excerpt_separator: <!--more-->

---

# Does Your Code Pass The Turkey Test? (JS Edition)

The credit for the title goes to Jeff Moserware and his excellent [post](http://www.moserware.com/2008/02/does-your-code-pass-turkey-test.html) which he authored in 2008. He basically discussed the dangers of improper localization and took a clever approach, stating <<“The Turkey Test.” It’s very simple: will your code work properly on a person’s machine in or around the country of Turkey?">>. Actually Turkish alphabet is based on Latin letters but there are few interesting things related with the Turkish alphabet that, 
Jeff proposes that if your code works fine in Turkish culture, then chances are good it is resillient to possible localisation problems. Perhaps this is a bit over statement as there are too many different languages and alphabets. However unless we have a specific focus in a particular language or have dedicated people working on each language, in practice it's next to impossible to have bug free localization. In that aspect, I beleive the Turkey test is a low effort way to do basic
smoke test on localization.

In his post Jeff discusses the localization problems mostly from .NET point of view. In this post, I will try to Jeff's footprints but from a JavaScript of view.
