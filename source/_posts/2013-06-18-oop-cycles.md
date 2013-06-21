---
layout: post
title: "Trivial Cycles in Object Oriented Programming"
date: 2013-06-18 16:41
comments: false
categories: 
---

I'm not against objects. I'm against programming in a style *oriented* by
objects.

One of the largest causes of big-ball-of-mud architectures is unnecessary cyclic
dependencies. Unfortunately, object-oriented languages allow programmers to
trivially create cyclic dependencies without warning or immediate
repercussions. Overtime, these cycles become a significant source of complexity
and corresponding suffering.

The problem can be summarized in a single line of pseudocode:

```ruby
person.address.residents
```

This innocuous-looking expression embodies a pleasant interface, but its
implementation hides an insidious cyclic relationship between the `Person`
class and the `Address` class. Worse than that, this cyclic relationship
is extended transitively to any other related classes. Consider the following
diagram with an additional `Company` class that may also have an address.

![Person / Address / Company ball-of-mud](/images/oop-cycle.png)

Any time you add a type to this graph, it is instantly tangled-up with
everything else. Compare this with this functional variant:

![Person / Address / Company untangled](/images/fp-dag.png)

Now you can analyze, compile, and reason about a subset of this graph.
Reasoning about any particular node only requires reasoning about the
transitive closure of its dependencies. However, in the presence of ubiquitous
cycles, the transitive closure is equivalent to the entire graph.

Unfortunately, we've now lost the convenient `noun.verb` or `noun.component`
syntax. Instead, our expressions are inside out. Here it is in Ruby or Python
syntax:

```ruby
residents(address(person))
```

And here it is in Lisp syntax:

```clojure
(residents (address person))
```

Luckily, we can recover the pleasant ordering with the help of a macro.  Here
is an example of Clojure's threading macro:

```clojure
(-> person address residents)
```

In the absense of macros, we can use a binary operator. Here's an example of
F#'s "pipeline" operator:

```fsharp
person |> address |> residents
```

Both Scala and C# provide tools for directly emulating the dotted access
syntax.  Respectively, implicit conversions and extension methods leverage the
type system to perform dispatch to non-members in a syntactically identical
way.

It is unfortunate that it is so easy to create cycles. A small sin at first,
but they quickly add up and really start to hurt as a system grows larger.
