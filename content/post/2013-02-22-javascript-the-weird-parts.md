---
author: minko_gechev
categories:
- Internet
- JavaScript
- Programming
- Programming languages
- Strange syntax
date: 2013-02-22T00:00:00Z
tags:
- Internet
- JavaScript
- Programming
- Strange syntax
title: JavaScript, the weird parts
url: /2013/02/22/javascript-the-weird-parts/
---

To say that JavaScript is becoming more and more popular is such a typical and boring way to start such an awesome post&#8230;Anyway, JavaScript is becoming more and more popular each day&#8230;There&#8217;s client-side JavaScript with awesome API, you can do whatever you wish with it &#8211; write 3D games, stream video and audio, process files&#8230;There&#8217;s also a server-side JavaScript which is also awesome (I guess awesome will be a common word for this post). JavaScript is exiting language with big expressiveness power. It is object-oriented with many functional elements. It has prototype-based inheritance but if you wish you can implement inheritance in at least three different ways, which have countless subtypes&#8230;

But with the big power it comes the big responsibilities&#8230;For people who are new to the language, new to the functional and prototype paradigms, it&#8217;s very hard to understand in depth the language itself, especially if they have used primary Java, C#&#8230;or PHP&#8230;In this post we won&#8217;t talk about the strange things for the developers who are new to JavaScript. In this post we&#8217;ll talk about strange things in the language&#8217;s syntax and semantics. So, let&#8217;s begin!

## Immediately-Invoked Function Expression

This is not uncommon thing in the language (we&#8217;ll look at far more strange things&#8230;) but I decided to include it because I haven&#8217;t met it in another languages and it has few interesting sides. That&#8217;s the common syntax for these functions:

{{< highlight JavaScript >}}(function foo() {
    //some stuff
}());
{{< / highlight >}}

There are few more variations, for example using the [&#8220;dog balls&#8221;][1] syntax:

{{< highlight JavaScript >}}(function foo() {
    //some stuff
})();
{{< / highlight >}}

or function expression:

{{< highlight JavaScript >}}var foo = function () {
    //some stuff
}();
{{< / highlight >}}

Or&#8230;

{{< highlight JavaScript >}}var foo = function () {
    //some stuff
}();
{{< / highlight >}}

or&#8230;

{{< highlight JavaScript >}}var foo = (function () {
    //some stuff
}());
{{< / highlight >}}

Or (last one, I promise):

{{< highlight JavaScript >}}var foo = (function () {
    //some stuff
})();
{{< / highlight >}}

The first strange thing here is that:

{{< highlight JavaScript >}}var foo = function () { 
    //some stuff
}();
{{< / highlight >}}

will work but:

{{< highlight JavaScript >}}function foo() {
    //some stuff 
}();
{{< / highlight >}}

Gives: SyntaxError: Unexpected token )

These functions are very useful and very common for the language, anyway, it looks a bit strange at first look&#8230;especially when we use:

{{< highlight JavaScript >}};(functions () {
    //Some stuff
}());
{{< / highlight >}}

And yes the use of ; is a good practice when you create plugins, for example. It&#8217;s useful to tell the interpreter explicitly that you are starting a new statement. Imagine that you&#8217;ve developed a new plugin and developer wants to use it in her project. As you know the semicolons in JavaScript are optional, so she might has something like:

{{< highlight JavaScript >}}function foo() { alert(arguments[0]); } foo{{< / highlight >}}

That&#8217;s code that do nothing but declare a single function named foo. If we add the plugin we get:

{{< highlight JavaScript >}}function foo() { alert(arguments[0]); } foo{{< / highlight >}}

{{< highlight JavaScript >}}(function () { return 'foo'; }());{{< / highlight >}}

This will invoke the foo function with single argument the returned result from the IIFE and cause unexpected results (alert foo in this case). Putting ; before your IIFE declaration will solve the problem.

## Parentheses position matters

As we speak for functions&#8230;Let&#8217;s look at the return statement and the object literals:

{{< highlight JavaScript >}}function foo() {
   return
   {
      foo: 'bar'
   }
}

function bar() {
   return {
      foo: 'bar'
   }
}

typeof foo() === typeof bar(); //false
{{< / highlight >}}

*So what do you mean, the object type is not equals to itself or what?!* Actually nope, the first function foo returns undefined&#8230;And yes, that&#8217;s because we&#8217;ve put the open parentheses on new line. The return statement &#8220;does not see&#8221; that it has something to return (; are optional in JavaScript) so it returns nothing&#8230;The second function, bar, acts as expected because in the same line where is the return statement we have {. The return statement &#8220;knows&#8221; that there begins declaration of new object literal which it should return so that&#8217;s why the returned types are different&#8230;

That&#8217;s one of the reasons the codding style in JavaScript to points out that it&#8217;s good practice to put the opening parentheses on the current line.

## Typeof null

One common mistake is:

{{< highlight JavaScript >}}if (typeof obj === 'object') {
    alert(obj.prop);
}
{{< / highlight >}}

And what happens if obj is null? TypeError: Cannot read property &#8216;prop&#8217; of null. Yes, null is object and we have to be careful about that fact. You can modify the upper code in few ways, for example:

{{< highlight JavaScript >}}if (typeof obj === 'object' && obj !== null) {
    alert(obj.prop);
}
{{< / highlight >}}

## Equal is not always equal

Let&#8217;s look at this situation:

{{< highlight JavaScript >}}1 == 1; //true
'foo' == 'foo'; //true
[1,2,3] == [1,2,3]; //false
{{< / highlight >}}

this is absolutely usual thing &#8211; one is equal to one and foo is foo. The array [1,2,3] is not equal to [1,2,3] because it&#8217;s simply a reference type. That&#8217;s common for most languages. But what if we:

{{< highlight JavaScript >}}new Array(3) == ",,"; //true
{{< / highlight >}}

Wow, true again?! Yeah&#8230;it&#8217;s true and there&#8217;s even a logical reason for that&#8230;Let&#8217;s look at the string representation of new Array(3):

{{< highlight JavaScript >}}new Array(3).toString(); //",,"
{{< / highlight >}}

JavaScript implicitely converts the values in the both sides of the == operator to strings and compares them. This is one more thing we should be careful for. Instead of == it&#8217;s better to use ===. Three times equal will check also the type of the operands:

{{< highlight JavaScript >}}new Array(3) === ",,"; //false
{{< / highlight >}}

## Comparing objects

If I show this paragraph to my Calculus professor he may perform a suicide&#8230;  
Lets create two objects and keep their references in two variables:

{{< highlight JavaScript >}}var o1 = {},
    o2 = {};
{{< / highlight >}}

We have two different references so:

{{< highlight JavaScript >}}o1 == o2 //false
o1 === o2 //false
{{< / highlight >}}

Nothing weird at all&#8230;from the elementary math we know that:

{{< highlight JavaScript >}}a >= b ^ a <= b <=> a = b
{{< / highlight >}}

Lets try this in JavaScript:

{{< highlight JavaScript >}}o1 >= o2 //true
o1 <= o2 //true
o1 != o2 //true
o1 !== o2 //true
{{< / highlight >}}

mmm, strange, isn't it? For further reading check <a href="http://www.ecma-international.org/ecma-262/5.1/#sec-11.8.3" target="_blank">the algorithms</a> used for comparing variables.

## Sum of objects and arrays

That's my favorite topic! When we try to use operation with typical operands numbers, with operands which type is not number we usually get NaN.

{{< highlight JavaScript >}}1 * 'foo'; //NaN
1 * 2; //2
'foo' * 1; //NaN
'foo' + 1; //'foo1'
{{< / highlight >}}

All is as expected, the last example shows simple string concatenation. If we use:

{{< highlight JavaScript >}}{} + {} //NaN
{{< / highlight >}}

we get NaN as expected ({} is an object literal). If we try to sum two empty arrays...

{{< highlight JavaScript >}}[] + []; //""
{{< / highlight >}}

Pretty strange, right? Here the + is concatenation again...We concatenate two empty strings and we get an empty string. But what happens if:

{{< highlight JavaScript >}}[] + {}; //[object Object]
{{< / highlight >}}

We get [object Object] which is {}.toString(). That's strange...

{{< highlight JavaScript >}}1 + 1 === 1 + 1 //true
1 + 2 === 2 + 1 //true
[] + {} === {} + [] //false
{{< / highlight >}}

We know that the plus is commutative operation but the third example shows as that it's not always true.

{{< highlight JavaScript >}}[] + {} //[object Object]
{} + {} //0
{{< / highlight >}}

I think that this is one of the strangest things I described since the beginning of the post...With these strange properties we can do even stranger things, for example:

{{< highlight JavaScript >}}+!+[]
{{< / highlight >}}

+!+[] is absolutely valid JavaScript syntax. Before I explain what happens first I must mention that undefined, \`\`, '' are all false in JavaScript. So now the explanation... As we saw [] + [] is "", so we might think for [] as for "". As I mentioned "" is false so !"" is true. The prefix plus converts the boolean value to numer, so +true === 1. With this approach we can get some text like:

{{< highlight JavaScript >}}([]+![])[+!+[]+!+[]+!+[]][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]](([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]])())[([]+!![])[+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]]](([][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]](([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]])())[([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]]](([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]])[+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[])+(+!+[]+!+[]+!+[]+!+[]+!+[]))+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]](([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]])())[([]+!![])[+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]]](([][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]][([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]](([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]])())[([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+![])[+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]]](([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]])[+[]]+(+!+[]+!+[]+!+[]+!+[])+(+!+[]+!+[]+!+[]))+([]+![])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]]()+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+![])[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+(+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]](+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[])+([]+[][+[]])[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+[][+!+[]])[+!+[]]+([]+([]+[])[([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+[][+!+[]])[+!+[]]+([]+![])[+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+!![])[+!+[]]+([]+!![])[+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]+([]+{})[+!+[]]+([]+!![])[+!+[]]])[+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]+!+[]]
{{< / highlight >}}

This is valid and executable code in JavaScript, you can try it in your console. There are few small projects which translates text to these characters, for example [translate.js][2] and [siggoscript][3].

But the strange things don't stop here...

## "Functional arithmetic"

Look at this expression:

{{< highlight JavaScript >}}[]+(-~function(){}-~function(){}-~function(){}-~function(){}) + (-~function(){}-~function(){})
{{< / highlight >}}

This is one more valid and executable JavaScript code and once again it's "strangeness" is connected with the implicit conversion to string:

{{< highlight JavaScript >}}~(function () {}).toString(); //-1

[]+ //Means that this will cast everything after it to string i.e. it will concatenate everything after it with the empty string
(-~function(){}-~function(){}-~function(){}-~function(){}); //"4"
(-~function(){}-~function(){}); //"2"
{{< / highlight >}}

## Final words

These are few of the strange things of JavaScript's syntax and semantics. Few of them can be dangerous in our daily work so we should be careful, another are funny and good to know but all of them are awesome! :-)

 [1]: https://www.youtube.com/watch?v=eGArABpLy0k
 [2]: https://gist.github.com/mgechev/4188098
 [3]: https://github.com/dherman/siggoscript