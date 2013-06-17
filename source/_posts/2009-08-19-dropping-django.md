---
layout: post
title: "Dropping Django"
date: 2009-08-19
comments: false
---

<div class='blogger'>
  <div class='post'>
    <p>My partner and I built a non-trivial web site on Django. When the next version ships, there might not be a single Django module imported.</p ><p>We're not trying to drop Django; it is just sort of happening. Piece by piece, it is failing to meet our needs. Despite the marketing copy on the Django site, most components of the framework are tightly coupled enough to make customization frustrating. It is often easier to rewrite core framework components than to implement them on top of the existing  extensibility points.</p ><p>What follows is a loose chronology of our migration away from Django.</p ><p><b>URL Routing</b></p ><p>A flat list of patterns violates the DRY principal when creating nested URLs. Trees are a superior representation. Having a tree of views also enabled us to optionally associate a "binder" function with each node. These bind functions are executed for each URL component from left to right, filling the template context as they go. Breadcrumbs are automatically generated as each node binds, but only the last node executes its full view logic.</p ><p><b>Authorization</b></p ><p>Our site enforces permissions on every resource, but Django's database ACLs would have been prohibitively numerous. Instead, views or their URL binders may raise an AccessDenied exception. Upon catching such an exception, a middleware layer serves a login form. This ensures users have permission to access the current resource, as well as all ancestor resources bound to the URL.</p ><p><b>Authentication</p ><p></b>Both of Django authentication's key extensibility points are flawed. These two extensibility points are "user profiles" (storing additional per-user data) and custom credentials (such as for logging in via email address instead of username). Django's documentation and numerous internet sources cover both topics, but all of the guidance lacks important caveats. The admin UI, in particular, is very easy to break with either extensibility mechanism.</p ><p>Extending the User model with the ORM requires a one-to-one database relationship. This relationship can be implemented with a "user profile" setting, an explicit foreign key, or model inheritance. Each approach has its own strengths and weaknesses in terms of performance, API semantics, subtle behavioral changes, and outright bugs.</p ><p>Enabling custom credentials requires implementing a trivial authorization "backend" object. Unfortunately, it is non-trivial to replace usernames with email addresses. The admin UI's login form refuses to accept email addresses without hacking the template. Even if you hacked the template, the User model would still enforce a non-null constraint on the username field and the generated database schema enforces a uniqueness constraint as well. It turns out to be easier to fill the username field with a dummy value and "support" both forms of authentication with your backend, but you won't come to that conclusion until your head has already bore a hole in your desk.</p ><p><b>Templating</p ><p></b>We do our best to keep view and template logic separate. Django's templates are targeted at designers, who aren't implementing any real logic anyway. However, we're a pair of hackers. Sometimes it is just more convenient to put a little bit of logic in the views. Besides, templates are code; code needs to be reviewed and tested. We wouldn't ever hire a designer who couldn't pass a code review for some trivial template logic.</p ><p>We needed a pragmatic template language to replace Django's idealistic one. Any template language with greater expressive power would have been welcome, but Jinja2 fit the requirements and provided the easiest migration path. Ultimately, we'd prefer to use something like HAML, but there doesn't seem to be a Python equivalent besides the inactive GHRML project. We are, however, using SASS. I will never write CSS by hand again.</p ><p><b>ORM and Admin UI</b></p ><p>One of Django's most touted features is the Admin UI. For simple "active record" style database models, the Admin UI is a huge time saver. Sadly, it struggles a little bit with nullable fields and is tricky to customize. You'll definitely need to write custom UI for complex models, but by and large the admin solves the problem at hand: viewing, creating, updating, and deleting database rows.</p ><p>After using the Admin for a little, I found myself missing Microsoft Access. I never thought I'd say that, but it is true. <strike>Django's admin does not support sorting, filtering, or other impromptu queries.</strike> <small><i>Edit: It turn's out I was mistaken about sorting and filtering, but I stand by the core message of this section.</i></small> I found myself writing impromptu queries in the database and Python shells. After a while, I just gave up and installed a desktop client. I haven't visited the Admin UI since.</p ><p>Django's ORM has shortcomings with respect to querying, especially for joins and aggregation. It has been improving over time, but it will likely never reach the capability of projects solely focused on databases, such as SqlAlchemy. With the admin having fallen into disuse, the Django ORM lost all advantage. Beyond Django's specific weaknesses, I've come to believe that the schema-generative ORMs paradigm is fundamentally flawed. That is a topic that deserves an entire (Django-agnostic) post of it's own. We are now using SqlAlchemy via schema reflection; no declarative layer.</p ><p><b>Form Validation and Generation</b></p ><p>Here is where our chronology meets present day. We are still using Django form validation, but never used form generation beyond scaffolding. Nearly all of our templates customize labels and display of errors. Additionally, embedding widget information in the Python code is cumbersome during template development. Django forms is a quality validation library, but there are some inconsequential style things that I like better about FormEncode. Preferences aside, the difference isn't large enough to justify switching.</p ><p>While I like FormEncode, I'm still not sold on its anti-form-generation companion, htmlfill. I think there is a middle ground with form generation that provides scaffolding during development, smoothly transitions to production use, and cooperates with validation. As we implement more complex client views, I'll be on the lookout for ways to improve our form development toolbox.</p ><p><b>So, ugh... What's left? </b></p><p>Besides a few isolated helper functions, not much is left of Django.</p ><p>The last big ticket item is the HTTP framework and WSGI server. We could continue using Django as if it were CherryPy or Paste, but Django has this nasty habbit of insisting on running your code for you. The settings and manage.py infrastructure are fiddly for deployment and don't really add any value over simple scripts using our application like a library. Might as well use a simpler WSGI library, and replace those over-engineered management/commands/foo.py files with vanilla scripts/foo.py files.</p ><p><b>Moral of the Story</b></p ><p>I'm sure there are numerous lessons to be generalized from this journey. Personally, I've developed a moderate fear of the word "framework", as well as altered the way I think about software abstractions. I think the most important lesson, however, is one I already knew: choose the right tool for the job. Unfortunately, we had no idea what the right tool was when we started. I'm not sure we know any better now.</p>  </div>
  <h2>Comments</h2>
  <div class='comments'>
    <div class='comment'>
      <div class='author'>Austin</div>    <div class='content'>
    Maybe it worked out exactly like it should have.  Django bootstrapped your app to a certain point.  Got you further faster than you would have if you implemented everything from scratch.  Then from there, you identified the things you considered inadequate and replaced them.  If it all goes away who cares.   You have learned something, shared it with us and moved on.  <br /><br />You seem to have successfully moved away from it to your own custom code.  I am impressed that it sounds like your decoupling your app from Django was not as traumatizing as it might have been.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    Yeah, everything seemed to work out OK. Django was definitely a big help, especially with my initial limited web experience (previously, I was a game and desktop application developer).<br /><br />If I were to do it all again, I probably wouldn&#39;t choose Django. That&#39;s not to say Django wouldn&#39;t be the right choice for someone else. I just think that I&#39;d choose a collection of tools that are less likely to get stuck glued together.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Eric</div>    <div class='content'>
    I think Pylons would be a great fit for you. It matches the way you want to work.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Varikin</div>    <div class='content'>
    Your URL routing sounds intreging.  I like have been looking at Werkzeug, Routing and Django. I prefer Django to all three, but still I feel like something is missing.<br /><br />Could you elaborate on your solution?      </div>
    </div>
    <div class='comment'>
      <div class='author'>Juan_Pablo</div>    <div class='content'>
    Take a look at Werkzeug. Despite the weird name is the most modular and yet easy to use wsgi wrapper and dispatcher I know.<br />Combine it with Jinja2 and SQLAlchemy... and voilà.      </div>
    </div>
    <div class='comment'>
      <div class='author'>WhatDoYouThink?</div>    <div class='content'>
    Good thing there are people like you who would share your improvements back to the community!      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    Thanks Juan, I&#39;ll take a look at Werkzeug.<br /><br />Eric: I&#39;ve been looking at Pylons, but I think I want to go with something more like Juan&#39;s suggestion.<br /><br />Varikin: I was able to implement my URL routing over Django by simple matching (.*) and then writing a view which splits by &#39;/&#39;. I then loop over each component and perform a depth-first search of a tree structure. Each node has an optional bind(request, template_context, path_component) function. After I find the node which matches the path, I call the ancestor chain&#39;s bind functions and then finally the current node&#39;s view function. We&#39;ve got some decorators and helper functions to make building this tree easy. I&#39;d share it, but it is too tightly tied to our application. Maybe I&#39;ll write more about this later.<br /><br />WhatDoYouThink: My improvements are application specific. I&#39;m sharing my experiences to help the community. That is far more than most people do.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Jeremiah</div>    <div class='content'>
    This comment has been removed by the author.      </div>
    </div>
    <div class='comment'>
      <div class='author'>gaspode</div>    <div class='content'>
    The tree stuff and permission scheme you&#39;d like perfectly matches up how repoze.bfg works with traversal and security based on traversal schemes (also known as object dispatch).<br /><br />I&#39;d highly recommend giving it a shot as it seems to mesh quite well with the way you&#39;ve laid out the app.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Tucanae Services</div>    <div class='content'>
    Think Web2Py.....      </div>
    </div>
    <div class='comment'>
      <div class='author'>Chris Shenton</div>    <div class='content'>
    Ditto what gaspode said. I&#39;ve been fighting Django for an app whose content is basically hierarchical and BFG&#39;s traversal would really be a relief.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Cesar</div>    <div class='content'>
    I&#39;d like to add my 2 cents.  Restish has no threadlocals (compatiable with eventlet/spawning) and focuses on resources/content negotiation.  I believe the URL routing is similar to what you want      </div>
    </div>
    <div class='comment'>
      <div class='author'>handi</div>    <div class='content'>
    <i>Enabling custom credentials requires implementing a trivial authorization &quot;backend&quot; object. Unfortunately, it is non-trivial to replace usernames with email addresses. The admin UI&#39;s login form refuses to accept email addresses without hacking the template. Even if you hacked the template, the User model would still enforce a non-null constraint on the username field and the generated database schema enforces a uniqueness constraint as well. It turns out to be easier to fill the username field with a dummy value and &quot;support&quot; both forms of authentication with your backend, but you won&#39;t come to that conclusion until your head has already bore a hole in your desk.</i><br /><br />We wanted to let users log into our app using their email address through contrib.auth.view.login. We don&#39;t care about having email addresses log into the Django Admin.<br /><br />You have to dig around a bit, but it can be done with a few lines:<br /><a href="http://gist.github.com/170691" rel="nofollow">http://gist.github.com</a>/170691      </div>
    </div>
    <div class='comment'>
      <div class='author'>nassrat</div>    <div class='content'>
    I think you were looking for Pylons not Django. Each has its own advantages. I learnt that Django should be taken as is, once you start stripping it apart, IMHO you should consider using Pylons.      </div>
    </div>
    <div class='comment'>
      <div class='author'>reedobrien</div>    <div class='content'>
    Sounds like you should look at http://docs.repoze.org/bfg/current/ http://bfg.repoze.org is a nice microframework which stays out of the way as much as you want it to.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Jonathan Ellis</div>    <div class='content'>
    If you like SQLAlchemy, you will probably like FormAlchemy for form generation from SQLA models.      </div>
    </div>
    <div class='comment'>
      <div class='author'>adeel</div>    <div class='content'>
    I&#39;ve written my last few web apps on top of <a href="http://pypi.python.org/pypi/berry" rel="nofollow">Berry</a>, which just lets you map URLs to methods (and lay them out however you want).  And obviously you can import SQLAlchemy, Jinja, and whatever else you want yourself.  I&#39;ve found this gives me the most flexibility possible without actually writing a raw WSGI app.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Michael Galpin</div>    <div class='content'>
    You really need to read this http://terrychay.com/blog/article/challenges-and-choices.shtml. It will help you understand why you were doomed from the beginning :-)      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    Thanks Michael. Despite the distracting *laughter* that was an interesting read.      </div>
    </div>
    <div class='comment'>
      <div class='author'>gunzip</div>    <div class='content'>
    &quot;<i>After using the Admin for a little, I found myself missing Microsoft Access.</i>&quot;<br /><br />it still happens to me every time i start a new data-entry project on the web. sometimes i think something went wrong in the latest 20 years...      </div>
    </div>
    <div class='comment'>
      <div class='author'>Dead Man</div>    <div class='content'>
    I&#39;m also building a project on Django (a medium sized, multi language web encyclopedia about a Northern-European state Estonia) and I have to admit that I&#39;ve run into mostly the same problems. What surprised me the most is that I&#39;m implementing a nearly identical to yours solution for URL mapping as our content is also in a (generic) tree structure. The difference being that I&#39;m going to use class based views that allow nesting/composition of other views for the purposes of chaining that you described.<br /><br />I&#39;m also thinking of open sourcing the code as the project matures more as it&#39;s already written in a clean way and decoupled from project specific code.      </div>
    </div>
    <div class='comment'>
      <div class='author'>ars</div>    <div class='content'>
    <i>Beyond Django&#39;s specific weaknesses, I&#39;ve come to believe that the schema-generative ORMs paradigm is fundamentally flawed. That is a topic that deserves an entire (Django-agnostic) post of it&#39;s own. We are now using SqlAlchemy via schema reflection; no declarative layer.</i><br /><br />Well you caught my attention with that. Are you planning to write such a post in the near future, say in the next two weeks?      </div>
    </div>
    <div class='comment'>
      <div class='author'>Andrew Shultz</div>    <div class='content'>
    One of the big things I appreciated about django is the unit test supports.  What&#39;s your plan to replace that stuff?      </div>
    </div>
    <div class='comment'>
      <div class='author'>proteusguy</div>    <div class='content'>
    Almost all of your technical feasibility issues are easily addressed by Django. URLs, authN/authZ, etc are all as flexible or more than any other python environment out there because it is so simple to leverage python to implement this things in an incredibly simple manner. This <a href="http://uswaretech.com/blog/2009/08/a-response-to-dropping-django/" rel="nofollow">blog entry</a> demonstrates it better than I could. It&#39;s really just a matter of perspective and context. Django may not be the context that best fits your perspective but it most certainly has the capability and power to address all the technical concerns you brought up. Good luck finding the one that fits you best. I actually thought I&#39;d be a TurboGears guy but ended up Django. Both are great and others are good as well.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Alexei</div>    <div class='content'>
    I am working on building a medium size application in Django too.  It is indeed a good way to start.  But I am hitting issues too.  I have multiple &quot;agencies&quot; so need to have users with multiple roles in agencies.  These are within single site.  That also means multiple &quot;admins&quot; with one &quot;super admin.&quot;  So Django Admin interface is out of the question and role/permission infrastructure is built from scratch.<br />Another thing is importing data.  I started writing scripts within Django framework (using settings.py, etc.) but need some tables/entities created during import and then dropped.  Oops.  Have to look at how South does it.<br /><br />I realize that I am moving out of Django domain just because of the nature and complexity of the application I am building.  Django cannot and should not be everything for everybody. <br /><br />If you are looking for a &quot;glue&quot; framework, I recommend checking out Cherrypy: I like its configuration and request/response handling better than Paster + WebOb.  It also has several types of routing, pick what you like or mix it (I would not recommend the latter though), one of them is Routes.  <br /><br />My next move will most likely be to Cherrypy + SQLAlchemy + FormAlchemy + Jinja2.<br /><br />By the way, repoze.bfg is not microframework either, it pulls quite a large chunk of Zope code, primarily for managing configuration but for templating too.      </div>
    </div>
    <div class='comment'>
      <div class='author'>RJ Ryan</div>    <div class='content'>
    Got a few issues with the things you said -- I&#39;ll list them by section.<br /><br />URL routing is not a flat list. You can make them hierarchical. How do you think all of the admin site routes get added? It&#39;s in the template urls.py for any starter project, so I really don&#39;t see how you missed this.<br /><br />Authorization -- I don&#39;t think I understand what you are saying is wrong -- &quot;too numerous?&quot; But designing a permission system using the Django auth system has usually been pleasant for me. To require certain permissions just use a decorator on your views which checks for it.<br /><br />Authentication. There&#39;s nothing that says you have to make a one-to-one with User and whatever profile system you have. That&#39;s just how most people do it because most projects want 1-1. Make a ManyToMany from your Profile to User if you want. There&#39;s nothing stopping you. Also, using email address as your login was simple -- swap the auth backend. It&#39;s like 10 lines of code.<br /><br />Templating: Fair enough, I&#39;ve wanted the templates to allow a little bit more in the way of doing logic only for purposes of output, but you realize if they make allowances for that, then it opens the templates up to abuse -- and then you get PHP. I&#39;ve actually come to appreciate the template strictness now, and when I come across something I just have to do in the template, I write a templatetag. <br /><br />ORM/Admin -- I can&#39;t really understand what you&#39;re trying to say here. The Admin UI is a very nice piece of software, and it is extensible at every point. Though, the documentation on doing so is skimpy. Take a look at Satchmo -- they do amazing things to the Admin UI that really showcase its flexibility. As for the ORM -- if it really doesn&#39;t suit your needs, upgrade to django-sqlalchemy or something. <br /><br />Forms -- it&#39;s true forms are sort of a go-between between views and templates. It does seem odd to specify the widget in the form declaration. Shrug, though the form is more of a prototype of a form, so you design the prototypical form that is instantiated in a view. This makes sense to me.<br /><br />I&#39;m sure you actually did run into problems with Django that caused it to be frustrating for you, and to that I say ymmv -- I just couldn&#39;t help reading your post and being like &quot;but that&#39;s not true, because I&#39;ve done X and he said it can&#39;t do X&quot;.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Shantanu Kumar</div>    <div class='content'>
    Did you consider Jython? You could then probably use any framework from the Java-space.      </div>
    </div>
    <div class='comment'>
      <div class='author'>EH</div>    <div class='content'>
    Faced same problems with Django during 2 years of its using. Looked also at Paste, web.py, Werkzeug, Pylons, WebOb to replace over-weight framework with more flexible tools. My current best python tools is Werkzeug, Mako, SQLAlchemy.      </div>
    </div>
    <div class='comment'>
      <div class='author'>reedobrien</div>    <div class='content'>
    Alexei said&gt; By the way, repoze.bfg is not microframework either, it pulls quite a large chunk of Zope code, primarily for managing configuration but for templating too.<br /><br />I guess we will have to agree to disagree. By alexei&#39;s definition 20 packages isn&#39;t &#39;micro&#39;. By mine 4 packages I wire together isn&#39;t a framework...but to clarify some misnomers:<br /><br />repoze.bfg is a microframework. it consists of 20 packages, 8 of which are zope packages. Although, BFG uses them you never have to.<br /><br />The default templating package implements TAL, but it is NOT a zope package. You are not bound to it either. You can plug in Jinha2, Mako, Genshi or whatever you want.<br /><br />There is no default database, form library or anything else.<br /><br />FWIW zope is about 170 packages. 8 is not a &#39;large chunk&#39; of that by any definition.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Sébastien</div>    <div class='content'>
    If you&#39;re looking for a HAML Python equivalent, there is PAML (Pamela) at http://github.com/sebastien/pamela -- also, you can check CleverCSS as a SASS replacement.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Gabriel</div>    <div class='content'>
    I totally agree with RJ Ryan.  No framework will suit every project, but Django covers a lot of the basics in a way that&#39;s good enough and doesn&#39;t get in the way when I need to extend it.  Still, thanks for telling us why it wasn&#39;t the tool for you.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Tony Landis</div>    <div class='content'>
    I use pylons + mako + sqlalchemy and love it      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    @Dead Man: I’ll probably publish something about our approach to URL routing soon. It seems that several people have shown interest in it.<br /><br />@ars: Yes, I will probably be writing about declarative ORM layers in the next week or so.<br /><br />@Andrew Shultz: We’re not doing a whole lot of automated testing yet. Small helper functions typically have doctests, we’ve got a couple unit tests, and we’re currently playing with Selenium for full tests. I’m much more confident in tests which drive the UI than tests which only poke at the views.<br /><br />@proteusguy: I just read that blog entry and will respond to it separately. Again, I’m not bashing Django so much as I am saying we’ve been slowly choosing tools that better fit each need as we identify those needs. Technical feasibility aside, we’ve been looking for a path of least resistance to accomplishing our goals and this post was just to describe them.<br /><br />@Alexei: That’s the impression I got too; Django would have had greater longevity on my tool shelf if I needed a highly customizable CMS. I don’t need more flexibility, I need less constraints.<br /><br />@RJ Ryan: Like I said about, I’ll try to follow up with more info about our URL routing system. What needs we had and why I felt I had to subvert Django to get them.<br /><br />@Shantanu Kumar: There are already too many choices of Python frameworks! :-)<br /><br />@Sebastien: Thanks for the PAML link! I’ll take a look at it. As for CleverCSS, I don’t see a need to switch from SASS. We only need Ruby and SASS installed on dev machines, the server only ever works with the compiled CSS.      </div>
    </div>
    <div class='comment'>
      <div class='author'>dkubb</div>    <div class='content'>
    <i>&quot;Beyond Django&#39;s specific weaknesses, I&#39;ve come to believe that the schema-generative ORMs paradigm is fundamentally flawed.&quot;</i><br /><br />I too would like to see some more detail on this.  I maintain a Ruby ORM called DataMapper, and while the API is completely different, it is still declarative.  I prefer the declarative style to ActiveRecord, since I can specify rich types and constraints than I could reflect from a database.  The way I see it, at some point the schema needs to be defined, and I&#39;d rather do it in a richer DSL and have the schema, validation and other constraints generated from that.<br /><br />Even so, I&#39;d love to hear a different view point.      </div>
    </div>
    <div class='comment'>
      <div class='author'>jgrant</div>    <div class='content'>
    http://jng.imagine27.com/articles/2007-07-12-163820_red-vs-blue.html<br /><br />A somewhat dated (and biased) comparison of Python/Django with Ruby/Rails.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Max</div>    <div class='content'>
    This is a subset of the reason for why we made <a href="www.web2py.com" rel="nofollow">web2py</a>. Thanks for explaining them so clearly.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Ian Lewis</div>    <div class='content'>
    pylons is great being a framework that allows mixing and matching libraries. <br /><br />My ideal would be <br /><br />Werkzeug<br />Jinja2<br />SqlAlchemy<br />Babel<br />wtforms (FormEncode and htmlfill&#39;s pattern of inserting html is gross)<br /><br />I would probably build on Django&#39;s idea of applications and middleware as that&#39;s a good paradigm.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    For anyone interested and tracking this thread, I&#39;ve posted a follow-up:<br /><br />http://blog.brandonbloom.name/2009/08/url-routing-and-views.html      </div>
    </div>
    <div class='comment'>
      <div class='author'>Angel</div>    <div class='content'>
    You <i>can</i> have non-flat URL routing. After your first point demonstrated a failure to grasp more than the basics before whining, I tuned out. <br /><br /><a href="http://docs.djangoproject.com/en/dev/topics/http/urls/#including-other-urlconfs" rel="nofollow">Nested URL routing confs and includes</a> (new in 1.1) are what you should have used instead of bitching on the internet. It wouldn&#39;t have made you seem as cool, but you would have gotten the job done instead.      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    Without the changes in Django 1.1, modules were the only recursive mechanism; too heavyweight for my taste. The 1.1 beta was released in March. I had developed my own solution to this problem several months before then.<br /><br />Even with the 1.1 improvements, Django doesn&#39;t meet my other requirements: namely binding URL segments to the database and creating breadcrumbs while filling a template context dictionary. Since that work involves parsing request.path myself, I might as well just bind (.*)+ and do my own routing. It was relatively trivial.      </div>
    </div>
</div>
