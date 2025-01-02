---
layout: post
date: 2012-12-21
title: 'The Node.js REPL is Broken'
permalink: /blog/:year/:month/:day/:title/
---

Not only is the Node.js REPL broken, but so are the stock REPLs for Python and
Ruby too! In fact, for many of you, every single REPL you've ever used has been
fundamentally flawed. Moreover, even if you used a fancy replacement REPL, such
as [UltraREPL](https://github.com/Benvie/Node.js-Ultra-REPL),
[IPython](http://ipython.org/), or [Pry](http://pryrepl.org/), you'd only be
mitigating the problem: JavaScript, Python, and Ruby are all fundamentally
flawed languages from a REPL-lover's perspective.

Now, I realize that this is a bold and, thus far, unsubstantiated claim. Python
and Ruby are revered for interactive, dynamic development and Firebug's
JavaScript console changed the game for web apps. So with so many happy REPL
users, what are these fundamental flaws? Since it is the worst offender, let me
pick on JavaScript for a moment...

Node's stock REPL provides a fragile and awkward notion of an evaluation
context. A fresh REPL is configured with an evaluation context as if by both
`with(context)` and `.call(context)` to enable unqualified access to context
variables, as well as introspection of the this context. This, however, is a
convenient illusion. If you evaluate `var x = 1; this`, you'll note that `x` is
added to `this`. If you were to use `var` inside a `with` block, you wouldn't
get that same behavior. The top-level inside the REPL is somewhat magical.

CommonJS Modules provides the blueprint for Node's module system. The key idea
is that Plain Old JavaScript Objects can be used to describe modules and their
exports. Simple, but perhaps too simple. Consider if you were working on some
function f in module `m`. You boot up your trusty REPL, evaluate `var f =
require('m').f;` and try out some corner cases. Ugh oh, `f` has a bug, so you
modify `src/m.js` and then you.... promptly restart your REPL! The require
function doesn't offer any sort of `{reload: true}` option, and even if you
did, you'd still be left with a reference f pointing to an old version of the
function. That might not be a big problem if you have just one function, but
what if you have several objects and functions in a larger system? The
too-simple plain old JavaScript objects approach results in the copying of
references and assigning those copies to local names. It is extremely difficult
to reason about changes. Even if you knew to cheat by reaching inside of
require.cache with a delete, you'd still be better off restarting your REPL to
preserve your sanity. That's a real shame if you had a bunch of interesting
test cases lying around in memory, or if there is an expensive initialization
computation you need to wait through each time.

Now that I'm done picking on JavaScript, I'll point out that the situations in
both Python and Ruby aren't much better. Python doesn't have a notion of a
current module, although it does provide the builtin `locals()` function, as
well as lets you call `code.interact(locals={...})` to start a nested sub-REPL
with something akin to Node's magical top-level. Ruby fairs marginally better
by providing a reasonably well behaved implicit self, which can be shadowed in
a nested sub-REPL via an argument to the confusingly named irb method. Python's
modules have a similar issue to plain old JavaScript objects with from m import
f and Ruby's metaprogramming facilities add more than enough complexity to make
REPL-coding hair-raising.

It doesn't have to be this way. There are languages that are much better suited
to iterative REPL development. If you've been following my blog, then this is
where you'd expect me to praise Clojure. I'll save that for a future post in
which I'll discuss what is really necessary for a successful REPL. And while
Clojure's REPL is far better than any other I've ever used, it has its own
shortcomings to discuss as well.


### Comments

#### Florian said...

Note that you're lamenting for the most part that python, ruby and JS can't do "hot-reload".

This has nothing to do with an interactive prompt.

Hot reloading never really does work whatever you do (it doesn't "just work" in
smalltalk and any lisp dialect either). The first reason is that even if you
can magically substitute all references to everything with the right version
(which smalltalk and most lisp dialects can) it still leaves in-memory data
corrupt (you haven't written an in-memory migration script have you?).

The situation is somewhat worse for any language that allows references to
everything (like python, ruby and JS) and is not side-effect free. In those
environments hot-reloading becomes all but impossible, and although that's a
bit sad, the very way those languages work also makes them pleasant to use
unless you want a hot reload.

#### Brandon Bloom said...

Being able to "hot-reload" a file is nice, but the failing is in the manner by
which names are resolved. Aliasing imports into a local variable lacks a level
of indirection; this precludes hot-swapping.

> This has nothing to do with an interactive prompt.

These are design decisions that impact the usability of interactive prompts.

> you haven't written an in-memory migration script have you?

Sometimes I do migrate my in memory data structures... I enjoy having that
option.

#### devdazed said...

I agree that the Node REPL is by far the worst offender, I think the largest
point should have been that the REPL decides when an error make it crash. For
instance, if you run:

`throw new Error('foo');`

All is well and it prints a stacktrace, however if you run:

`process.nextTick(function(){ throw new Error('foo') });`

It will cause the entire REPL to crash, and thus you need to start from square
one. It's this type of inconsistent behavior that makes the Node REPL
infuriating.

IMO, the REPL should never crash and if there is anything a user can do to make
it crash (aside from sending a kill -9 command) then it is fundamentally
flawed.

Another thing is that you can cause a JS OOM error by simply creating a tight
look that prints to the console.

Additionally, standard sigterm signals (such as ctrl+c) won't break such a
tight loop, so your only choice is kill the entire application.
