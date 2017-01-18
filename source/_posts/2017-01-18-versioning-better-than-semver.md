---
layout: post
title: "Versioning Better Than SemVer"
date: 2017-01-18 01:30
---

I've written before about [my dislike for SemVer][1]. Recently, Rich Hickey
[spoke about this very same issue, and more][2]. I'm currently working on some
software that I'm hoping people will use, so I decided to make my thoughts on
a better versioning scheme clear. Below you'll find an exerpt from my new
project's documentation. Each point below could be elaborated on in a full post
of it's own, but this summary has value as a checklist of sorts for those
discussions.


## Contracts

Software should make explicit what it promises to provide, as well as make
explicit the requirements it relies on. Implicit promises or reliance should
be avoided. This is a as much a social contract as it is a technical one.

### Breakage

If software relies on an explicit promise, it should never break.

### Instability

It is often useful to defer commitments. The default social contract should be
one of instability. In the absence of explicit promises, everything should be
considered unstable, and subject to change.

### Accretion

It should always be possible to provide more. Making additional promises should
never cause breakage.


## Migrations

Occasionally supporting old contracts becomes burdensome. Parties to these
contracts should be provided a path to a new contract without breakage.

### Retractions

Retracting promises should always be preceded by sufficient warning in order
to avoid breakage. This entails a new contract which specifies deprecated
provisions at the same time as providing new promises to migrate to.

### Timelines

Contracts are identified by a ordinal version number and an instant in time.
Any contract may accrete new promises or bug fixes by creating an artifact
tagged with a new instant. Successive ordinal versions may retract promises
deprecated by previous ordinals.


## Coexistence

Even in the presence migrations, grandfathered contracts should be honored.

### Side-by-Side

A contract timeline may be forked by accreting a new contract with a new name.

### Layering

Platform systems should facilitate openness such that side-by-side systems may
interoperate. Where this is not immediately possible, lower layers should be
extended to facilitate this before forking higher layers.


[1]: http://www.brandonbloom.name/blog/2013/06/19/semver/
[2]: https://www.youtube.com/watch?v=oyLBGkS5ICk
