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

## Dotted and dotless I

Well the obvious question, why "Turkey"? The primary reason to take use the Turkish alphabet that the alphabet has two "I" letters. One with dot and one without.
Namely upper case  "I" and "İ and lower case "ı" and "i". There is even a dedicated Wikipedia [article] on it (https://en.wikipedia.org/wiki/Dotted_and_dotless_I)

The challenge with these "I"' usually occurs when you try todo a case insensitive equality comparison. I being Turkish have been very badly burned from this.
One particular case I remember is I was developing an application for a Bank. We have done extensive testing of the application on local development environment.
But we deployed the application to the Bank's prod machines, it immediately crashed.

## Problems with Oracle (fixed long time ago)
After 13 hours of investigation (at that time my debugging skills wasn't so sharp so I mostly relied on the logs), I realized my query was in the form of
"select description from ..." and Oracle database will always capitalize the column names in it's results returning a column with "DESCRIPTION". 
The developers of ODP.NET was aware of this inconsistency so as a work around they were calling ToUpper to match the column names from your query. However,
if your code runs in within Turkish culture then **ToUpper** call will make the column "DESCRİPTİON" and boom! your code would bail out with a cryptic exception.
Luckily only 3 years later Oracle has fixed the issue by using **ToUpperInvariant** instead of **ToUpper**


##JavaScript world

Having reiterated the problem on .net let's explore the situation with the browsers. The browsers take the current culture from the operating system on MacOS
and Linux. And on windows depending on the browser they may obey "preferred langauge" settings. Perhaps one of the easiest way to make case-insensitive but 
safe comparisons is to use ToLocalUpperCase and ToLocalLowerCase


```JavaScript
const city = 'istanbul';

console.log(city.toLocaleUpperCase('en-US'));
// expected output: "ISTANBUL"

console.log(city.toLocaleUpperCase('TR'));
// expected output: "İSTANBUL"

const cityU = 'İSTANBUL';

console.log(cityU.toLocaleLowerCase('en-US'));
// expected output: "i̇stanbul"

console.log(cityU.toLocaleLowerCase('TR'));
// expected output: "istanbul"
```
Notice the interesting dot on lower case en-us tranformation. I have no idea what it is.


