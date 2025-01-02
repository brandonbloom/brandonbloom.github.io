---
layout: post
title: "Flexibility belongs in APIs"
date: 2008-08-28
permalink: /blog/:year/:month/:day/:title/
---

Software comes in a large spectrum of power and flexibility, as well as an
equally large spectrum of complexity and usability. Users also come in a wide
range of intelligence and skill. However, if you create software of average
power and average complexity, you may satisfy the average users' needs, but
they won't love you for it.

One of my favorite books on software, [The Inmates Are Running the Asylum][1],
describes the concept of "perpetual intermediates". The book explains that
beginners are only beginners for a little while, then they become
intermediates. Most users are intermediates for as long as they use the
software. Very few users become true experts. At any given time, virtually all
of your users will be varying intermediates.

With this powerful realization in mind, you may be tempted to design
exclusively for these intermediates. When designing your software you can spin
the flexibility and complexity dials to try to hone in this average user.
Unfortunately, you would wind up pleasing more users by cranking these dials
down much lower.

The fundamental problem is that adding a bit of power and flexibility also adds
a bit of complexity. Since most users are intermediates, they can tolerate some
degree of complexity, but would honestly prefer not to. They avoid the
complexity by only using the small subset of features they have come to
understand and only venturing beyond their comfort zone when they absolutely
have to. They quickly become intermediates and are no longer troubled by
complexity.

When users have to venture into uncharted features, they are effectively
beginners, not intermediates. However, the more esoteric or powerful the
feature, the more likely it was designed for that small percentage of experts.
When the infrequent need arises for these features, users would rather force
the problem into the features they know than learn new ones.

Given that users actively avoid complexity and more powerful features, it
becomes apparent that these powerful features should be excluded to keep
complexity to a minimum. In doing so, you may annoy the 1% of your users who
are truly experts and need the power. If you have a million users, 1% -- 10,000
users -- can not be forgotten. You need to give these users this power somehow.

Put the power and flexibility into APIs! APIs enable power users to automate
frequent tasks and create the unique functionality they need. As a bonus, new
tools will pop up to fill needs you didn't even consider when you originally
designed your software.

"Wait a minute!" I hear your cry. "Expert users are not necessarily
programmers", you say. This is very true, but there are two factors which
mitigate this problem.

First, most expert users of any software have at least some technical ability
with which to hack together solutions. If you don't believe me, take a look at
the [popularity of Visual Basic][2]. Despite the relatively small number of
professional software engineers using Visual Basic, it still has very strong
search volume and book sales. This is presumably due to its inclusion in
Microsoft Office. However, the "real" software engineers view it as not worth
their time and these are the people producing technical content on the web. As
such, there is less online discussion of the language. Even if an expert is
unable to put something together, they probably know someone who can. And even
if they don't know someone who can, then they probably have just identified a
  need to hire someone.

Second, the needs of experts are so specialized that they need to exert a large
amount of effort bending flexible software to their needs. Sometimes, no amount
of flexibility will solve their problem. Other times, the flexibility is hidden
behind so much complexity that it is not cost effective to uncover it. Still
other times, users bend software until it breaks. Again looking at Visual
Basic, it is quite common for Office users to create macros or scripts to solve
problems that built in tools could have solved. Users do this because they
didn't know they could or couldn't figure out how to utilize the existing
features in the way they wanted to.

The bottom line is that software should be [as simple as possible, not
simpler][3]. Treat users as intelligent, capable intermediates; do not engage
in excessive hand-holding or assume expertise. Where applicable, provide
plug-in or extension mechanisms, so that power and flexibility can be leveraged
by APIs. Your users will love you for it.

  [1]: http://www.amazon.com/gp/product/0672326140?ie=UTF8&tag=youworkformec-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0672326140
  [2]: http://www.langpop.com/
  [3]: http://www.quotedb.com/quotes/1360
