---
layout: post
title: "Procedures vs pure functions"
date: 2019-07-10 05:00:00 +0000
# categories:
permalink: /procedures-vs-pure-functions
---

## Basic syntax

Both types of **routines** (pure functions and procedures) _have_ to return a value:

```
fn -> i32 : 42

proc -> i32 : 42
```

When you need input arguments, you can't omit the parentheses surrounding them:

```
fn (a i32, b i32) -> i32 :
    a + b

proc (a i32, b i32) -> i32 :
    a - b
```

If you don't want to return anything in particular, an **empty tuple** `()` will do the trick. Actually, you can omit the return type, the empty tuple will be implied:

```
; Recommended (shortened) forms

let a = fn :
    ...

let b = proc :
    ...
```

Whether you do not want to omit the parentheses nor the return type, you can rewrite the previous routines like this:

```
; Complete forms (discouraged, just for the sake of completeness)

let a = fn () -> () :
    ...

let b = proc () -> () :
    ...
```

By the way, routines returning an empty tuple won't require its body to be an expression matching the return type:

```
; Legal

fn : 42
```

Nor an expression at all:

```
; Also legal

fn :
    let a i32
```

However, they still require a body. So, if you have to write a 'no-op' routine, you can use an empty tuple as well:

```
fn : ()
```

### Rationale and discarded alternatives

Initially, in the [original syntax](https://mikelcaz.github.io/yagnislang/first-bits), I didn't use any keyword at all. I added `fn` first to make routines more recognizable when read (`() -> () :` may seem confusing). Now, you can omit the unused parts when needed, so `fn :` is nicer to read and write.

Then, I wanted to remove entirely the parentheses. I discarded the idea, because a function with a single argument (especially if it returns an empty tuple) may would be misleading:

```
; This is a unnamed function which takes an i32 and returns ().
; But it _seems_ a function named 'a', which returns an i32.

fn a i32 :
        ...
```

## So what is the difference between procedures and pure functions?

> Pure functions always return the same output for a given input, without any observable side effect.

```
; This is allowed.
let add = fn (a i32, b i32) -> i32 :
    a + b

; This will raise an error at compile-time.
let foo = fn -> i32 :
    let n = get_state() + 1
    set_state(n)
    n
```

Yagnis is not a functional language, but this difference is relevant because it enables some special semantics and optimizations.

First, in some contexts, expressions are required to always be evaluated to the same value (even if the value itself is not known at compile-time):

```
; Legal
for each in 1..function(1, 2) :
    ...

; Not allowed
for each in 1..procedure(1, 2) :
    ...

; Workaround
let upto = procedure(1, 2) :
for each in 1..upto
    ...
```

Do you want to call the routine once each iteration, or it is expected to be called just at the beginning? With functions, both are the same. With procedures, use can use the workaround to achieve the latter. If you want the former, that is not actually a `for` loop. You can use `while` instead.

Second, the compiler is free to rearrange functions at will. Plus, any function without inputs, or those with inputs whose values are known at compile-time can be (and most probably will be) precomputed. Think of them like a kind of macro, but more powerful and with the type system built into it.

```
let one = proc -> i32 :
    1

let two = proc -> i32 :
    2

let add = fn (a i32, b i32) -> i32 :
    a + b

let main = proc :
    let rt = add(one(), two())
    let ct = add(1, 2) ; This one can be evaluated at compile-time!
```

And third, there are expressions that cannot be evaluated at runtime. For example, it has universal numeric types like Ada:

```
let a = 42

let b i32 = a

; The value of `a`, whose type is `integer`,
; _has_ to be computed at compile-time.
```

Knowing that, this is _only_ possible with functions:

```
; Note the universal `integer` type is used.
let add = fn (a integer, b integer) -> integer :
    a + b

let main = proc :
    let r = add(1, 2) ; This _has_ to be evaluated at compile-time.
```

## About type inference and function suitability

Just as a reminder, identifiers are declared this way:

```
let <name> [<type>] [= <value>]
```

The type is usually omited and inferred from the value. For instance, you could type:

```
let add fn (a i32, b i32) -> i32 =
    fn (a i32, b i32) -> i32 :
        a + b
```

But it is more idiomatic to write it this way:

```
let add = fn (a i32, b i32) -> i32 :
    a + b
```

The type may seem redundant. Actually, letting the programmer to be explicit with it is very useful. For instance, any function can be used in the context of the equivalent procedure type:

```
; Legal

let add = fn (a i32, b i32) -> i32 :
    a + b

let function fn (a i32, b i32) -> i32
let procedure proc (a i32, b i32) -> i32

function = add
procedure = add
```

Although all the special semantics will be disregarded (at least when called as a procedure), this can be used to pass callbacks as procedure arguments: the same 'caller' code can work with both of kinds of routines.

Finally, the compiler is completely able to tell you if a routine definition should be a function or has to be a procedure. It needs the programmer to tell it to other humans. If a `proc` could be implemented as a `fn`, the compiler will warn you (and may apply some of the optimizations as if it _actually_ were a function).

Also, the programmer can told the compiler that a routine must be a function, so if the contract is broken, the compiler will treat it as an error.
