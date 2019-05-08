---
layout: post
title: "No 'if' nor 'switch', just 'when'"
date: 2019-05-08 18:50:00 +0000
# categories:
permalink: /expressions-when
---

Many programming languages has `if-else` and `switch` constructions in one way or another. Usually, they overlap in functionality:

```kotlin
// Kotlin's if-else.

if (a == 0) {
    // ...
} else if (a == 1) {
    // ...
} else {
    // ...
}
```

vs:

```kotlin
// Kotlin's when (the language version of 'switch').

when (a) { // 
    0 ->
        // ...
    1 ->
        // ...
    else ->
        // ...
}
```

To keep it simply, I will pick just one _clean syntax_. The compiler should take care of optimizations, lefting human biases aside.

Considerations about `switch`:

- Intended to check equality against constants.
- Several cases are expected and nicely laid out.
- Unflexible (ranges not allowed).
- Introduces two nesting levels gratuitously.

Considerations about `if-else`:

- Succint when checking a single condition.
- You must remember to add `else` before `if` / change to `elif` in sequences (beware refactors!).

Personally, I prefer to mark the end of `if-else` sequences rather than mark I want to extend it every time (I found the latter error-prone).

## The 'when' expression

```
when <condition> :
    <branch>
when <condition> :
    <branch>
...
end
```

A `if-else`-like example could go like this:

```yagnis
def x =
    when a < 42 :
        42
    else :
        a
    end
```

A `switch`-like example would be like:

```yagnis
when charpoint == 8#0A# :
    is_newline()
when charpoint < 0 :
    is_broken()
when 0 <= charpoint < 128 :
    is_ascii()
else :
    is_unicode()
end
```

Unlike in Kotlin, those are particular cases, not special ones. A train of `when`s does what `else if`/`elif` achieves in other languages.

Nesting example:

```
when a == 1 :
    when b == 42 :
        foo()
    end
    bar()
end
```

Note how these examples use the [alternative proposed syntax](https://mikelcaz.github.io/yagnislang/alt-syntax-i), where indentation is meaningful.
