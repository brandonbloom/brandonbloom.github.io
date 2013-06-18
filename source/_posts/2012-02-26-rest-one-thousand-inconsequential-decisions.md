---
layout: post
title: "REST: One Thousand Inconsequential Decisions"
date: 2012-02-26
comments: false
---

Hardly a week goes by without yet another Hacker News front page post by a
blogger making a bold declaration about some important thing you <i>absolutely
must do</i> if you want your API to be "RESTful". With
<a href="http://weblog.rubyonrails.org/2012/2/26/edge-rails-patch-is-the-new-primary-http-method-for-updates">Rails' recent PATCH announcement</a>, REST is all over the front page again.

In my experience designing and consuming APIs of varying levels of RESTfulness,
I've concluded that most of the decisions involved in designing a RESTful API
are completely inconsequential. PUT vs POST vs PATCH? HATEOAS vs manual URL
construction? JSON vs custom Mimetype? It's all the same to an API consuming
<i>developer</i>. It might matter to some kind of generic crawler, but I'm not
building an API for crawlers, so I don't care.

What follows is a series of rants about various parts of the grand REST debate.
This is by no means comprehensive. My goal with this post isn't to discourage
you from writing a REST API. I simply want you to spend your time on the API
decisions that matter, not the cosmetic, inconsequential ones.

#### HTTP Verbs & Status Codes

The Rails PATCH announcement states "both PUT and PATCH are routed to update".
So what's the difference? Apparently PATCH is for partial updates, where as PUT
is for complete replacement of resources. Here's the funny bit: Both of these
are implemented via POST with a `_method` parameter for support of older
browsers. The only two HTTP Verbs that actually matter are GET, for idempotent,
cachable requests; and POST, for side-effects. Even DELETE is insufficiently
defined for any real application. What if you want to support both Archive and
Delete like Gmail?

Which response codes should you use? Those which have specific meaning to HTTP
clients. Specifically: those treated differently by browsers. Should you use
201 on create instead of 200? Who cares? Should you use 301 or 302 for
redirects? In an API, it doesn't matter. Pick one. Document it. For your actual
web page, consult an SEO guide.

What about errors? If you don't have permission to see a resource, is that a
403? Or do you hide the existence of the resource and return a 404 (like
GitHub)? It doesn't matter. Return a 4xx for expected caller errors and a 5xx
for unexpected errors. Pick one. Document it.

The smart thing to do is to always return your resources within one level of
nesting. That is an <i>item</i> key in JSON or an <i>item </i>element in XML.
Do this so that you can add an <i>error</i> key or other metadata without
breaking changes. For example, you may want a <i>timings</i> key for perf
debugging. For lists, use an <i>items</i> key because pretty soon, you're going
to want a <i>paging</i> key. Also, for errors, make sure you include a
non-localized (and hence, Google-able) error string; a status code is
insufficient.

#### Hating On HATEOAS

HATEOAS, an absurd mouthful of an acronym, is a complete waste of time outside
of read-only semi-structured datasets. If you're FreeBase, then go to town with
HATEOAS conventions that are useful to you. However, for your typical webapp
API, you simply cannot build a useful API client application without deep
knowledge of the problem domain and the available API calls. You're going to
have API documentation and you're going to have to read it.

By all means, feel free to use HTTP 201 and a <i>Location</i> header to return
the location of a newly created resource. Or you could just return the resource
as JSON with an <i>href</i> or <i>id</i> key. That's yet another
inconsequential decision. Might as well go with the path of least resistance:
return interesting data in the body of the response (ie. in the JSON) where it
can be accessed without additional effort to parse the headers. As long as your
response code is in the 200 range, no one is going to look at it further.

I've seen people advocate <i>Link</i> headers such as <i>rel="newcomment".</i>
It's weird to me because you still need to know which <i>rel</i> to lookup. The
same thing applies to returning a bunch of extra URLs as metadata in your JSON.
You still need to know which key to look up for the post-new-comment URL. You
might as well just appended <i>"/comments"</i> and avoid the indirection.
<i>Link</i> and <i>rel</i> are useful to generic tools that want to treat a
wide range of resources the same. There aren't enough common aspects between
the domain models of a typical API to justify the spare brain cycles. Again, if
you're Wikipedia, your use case is different. You have generic data that you
want to traverse generically. And again, that's a mostly read-only use case.

#### Versioning

Some REST fans advocate the use of custom MIME types for API versioning. These
folks believe that version numbers in URLs are ugly. The usual objection is
that resources should be equitable by URL and that <i>/v1/users/5</i> and
<i>/v2/users/5</i> are different resources. The problem here is that this
resource is actually identified by just the number. You don't need the whole
URL and practical constraints are going to make it impossible to equate
resources by URL. Consider the constraint of browsers' Same Origin Access
Policy. You need <i>api.example.com</i> to run your public API on it's own IP
address. And you need <i>example.com/api/</i> to make calls from your web
frontend without jumping through crazy IFrame interop hacks.

There are two types of API changes: 1) Backward Compatible, and 2) Breaking
Changes. In the case of backwards compatible changes, you don't have to, nor
should you, change version numbers. Simply add the additional method or
additional return data. Nothing to worry about. In the case of breaking
changes, you <i>want</i> to break clients as loudly as possible. If you simply
change the MIME type, most consumers of the API are totally going to ignore it.
If you deploy your new API at <i>api2.example.com</i>, then there is no risk of
breaking older clients and they have to make a conscious decision to upgrade.

In general, you're probably going to want to protect yourself with API keys.
API keys provide the best way to version your API: record the desired API
version with the generated key. Just stick an <i>api_version</i> column on your
<i>api_keys</i> table. Expose that column as a drop down box in your API key
management UI. This solution let's you leave the "ugly" version number out of
the URL, but still enforce breakage on breaking changes. The best part is that
you can totally ignore API keys and versioning when you start: Simply default
unregistered API consumers to use v1 when you do implement v2.

Last word on custom MIME types: One of the great promises of REST is the
ability to treat generic things, generically. If your MIME type isn't a
standard JSON MIME type, then how can you expect your browser to render it
nicely with syntax highlighting and code folding? Your data is structured as
JSON or XML; if you want to version the schema, use the facilities provided by
XSDs or JSON Schemas, etc. But you're better off with documentation written by
humans, for humans, than you are with formal schema.

#### REST: The Good Parts

There are a lot of good reasons why REST has gained popularity. For one thing,
it isn't SOAP.

The good ideas in REST are primarily predictable URLs, contractual idempotence,
the <a href="http://news.ycombinator.com/item?id=2858712">preference of nouns
over verbs</a>, and CURL as a client library. In practice, you can get
contractual idempotence via GET vs POST. Predictable URLs are no different than
predictable function names in all typical library code.

All this other stuff, the finer points of URIs and HTTP headers, are completely
inconsequential to a developer programming against a well documented API.


## Imported Comments

### Xavier Noria

Note that the motivation for PATCH in Rails is to follow HTTP semantics, it is
unrelated to REST.

### Lars Gierth

> It's all the same to an API consuming developer. It might matter to some kind
> of generic crawler, but I'm not building an API for crawlers, so I don't
> care.

That's how you're consuming APIs now! Imagine building a client that doesn't
break if the API changes its semantics.

The idea of versioning APIs is bad though, as it's contradicting the idea of
building sustainable web services.

### Brandon Bloom

@Xavier: What benefit do I get from following the rules of HTTP semantics on
this point? Semantics are only worth having if they make a distinction that is
useful in practice. HTTP experimented with various verbs and it turns out that
two are universally useful: GET and POST. The rest are ancillary.

@Lars: Your two points conflict. You should only make breaking changes if
you're changing your models meaningful. When you make a breaking change, you
use versioning strategies like changing the base URL. When your models change
meaningfully, your clients will need to change to present the new view of the
problem domain. If you've versioned correctly, old apps will work on the old
API version and new ones will work on the new API version. It's impossible for
an old client to magically support new concepts.
