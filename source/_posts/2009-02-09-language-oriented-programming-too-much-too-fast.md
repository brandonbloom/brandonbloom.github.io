---
layout: post
title: "Language-oriented programming: too much, too fast"
date: 2009-02-09
comments: false
---

There was some [recent discussion on Hacker News][1] about the [2004 article on
"Language Oriented Programming"][2] by [Sergey Dmitriev][3]. With my growing
interest in programming languages, this article, and the pending release of
[JetBrain's Meta Programming System][4], I have been thinking a lot about the
future of programming.

As Sergey points out, MPS is not the first entry into this paradigm of software
development. [Intentional Programming][5] was being [demonstrated by Microsoft
Research][6] as early as 2000. Wikipedia lists several implementations of the
[language-oriented programming][7] concept. (OK, that is enough links for now!)
Sadly, none of these systems have been met with wide spread success. Despite my
unlimited respect for the JetBrains team and love of their products --
especially Resharper -- I expect MPS to fail to achieve critical mass. I don't
think many programmers would disagree. It's just too much, too fast.

Software development needs to evolve, not start anew. If you change too much at
once, the common developer simply won't accept it. Language designers are still
re-inventing Lisp piece by piece in an effort to make sense of all the
different concepts for [Mort][8]. In order to improve adoption of
language-oriented programming concepts, the critical path must be identified
and the programming community must be lead down that path one step at a time.

I believe that the first stepping stone is the box editor.

Both MPS and Intentional Programming provide a visual tree of boxes editor and
store the abstract syntax tree in a sort of source code database. Both of these
concepts are critical to power of language-oriented programming, but changing
the storage of source code is simply too radical of a change. Well, actually,
source code is routinely stored in databases for IDE features, but the
authoritative storage always remains text files. All of a programmers most
trusted tools operate at the string level and it is simply impractical to throw
everything out at once.

This is why I propose the creation of a new editor which uses an abstract
syntax tree box editing model, but preserves the source as text. This editor
would have to play nice with other developers who are using traditional text
editors. It should be possible for a single developer to try the editor for a
day or two without another collaborator even noticing. There are clear benefits
to a box editor even in the absence of language-oriented features. For example,
navigation of source code by parent, child, or sibling relationships instead of
by word or by line. Advanced renders can be used to layout math expressions or
to show a referenced image file in a comment.

Editors are a religion, start a new one. If some box editor becomes popular
enough, other tools would spring up around them. To help the process, the
editor should have well exposed extension and composition mechanisms. For
example, it should be trivially easy to add new renderers for various nodes. It
should be equally trivial to utilize the abstract syntax tree system to create
stand alone code analysis tools.

If box editors garner enough of a following, then the language-oriented
programming advocates can seek the next stepping stone.

## Imported Comments

### Shawn Presser

> "I believe that the first stepping stone is the box editor."

I went through this phase too.  You'll eventually realize that visual editors
are simply less efficient than text editors for programming purposes.  The
boxes convey less meaning than symbols in a text editor.  The boxes and
connections are also symbols, but they take up far more screen space than text,
so you see much less code simultaneously, which makes the box editor less
efficient.  Also, there aren't usually restrictions on the layout of the boxes,
so it's very hard to follow the "flow" of code in a visual editor.  In a text
editor, the flow of code within a function is at least mostly linear (left to
right, top down).

### Brandon Bloom

I'm not talking about "boxes and connections" I'm talking about representing
the actual code blocks. Take a look at this tutorial:
http://www.jetbrains.com/mps/docs/tutorial.html

### warjan

I think you are right saying it is too much. Especially application developer
might find MPS to be too much for achieving common programming task, but for
library/frameworks and DSL creators it is IMHO very valuable proposal. It could
be also good for providing DSL for clients to cusotmize products.

### Brandon Bloom

"good for providing DSL for clients to cusotmize products"

Maybe if your clients are hardcore engineers, but in general asking clients to
write code is a tough value proposition.

However, let's assume your clients are hardcore engineers. Not only does MPS
ask them to learn a DSL, but it is also asking them to learn a new editor.

One step at a time.

[1]: http://news.ycombinator.com/item?id=452919
[2]: http://www.onboard.jetbrains.com/is1/articles/04/10/lop/index.html
[3]: http://www.sergeydmitriev.com/
[4]: http://www.jetbrains.com/mps/index.html
[5]: http://en.wikipedia.org/wiki/Intentional_programming
[6]: http://www.youtube.com/watch?v=tSnnfUj1XCQ
[7]: http://en.wikipedia.org/wiki/Language-oriented_programming
[8]: http://www.nikhilk.net/Personas.aspx
