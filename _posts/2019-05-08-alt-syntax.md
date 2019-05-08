---
layout: post
title: "Alternative syntax"
date: 2019-05-08 18:45:00 +0000
# categories:
permalink: /alt-syntax-i
---

Following the spirit of my [previous entry](https://mikelcaz.github.io/yagnislang/holy-war-editor-part-i), I will propose a different syntax for the language.

See the [original proposed syntax](https://mikelcaz.github.io/yagnislang/first-bits).

## Meaningful indentation, colon character repurposed

Well, at least without the help of square brackets:

```
block :
    statement1
    statement2
```

Expressions and statements starting blocks use a colon character to separate it and to add some visual redundancy.

## A `def` keyword

The original syntax for defining identifiers...:

```
name : [ type ] = value
```

... would be replaced with this one:

```
def name [ type ] = value
```

In other words, compare before and after:

```yagnis
a := 42

def a = 42
```

Don't confuse it with Python's `def`, which is aimed to define functions.

This also would make those [`for` loops](https://mikelcaz.github.io/yagnislang/loops-part-i) much more fun:

```yagnis
for each i8 in 0 <= .. < 256 :
    // each ...
```

Note the paralelism between `def`+`=` and `for`+`in`.
