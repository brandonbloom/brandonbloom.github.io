---
layout: post
title: "Contributions"
date: 2009-01-26
permalink: /blog/:year/:month/:day/:title/
---

The modern, read/write web thrives on processes which accept and coordinate a
large set of contributions from a set of contributors that is almost as large.
Before this was common on the web, open source software capitalized on the
aggregate efforts of communities. However, not every web 2.0 site or open
source project succeeds in attracting or retaining contributors. In the case of
web sites, this is often due to poor user interface or other highly visible
problems

Software projects, however, typically suffer from a different problem: the cost
of creating and integrating contributions. This ailment also afflicts
proprietary software, where it is actually rarely diagnosed, and even more
rarely treated.

I argue that all substantial software projects should strive to minimize two variables:

* Aggregate cost adding a contributor [1]
* Maximum individual cost of accepting a contribution
* If either of these variables are not minimized, a project will fail to fully
  utilize the efforts of its key members, never mind begin to capitalize on the
  long tail of contributions. High contribution costs waste countless millions
  of hours of productivity.

In a company, the easiest way to measure the cost of adding a contributor is to
measure the time from signing the employee agreement until the moment that the
software builds and runs on the new-hire's development machine. Don't forget to
count this in man-hours because this will surely require the efforts of a
manager, a more senior engineer, or both.

Additionally, you should also count the time before the new developer feels
some level of confidence in the codebase. Can this new person dive right in and
start hacking on a new feature? Could they fix a bug without everything falling
apart? How many times will they need to knock on the door of the expert? [2]

Measuring the cost of accepting a contribution is subtly different. The
aggregate cost is not of critical importance as long as it is justifiable given
the benefit of accepting a contribution. However, minimizing the aggregate cost
of accepting a contribution is already the goal of any successful software
project. Teams are always striving to be more efficient, but it is a local
optimization. If you want to increase the number of contributors and
contributions, you need to minimize the maximum cost to maintainers, newcomers,
other team members, and communities.

As any good manager knows, they must suffer the pain of meetings, accept
frequent context switching, and act as a shield from politics. They must do all
of this to ensure their reports are as productive as they possibly can be.
Similarly, senior engineers should donate some of their time to analogous tasks
in component and systems ownership. Although these tasks introduce a cost which
causes an individual contributor to spend less time actually contributing, it
opens the door to greater net contributions.

So here come's the concrete advice part of the post...

### Some ways to reduce the cost of adding a contributor

* Buddy builds - One button should instantly and automatically allocate a fresh
  machine, enlist in the source tree, build without additional dependencies,
  execute all tests, and provide detailed status and results.
* Standardized tools - Allow contributors to transfer their skills in version
  control, issue tracking, building, testing, and communication. Every team is
  different, but the more you can reasonably share, the more you simulate one
  cohesive team.
* [Encourage branching](http://en.wikipedia.org/wiki/Distributed_revision_control)
* Understand that contributions can come from anyone [3]
* Keep documentation up to date and complete - Hold new project members
  accountable for the correctness and completeness of the newcomers wiki page.

### Some ways to reduce the cost of accepting a contribution

* Automated unit tests - Insist on a high code coverage and a low flakiness
  rating. Require a 100% pass-rate before committing to the main branch.
* [Explicit coding standards](http://www.python.org/dev/peps/pep-0008/)
* Code reviews
* "Lieutenants" - Put an "owners" file in each directory of your source tree.
  Fill it with the usernames of those who should perform the code reviews for
  those files and sub-directories. Keep these files up to date.
* [Widely and clearly communicate project vision and
  direction](http://code.djangoproject.com/wiki/VersionOneRoadmap) - This
  ensures misguided contributions will be rejected swiftly.
* Regular release cycle - Do I really need to say "release early, release
  often"? Releasing often enough will force you to reduce the cost of your
  release process as well.
* Got any more?

[1] This post was prompted by my ~~startup~~ hobby project's failure in this
respect. Adding a third team member took several hours of my attention, but
should have taken several minutes.

[2] Sorry Stephen!

[3] Some discussion lists at Microsoft are notorious for responding to internal
feedback with the URL http://career/ and possibly a snarky comment. These
people are artificially setting the cost of adding a contributor to be a career
left-turn and the cost of accepting outside contributions to infinity. I hope
these dinosaurs go extinct.
