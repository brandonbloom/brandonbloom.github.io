---
layout: post
title: "ORMs and Declarative Schemas"
date: 2009-10-06
permalink: /blog/:year/:month/:day/:title/
---

My [prior post][1] was more controversial than I anticipated. In hindsight, I
should have realized what a hot button issue web frameworks are. One assertion
went all but unnoticed. I expect it may be even more controversial:

_the schema-generative ORM paradigm is fundamentally flawed_.

#### Disclaimer

I came to this conclusion while working with Django's ORM, but this post is
completely object-relational mapper agnostic. We are now using SqlAlchemy, but
we are not using any of the many available declarative layers. Instead, we are
using schema reflection and semi-automatically configured mappers. This is not
an argument against ORMs. It is an argument against generating database schemas
from ORM declarations. By extension, this is an argument against Django's ORM
because Django uses an exclusively schema declarative model. That said,
Django's ORM is far from alone in this camp.

#### Data Outlives Code

When code is dead and gone -- be it through rewrite, obsolescence, or by other
means -- the data will still be there. Longevity implies slower evolution; data
is always more difficult and riskier to change. Data is also more valuable.
What if Facebook rebooted their database? They've already rebooted their
software several times.

Schemas are data. As data, schemas are longer lived, less flexible, and more
valuable than code. These factors alone suggest that the database itself should
hold the authoritative schema, not a class declaration in the code.

If you have inherited data from another project, you already know this lesson.
You can't generate the schema from code because the schema already exists. You
can mimic the authoritative schema in your declarations, but it is easier and
more accurate to use reflection.

#### Ineffective Domain Objects

Object relational mapping is primarily a serialization problem. Every
serialization solution has its quirks. The scale and number of quirks seem
directly proportional to the absolute difference between the runtime and
storage representations. Since a database is a completely distinct type system,
rather than an opaque byte array, serialization to a database can have
particularly quirky quirks.

Modeling is one of the fundamental challenges of software development. Capable
developers prefer highly expressive or unconstrained type systems to aid with
modeling. Generally, runtime type systems are more expressive than those found
in databases. Declarative relational mappers, however, constrain the
programming language type system to its less expressive counterpart. When
building domain objects, the developer must think in terms of the database's
type system, not the programming language's.

While using a declarative object relational mapper, developers are effectively
trying to design storage and runtime models simultaneously. On average,
superior results are achieved by modeling these two concerns separately and
then solving an additional subproblem: serialization mapping. You might wind up
with slightly more code, but it will be easier to understand and maintain.

#### SQL Is Not Going Away

Despite forcing a less expressive type system on to the developer, declarative
ORM layers attempt to treat the database as an implementation detail. However
much we wish this were true, the database is not a detail which can be ignored.
Sooner or later, you are going to have to open your database shell and write a
SQL expression. This requires knowledge of your database's particular SQL
dialect and idiosyncrasies. Exacerbating this issue, generated schemas are
typically full of name mangling and other ugliness. It is far more pleasant to
work with a carefully designed schema than one that compromises for the ORM or
the runtime type system.

#### Schema and Declarations Diverge

Data migrations present the most pressing need to work with SQL directly. As an
widely unsolved problem, automation can not be trusted. Unless you are
painstakingly simulating the schema generator, the production database schema
will slowly diverge. Most deviations are tolerable as they will not affect the
runtime behavior, but it is wise to minimze differences between production,
staging, and development environments. To faciliate this, store migration
scripts and backed-up schemas in version control.

Some deviations will directly affect runtime behavior. For example, consider
the case where two versions of an application are running in production. An
`is_read` boolean column was added to a message table in the database. It's
default value is false. When a row's page is viewed, the `is_read` column is set
to true. The old version of the application doesn't know about the column, so
it can not set the flag. When the new version is rolled out to everyone,
affected user will see a bunch of read items marked as unread! The solution is
to set the column's default to true, but initialize it to false in the
application. Declarations must either deliberately deviate from the schema or
present a misleading default value to be overridden during initialization. This
is just a simple example, real world schema migrations can be significantly
more complex and suffer from numerous more subtle problems.

## Imported Comments

### Martin Diers

These are all good points, and largely irrelevant for someone developing to
Django. Yes, if you are using Django to develop to a long-term data store,
enterprise data, etc., you are dead right, and you should be using SQLAlchemy
with Django instead of the Django ORM.

However, consider Django's original target use: a newspaper, on the cutting
edge, putting up quick apps on-the-fly with a very short development cycle. A
CMS. A Polling app. A Blog. Think about the type of apps that people actually
build with Django, and you will find that the Django ORM makes perfect sense.
Short development cycle. Easy deployment. "Good enough" query
optimizations.

### Ezequiel

I agree with Martin, Jacob Kaplan-Moss himself said that, when you get to a
point where you're building something really big (e.g. Twitter) you basically
have to throw your framework away.

Django is really good, but, like any technolgy, sometimes is not enough.

### Brandon Bloom

@Martin: The "original target use" argument is no longer relevant. Django is
being pushed as a general purpose web framework for developing sites or apps,
big and small.

@Ezequiel: I'm not building something Twitter big. I'm building something that
has to deal with real user data and survive real design evolution. The problems
I describe affect any application with any longevity of any kind.

### Julien

I agree I'd rather go schema first than model first. As a side note though it's
interesting to think about it in the context of the whole NOSQL mouvement
that's been gaining a lot of traction over the last year. Facebook, LinkedIn or
Amazon for some subset of its data and others are doing away with the
relational stores and going with pure key/value pairs persistence.

Not relevant for every application but very interesting nonetheless.

### Ergo

Have you ever considered something else than django? Like pylons or repoze.bfg?
Django is really not so good solution for more complicated tasks if you want to
work efficiently.

[1]: http://blog.brandonbloom.name/2009/08/dropping-django.html
