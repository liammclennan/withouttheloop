---
title: Why C#'s `var` keyword is not always a good idea
author: Liam McLennan
date: 2021-05-31 20:32
template: article.jade
---

The C# `var` keyword is used to declare a variable whose type is inferred by the compiler. 

It's good because it means we can go from:

```
LongLongTypeName thing = new LongLongTypeName();
```

to

```
var thing = new LongLongTypeName();
```

That is shorter and hence easier to read, while conveying the same amount of information to both the compiler and a person reading this code. 

Alternatively, C# 9 allows

```
LongLongTypeName thing = new();
```

which, as Robert Frost said, is also great, and would suffice. 

![ice](ice.jpg)
Photo by Erin Mckenna

All writers should consider their audience, and this also applies for writers of code. Once upon a time we wrote code for the compiler, but for the last thirty years or so the next programmer who will have to read your code has been the more important audience (the compiler is clever). Which brings me to the situation where type inference, and the `var` keyword, is a problem:

```
var thing = gimmeSomeThing();
```

The compiler, in its omnipotent wisdom, knows exactly what is going on, because it can see the `gimmeSomeThing` function and know what type it denotes. The humble programmer however is left in the dark. Merely reading this code it is impossible to know what is the type of `thing`. A clever IDE can tell me, or I can go and inspect the `gimmeSomeThing` function, but all of this slows me down and creates one more thing that I need to keep track off. 

Hence, the takeaway:

> Write code for the reader, not the compiler. Don't use `var` unless the infered type is obvious. 
