---
layout: post
title: "Rarely Reversible"
date: 2015-04-26 15:33
comments: true
categories: 
---

[React.js][1] and its ["IMGUI"][2] inspired rendering model is exploding in
popularity.  After [my talk at Clojure/West][3], several folks asked me about
two seemingly separate discussion topics: Two-way Bindings and [Cursors][7].

Among those asking me about data binding, there are a few campaigning for a
"spreadsheet-like" method of defining user interfaces. On one hand, they're
right to seek a direct-manipulation experience for building artifacts in a
fundamentally visual and interactive medium. However, the code-level approach
of using spreadsheet-like "equations" is fundamentally flawed.

Meanwhile, folks using [Om][4] in practice have expressed their frustrations
with cursors. A few suggested using something more principled like Haskell's
[lenses][5].  While this may be useful in some situations, it's fundamentally
flawed for the same underlying reasons.

Both designs share a flaw born of a common desire: To automatically map user
input back to data sources. When there's a 1-to-1 mapping from data sources to
user interfaces, this is appropriate. However, it's not sufficient for the
general case. In fact, it's not sufficient for the _common_ case.

Transformations of source data in to views beyond trivial editable fields is
almost never reversible or equational.

To substantiate my position, let me demonstrate just how difficult it is to
write reversible relationships with some examples in the domain of grammars.

Let's say you have this simple grammar:

```
A ::= a+
```

The production `A` is formed of 1 or more `a` characters.

An infinite number of substitutions will satisfy the rule.

```
a -> A
aaaaaa -> A
```

Here, the direction of the arrow is critically important.

How can you reverse this production rule?

```
??? <- A
```

The only *universal* way to convert directed rules in to reversible relations
is to never discard "information".

For example, we could parameterize `A` by a number.

```
aaaa <-> A(4)
```

Alternatively, you can specify a general principal for lossy reversal rules,
like "always produce the smallest value that would satisfy the rule".

```
a <- A
```

However, this falls flat on its face if you introduce alternation:

```
AB ::= a | b

a -> AB
b -> AB

??? <- AB
```

Neither `a` nor `b` is "smaller", so you need a new principle to reverse by.
In a traditional context free grammar, `|` doesn't imply order, but in a
[PEG][6], for example, you have ordered choice. You could say that you reverse
to the first choice which satisfies the rule. Let's use `/` as the ordered
choice operator.

```
AB ::= a / b

a -> AB
b -> AB

a <- AB
```

Even in this simplified context, we haven't even begun to scratch the surface
of potential problems with reversibility. Constructs such as binders or loops
will cause the reversal principles to explode in complexity.

Grammars offer pretty simple and well defined operations. However, practically
every new operation introduces new reversal principles. Once real world
business logic get involved, things get hairy quickly.

In the context of user interfaces, these problems are magnified to be so large
that they are practically unrecognizable. However, if you zoom in on individual
cases in your own applications, you'll spot this inherit complexity.

A robust UI solution should not attempt to build upon a foundation of
automatic reversibility.


[1]: https://facebook.github.io/react/
[2]: http://mollyrocket.com/861
[3]: https://www.youtube.com/watch?v=LNtQPSUi1iQ
[4]: https://github.com/omcljs/om
[5]: https://hackage.haskell.org/package/lens
[6]: https://en.wikipedia.org/wiki/Parsing_expression_grammar
[7]: https://github.com/omcljs/om/wiki/Cursors
