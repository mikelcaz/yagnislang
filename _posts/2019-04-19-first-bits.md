---
layout: post
title: "First bits"
date: 2019-04-19 09:15:00 +0000
# categories:
permalink: /update/first-bits
---
Trying to design the syntax, I have come to understand there are two laws that, to my mind, describe the process very well. You can think of them as Newton's Laws (but they are not the same, because Newton's Laws are just approximations of the reality). The first one is:

- I. The decision \#`x` you are going to take will haunt you at `x`+1 for all `x`.

Sometimes happens that, after a lot of trial and error, and undoing things that do not fit, you discover you made a good decision (or at least one not too harmful). Thus, the first law has no impact anymore. Hence the second law:

- II. If you found that the first law not longer applies, it is still applying, and the monster you have created has grown into such giant mess you can't even reason how screwed you are.

At this point you are smiling, while your 'tiny' star at your back is reaching the critical mass and density to become into a black hole. Regrettably, it's just a metaphor: if it were a real black hole, it would suck its creator in after that.

It is also estimated this is the very process that every language goes through (except in those which are a giant mess from the beginning, like Java).

This is exactly why every feature added and decision taken could be reverted. Take this as a disclaimer. While the first draft of the language specification is made, all is regarded as **highly unstable**. Even after its completion, the language could be changed if needed.

Let's begin.

## Square brackets for blocks

Although I found interesting how Python uses the indentation level to mark the compiler where code blocks are, it seems to me that the indentation level itself is represented (and handled by most editors) inconveniently beyond repair.

I have some ideas to overcome this problem, and I will probably talk about this in a later post, but in short, I'll keep those brackets for now:

```
block {
    statement1
    statement2
    ...
}
```

(Instead of):

```
block:
    statement1
    statement2
    ...
```

## Constant declaration/assignment

**Sean Barrett's syntax**:

```
name : type = value

// The same with type inference.
name := value
```

Note how the colon (`:`) means some _name_ is **declared**, and leaves room to specify a _type_ at the same time.

However, I believe this syntax won't last long, mainly because the colon couldn't be used in other places where is more needed (and [this could be a greater problem in the near future](#square-brackets-for-blocks)).

Also, I am not satisfied with its unintuitive placement in diferent kinds of declarations.

## Variable declaration/assignment

```
name : var type = value

// The same with type inference.
name : var = value

// Declaration and initialization in different lines.
name : var type

name = value
```

I'm not very fond of this, but the `var` keyword would be the **variability permission modifier**. Note how even whether the _type of the value_ has the variability permission, the _value itself_ loses it by default when used as rvalue. A single `var` can be used to override this behavior while still using type inference.

I considered using `var` just before the value:

```
// Stupid idea.
name := var value
```

But then would be inconsistent, and a pain switching between using type inference and the explicit type (omitting the type cleanly is one of the advantages of Barrett's syntax, as is in other languages). I know the First Law is doing its thing here, and I'll regret it in the near future...

The reasons why I'm not using the exact Jai syntax is:

1. Declaring constants should not be more difficult than variables. I don't mind the other way around.
2. Some types are defined based on other types, with their own variability permissions.
3. I prefer constant arguments in functions, but it would be great if it would be a default that could be overridden.

For `2`, consider C++:

```
int const * const ptr;
```

For `3`, consider that languages like Java, Kotlin or C# use objects always through _references_, and not the object value itself.

In Kotlin, arguments (references) are constant, but the value can be mutable depending on the interface used. Yagnis cannot be done like this.

## Function declarations

Mostly the same as in Jai, but a little bit more _Rust-y_.

```
square := (x: i64) -> i64 {x * x}
```

Note how functions can be used as constants or variables:

```
foo := square
bar : var = square
```

Or being completely anonymous without changes:

```
(x: i64) -> i64 {x * x}
```

No `return` keyword here (almost everything should be an expression).

For the people thinking this is inconsistent with the 'regular Sean's syntax', it could be surprising to found it is the same:

```
square : (x: i64) -> i64 = (x: i64) -> i64 {x * x}
```

The 'type' must be present in the definition to declare the arguments. For now, the **return type** must be explicitly told in the definition too. Most of the time, the **function type** of the constant can be omitted, as is redundant, and will be inferred instead. That's one of the points of using type inference after all.

## Explicit end of statements

I'd rather avoid it at all, but we'll see:

```
    statement1
    statement2
```

(Instead of):

```
    statement1;
    statement2;
```

More about this in a later post.

## Nested block comments

Nothing important, but there are many programmers that does not use block comments because (forgive the redundancy) 'commenting them out' is a pain. I think it would be even more intuitive if block comments could be nested:

```
// I am a single-line comment.

/*
    I am a block comment.
*/

/*
    // I'm nested!
    /*
        Me too!
    */
*/
```

## Alignment considered harmful

As I said in the part about the use of square brackets, I think both spaces and tabs don't do a very good job. But still, we need them. I won't start a crusade after deciding which one is the least bad option. It will be matter of each programmer's culture after all. I guess the compiler could emit a `warning` if you don't set which one will be used in the configuration file... Never mind.

**But please**. Don't treat your code (or any other kind of _plain text_) as if it were punched in cards.

```
foo       helloworld
myowntype goodbye
```

I.e.,

```
foo🕳🕳🕳🕳🕳🕳🕳helloworld
myowntype🕳goodbye
```

> Every space has been replaced with holes (U+1F573). It may have a different width. If you rely on monospaced fonts to align your code, you probably deserve it.

Code is text, and nobody prevents the user from changing to proportional fonts. No one should be forced to use monospaced fonts, it's a silly requirement (and a few times unmaintainable). Please stop making ASCII art out of your code.

Of course, as tabs relies on tabstops, they should not be used to align code either. So don't bother with aligning your code at all, nor write it in a punched card, because it won't work as you intend in your colleagues's computers.

Apparently aligned code:

```
int hello
www world
```

(Could become unexpectedly into):

int hello  
www world

> Note how you need a proportional font to see the difference.

---
This is all for now!
