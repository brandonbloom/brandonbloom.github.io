---
layout: post
title: "Dropping Django"
date: 2009-08-19
permalink: /blog/:year/:month/:day/:title/
---

My co-founder and I built a non-trivial web site on Django. When the next
version ships, there might not be a single Django module imported.

We're not trying to drop Django; it is just sort of happening. Piece by piece,
it is failing to meet our needs. Despite the marketing copy on the Django site,
most components of the framework are tightly coupled enough to make
customization frustrating. It is often easier to rewrite core framework
components than to implement them on top of the existing  extensibility points.

What follows is a loose chronology of our migration away from Django.

#### URL Routing

A flat list of patterns violates the DRY principal when creating nested URLs.
Trees are a superior representation. Having a tree of views also enabled us to
optionally associate a "binder" function with each node. These bind functions
are executed for each URL component from left to right, filling the template
context as they go. Breadcrumbs are automatically generated as each node binds,
but only the last node executes its full view logic.

#### Authorization

Our site enforces permissions on every resource, but Django's database ACLs
would have been prohibitively numerous. Instead, views or their URL binders may
raise an AccessDenied exception. Upon catching such an exception, a middleware
layer serves a login form. This ensures users have permission to access the
current resource, as well as all ancestor resources bound to the URL.

#### Authentication

Both of Django authentication's key extensibility points are flawed. These two
extensibility points are "user profiles" (storing additional per-user data) and
custom credentials (such as for logging in via email address instead of
username). Django's documentation and numerous internet sources cover both
topics, but all of the guidance lacks important caveats. The admin UI, in
particular, is very easy to break with either extensibility mechanism.

Extending the User model with the ORM requires a one-to-one database
relationship. This relationship can be implemented with a "user profile"
setting, an explicit foreign key, or model inheritance. Each approach has its
own strengths and weaknesses in terms of performance, API semantics, subtle
behavioral changes, and outright bugs.

Enabling custom credentials requires implementing a trivial authorization
"backend" object. Unfortunately, it is non-trivial to replace usernames with
email addresses. The admin UI's login form refuses to accept email addresses
without hacking the template. Even if you hacked the template, the User model
would still enforce a non-null constraint on the username field and the
generated database schema enforces a uniqueness constraint as well. It turns
out to be easier to fill the username field with a dummy value and "support"
both forms of authentication with your backend, but you won't come to that
conclusion until your head has already bore a hole in your desk.

#### Templating

We do our best to keep view and template logic separate. Django's templates are
targeted at designers, who aren't implementing any real logic anyway. However,
we're a pair of hackers. Sometimes it is just more convenient to put a little
bit of logic in the views. Besides, templates are code; code needs to be
reviewed and tested. We wouldn't ever hire a designer who couldn't pass a code
review for some trivial template logic.

We needed a pragmatic template language to replace Django's idealistic one. Any
template language with greater expressive power would have been welcome, but
Jinja2 fit the requirements and provided the easiest migration path.
Ultimately, we'd prefer to use something like HAML, but there doesn't seem to
be a Python equivalent besides the inactive GHRML project. We are, however,
using SASS. I will never write CSS by hand again.

#### ORM and Admin UI

One of Django's most touted features is the Admin UI. For simple "active
record" style database models, the Admin UI is a huge time saver. Sadly, it
struggles a little bit with nullable fields and is tricky to customize. You'll
definitely need to write custom UI for complex models, but by and large the
admin solves the problem at hand: viewing, creating, updating, and deleting
database rows.

After using the Admin for a little, I found myself missing Microsoft Access. I
never thought I'd say that, but it is true. ~~Django's admin does not support
sorting, filtering, or other impromptu queries.~~ <small><i>Edit: It turn's out
I was mistaken about sorting and filtering, but I stand by the core message of
this section.</i></small> I found myself writing impromptu queries in the
database and Python shells. After a while, I just gave up and installed a
desktop client. I haven't visited the Admin UI since.

Django's ORM has shortcomings with respect to querying, especially for joins
and aggregation. It has been improving over time, but it will likely never
reach the capability of projects solely focused on databases, such as
SqlAlchemy. With the admin having fallen into disuse, the Django ORM lost all
advantage. Beyond Django's specific weaknesses, I've come to believe that the
schema-generative ORMs paradigm is fundamentally flawed. That is a topic that
deserves an entire (Django-agnostic) post of it's own. We are now using
SqlAlchemy via schema reflection; no declarative layer.

#### Form Validation and Generation

Here is where our chronology meets present day. We are still using Django form
validation, but never used form generation beyond scaffolding. Nearly all of
our templates customize labels and display of errors. Additionally, embedding
widget information in the Python code is cumbersome during template
development. Django forms is a quality validation library, but there are some
inconsequential style things that I like better about FormEncode. Preferences
aside, the difference isn't large enough to justify switching.

While I like FormEncode, I'm still not sold on its anti-form-generation
companion, htmlfill. I think there is a middle ground with form generation that
provides scaffolding during development, smoothly transitions to production
use, and cooperates with validation. As we implement more complex client views,
I'll be on the lookout for ways to improve our form development toolbox.

#### So, ugh... What's left?

Besides a few isolated helper functions, not much is left of Django.

The last big ticket item is the HTTP framework and WSGI server. We could
continue using Django as if it were CherryPy or Paste, but Django has this
nasty habbit of insisting on running your code for you. The settings and
manage.py infrastructure are fiddly for deployment and don't really add any
value over simple scripts using our application like a library. Might as well
use a simpler WSGI library, and replace those over-engineered
`management/commands/foo.py` files with vanilla `scripts/foo.py` files.

#### Moral of the Story

I'm sure there are numerous lessons to be generalized from this journey.
Personally, I've developed a moderate fear of the word "framework", as well as
altered the way I think about software abstractions. I think the most important
lesson, however, is one I already knew: choose the right tool for the job.
Unfortunately, we had no idea what the right tool was when we started. I'm not
sure we know any better now.


## Imported Comments

### Austin

Maybe it worked out exactly like it should have.  Django bootstrapped your app
to a certain point.  Got you further faster than you would have if you
implemented everything from scratch.  Then from there, you identified the
things you considered inadequate and replaced them.  If it all goes away who
cares.   You have learned something, shared it with us and moved on.

You seem to have successfully moved away from it to your own custom code.  I am
impressed that it sounds like your decoupling your app from Django was not as
traumatizing as it might have been.

### Brandon Bloom

Yeah, everything seemed to work out OK. Django was definitely a big help,
especially with my initial limited web experience (previously, I was a game and
desktop application developer).

If I were to do it all again, I probably wouldn't choose Django. That's not to
say Django wouldn't be the right choice for someone else. I just think that I'd
choose a collection of tools that are less likely to get stuck glued together.

### Eric

I think Pylons would be a great fit for you. It matches the way you want to
work.

### Varikin

Your URL routing sounds intreging.  I like have been looking at Werkzeug,
Routing and Django. I prefer Django to all three, but still I feel like
something is missing.

Could you elaborate on your solution?

### Juan_Pablo

Take a look at Werkzeug. Despite the weird name is the most modular and yet
easy to use wsgi wrapper and dispatcher I know.  Combine it with Jinja2 and
SQLAlchemy... and voilà.

### WhatDoYouThink?

Good thing there are people like you who would share your improvements back to
the community!

### Brandon Bloom

Thanks Juan, I'll take a look at Werkzeug.

Eric: I've been looking at Pylons, but I think I want to go with something more like Juan's suggestion.

Varikin: I was able to implement my URL routing over Django by simple matching
(.*) and then writing a view which splits by '/'. I then loop over each
component and perform a depth-first search of a tree structure. Each node has
an optional bind(request, template_context, path_component) function. After I
find the node which matches the path, I call the ancestor chain's bind
functions and then finally the current node's view function. We've got some
decorators and helper functions to make building this tree easy. I'd share it,
but it is too tightly tied to our application. Maybe I'll write more about this
later.

WhatDoYouThink: My improvements are application specific. I'm sharing my
experiences to help the community. That is far more than most people do.

### Jeremiah

This comment has been removed by the author.

### gaspode

The tree stuff and permission scheme you'd like perfectly matches up how
repoze.bfg works with traversal and security based on traversal schemes (also
known as object dispatch).

I'd highly recommend giving it a shot as it seems to mesh quite well with the
way you've laid out the app.

### Tucanae Services

Think Web2Py.....

### Chris Shenton

Ditto what gaspode said. I've been fighting Django for an app whose content is
basically hierarchical and BFG's traversal would really be a relief.

### Cesar

I'd like to add my 2 cents.  Restish has no threadlocals (compatiable with
eventlet/spawning) and focuses on resources/content negotiation.  I believe the
URL routing is similar to what you want

### handi

> Enabling custom credentials requires implementing a trivial authorization
> "backend" object. Unfortunately, it is non-trivial to replace usernames with
> email addresses. The admin UI's login form refuses to accept email addresses
> without hacking the template. Even if you hacked the template, the User model
> would still enforce a non-null constraint on the username field and the
> generated database schema enforces a uniqueness constraint as well. It turns
> out to be easier to fill the username field with a dummy value and "support"
> both forms of authentication with your backend, but you won't come to that
> conclusion until your head has already bore a hole in your desk.

We wanted to let users log into our app using their email address through
contrib.auth.view.login. We don't care about having email addresses log into
the Django Admin.

You have to dig around a bit, but it can be done with a few lines:
<a href="http://gist.github.com/170691" rel="nofollow">http://gist.github.com/170691</a>

### nassrat

I think you were looking for Pylons not Django. Each has its own advantages. I
learnt that Django should be taken as is, once you start stripping it apart,
IMHO you should consider using Pylons.

### reedobrien

Sounds like you should look at http://docs.repoze.org/bfg/current/
http://bfg.repoze.org is a nice microframework which stays out of the way as
much as you want it to.

### Jonathan Ellis

If you like SQLAlchemy, you will probably like FormAlchemy for form generation
from SQLA models.

### adeel

I've written my last few web apps on top of
<a href="http://pypi.python.org/pypi/berry" rel="nofollow">Berry</a>,
which just lets you map URLs to methods (and lay them out however you want).
And obviously you can import SQLAlchemy, Jinja, and whatever else you want
yourself.  I've found this gives me the most flexibility possible without
actually writing a raw WSGI app.

### Michael Galpin

You really need to read this
http://terrychay.com/blog/article/challenges-and-choices.shtml. It will help
you understand why you were doomed from the beginning :-)

### Brandon Bloom

Thanks Michael. Despite the distracting *laughter* that was an interesting
read.

### gunzip

> After using the Admin for a little, I found myself missing Microsoft Access.

it still happens to me every time i start a new data-entry project on the web.
sometimes i think something went wrong in the latest 20 years...

### Dead Man

I'm also building a project on Django (a medium sized, multi language web
encyclopedia about a Northern-European state Estonia) and I have to admit that
I've run into mostly the same problems. What surprised me the most is that I'm
implementing a nearly identical to yours solution for URL mapping as our
content is also in a (generic) tree structure. The difference being that I'm
going to use class based views that allow nesting/composition of other views
for the purposes of chaining that you described.

I'm also thinking of open sourcing the code as the project matures more as it's
already written in a clean way and decoupled from project specific code.

### ars

Beyond Django's specific weaknesses, I've come to believe that the
schema-generative ORMs paradigm is fundamentally flawed. That is a topic that
deserves an entire (Django-agnostic) post of it's own. We are now using
SqlAlchemy via schema reflection; no declarative layer.

Well you caught my attention with that. Are you planning to write such a post
in the near future, say in the next two weeks?

### Andrew Shultz

One of the big things I appreciated about django is the unit test supports.
What's your plan to replace that stuff?

### proteusguy

Almost all of your technical feasibility issues are easily addressed by Django.
URLs, authN/authZ, etc are all as flexible or more than any other python
environment out there because it is so simple to leverage python to implement
this things in an incredibly simple manner. This <a
href="http://uswaretech.com/blog/2009/08/a-response-to-dropping-django/"
rel="nofollow">blog entry</a> demonstrates it better than I could. It's really
just a matter of perspective and context. Django may not be the context that
best fits your perspective but it most certainly has the capability and power
to address all the technical concerns you brought up. Good luck finding the one
that fits you best. I actually thought I'd be a TurboGears guy but ended up
Django. Both are great and others are good as well.

### Alexei

I am working on building a medium size application in Django too.  It is indeed a good way to start.  But I am hitting issues too.  I have multiple "agencies" so need to have users with multiple roles in agencies.  These are within single site.  That also means multiple "admins" with one "super admin."  So Django Admin interface is out of the question and role/permission infrastructure is built from scratch.
Another thing is importing data.  I started writing scripts within Django framework (using settings.py, etc.) but need some tables/entities created during import and then dropped.  Oops.  Have to look at how South does it.

I realize that I am moving out of Django domain just because of the nature and complexity of the application I am building.  Django cannot and should not be everything for everybody. 

If you are looking for a "glue" framework, I recommend checking out Cherrypy: I like its configuration and request/response handling better than Paster + WebOb.  It also has several types of routing, pick what you like or mix it (I would not recommend the latter though), one of them is Routes.  

My next move will most likely be to Cherrypy + SQLAlchemy + FormAlchemy + Jinja2.

By the way, repoze.bfg is not microframework either, it pulls quite a large chunk of Zope code, primarily for managing configuration but for templating too.      </div>
</div>

### RJ Ryan

Got a few issues with the things you said -- I'll list them by section.

URL routing is not a flat list. You can make them hierarchical. How do you
think all of the admin site routes get added? It's in the template urls.py for
any starter project, so I really don't see how you missed this.

Authorization -- I don't think I understand what you are saying is wrong --
"too numerous?" But designing a permission system using the Django auth system
has usually been pleasant for me. To require certain permissions just use a
decorator on your views which checks for it.

Authentication. There's nothing that says you have to make a one-to-one with
User and whatever profile system you have. That's just how most people do it
because most projects want 1-1. Make a ManyToMany from your Profile to User if
you want. There's nothing stopping you. Also, using email address as your login
was simple -- swap the auth backend. It's like 10 lines of code.

Templating: Fair enough, I've wnted the templates to allow a little bit more in
the way of doing logic only for purposes of output, but you realize if they
make allowances for that, then it opens the templates up to abuse -- and then
you get PHP. I've actually come to appreciate the template strictness now, and
when I come across something I just have to do in the template, I write a
templatetag.

ORM/Admin -- I can't really understand what you're trying to say here. The
Admin UI is a very nice piece of software, and it is extensible at every point.
Though, the documentation on doing so is skimpy. Take a look at Satchmo -- they
do amazing things to the Admin UI that really showcase its flexibility. As for
  the ORM -- if it really doesn't suit your needs, upgrade to django-sqlalchemy
  or something.

Forms -- it's true forms are sort of a go-between between views and templates.
It does seem odd to specify the widget in the form declaration. Shrug, though
the form is more of a prototype of a form, so you design the prototypical form
that is instantiated in a view. This makes sense to me.

I'm sure you actually did run into problems with Django that caused it to be
frustrating for you, and to that I say ymmv -- I just couldn't help reading
your post and being like "but that's not true, because I've done X and he said
it can't do X".

### Shantanu Kumar

Did you consider Jython? You could then probably use any framework from the
Java-space.

### EH

Faced same problems with Django during 2 years of its using. Looked also at
Paste, web.py, Werkzeug, Pylons, WebOb to replace over-weight framework with
more flexible tools. My current best python tools is Werkzeug, Mako,
SQLAlchemy.

### reedobrien

Alexei said> By the way, repoze.bfg is not microframework either, it pulls
quite a large chunk of Zope code, primarily for managing configuration but for
templating too.

I guess we will have to agree to disagree. By alexei's definition 20 packages
isn't 'micro'. By mine 4 packages I wire together isn't a framework...but to
clarify some misnomers:

repoze.bfg is a microframework. it consists of 20 packages, 8 of which are zope
packages. Although, BFG uses them you never have to.

The default templating package implements TAL, but it is NOT a zope package.
You are not bound to it either. You can plug in Jinha2, Mako, Genshi or
whatever you want.

There is no default database, form library or anything else.

FWIW zope is about 170 packages. 8 is not a 'large chunk' of that by any
definition.

### Sébastien

If you're looking for a HAML Python equivalent, there is PAML (Pamela) at
http://github.com/sebastien/pamela -- also, you can check CleverCSS as a SASS
replacement.

### Gabriel

I totally agree with RJ Ryan.  No framework will suit every project, but Django
covers a lot of the basics in a way that's good enough and doesn't get in the
way when I need to extend it.  Still, thanks for telling us why it wasn't the
tool for you.

### Tony Landis

I use pylons + mako + sqlalchemy and love it

### Brandon Bloom

@Dead Man: I’ll probably publish something about our approach to URL routing
soon. It seems that several people have shown interest in it.

@ars: Yes, I will probably be writing about declarative ORM layers in the next
week or so.

@Andrew Shultz: We’re not doing a whole lot of automated testing yet. Small
helper functions typically have doctests, we’ve got a couple unit tests, and
we’re currently playing with Selenium for full tests. I’m much more confident
in tests which drive the UI than tests which only poke at the views.

@proteusguy: I just read that blog entry and will respond to it separately.
Again, I’m not bashing Django so much as I am saying we’ve been slowly choosing
tools that better fit each need as we identify those needs. Technical
feasibility aside, we’ve been looking for a path of least resistance to
accomplishing our goals and this post was just to describe them.

@Alexei: That’s the impression I got too; Django would have had greater
longevity on my tool shelf if I needed a highly customizable CMS. I don’t need
more flexibility, I need less constraints.

@RJ Ryan: Like I said about, I’ll try to follow up with more info about our URL
routing system. What needs we had and why I felt I had to subvert Django to get
them.

@Shantanu Kumar: There are already too many choices of Python frameworks! :-)

@Sebastien: Thanks for the PAML link! I’ll take a look at it. As for CleverCSS,
I don’t see a need to switch from SASS. We only need Ruby and SASS installed on
dev machines, the server only ever works with the compiled CSS.

### dkubb

> "Beyond Django's specific weaknesses, I've come to believe that the
> schema-generative ORMs paradigm is fundamentally flawed."

I too would like to see some more detail on this.  I maintain a Ruby ORM called
DataMapper, and while the API is completely different, it is still declarative.
I prefer the declarative style to ActiveRecord, since I can specify rich types
and constraints than I could reflect from a database.  The way I see it, at
some point the schema needs to be defined, and I'd rather do it in a richer DSL
and have the schema, validation and other constraints generated from that.

Even so, I'd love to hear a different view point.

### jgrant

http://jng.imagine27.com/articles/2007-07-12-163820_red-vs-blue.html

A somewhat dated (and biased) comparison of Python/Django with Ruby/Rails.

### Max

This is a subset of the reason for why we made
<a href="www.web2py.com" rel="nofollow">web2py</a>.
Thanks for explaining them so clearly.

### Ian Lewis

pylons is great being a framework that allows mixing and matching libraries.

My ideal would be

* Werkzeug
* Jinja2
* SqlAlchemy
* Babel
* wtforms (FormEncode and htmlfill's pattern of inserting html is gross)

I would probably build on Django's idea of applications and middleware as
that's a good paradigm.

### Brandon Bloom

For anyone interested and tracking this thread, I've posted a follow-up:

http://blog.brandonbloom.name/2009/08/url-routing-and-views.html

### Angel

You <i>can</i> have non-flat URL routing. After your first point demonstrated a
failure to grasp more than the basics before whining, I tuned out.

<a href="http://docs.djangoproject.com/en/dev/topics/http/urls/#including-other-urlconfs" rel="nofollow">Nested URL routing confs and includes</a> (new in 1.1) are what you should have used instead of bitching on the internet. It wouldn't have made you seem as cool, but you would have gotten the job done instead.      </div>
</div>

### Brandon Bloom

Without the changes in Django 1.1, modules were the only recursive mechanism;
too heavyweight for my taste. The 1.1 beta was released in March. I had
developed my own solution to this problem several months before then.

Even with the 1.1 improvements, Django doesn't meet my other requirements:
namely binding URL segments to the database and creating breadcrumbs while
filling a template context dictionary. Since that work involves parsing
request.path myself, I might as well just bind (.*)+ and do my own routing. It
was relatively trivial.
