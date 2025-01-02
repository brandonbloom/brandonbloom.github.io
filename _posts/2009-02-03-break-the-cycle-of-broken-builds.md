---
layout: post
title: "Break the cycle of broken builds"
date: 2009-02-03
permalink: /blog/:year/:month/:day/:title/
---

Broken builds suck. Breaking the build sucks. No one wants to have their work
stopped by others' build breaks and no one wants to cause work stoppage on
account of their own build breaks.

So then why do builds break so often? You'd think that after so many years of
software development, someone would have solved this problem. Microsoft
employees tens of thousands of engineers across hundreds, if not thousands, of
code projects. Many of those code projects are development tools for the
engineering community at large as well as engineers within Microsoft. Yet,
still, all of the tools at the disposal of those engineers are highly prone to
build breaks.

Below, are three ideas for reducing build breaks. Together, they could all but
eliminate the problem.

### Use a better version control system

Everyone keeps raving about distributed version control, and for good reason. I
won't rehash the argument here, but I will say: branch early and merge often.
Perforce (better known as Source Depot within Microsoft) and it's spiritual
successor Team Foundation Server are [completely unbearable][1] . A great
majority of build breaks can be contained if people are only pulling known good
changes from dedicated integrators. Everyone checking in all at once to a main
branch simply doesn't work with anything but the most tightly knit of teams.
Even if the does build break, it should only block the person waiting on that
particular change.

### Isolate build breaks

`sd submit -c 12345` translates roughly to "I'm 100% confident that my changes
are solid and am equally confident that I am submitting the changes that I
think I am." You never can be 100% confident, don't take any chances: submit to
a private branch which is being monitored by a build service. The server should
detect your submission, run a full buddy build (with unit tests!), and then
submit it to the shared branch only after it
has passed.

If your builds take too long for this, people should be able to grab changes
down from your unverified branch on an as-needed basis. If this happens too
frequently: your builds take too long. Refactor your system into smaller
components and formalize the contractual interface between those components.
Then buddy build just the components which have changed. Verify that the
interface hasn't changed with a unit test. This will stop compile errors at the
boundaries.

### Go lazy, be late

This one is a big of a pipe dream... software systems developed with dynamic
languages do not experience build breaks nearly as often as those developed
with static languages. This is because dynamic languages are typically
lazy-loaded and utilize late bounded method invocations. If someone adds a new
button to the UI which calls a method in a file they forgot to add to source
control, that should only block people who want to click the button!

There is no firm technical reason why statically or JIT compiled systems can't
simulate this. Most compile errors could theoretically be treated as a runtime
error. Clearly, compile errors are valuable, but should be ignorable whenever
possible. Don't ignore them in your known good builds, but ignore them when
they stand between you and forward progress.

Since most of us aren't writing our own compilers are full tool chains, here is
some more practical advice for projects using statically compiled languages:
write code which fails gracefully. Runtime errors can be build breaks just the
same as compile errors, but try to defer the negative consequences as much as
possible. One broken feature shouldn't break the whole project; unless, of
course, you are about to ship. In production builds, perform verification code
at startup or in unit tests, but try to be lazy and late in development builds.

[1]: http://weblog.masukomi.org/2007/8/31/dear-perforce-fuck-you
