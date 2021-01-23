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
Jeff proposes that if your code works fine in Turkish culture, then chances are good it is resillient to possible localisation problems in other cultures using Latin alphabets. Perhaps this is a bit over statement as there are too many different languages and alphabets. However unless we have a specific focus in a particular language or have dedicated people working on each language, in practice it's next to impossible to have bug free localization. In that aspect, I beleive the Turkey test is a low effort way to do basic
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

## JavaScript Internationalization objects

JavaScript also offers several built in objects for internationalization.

* Intl
* Intl.Collator
* Intl.DateTimeFormat
* Intl.ListFormat
* Intl.NumberFormat
* Intl.PluralRules
* Intl.RelativeTimeFormat
* Intl.Locale


Although I appreciate the effort, I personally find these API relatively limited although the api extends towards interesting problems like pluralization.
However if you study the api, it's mostly geared towards formatting and displaying but relatively weak when it comes to parsing. First let's visit our original
problem. Is there another API we could compare "İSTANBUL" with "istanbul" case insensitively and find them equal?

```JavaScript
const a = 'İSTANBUL'; 
const b = 'istanbul'; 

console.log(a.localeCompare(b, 'en', { sensitivity: 'base' }));
// expected output: 0

console.log(a.localeCompare(b, 'tr', { sensitivity: 'accent' }));
// expected output: 0

console.log(a.localeCompare(b, 'en', { sensitivity: 'accent' }));
// expected output: 1

```

It seems to work expectedly.  What about the dotless I case ?
Let's compare 'IĞDIR' and 'ığdır' which are considered equal in Turkish case-insensitively.

```
const a = 'IĞDIR'; 
const b = 'ığdır'; 

console.log(a.localeCompare(b, 'en', { sensitivity: 'base' }));
// expected output: -1
console.log(a.localeCompare(b, 'tr', { sensitivity: 'base' }));
// expected output: 0

console.log(a.localeCompare(b, 'tr', { sensitivity: 'accent' }));
// expected output: 0
console.log(a.localeCompare(b, 'en', { sensitivity: 'accent' }));
// expected output: -1
```
Notice here you must set the culcure to 'tr' otherwise it won't work.

## Date and Time
Most languages have different format for dates so if we have **05/01/2021** does it mean January 5 or May 5?. Some people would argue we should use 
**2021-05-01** but then your users will complain. How about 01 MAY 2021, this is good but then you have to localize MAY. So pick your poison. Worse, 
languages like Turkish use "." instead of "/" as a date seperator.

Further more there is no builtin way to parse such strings to convert JavaScript date objects. You have to rely on external libs like moment.js and
there goes another 75 kb to your bundle.

Finally I should add that, **1PM* is shown as *13.00* in Turkish culture. One more thing to be careful

##Numbers

Well it's a well known fact that different languages use different symbols for thousands and decimals seperator. But being uncareful with number 
localization has dire consequences. In Turkish thousands is split by dot "." and for the decimal seperator "," is used. For example
*1.234,56* is one thousand two hundred thirty four point fifty six. In .NET if you try pasing in Turkish culture

```csharp 
double.Parse("12.34") 
```

What do you think the output will be? Well it is **1234** as .NET parser completely ignores the thousands seperator even if that wasn't your intention.
What about the situation in JavaScript? You are completely on your own. parseFloat is just using en-US culture and that's fixed.

That's not all. How many digits are there? 10 you say? [Raymond Chen disagrees](https://devblogs.microsoft.com/oldnewthing/20040309-00/?p=40333) 
Well at least from .NET stand point arabic digits are also considered as digits. In JavaScript, you are on your own again.


## Currency
Well the simple fact is in Turkish and many other languages the currency symbol is written after not before as in **100$**.

## Percentage symbol 

In Turkish the percentage symbol comes after e.g **50%**


## Other alphabets

Let's see some pecuilarities with other Alphabets. One might think "Z" is probably the last letter in Latin alphabets. Well enter nordic langauges

Danish and Norwegian Alphabets:
A	B	C	D	E	F	G	H	I	J	K	L	M	N	O	P	Q	R	S	T	U	V	W	X	Y	Z	Æ	Ø	Å

As you can see there are three letters coming after Z.

In Icelandic alphabet Z is obsoleted!!!

Some alphabet like Dutch has digraphs like "IJ" is sometimes considered a single letter. Some fun facts taken from the [wikipedia article](https://en.wikipedia.org/wiki/IJ_(digraph)):

* In Dutch primary schools, ij used to be taught as being the 25th letter of the alphabet, and some primary school writing materials list 'ij' as the 25th letter of the alphabet.

* When a word starting with IJ is capitalised, the entire digraph is capitalised: IJsselmeer, IJmuiden.[5]

* On mechanical Dutch typewriters, there is a key that produces 'ij' (in a single letterspace, located directly to the right of the L). However, this is not the case on modern computer keyboards.

* In word puzzles, ij often fills one square.


## Conclusion

Despite it's widespread use I have a feeling that JavaScript is not on par with backend langauges like .NET when it comes to localization capabilities. 
What is the solution? You probably have to rely on third party libraries. An alternative route might be to use Blazor which brings .NET Runtime into browser.
That way you can get benefit of all Globalization goodies of .NET but your app can still reside in the browser. Feel free to share your thoughts in the comments section.
