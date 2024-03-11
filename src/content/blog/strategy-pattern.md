---
title: 'How to write extensible code using the Strategy Pattern'
description: 'Here is how I refactored my Telegram chatbot to use the strategy pattern'
pubDate: 'March 11 2024'
heroImage: '/strategy-pattern.png'
---

I've always been curious about what it meant to write 'clean' or 'extensible' code. I've watched a few videos about **S.O.L.I.D Design principles** and **design patterns** but in my initial research, it was just too abstract for me to know how and when to use it.

Thankfully, a few things clicked when I recently refactored my Chatbot code to be more extensible using the strategy pattern. You can find the previous article on how to make the Chatbot [here](https://david-chiang-blog.netlify.app/blog/telegram-bot/).

This article seems like a simple tutorial on how to implement the strategy pattern. But **I actually want to convey the mindset to creating better code quality and explore what may have been the mental triggers that led to me understanding these concepts.**

I believe if I understand this, then it would help me effectively mentor other developers. But if I also know what led to those break though moments of understanding, then I can intentionally re-create those mental triggers when continuing to learn more senior coding concepts in the pursuit of gaining professional code quality.

<hr>
<br />

## When to refactor to the strategy pattern

*"Strategy is a behavioral design pattern that lets you define a **family of algorithms**, put each of them into a **separate** class, and make their objects **interchangeable**."* - [refactoring.guru](https://refactoring.guru/design-patterns/strategy)

Ok, let's say that you have a function that takes in a string called `input`. Based on that string, it **executes different algorithms/logic**.

```javascript
function stringParser(input) {

    // algorithm 1
    if (input === "Hello") {
        console.log('Hello')
    }

    // algorithm 2
    if (input === "Good Bye") {
        console.log("Bye")
    }
}
```

Here it console logs different strings based on the word input. Now what if you were told that the function needed to parse more words? You could just use a switch statement like this and add more algorithms.

```javascript
function stringParser(input) {
    switch (input) {
        case: "Hello"
            console.log('Hi');
            break;
            
        case: "Good Bye"
            console.log('Bye');
            break;

        case: "Good Morning"
            console.log('Morning');
            break;
    }
}
```

This is fine for small functions and if the code would never have to change. But let's say instead of a console.log(), each case requires an algorithm of 50+ lines of code. If developers would have to add more implementations for words over time, you can imagine how the main function would just get too big and harder to navigate. 

**What's important here is thinking about how developers would have to interact with my code and if it makes it easier for them or not.**

For example, if developers also wanted to **isolate and test** just ONE of the implementations, you wouldn't be able to conveniently do it with just a switch/case or if statements.

### Here's where the strategy pattern comes in
* The idea with the strategy pattern, is that if you have a family of algorithms, then we want to put them each into separate classes which are called `strategies`. 

So the different algorithms which were in our switch cases are now **outside of our main function** which we call the `Context`. I have specifically done so by storing them in an object called `strategies`.


```javascript
// STRATEGIES
const strategies = {
    "Hello": () => console.log('Hi'),
    "Good Bye": () => console.log('Bye'),
    "Good Morning": () => console.log('Morning')
    // Add additional strategies here
}

// CONTEXT
function stringParser(input) {
    strategies[input]();
}
```

*Here I have used functions and objects instead of classes, but it still follows the same principles as the strategy pattern and arguably makes it easier to read.*

Now the strategies are placed **outside** of the `stringParser` function which is our Context.
The key thing here is that if you wanted to create a **new strategy**, then you would simply **add it to the strategies object**. While this happens, `stringParser` would **never be touched** and this is an example of the `Open/Closed` principle in **SOLID Design**.

## Let's take it a step further
We still haven't solved the previously asked question: 
 **"how then would you isolate just one of the form parsing implementations so that you can test it?"**

Basically, you just have to imagine how you would refactor the algorithms to make each strategy available as an exportable function to import into a jest testing suite.

**Instead of our old implementation here**
```javascript
// So instead of
const strategies = {
    "Hello": () => console.log('Hi'),
    "Good Bye": () => console.log('Bye'),
    "Good Morning": () => console.log('Morning')
}
```

**We now have testable strategies**

```javascript
// REFACTORED TESTABLE STRATEGIES
// This is a separate file - strategies.js
export function hello() {
    console.log('Hi');
}

export function bye() {
    console.log('Bye');
}

export function morning() {
    console.log('Morning');
}
```

```javascript
// This is a separate file - strategy.js
import { hello, bye, morning } from './strategies';

export const strategies = {
    "Hello": () => hello(),
    "Good Bye": () => bye(),
    "Good Morning": () => morning()
}
```

It might be a bit harder to see the benefit of this as the new code seems to be longer. But if you were to imagine 50+ lines of code in `bye`, `hello` and `morning` functions, then it makes more sense to decouple them from the `strategies` object and placing them into their own files.
This follows the **single responsibility principle**.

We don't aim to follow the principles for the sake of **best practice**, but rather the clear **value** it provides.
It is the value of the pattern which I hope to communicate so that both of us know when to use it in a way that helps others instead of adding unecessary verbosity to our code.

## A summary through a final comparison

Our `stringParser` function has gone from this:
```javascript
// OLD IMPLEMENTATION
function stringParser(input) {
    switch (input) {
        case: "Hello"
            console.log('Hi');
            break;
            
        case: "Good Bye"
            console.log('Bye');
            break;

        case: "Good Morning"
            console.log('Morning');
            break;
    }
}
```

To this
```javascript
import strategies from './strategy'

function stringParser(input) {
    strategies[input]();
}
```

Pretty neat. It has nicely decoupled testable strategies and is now closed for editing but open for extension.

## What contributed to understanding this to begin with?
I had a goal of creating a design system and the way I planned on doing that was by **reverse engineering the source code** of the Next.js design system for [Australian Agriculture Gov](https://github.com/agriculturegovau/agds-next). 

When I read through it, I assumed that something as atomic as a button component would be just one file, but instead I saw dizzying network of linked util and style files. But as I began to read through each of the files, I asked myself why the engineer would write it in this way and from here I began to get a **feel** for how to create appropriate separation of concerns. 

This **feel** is what helped become a trigger to identify how I could refactor my chatbot code and I began to think whether I could separate certain blocks of code and what benefit it would provide. From here, my previous high level knowlege of the strategy pattern came to mind and I was able to implement it.

## Practical steps to help learn these patterns as a self-taught dev
* First understand a **high level** overview of the concept.
* Read through **source code** of production level code and analyse each **block of code** at a glance to see what is its main **purpose**.
* If there are **methods that you don't understand**, then it would be good to see their implementation and always ask why they are used. (I used ChatGPT for this)
* From your own high-level understanding of the concepts you learnt, try to **implement them in code at the most basic level**. The abstract code of the strategy pattern shown in this article is an example of it. This was the key point that led me to the **knowledge** for how to implement the pattern in a code base. 
* BUT! Once I applied it to a real code base in my chatbot, it was at this point that I felt the **VALUE** of it, where it becomes a clear trigger to use it the next time I read a code base.

This break through in learning is what I was so excited to share about and I hope that these practical tips may help someone whenever they come across design principles and patterns they need to learn and apply in their code.
