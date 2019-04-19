---
layout: post
title: "Why type inference is part of Yagnis"
date: 2019-04-19 09:20:00 +0000
# categories:
permalink: /update/about-yagnis-type-inference
---

_What problems Yagnis tries to resolve through type inference?_ 

Compare these ficticious snippets:

```
// C++

AnExtremelyVerboseClassName a;
```

vs:

```
// Java

AnExtremelyVerboseClassName a = new AnExtremelyVerboseClassName();
```

The second one is also extremely redundant! However, there is a good use case for this:

```
// Java again

SuitableInterface b = new AnExtremelyVerboseClassName();
```

And don't get me wrong: I'm not against being explicit. Besides, I rather prefer a syntax that _allows_ being explicit (whether or not being optional is a defect, is up to you) than another which insists to get in the middle all the time. Especially when you already _were_ explicit, and yet you have to bear its terrible ergonomics.

Here a sane version of what Java does:

```
// Kotlin

var a = AnExtremelyVerboseClassName()
var b : SuitableInterface = AnExtremelyVerboseClassName()
```

> Java 10 includes the `var` type name. But Java should disappear altogether, now that Kotlin is here.

😈

Although Yagnis probably won't be 'object oriented', this example introduces an essential idea: _its purpose of using type inference is not to save typing, but to make you able to lessen meaningless code_, because it is bad to read or write.

Note how as a result of all this, there will also be situations where Yagnis _could_ save you from having to type adding some syntactic sugar, but won't do it anyways, because what can be omited is still meaningful for human beings!
