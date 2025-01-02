---
layout: post
title: 'Dataflow Solver (qplan for Clojure)'
date: 2012-12-01
permalink: /blog/:year/:month/:day/:title/
---

This post describes the motivation for my next Clojure library: [qplan][1]

The overwhelming majority of graphical applications are built using ad-hoc
event callbacks. Some portion of graphical applications, however, use a
databinding model of varying expressivity. Often, these databinding models are
simply shorthand for ad-hoc dependency graphs composed of event callbacks.
Inevitably, the databinding system will be insufficiently expressive,
necessitating manual callback wiring. Even with the most expressive data
binding systems available in application frameworks these days, there is a
shockingly fragile rat's nest of control flow and execution order.

Quite a bit of activity has occurred in the research community surrounding more
formal, more composable, and less fragile event handling. The buzzword de jour
is "[Functional Reactive Programming][2]" (FRP) which, although quite varied by
author, generally involves the concepts of continuous "signals", discrete
"events", and some evaluation strategy. FRP was born of animation systems and
is a fertile research area. More generally, FRP is a type of
[dataflow programming][3].  The "reactive" moniker also applies to the area of
push-sequences, such as .NET's [IObservable][4] and associated [Reactive
Extensions][5] (Rx).

As fun as it has been to study FRP, there has been little success building real
applications using its techniques. In my opinion, this is because FRP
encourages a continuous model of your application, but most graphical
applications are much more discrete in nature. Multimedia systems, like the
animation system that motivated the first FRP paper, and games have quite a few
continuous use cases. Point-and-click software, however, generally has discrete
controls, with discrete values, and discrete commands. Consider, for example,
the undo-able discrete mutations in the most broadly deployed dataflow
application on the planet: spreadsheets. Although capable of representing
discreet events, FRP systems project the mental framework of water flowing
through pipes. This mental framework clashes badly with the more familiar event
callback model. Furthermore, even for truly continuous use cases,
discretization is essential to the nature of computation; every game
fundamentally distills down to an update and render loop.

I'm still contemplating whether or not it is possible to build a databinding
system that is an order of magnitude more expressive and composable than the
existing state of the art. However, I'm certain that the underlying
architecture will be a discrete system which embraces a formal notion of time
through an explicit notion of [identity and value][6].  Consider again the
spreadsheet, where cells have an identity composed of their row and column
labels. As you change the spreadsheet, the document updates the values in all
cells synchronously in response to a discrete command. This approach can be
extended to an arbitrary graph of identities connected by dataflow constraints.

Given a sprawling graph of identities, you need some evaluation system for
assigning values to each identity. Unlike a spreadsheet, many applications
demand sophisticated and potentially cyclical bindings. In the face of
multi-input, multi-output, and multi-directional dataflow, there are many
potential evaluation strategies yielding many different graph linearizations.
You need a constraint solver suited to the task. Luckily, Texas A&M has studied
this problem in collaboration with Adobe. Their approach is referred to as
[Property Models][7] and addresses many of the issues with relying on solvers
for user-facing constraint systems. The Property Models approach is much
broader than simply databinding, it also considers predictability and
effectively synthesizing commands, such as those recorded in Photoshop's
History pane.

At the core of the Property Model system, is a variation of Zanden's
[Incremental Algorithm for Satisfying Hierarchies of Multiway Dataflow Constraints][8]
as described by [Freeman, et al][9]. I've produced a Clojure implementation of
this algorithm called qplan.

My intention is to use qplan as part of a larger approach to building graphical
user interfaces. I expect a that a complete system requires a variety of
algorithms and solvers tuned for particular use cases. For example, Adobe's
[Adam and Eve][10], has both a Quickplan solver for property models and a
linear constraint solver for layout.  An even more complete system may have an
FRP evaluation strategy for animations.

[1]: https://github.com/brandonbloom/qplan
[2]: http://c2.com/cgi/wiki?FunctionalReactiveProgramming
[3]: http://en.wikipedia.org/wiki/Dataflow_programming
[4]: http://msdn.microsoft.com/en-us/library/dd990377.aspx
[5]: http://msdn.microsoft.com/en-us/data/gg577609.aspx
[6]: http://www.infoq.com/presentations/Value-Identity-State-Rich-Hickey
[7]: https://parasol.tamu.edu/~jarvi/papers/gpce08.pdf
[8]: http://www.cparity.com/projects/AcmClassification/samples/225543.pdf
[9]: https://parasol.tamu.edu/publications/download.php?file_id=670
[10]: http://stlab.adobe.com/group__asl__overview.html
