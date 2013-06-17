---
layout: post
title: "URL routing and views"
date: 2009-08-24
comments: false
---

As promised, I'd like to elaborate on the URL routing system I came up with.

Weighing at less than 200 lines of code (including example), I'll let it speak for itself:
<a href="http://www.brandonbloom.name/static/blog_files/viewdemo.zip">download it</a>.

This approach seems to be working great for us. Love it?
Hate it? Feel free to let me know what you think.

### Imported Comments

### Dead Man

You are hardcoding child views in the children method of each view which means
the view can't decide what (type of) children it has based on the current path
segment. For example, I have a use case in which some content types can contain
children of more than 1 type, so for example /foobar/child/ can either be a
TypeAView or TypeBView depending on whether a TypeA with name "child" can be
found or not - if it cannot be found, TypeBView will be returned. If your
children() method took a path segment as an argument, it would probably solve
this problem.

BTW have you checked out how Restish
resolves/dispatches URLs? I think they have it right when it comes to dynamic
URL trees.

### Brandon Bloom

Child views are not "hard coded". They are determined by the children method.
You could pass segment, but it would be easier for the bind method to just
store something on self for use later.

I looked at Restish a bit. It seems like a nice library; definitely the closest
to my taste from what I've seen.

### Thomas

I'd recommend taking a look at Werkzeug for your routing & other wsgi basic
needs.

As a follow-up to the previous post, Jinja 2 makes for an excellent templating
engine (think django but much faster and without the stupid limitations).

And as a shameless plug, have a look at WTForms for your forms:
http://wtforms.simplecodes.com/

### Andriy Drozdyuk

I am sorry I still don't get how this serves as an improvement over Django's
url system?

Can you provide me with a concrete example of what you cannot do in Django in
terms or url-routing?

### Andriy Drozdyuk

in terms of* I meant. Spelling is not one of my strongest areas eh?

### Brandon Bloom

There is nothing that Django's routing can't do. In fact, the first version of
this simply generated Django routes. This is about making is faster and easier
to define hierarchical permissions and trees of URLs in a way that queries the
database on a per-url-segment basis.
