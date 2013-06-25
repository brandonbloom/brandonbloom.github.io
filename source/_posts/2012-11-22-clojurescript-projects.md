---
layout: post
title: ClojureScript Projects
date: 2012-11-22
---

I stumbled across [Clojure][1] a few times, but I've fallen in love with it
sometime over the past year. I've always loved the idea of Lisp, but I never
loved any particular Lisp until Clojure.

The name "CLoJure" comes from C#, Lisp, and Java. The best parts of each of
community clearly shine through. Bolstered by the Java community, Clojure
combines all the expressiveness of a Lisp in a package that makes everything
seem as easy as C#. That last point deserves elaboration: C# has managed to
empower the average code monkey with lambdas, closures, generics, expression
trees, and futures. Similarly, Clojure empowers the average senior developer
with immutability, macros, flexible dispatch, software transactional memory,
interactive development, and a whole lot more! Plus, it's free and open source!
Rich Hickey has an incredible talent for distilling good ideas down into
understandable bite sized chunks, making agreeable tradeoffs, and then deftly
guiding the community.

Lately, I've been spending a lot of my spare cycles working on
[ClojureScript][2].  Hacking on the ClojureScript compiler and runtime has been
more fun than I can ever recall in my programming career. It's an incredible
toolkit that I intend to utilize heavily in my next startup, so I feel like my
hours and hours of fiddling with cljs.core is extremely worthwhile. I've
already contributed 20+ patches!

As fun as hacking on ClojureScript is, it's probably a better idea to hack on
something that uses ClojureScript, rather than ClojureScript itself. Paul
Graham described Hacker News as [an application to sharpen Arc on][3]. Paul is
right in saying that you need the top down pressure of a real application to
motivate the design and implementation of the language from the bottom up.
Although where his goal is [brevity][4], Rich's goal is [simplicity][5]. While
Paul has seemingly effortlessly scaled Hacker News to a 1M+ daily pageview
community, Rich has seemingly effortlessly [reinvented the database][6].

ClojureScript hasn't found its killer application yet. There is a lot of
interest around the project, but only a handful of active contributors and
production users. People like [Chris Granger][7] are pushing down on the top
with ambitious applications like [Light Table][8], but we need others. Chris is
starting to make re-inventing the IDE look effortless, but his application
prefers to be installed outside the browser, generally on Macs, so it isn't
clear to me if his vision remains squarely in the
HTML/CSS/JavaScript-targeting, client/server sweet spot.

It has been over seven years since Gmail and Google Maps has made us re-think
what is possible in the browser. I sense that ClojureScript holds the key to
the next generation of re-thinking what's possible in client-side applications.
As I ponder my next startup, I'm going to be on the lookout for just such an
application. In the meantime, I'll be open sourcing some of the technology I'm
building as I explore that path.

[1]: http://clojure.org/
[2]: http://github.com/clojure/clojurescript
[3]: http://www.paulgraham.com/hackernews.html
[4]: http://paulgraham.com/arcchallenge.html
[5]: http://www.infoq.com/presentations/Simple-Made-Easy
[6]: http://www.datomic.com/
[7]: http://www.chris-granger.com/
[8]: http://www.lighttable.com/
