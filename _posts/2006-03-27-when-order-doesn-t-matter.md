---
layout: post
title: "When order doesn't matter"
date: 2006-03-27
permalink: /blog/:year/:month/:day/:title/
---

Programming languages, imperative ones in particular, place a lot of emphasis
on sequence. Take this simple pseuodocode for example:

```
components = [4.5, 3.2, 4.0, 3.8]
min(round_down(components))
```

The result of this code is 3, but that same result can very clearly be achieved
in a less computationally expensive manner by performing:

```
round_down(min(components))
```

Correct me if I am wrong, but current day compilers operate on source code that
lacks the semantic to perform such an optimization.
