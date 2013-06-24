---
layout: post
title: Code Density
date: 2013-06-24 18:26
comments: true
categories: 
---

I've heard from many folks who have had difficulty learning functional
programming. In attempting to complete seemingly simple tasks, even the most
experienced imperative and object oriented programmers fall flat on their faces
when first attempting to program in a functional style. Yet, experienced
functional programmers rave about their productivity and their small, beautiful
codebases. Are these functional programmers simply that much smarter? Or is
there some other force at work?

Even in a familiar language, a small, but powerful library might be described
as "write-only code" by those who do not grok the fundamental ideas. Powerful
ideas produce powerful programs, but powerful programs don't always shed light
on their underlying powerful ideas. Functional programming is a powerful idea.

A lot of businesses just need somebody to bang out some pretty vanilla
solutions to some pretty vanilla problems. If you're in one of these businesses
(and most of us are), then your team's resident curmudgeon is right: Clever is
not a good thing. Familiar and understood beats simple and powerful ideas every
time.

However, if you're looking to up your game; If you want to tackle bigger
problems, you need more powerful tools. If you've tried to read some Lisp or ML
or Haskell, but found your head swimming; If you've tried to fight your way
through [Project Euler][1] with an unfamiliar language, but got stumped by
tasks you aced in freshman year; Don't panic. There's a good explanation for
why these things seem harder. Those functional programmers really aren't
smarter than you.  They're just a little more persistent.

The key issue is one of code density. Powerful ideas can succinctly express
solutions to tough problems. The problem isn't necessarily any easier, nor is
expressing the solution necessarily any more difficult. There is simply more
meaning per line of code, so reading and writing each line of code takes
proportionally longer. If you've jumped into a 100,000+ lines-of-code Java
project and found your way around like a champ, you shouldn't feel ashamed by
jumping into a 10,000 line Clojure project and being horribly lost. Those
10,000 lines of code might take you five times as long to read per line.  These
numbers are only backed by anecdotal evidence, but that's a hypothetical 50%
reduction in total code reading time!

When you start writing some object-oriented code, you spend a bunch of time
creating classes and defining the shape of your domain. There is some delay
between when you begin typing and when you run head first into the problem at
hand. During that delay, you have a lot of time to think about the problem.
While you're fingers are hard at work, your mind is too. Your brain needs time
for solutions to bake. If you encounter the hard problems as soon as you start
typing, you need to walk away from your desk!

Functional programming is a particularly compelling powerful idea because it
captures the essence of a lot of computational problems. There is a great deal
of complexity that is hidden away in mutable state, implicit context, and
abstract interfaces. If you're used to the constant drip of progress afforded
by stitching together class hierarchies, you suffer immediate withdrawals when
faced with all of the hidden moving parts laid bare in an individual function's
signature. Suddenly, you need to solve many more problems at once, but you've
become accustom to having those problems spread out across a wider
cross-section of your program.

It takes longer to get started with a powerful idea because you must first
understand and internalize that powerful idea. Take a deep breath, lean back in
your chair, close your eyes, and think through the problem. Untangle the mess
in your head, maybe explore a bit in the REPL, then come back and try the
problem again. You might find that you spend half as much time thinking as you
used to spend typing.

[1]: http://projecteuler.net/
