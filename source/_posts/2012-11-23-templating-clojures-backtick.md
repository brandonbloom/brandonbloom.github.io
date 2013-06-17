---
layout: post
title: "Templating: Clojure's Backtick"
date: 2012-11-23
comments: false
---

This post describes the motivation for my new Clojure library:
[Backtick](https://github.com/brandonbloom/backtick)

There is no shortage of template languages. In fact, there are probably far too
many of them. Template languages fall into two major categories that I'll call
*procedural* and *structural*. Procedural templating languages operate by
emitting code as a side-effect. Structural templating languages operate on
trees. For example, Ruby's
[ERB](http://ruby-doc.org/stdlib-1.9.3/libdoc/erb/rdoc/ERB.html) is procedural,
where as [HAML](http://haml.info/) is structural.

The primary advantage of a text-based procedural template language is reach.
ERB can be used to generate any type of text file, while HAML can only really
be used to emit HTML. Reach is not without disadvantages, however. For one,
it's quite easy to write incorrect ERB templates which emit invalid syntax,
such as missing a close tag. Additionally, code with side effects can be
difficult to refactor because execution order is critical. You can trivially
generate SQL statements with an ERB template, but you'll quickly accumulate an
incomprehensible mud ball, so you're better off working with a system that
treats queries as data and lets you manipulate them structurally.
Unfortunately, you'll need to implement a unique structural tool for each
target data structure. If you're generating a lot of HTML, then using HAML is a
great choice. But if you're only generating one HTML file among a sea of dozens
of other arbitrary config file formats, then you really want ERB.

Generalizing, structural templating systems are really just plain old pure
functions. You take some data, let's say describing a blog post, and then you
return some other data describing the rendered representation of that same
post. This is a powerful realization because it enables you to utilize your
language's full set of utility functions for refactoring your templates. For an
HTML example, see [Noir's partials](http://webnoir.org/tutorials/html) and
their use of [Hiccup](https://github.com/weavejester/hiccup).

When you get into the wonderful world of metaprogramming, templating systems
encounter a series of new problems. First, there is multi-level escape
character hell. Anyone who has ever tried to write a shell script, which
substitutes arguments into an Awk script, which generates a config file, knows
what I'm talking about. Second, there is the issue of names. Unlike HTML or
many simple config files, code generally have one or more notions of context,
such as scoping or namespaces. You need to worry problems like
[variable capture](http://www.bookshelf.jp/texi/onlisp/onlisp_10.html). Yikes!

Early lisps discovered a mechanism for minimizing the pain of escape character
hell: quoting. Quoting is similar to escaping, in much the same way that
structural templates relate to procedural ones. Escaping lets one combine any
two languages, provided the language on top provides a uniform escaping policy.
However, with each new layer of languages, there is a new layer of escaping
rules and, if those escape sequences conflict, you'll have to double-, or even
triple-escape common metacharacters like apostrophes, backslashes, and dollar
signs. There's no protection against incorrectly escaped strings and
refactoring is thwarted by the need to increment and decrement escaping levels.
In contrast, quoting takes advantage of Lisp's
[homoiconicity](http://en.wikipedia.org/wiki/Homoiconicity) to simplify
template indirection into four composable, primitive operators: `quote`,
`syntax-quote`, `unquote`, and `unquote-splicing`.

_If you're already a Clojure syntax-quote pro, you may want to skip down to the
next section_

Due to their high frequency of use, both Common Lisp and Clojure reserve some
of their limited syntax for these primitives. Common Lisp uses the apostrophe
('), backtick (`` ` ``), comma (,), and comma-at (,@) character sequences as
shorthand for quote, syntax-quote, unquote, and unquote-splicing respectively.
Because Clojure treats commas as whitespace, it substitutes tilde (~) and
tilde-at (~@) sequences for the unquoting operators. I'll use Clojure's
notation for my examples.

The quote operator is used to protect symbols from evaluation. If I have a
symbol x with value 5, then the expression (quote x) or 'x does not evaluate to
5, it evaluates to the symbol x. Quoting is distributed recursively throughout
a form. '(f x) will protect both f and x from evaluation. It's this
distributive property that allows quoting and unquoting to compose across
multiple levels of templating.

You might guess that `(unquote 'x)` or `~'x` would return `5`, but that operation is
performed by the eval function: `(eval 'x)` does return `5`. Trying to unquote here
will generate an error. This distinction is because eval is generally
implemented as a function, not a macro or special form. It does not alter the
interpretation of its inputs, so it's behavior doesn't play nice with the
distributive property of quoting.

Unquoting comes into play once you introduce syntax-quoting. Syntax-quotes are
demarcated by a backtick, which can be thought of as switching Lisp into
template mode. Like regular quotes, syntax-quotes are distributive. Unlike
regular quote, which leaves its input form alone, syntax-quote transforms its
input in two important ways: First it enables unquoting. You can think of
unquoting like template variable substitution. Second, it resolves symbols.
We'll come back to this second point later.

Let's say that you want to generate the code `(f 1 (g 2 3))` where the numbers
are provided by variables `x` and `y` holding the number `1` and the vector `[2 3]`
respectively. You can accomplish this with the expression `(syntax-quote (f
(unquote x) (g (unquote-splicing y))))`. Well, sort of. Clojure does not provide
direct access to syntax-quote as a symbol. You need to use the shorthand form:
`` `(f ~x (g ~@y)) ``. Luckily, that's nicer to look at anyway.

Unquote substitutes in a single value, like a dollar-sign in a shell script.
Unquote-splicing is a little bit more interesting. When a syntax quote is
transforming its inputs, it looks for unquotes and evaluates them. The
evaluated result is substituted into the parent expression. Regular unquotes
are substituted directly, but splicing unquotes cause the parent expression to
be rewritten as a concatenation. The expression `` `(a b ~@x c d) `` is transformed
into an expression resembling `(concat '(a b) x '(c d))`. The splicing operation
is a more powerful alternative to a traditional templating language's looping
constructs. It can also emulate a traditional templating language's if
statement. The expression `` `(f ~@(when b [x])) `` will return code that passes the
`x` argument to `f` only when `b` is true.

As discussed previously, quoting and unquoting are distributive. This is what
allows you to escape from escape character hell. The expression
`` `(f (g x) ~(h y `z)) `` distributes the quoting behavior down to `f`, `g`,
and `x`, but the unquoting operator decrements the quoting level, such that
neither `h` nor `y` are quoted.  The `z` is explicitly quoted, re-incrementing
the quoting level. You can't do that with escape sequences, because, like a
procedural template language, there is no explicit notion of the inherent tree
structure.  It's worth noting that most lisps don't have an explicit quoting
level, instead syntax-quote processes its inputs recursively; the quote level
is maintained implicitly by the execution stack.

That's not all you need to know about syntax-quote; there's also symbol
resolution. Now that you've escaped escape hell and you're writing structural
templates, you're able to do bigger and better metaprogramming. If you're
writing Common Lisp, you'll quickly run into the aforementioned variable
capture problem: You'll generate code that includes name collisions. Clojure's
syntax-quote differs from Common Lisp's by providing two mechanisms to combat
this problem. First is namespace resolution. Clojure will expand `` `inc ``
into clojure.core/inc and `` `foobar `` into `user/foobar`. This protects you
from [free symbol capture](http://www.bookshelf.jp/texi/onlisp/onlisp_10.html#SEC69).
Secondly, Clojure provides automatic gensyms. Symbols ending with a # character
inside a syntax-quote are replaced by an automatically generated name. So the
expression `` `(x# x# y#) `` will expand into something like `(x__1__auto__
x__1__auto__ y__2__auto__)`, which is virtually impossible to cause [macro
argument capture](http://www.bookshelf.jp/texi/onlisp/onlisp_10.html#SEC68). As
linked several times, [Paul Graham's On
Lisp](http://www.paulgraham.com/onlisp.html) is the best source for
understanding these issues more deeply.

[Experienced Clojurians can start reading from here]

OK, so now that you have an extremely powerful template language baked into
Clojure's syntax-quoting mechanism, you still have the problem of reach: What
good is a template language if you can only generate Clojure code with it? What
if you need to generate some HTML? Or what about that Awk script?

Unfortunately, the template language can only really yield Clojure forms.
Luckily, Clojure forms are relatively rich with a variety of primitives and
collection types, so much of the Clojure community uses them for configuration
files and DSLs. If you're generating input to these Clojure systems, you're in
good shape. You don't have quite the same reach as "all tools that operate on a
stream of characters", but "any library in the Clojure ecosystem" is a pretty
damn good start. If you're going to create a system to structurally generate
SQL queries, you'll need to build some kind of query representation that can be
compiled to a SQL string. In a non-homoiconic language, you'd eventually need
to come up with a new structural template engine too. In Clojure, your query
library will get that template engine for free!

However, there is still a problem. When you first encountered code duplication,
you reached for a simple procedural text template. Now you have the escape
sequence problem. Lisp mitigates the escape sequence problem with quoting. But
once you have larger scale templates, you start running into the variable
capture problem. So Clojure solves the variable capture problem with an
enhanced syntax-quote. Well just as each prior solution yields a new problem,
so too does Clojure's enhanced syntax-quote.

The problem is that Clojure tightly couples the syntax-quote symbol resolution
to Clojure's namespace system. This isn't usually a problem if you're always
using syntax-quotes to generate code that will ultimately be executed in your
local Clojure environment. However, consider the case of generating code to be
executed in a *remote* Clojure environment. Or what about generating Clojure
forms that will be compiled to SQL procedures to be executed on your database
server? In those two cases, it's less likely that resolving symbols against the
locally loaded namespaces is a useful behavior. Suddenly, Clojure's powerful
template language is unavailable to you! Either you have to piece together your
own template system, just as a non-homoiconic language, or you need to play
tricks by manually or procedurally defining vars. Ouch.

Enter [Backtick](https://github.com/brandonbloom/backtick).

Backtick is an absurdly simple library. It provides Clojure's syntax-quote
reader macro as a normal macro. You can create new template quoting macros by
providing a custom symbol resolver. Check it out!


### Comments

#### drcode said...

Thanks- I need this.

Conrad Barski


#### Brandon Bloom said...

Cool! Let me know how it works out for you.

#### Unknown said...

I really needed this!
Thank you for making this available.
