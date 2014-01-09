---
layout: post
title: "Unsound and Incomplete"
date: 2014-01-08 17:36
comments: true
---

The academic programming language community spends lots of time studying and
building type systems. It's widely considered that [soundness][1] is the mark
of a well designed type system. A sound type system never permits an
incorrectly typed program to pass type checking. An ideal type system would
also be [complete][2]; it would never reject a correctly typed program because
it was unable to prove its correctness. If you're not familiar with this
terminology, Ben Karel offers [an illuminating analogy][3] to help you.

Recently, a flurry of new, statically typed languages have entered the public
stage. Among these are Google's [Dart][4], as well as Microsoft's
[TypeScript][5]. One thing that these languages have in common is that they
have unsound type systems. Cue the horrified gasps of the type theorists!

Ignoring the topic of completeness for a moment, since most critics do so
themselves (I think "unsound" just sounds scarier than "incomplete"). Surely
Google and Microsoft employ some smart folks who have followed the latest
research in type systems, so why, in 2014, are we still building languages
that have unsound type systems? Well, I'll let the [Googlers][6] and
[Microsofties][7] explain themselves. In short, it's difficult to maintain
soundness in feature-rich languages. Even when possible, it may cause an
[astronomical increase in complexity][8]. Achieving soundness requires
a trade off against completeness, complexity, toolability, and more. Never
mind the fact that being fully sound isn't even that useful to begin with!

Let's step back and examine why we even bother with type systems at all.
In many cases, static analysis can dramatically reduce programmer errors and
opens up significant optimization opportunities. Programming languages that
are amenable to formal static analysis are [highly correlated][9] with
programming languages that are amenable to informal static analysis, i.e. by
programmers! Type systems have proven to be a particularly useful category of
static analysis; so much so, that many programmers can't imagine building
software without a type checker on hand at all times.

Sadly, type systems fundamentally can't catch all kinds of programming
mistakes, nor will they magically improve the time or space complexity of your
algorithms. You'll have to write tests and benchmarks either way, to say
nothing of validating your designs with stakeholders! Still, there are other
reasons to have a good type system, like enforcement of [type safety][10]
(which is a different thing than "type correctness"). However, safety can also
be [enforced by dynamic means][11] and dynamic mechanisms such as
[capabilities][12] can enforce security far beyond that of static safety
checks. You might even want to [intentionally forego safety][13] in order to
achieve maximum performance. Furthermore, there's lots of other kinds of
static analysis, both formal and informal, that can be used to find bugs or
speed your programs up.

Assuming we want a tool that finds as many errors as possible, we should be
willing to forego completeness, instead preferring to have our static analysis
be overly cautious, warning us of programs that *might* be incorrect. We can
always suppress such warnings after informal analysis (or alternative formal
analysis!). Assuming we're realistic about the fact that no static analysis
tool will ever catch all errors, we should also be willing to tolerate
unsoundness in order to [spend our mental complexity budgets more wisely][14].
Assuming we want a tool that independently improves with time, we should not
create a [cyclic dependency between languages and type systems][15].

There is a class of such tools. They're called [linters][16].

[1]: http://en.wikipedia.org/wiki/Soundness
[2]: http://en.wikipedia.org/wiki/Completeness#Logical_completeness
[3]: http://eschew.wordpress.com/2009/08/31/sound-and-complete/
[4]: http://golang.org/
[5]: http://www.typescriptlang.org/
[6]: https://www.dartlang.org/articles/why-dart-types/
[7]: https://typescript.codeplex.com/discussions/428572
[8]: http://i.imgur.com/YAls4tQ.png
[9]: http://www.cse.chalmers.se/~nad/publications/danielsson-et-al-popl2006.html
[10]: http://en.wikipedia.org/wiki/Type_safety
[11]: http://docs.oracle.com/javase/7/docs/api/java/lang/ClassCastException.html
[12]: http://erights.org/elib/capability/ode/ode-capabilities.html
[13]: http://msdn.microsoft.com/en-us/library/t2yzs44b.aspx
[14]: http://www.haskell.org/haskellwiki/Hoare_Property
[15]: http://bracha.org/pluggableTypesPosition.pdf
[16]: http://en.wikipedia.org/wiki/Lint_(software)
