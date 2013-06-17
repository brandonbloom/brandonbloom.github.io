---
layout: post
title: "Control flow has to flow"
date: 2008-10-27
comments: false
---

I've been experiencing MSBuild a bit at work lately. Ignoring the mortal sin of
designing a programming language within XML, MSBuild fails on usability in
another fundamental way: control flow has to flow.

In general, there are two key approaches to control flow. One approach is the
procedural "jump" approach. This includes all manner of conditionals, loops,
and more. The second approach involves functional constructs such as recursion
and pattern matching. These two approaches are blended between procedural
languages, functional languages, and everything in between and beyond.

The procedural and functional mechanisms can be visualized in terms of forwards
and backwards. Procedural control flow moves forward in a set of steps which
are executed in sequence as defined. Functional control flow begins with the
results and works its way backwards pulling in bits and pieces of data and
logic along the way.

MSBuild uses neither of these approaches.

That was a lie. MSBuild targets are executed in sequence procedurally. Targets
can also define a "DependsOn" property which works functionally to pull in
pre-requisite tasks.

The truth is that MSBuild introduces (and often requires) a very unwelcome
third approach in the form of the "Condition" property. The "Condition"
property is neither forwards nor backwards control flow. It is sideways.
MSBuild has only one global namespace of variables (confusingly, called
"properties") which can be set or tested at any time.

With either a procedural or functional control flow, you can follow the flow
simply in your text editor. In a structured procedure, you simply trace through
the code pretending to be a human interpreter. In a functional method or when
dealing with sub-procedures, you can trivially search for functions by name as
they are called. In a sense, you are "flowing" through the code like water
though pipes. You are either pumping water through or sucking water out.

With sideways control flow, you need to search for all references to every
variable at every step of both the procedural and functional code flows. This
is a laborious and error prone operation because after finding a relevant
"Condition", you need to analyze the forward and backwards control flow which
led to that sideways control flow construct! There is no "flowing".

Sideways control flow leads to a combinatorial explosion of forward and
backwards control flows. The developer is required to keep all of these flows
in their heads all at once. Meanwhile, in normal programming languages, you
only ever need to deal with just two: forwards and backwards.
