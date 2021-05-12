---
layout: post
title:      "“This” and implicit binding in JavaScript"
date:       2021-05-12 03:10:29 +0000
permalink:  this_and_implicit_binding_in_javascript
---

After learning JavaScript for the first time, I really thought I had mastered the concept of the “this” keyword. While I was studying for the final review at the end of my software development program, I didn’t even bother to go over it. So, you can imagined how surprised I was when I was asked to explain “this” in the review, and found myself totally lost for words (well, in my defense, I had SOME words… just not enough!) Thinking you understand something and being able to explain it to someone else are two very different things!

First of all, why do we use “this”?
“This” allows us to reuse functions within different contexts. There are many situations in which you’ll want to be able to call the same function on different objects and get the desired result every time. By using “this”, we can make sure that our functions always work properly, even when they are called on a variety of objects.

How do we know what “this” is referring to?
In order to find out what “this” is, we need to pay close attention to where the function is being invoked. “This” works in a few different ways but the most common is through implicit binding, so that’s what we’re going to focus on today.
Here’s an example:

```let dog = {
    breed: "Boston Terrier",
    age: 11,
    name: "Loki",
    petDog: function() {
        console.log("{this.name} is happy!")
    }
};```

Now, to invoke the function petDog(), we’ll do this:

```dog.petDog();```

Look to the left of the dot: “dog”. With implicit binding, we look to the dot just prior to the function being invoked. Then, we look to the left of that dot. Whatever is to the left of the dot is the object that “this” will refer to inside our function.
Now, in our console we should see:

```Loki is happy!```

It worked! petDog() knew that this.name would refer to the name of the object “dog”, because “dog” was just to the left of the dot.
Now… what if there’s more than one dot? Let’s add on to our “dog” object.

```let dog = {
    breed: "Boston Terrier",
    age: 11,
    name: "Loki",
    petDog: function() {
        console.log("{this.name} is happy!")
    }
    owner: {
        name: "Lucia",
        whoseDog: function() {
            console.log("This dog belongs to {this.name}.")
        }
    }
};```

Now, to find out whose dog this is, we’ll do the following:

```dog.owner.whoseDog();```

What we WANT to see is this:

```This dog belongs to Lucia.```

Will it work? YES, it will! “this” in this context will refer to the dog’s owner. We know this because we look to the dot just prior to the function, then directly to the left of that dot.

There you have it! Now we know what “this” is referring to through implicit binding of a function. There are other techniques for finding “this”, such as explicit binding, but that’s a story for another post.
