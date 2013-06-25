---
layout: post
title: "Learn to Read the Source, Luke"
date: 2012-04-16
comments: false
---

Apparently, I'm Jeff Atwood's guest blogger today:
[Learn to Read the Source, Luke][1]

Since I wrote [the original Hacker News comment][2], I figured I might as well
reproduce it in full here.

> I started working with Microsoft platforms professionally at age 15 or so. I
> worked for Microsoft as a software developer doing integration work on Visual
> Studio. More than ten years after I first wrote a line of Visual Basic, I
> wish I could never link against a closed library ever again.

> Using software is different than building software. When you're using most
> software for its primary function, it's a well worn path. Others have
> encountered the problems and enough people have spoken up to prompt the core
> contributors to correct the issue. But when you're building software, you're
> doing something new. And there are so many ways to do it, you'll encounter
> unused bits, rusty corners, and unfinished experimental code paths. You'll
> encounter edge cases that have been known to be broken, but were worked
> around.

> Sometimes, the documentation isn't complete. Sometimes, it's wrong. The
> source code never lies. For an experienced developer, reading the source can
> often be faster… especially if you're already familiar with the package's
> architecture.  I'm in a medium-sized co-working space with several startups.
> A lot of the other CTOs and engineers come to our team for guidance and
> advice on occasion.  When people report a problem with their stack, the first
> question I ask them is: "Well, did you read the source code?"

> I encourage developers to git clone anything and everything they depend on.
> Initially, they are all afraid. "That project is too big, I'll never find
> it!" or "I'm not smart enough to understand it" or "That code is so ugly! I
> can't stand to look at it". But you don't have to search the whole thing, you
> just need to follow the trail. And if you can't understand the platform below
> you, how can you understand your own software? And most of the time, what
> inexperienced developers consider beautiful is superficial, and what they
> consider ugly, is battle-hardened production-ready code from master hackers.
> Now, a year or two later, I've had a couple of developers come up to me and
> thank me for forcing them to sink or swim in other people's code bases. They
> are better at their craft and they wonder how they ever got anything done
> without the source code in the past.

> When you run a business, if your software has a bug, your customers don't
> care if it is your fault or Linus' or some random Rails developer's. They
> care that your software is bugged. Everyone's software becomes my software
> because all of their bugs are my bugs. When something goes wrong, you need to
> seek out what is broken, and you need to fix it. You fix it at the right spot
> in the stack to minimize risks, maintenance costs, and turnaround time.
> Sometimes, a quick workaround is best. Other times, you'll need to recompile
> your compiler. Often, you can ask someone else to fix it upstream, but just
> as often, you'll need to fix it yourself.

> Closed-software shops have two choices: beg for generosity, or work around
> it.  Open source shops with weaker developers tend to act the same as
> closed-software shops.  Older shops tend to slowly build the muscles required
> to maintain their own forks and patches and whatnot.  True hackers have come
> to terms with a simple fact: If it runs on my machine, it's my software. I'm
> responsible for it. I must understand it. Building from source is the rule
> and not an exception. I must control my environment and I must control my
> dependencies.

After reproducing my comment, Jeff wrote:

> Nobody reads other people's code for fun. Hell, I don't even like reading my
> own code. The idea that you'd settle down in a deep leather chair with your
> smoking jacket and a snifter of brandy for a fine evening of reading through
> someone else's code is absurd.

[I disagree][3].

[1]: http://www.codinghorror.com/blog/2012/04/learn-to-read-the-source-luke.html
[2]: https://news.ycombinator.com/item?id=3769665
[3]: https://news.ycombinator.com/item?id=3851767
