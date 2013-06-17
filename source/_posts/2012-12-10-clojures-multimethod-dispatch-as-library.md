---
title: "Clojure's multimethod dispatch as a library"
date: 2012-12-10
comments: false
---

### In keeping with the theme, this post motivates [dispatch-map](https://github.com/brandonbloom/dispatch-map)

Clojure's multimethods embody a powerful polymorphic dispatch mechanism. With
multimethods, you can define a function which is polymorphic with respect to a
given hierarchy and arbitrary dispatch value. The hierarchy is actually a
directed acyclic graph, but you can adjust method priority to address the
[diamond problem](http://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem).

Unfortunately, Clojure's dispatch mechanism is baked into the defmulti and
defmethod forms. If you want multimethod-style dispatch, you need a Var to hold
your multimethod. Similar to the circumstances which prompted the creation of
[backtick](https://github.com/brandonbloom/backtick), I discovered that there
was an opportunity to decomplect a part of the core library to extract a useful
subcomponent. In this case, the datastructure which describes dispatch rules
was complected with the identity which points to those dispatch rules. As a
result, it's impossible to treat a multimethod as an immutible value.

So I created [dispatch-map](https://github.com/brandonbloom/dispatch-map).  A
dispatch map is just like a regular Clojure map, but it comes coupled with a
dispatch function and hierarchy. Looking up keys in a dispatch map has the same
dispatch behavior and caching functionality of multimethods. However, unlike a
multimethod, a dispatch-map is a true value.  The standard map operators assoc,
dissoc, etc, return a new dispatch-map, leaving the original unmodified. This
enables you to manipulate dispatch maps like any other Clojure data structure,
without the need for a named, mutable Var. Clojure's multimethods and all
related functions could trivially be reimplemented in terms of a dispatch-map
and an atom _(edit: and now are!)_.

One use for a dispatch-map as a value is to store them within some other data
structure, without breaking the value guarantees of the enclosing data
structure. In my case, I've got a dispatch-map of data types to their GUI
representations. In a hierarchical definition of a GUI, each node can have a
:templates key which is a `(dispatch-map class)`. If you want to render a
`(Person.)` inside a list box, you can rely on automatic dispatch by type to find
the appropriate template: Simply walk up the GUI hierarchy and look in the
:templates map. Thanks to the powerful dispatch functionality, this will also
work if you have an `(Employee.)` too!


### Comments

#### tgoossens said...

Doesn't one restrict himself then. What to do when you have a new
type that you need to get working with the existing code? You cannot add any
new methods. Its a neat idea and I'm sure you can use it for a lot of stuff.
But I think - if my previous statement is correct - that the advantage of
having it as a value doesn't compensate for losing the ability to extend (which
is basically what you want to do to solve the expression problem right?)


#### Brandon Bloom said...

    You cannot add any new methods.

If that were true, you couldn't add any new key value pairs to an immutable
map...

    the advantage of having it as a value doesn't compensate for losing the ability to extend

You're not losing your ability to extend, you're simply shifting it's
responsibility elsewhere.

Just put your dispatch-map in an atom, and you've basically got a multimethod.
More accurately, a multimethod is a dispatch-map plus a Var. The goal of
dispatch-map is to decomplect dispatch from state.

Consider, for example, if I had two separate multimethods (call them f and g)
and wanted to add a method to both atomically. With a multimethod, you would
have to (locking xx (defmethod f ...) (defmethod g ...)) when you could instead
(swap! yy #(-> (assoc-in [:x a] ...) (assoc-in [:y a] ...)))


#### tgoossens said...

Ok cool. I think that now I get what you were trying to achieve. Thanks for the
explanation!


#### Paul Stadig said...

Conceptually, an atom and a dispatch-map could replace multimethods. It might
be possible with multimethods, since they are so slow anyway. However, there
could be performance issues.

I did some similar experiments trying to extract type based dispatch from
protocol functions. I started with an atom and a dispatch table, and it turned
out to be pretty slow.


#### Brandon Bloom said...

"Slow" is a relative term. Surely multimethods are slower than protocol
dispatch. And protocol dispatch is surely slower than direct dispatch.

However, each yields an additional level of indirection for an additional
abstraction need. Direct dispatch offers no abstraction. Protocol dispatch
offers abstraction by type. Multimethods offer arbitrary dispatch with respect
to a directed acyclic graph. In theory, you could also create a
predicate-dispatch-map, which would allow dispatch with respect to an arbitrary
decision tree.

With each level of abstraction, you gain a little more flexibility and dynamism
at the cost of a little more performance. However, if you can statically
determine a dispatch value, you can eliminate those performance costs at
compile time. That's likely not a worthwhile exercise, though, since dynamism
is often the whole point of that level of abstraction.

In the case of both multimethods and my dispatch-maps, dispatch values are
memoized. Subsequent calls given the same dispatch key do not require
traversing the hierarchy to find a "best" match. The result is that the
amortized cost of a lookup is the cost of the dispatch-fn plus the cost of one
tree lookup. That's quite reasonable in my mind.  December 14, 2012 at 1:26 PM  
