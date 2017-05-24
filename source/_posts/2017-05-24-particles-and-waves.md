---
layout: post
title: "Particles and Waves"
date: 2017-05-24 09:45
---

If simpler is better, is fewer better? Is one better than two? When
designing programming constructs, there is a tendency towards
[extremist programming][1]. There is power and beauty in a grand unified
approach that has just one class of elements. Alluring as singularity may be,
these one-element approaches tend to fail in ways that more complex designs do
not. Hybrid approaches tend to win in the end. Why? And how should this
reality inform our designs? Can we predict and enable hybrid solutions?

During the 17th century, opposing theories of light were being considered:
particles vs waves. A great deal of science was conducted on behalf of either
camp, but no clear victor emerged. It wasn't until the 20th century that the
dual nature of light was widely accepted. I'm far from the first to notice the
parallels to logic and computing. Similarities are plentiful here. We even use
the same word, “duality,” to informally describe the frequent occurrence of
this two-element, yin-and-yang pattern in computing.

Where do dualities come from? Why are there so many of them? Why aren't there
more trinities? Quaternities?

Binary classifications arrises when we define a domain of discourse, then
segregate that domain by a predicate. That is, some elements of the domain
possess some property and the other elements do not. When this property is
defined in terms of some bounded context, the binary classifications becomes a
metaphysical, particle/wave-style duality. An element of such a domain may or
may not satisfy such a predicate depending on your frame of reference!

Consider the famous Rubin's Vase illusion:

<img style="box-shadow: none; -webkit-box-shadow: none; " src="/images/vases.png">

In each image, either a vase or a pair of face profiles can be scene. Consider
which subset of pixels satisfies the foreground predicate, and which satisfies
its negation or dual, the background predicate. While the “meta-image” has a
white background, the left and rightmost sub-images have a black background.
Inverting the colors can switch which object appears in the foreground.
However, adding a border can achieve the same illusion! Despite the center
image being a pixel-perfect, color-matching, sub-rectangle of the rightmost
image, the vase and faces appear to have swapped between the foreground and
background. The border provides the bounded context that governs the
foreground/background duality.

Returning to the duality of light: What property separates particles from
waves? Unlike waves, particles possess a definite position in space. Waves
have indefinite position, existing only with respect to some medium. A
physical medium serves as a bounded context. There's no splash without a pool
of water; just as there is no background without a border.

Faced with duality, there is natural tendency to seek a preference between the
two sides. For many binary classifications, there is no clear winner. Heads or
tails? White or black? There's no right answer. However, the equation is not
as balanced when it comes to context-bounded dualities. Our preference is
necessarily situational. On a white canvas, we paint a black vase in the
foreground, but just the opposite on a black canvas. We must consider the
context.

Frequently, the “particle” side of a duality is easier to recognize and reason
about. The particle theory of light was proposed in the 11th century, nearly
600 years prior to the wave theory. What makes waves more difficult to
recognize or reason about? Typically, context is implicit. The choice of
context is often already made for you. When I set out to write this blog post,
the background of my blog was already white. On a small beach, it's easy to
see the particles of sand, but it's just as easy to see the waves of the
ocean. Our relative scale influences our perspective, and that grants
dominance to one of the two perspectives on the duality. Luckily, perspective
can be shifted. Placing a handful of sand in to a carefully selected context,
such as the surface of a speaker, will instantly reveal a wave. Thinking in
terms of bounded contexts can put reasoning about waves on a level playing
field with particles.

One last bit of metaphysics is worthy of mention here: spacetime. It is
especially difficult to reason about time as a contextual bound on space.
Notably, many waves only appear when observing a medium over time, or by
projecting the time dimension on to a spatial dimension. Despite being
unbounded at a universal scale, time is scarce for us mortals. We can't
capture time, holding on to it for study. At best, we can capture information
about the past, or make predictions about the future, effectively transforming
time in to the space such information takes up.

A good design takes things apart with an eye towards putting them back
together again. However, focusing on the pieces to the exclusion of their
contexts will yield a poor design. Even if the parts snap together nicely, a
gestalt may not arise, or the composition may clash with its surroundings. The
particles and waves or spacetime dualities can be found hiding under every
rock in computer science: Data vs interfaces, logic with negation, values and
state, imperative vs declarative, structural vs relational, and many more.
Learning to recognize these dualities will enable you to design better systems
and to produce better compositions within existing systems.

In future posts, I intend to discuss a wide range of programming topics with
an eye for context and gestalt. We'll use the lense of duality to deepen our
understanding of the design tradeoffs in systems ranging from calculators to
databases and beyond. I'll then abuse the perspective to argue against the
fetishization of modern programming ideals, such as functional purity, or the
overuse of constructs such as promises and observables.

[1]: http://blog.ezyang.com/2012/11/extremist-programming/
