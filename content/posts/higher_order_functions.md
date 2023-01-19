---
title: "Higher-Order Functions"
summary: Functions as Arguments
date: 2023-01-18
weight: 7
aliases: ["/papermod-features"]
tags: ["Computer Science"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/higher_functions.jpg" # image path/url
    alt: "mathematics functions" # alt text
    caption: "mathematics functions" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
This year I set out to learn a new programming language just for fun and I've always been curious about web development so I chose JavaScript.

In the first few weeks I learned about syntax, DOM, primitive values, objects, scope, etc. but one thing that caught my attention was the concept of **Higher Order Functions**.

Disclaimer: maybe you use it but you didn't know the name.

### Higher-Orders Functions

#### What is a Higher-Order Function?
> In mathematics and computer science a **higher-order function** is a function that does at least one of the following:
>- Take one or more function as argument.
>- Return a function.


### How is that possible?
Well this happen because the functions are considerer as **First Class Objects** that meaning that the functions are consider as objects and they can do anything an object do.

The properties of a first class functions:
>- A function is an instance of the Object type.
>- You can store the function in a variable.
>- You can pass the function as a parameter to another function.
>- You can return the function from a function.
>- You can store them in data structure such as hash, tables, list, ...

### Why are importants?
Well, higher order functions simplify our code and keep it DRY.
Let's do some examples to demonstrate this. In or first example take the following function.

#### first order function
Suppose we have the **copyArrayAndAddOne** function that copy and array of integer and add 1 to each element.
```shell
function copyArrayAndAddOne(array) {
    const output = [];
    for (let i=0; i < array.length; i++){
        output.push(array[i] + 1);
    }
    return output
}
const myArray = [1, 2, 3];
const result = copyArrayAndAddOne(myArray);
```

What if now we want to copy array and rest by 1.
```shell
function copyArrayAndRestOne(array) {
    const output = [];
    for (let i=0; i < array.length; i++){
        output.push(array[i] - 1);
    }
    return output
}
const myArray = [1, 2, 3];
const result = copyArrayAndRestOne(myArray);
```