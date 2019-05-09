---
layout: post
title: "The End of the Bloodiest Holy War, Part I"
date: 2019-04-22 18:35:00 +0000
# categories:
permalink: /holy-war-editor-part-i
---

> Disclaimer: I'm not by no means a Python expert. As a general rule, take everything with a grain of salt.
>
> This is a somewhat long post, take your time.

I have disregarded Python's indentation-based block syntax for Yagnis (at least for now).

To illustrate the problem, I put in place the foundations of a (not so) ficticious new text editor.

As a side effect, I ended the bloodiest holy war in history just for fun.

## Don't believe \[the whitespace\] lies

> The choice of indentation for grouping was not a novel concept in Python; I inherited this from ABC \[...\].
>
> Of course, I could have chosen not to follow ABC’s lead \[...\], but I had come to like the feature quite a bit while using ABC, as it seemed to do away with a certain type of pointless debate common amongst C users at the time, about where to place the curly braces.
>
> —[Guido van Rossum](https://impythonist.wordpress.com/2014/02/16/open-heart-with-guido-van-rosuuma-lost-interview-of-python-creator-part2/)

There is a lot ways of placing the curly braces. Some of the well-known ones are:

- Allman
- K&R
- One True Bracing (which variant? Ha!)

_No, this is not the holy war I meant_. But believe me, some programmers are really _ferocious_ with this. They will grow angry in no time ('_Don't-mess-with-my-style-thank-you_'), throwing any nearby blunt object at you.

Apart from that, Python has no redundancy between curly braces/keywords and indentation itself. In other languages, whether both contradict each other, they can mislead programmers. In Python, getting the indentation right is mandatory instead, because _code should be readable for human beings anyway_:

> \[...\]. As indentation also contains all the information for the compiler, to use both would be redundant. \[...\]. It has the advantage that Python programs tend to be uniformly and consistently indented, removing one hurdle to understanding other people's code. \[...\]. Those that get used to the Python way of doing things tend to start seeing curly braces as unnecessary line noise that clutters code. \[...\].
>
> [Python wiki](https://wiki.python.org/moin/Why%20separate%20sections%20by%20indentation%20instead%20of%20by%20brackets%20or%20%27end%27) as it was in April 20th, 2019.

Thus Guido van Rossum made his decision. And there are many Python programmers very fond of this.

Ironically, this began another holy war. Soon enough, people started to point out that **whitespace cannot be trusted**.

> \[...\]. On the other hand, 'the whitespace thing' is possibly the single biggest reason why some developers refuse to even try Python.
>
> [Python wiki](https://wiki.python.org/moin/Why%20separate%20sections%20by%20indentation%20instead%20of%20by%20brackets%20or%20%27end%27) as it was in April 20th, 2019.

People and some tools don't respect whitespace. Thus, copying Python code may become cumbersome. But keywords (e.g., 'begin' and 'end') and curly braces are a more robust solution. First, the block levels are always visible regardless if whitespace and indentation are all wrong:

```c
// 😱
int main () {
    {
{
                //
        }   }
}
```

It is also more difficult to mess them up by accident pressing a single key. For Python critics, **'redundancy' can be a good thing**.

Finally, tools can take care of the formatting.

💬 \*Casts `reformat`\*:

```c
// 👌
int main ()
{
    {
        {
            //
        }
    }
}
```

In contrast, after some refactoring, Python formatters show themselves as lacking. **By the time they have to rectify the indentation, what they need is gone for good**, resulting in (usually) _valid code that does the wrong thing_. Even worse, the same is for the compiler now...

This (silly) example in Python is indented with four spaces per level.

```python
def foo(a):
    if(a < 0):
        return 0
    return a

def bar(cond, a):
    if(cond and a == 5):
        if(a == 12)
            return 0
        # <--- Something is missing here!
    return 255
```

Don't ask me why, but consider you want to write that '`if`-code' (from `foo`) in the place indicated of `bar`.

This is the code to move (indentation spaces replaced by holes):

```
🕳🕳🕳🕳if(a < 0):
🕳🕳🕳🕳🕳🕳🕳🕳return 0
```

And here is the `bar` function (indentation spaces replaced by holes):

```
🕳🕳🕳🕳if(cond and a == 5):
🕳🕳🕳🕳🕳🕳🕳🕳if(a == 12)
🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳return 0
🕳🕳🕳🕳🕳🕳🕳🕳# <--- Something is missing here!
🕳🕳🕳🕳return 255
```

This is the result you would expect:

```python
    if(cond and a == 5):
        if(a == 12)
            return 0
        if(a < 0):
            return 0

    return 255
```

But if you try to copy/move it into, this will be the result:

```python
    if(cond and a == 5):
        if(a == 12)
            return 0
        # if(a < 0):
        #    return 0
    if(a < 0):
        return 0

    return 255
```

```
🕳🕳🕳🕳if(cond and a == 5):
🕳🕳🕳🕳🕳🕳🕳🕳if(a == 12)
🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳🕳return 0

🕳🕳🕳🕳if(a < 0):
🕳🕳🕳🕳🕳🕳🕳🕳return 0

🕳🕳🕳🕳return 255
```

Your **intention** has been deleted, and neither the compiler nor the formatter can tell if you did this deliberately. Now, you have to carry the burden of fixing _the syntax_ on your shoulders. Ouch!

Why this happens? Well, it may _seem_ that the 'culprit' is your text editor. Most plain-text editors are dumb (they are intended to be so). As you refactor your code, you are **moving the whitespace around as it is: a bunch of mere characters**.

**This problem can only be addressed at editing time**, so formatters are rendered useless.

You may think that preserving every character of the indentation unchanged is sensible in Python, as the language makes indentation meaningful. _And you would be wrong_. Python _does not care_ about the number of spaces or tabs you are using as long it can tell in which level of indentation a given line is.

```python
if(a < 0):
    return 0 # ⇥
```

```python
if(cond and a == 5):
    if(a == 12) # ⇥
        return 0 # ⇥
    # ⇤
return 255 # ⇤
```

And it can do it comparing the whitespace on the left sides of two consecutive lines:

```
#
    # ⇥
    #
        # ⇥
        #
    # ⇤
    #
# ⇤
```

**What matters is the _difference_ of indentation, its nesting**. Not how is it represented with individual characters in each line. That is an implementation detail.

What does this remind us of?

```c
{
    //
    {
        //
        //
        {
            //
            //
        }
        //
        //
    }
    //
}
```

But as our text editor is treating indentation as characters, no matter what you use, **spaces AND tabs are just unable to do the job**. This means if you try to refactor something, the editor won't bat an eye when shredding the indentation.

So, to sum it up, Pythonists usually think 'that dreadful scaffold' is redundant and awful. Under the hood, a 'dreadful padding system' is doing its own thing, and Python critics argue the lesser evil is the one which is not screwing their code.

## It's OK! No problem!

When a newcomer to Python approaches forums, some of these issues are highlighted. The most common answer is usually one I found unsatisfactory:

> It's OK! No problem! Just install a Python-centric IDE, **because it knows what to do with Python code**, and ta-da ta-da ta-da...

If you are taking your first steps in programming, or you use only Python, it is really 'OK, no problem'.

But programming languages are based on plain text for a reason. If a programming language needs a special tool aware of it, or editing sucks otherwise, something is really broken there—_and I am not saying Python is such a language_—. What will be next, code stored in a opaque, binary format?

It is not absolutely far-fetched people just want to use their favourite text editors! Beyond Python-centric editors, there are a lot of them providing plugins. I'm not against the idea, but we are back to square one: **plugins and special editors can (and should) help coders, but writing code without them must be compelling enough to be envisaged**. In some contexts, I found the named answer as a surrender.

I did not found any editor (not even a Python-centric one) able to resolve all those issues at the same time. Not only that, **any of the partial solutions I have seen are based on the language awareness**. Therefore, all they can do depends on their ability to not regard source code as plain text.

## So braces and keywords are just the way to go?

> Spoiler alert: they are not.

This is not a language-specific problem. Is not even a programming language problem, at all.

The problem can be more pronounced in Python-like languages because formatters cannot read your mind, but you shouldn't be relying upon them here to begin with. You just need them because your text editor **shreds** indentation, do you remember? And that is not a good thing.

Also, whereas there are formatters that prefer not going too far meddling with your own style, some of them fall short. Arguably, this is less an issue than in Python because the compiler is safe. But considering your code is meant to be read by other human beings—or at least that is my hope—you still have to redo your own indentation time after time. There must be a better way.

## In a nutshell

Long story short: yes, text editors are guilty, but not because they need more language-aware features, but due to their naivety handling indentation. And plain text indentation is almost universal, even outside programming languages.

It is a pity. People fight to choose between **spaces and tabs** to represent indentation, but in my honest opinion, the real game changer is to talk about **perception** and **behaviour**.

---
Part II coming soon!
