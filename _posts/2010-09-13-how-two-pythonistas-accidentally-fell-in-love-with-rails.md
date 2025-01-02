---
layout: post
title: "How two Pythonistas accidentally fell in love with Rails"
date: 2010-09-13
permalink: /blog/:year/:month/:day/:title/
---

One week ago, <a href="http://stevekrenzel.com/">my co-founder</a>
and I found ourselves staring down a thick stack of paper UI
prototypes; almost 50 pages total. One week before that, we threw
out a perfectly good, working beta product because customer
feedback suggested a significantly better path. The tale of how we
ended up at that point is entertaining as well, but it will have to
wait for another time. This is the tale of how our paper prototype
turned a pair of long time <a href= "http://www.python.org/">
Python</a> developers (and <a href= "http://blog.brandonbloom.name/2009/08/dropping-django.html">
documented framework haters</a>) into <a href="http://www.ruby-lang.org/">
Ruby</a> on <a href="http://rubyonrails.org/"> Rails</a> developers.

Paper prototyping proved to be an extremely worthwhile exercise,
but we really wanted something we could click around. We devised a
strategy to build a clickable prototype that would evolve into
production code. The plan was to stub out all of the views with
static HTML and CSS. The CSS could be directly carried over to the
product and the HTML could be retrofitted as templates. Having
become religious about <a href="http://sass-lang.com/">Sass</a>
about a year ago, I fired up the file watcher (which automatically
rebuilds CSS) and set to work filling a directory with sass and
html files.

After some time, I asked myself "I wonder if <a href="http://haml-lang.com/">Haml</a>
has a `--watch` argument?" (it doesn't) because Sass
makes me hate Html's verbosity. Armed with
practically zero Ruby knowledge, I hobbled together a simple
<a href="http://rake.rubyforge.org/">Rakefile</a>
and Haml watcher script and got back to work. A few Git pushes
later, my co-founder, being the clever bastard that he is, simply
installed Rails and the Haml plugin to replace my hacky scripts.
"Neat", I said and thought nothing of it.

Then we designed our URLs in an indented plain text file, which
pretty much directly mapped into the <a href= "http://edgeguides.rubyonrails.org/routing.html">
routes.rb</a> file and some trivial (empty method) controllers. Whoops! Now
we're writing Ruby.

"At this point, we might as well try Rails proper."

Generated some models and corresponding migrations:
"Wow, explicit schema with reflective model
objects. None of this
<a href= "http://blog.brandonbloom.name/2009/10/orms-and-declarative-schemas.html">declarative schema bullshit</a>."

Wired up some controllers. "The <a href= "http://rails.nuvvo.com/lesson/6371-action-controller-parameters">
structured params</a> infrastructure is pretty cool."

"Ruby is kinda fun.
<a href= "http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/">Blocks</a>
are super useful."

Integrated <a href= "http://github.com/binarylogic/authlogic">Authlogic</a>.
"Wow, that was easy. I really like how it does not force any front-end decisions on me."

"Rails is full of evil black magic that I do not understand, but it seems to
read my mind, so I don't really care."

Started to apply some polish and work on the 10% that takes 90% of the time. "I
think I can safely add Ruby to my resume now."

Late at night, at the very end of the week, exhausted, we stared at each other.
"Dude, I think we can show this off to people tomorrow." Walking out the door
of the office, my co-founder said to me "I feel so dumb for having ignored
Rails for so long. I just assumed it was Ruby's Django".

We accidentally implemented our entire beta product in one week, with zero
prior Ruby or Rails experience.

When I have some more time (and experience), I'll try to write about specific
design decisions that set Rails apart.

## Imported Comments

### Veezus Kreist

Great story! I love the black magic quote - but I think it will feel more
apropos when you use rspec as a test framework.

### Joe Lallouz

Sweet. A very similar situation happened when my co-founder and i switched to
Groovy and Grails.

### Dan

How did I never notice you on Hacker News before? I read it every day!

### Andrew Ingram

Weird, I moved to Django because I was fed up with building sites using Rails :p

I guess everyone has their own preferred style.

### Eric Floehr

The best language for you is the one in which you are most productive in, so
awesome!

My first public website was in Rails, and I assumed Django was just Python's
Rails.

I have since converted it to Django because Django allows me to be more
productive, and ActiveRecord didn't work well for me.

Best of luck, and congrats again for finding such productivity!

### Rives

lol, do you always call your co-founder a bastard?

### Max

Did you give web2py a try? I believe it provides in Python what you found in
Rails.

### JonnieCache

"lol, do you always call your co-founder a bastard?"
I'm guessing he's in the UK :)

### Brandon Bloom

@Dan: I haven't been hiding :-)

@Andrew: I think that's why these topics are such hot issues. It is more
preference and religion than anything else. Django is a solid piece of code and
a stellar community, but it just doesn't mesh with my brain.

@Rives & JonnieCache: I called him a *clever* bastard. I consider that a
complement. And no, I'm not from the UK. I'm from NY, he's from Philly, and we
are currently located in Seattle.

@Max: I've played with web2py, but wasn't super impressed. But hey, I thought
the same thing about Rails. I guess you need to really try to make something to
understand. What about it is interesting in particular?

### Nicko

Just out of interest, which version of rails are you using?

### Brandon Bloom

Rails 3

### Psylinse

And here I am going from Rails (because of black magic :) to Pylons, which took
a lot from Rails, but its in Python!

Perhaps you should give Pylons a look over, just some food for thought.

### jbaker

I originally began programming in Python with Django myself, but I saw Rails
and immediately went.. WTF!? to Django and Python in general.

### fitzgeraldsteele

I think the real lesson here is to find the right tool for the right job.  Both
Rails and Django are pretty strong pieces of framework, allowing you to do a
lot in a little amount of time.

I had a similar experience, where I quickly prototyped a website in Rails.  I
showed it off and said, "see...this is pretty much done."  And then developers
on my team felt the need to rebuild the site in PHP since that's what our
company 'officially supports.'  :)

On another project, I was able to quickly use the Django ORM to introspect an
existing MySQL database, and build an admin interface for some of our internal
users.  That was a HUGE win for me to be able to quickly make the admin site.

So, the right tool for the right job...

### Max

@Brandon

Given your article I thought your would like the fact that in web2py everything
has a default. For example is you just type:

```python
db.define_table('post',Field('body'))
```

web2py creates the table (if it does not exist, or alters it as necessary), a
web based database administrative interface, and crud.create, crud.update,
crud.read, crud.select, crud.search forms that you can insert in your app like 

```python
{{=crud.create(db.post)}}
```

It also includes role based access control out of the box with pluggable
authentication mechanisms.

Web2py also has a component/plugin architecture that is best described by this
video http://vimeo.com/13485916

Since you are coming from Python, I though you may be interested. Anyway, I too
really like Rails and web2py was originally designed to be in Python but closer
to Rails than other Python frameworks.

### brokenladder

Semantic indenting is far and away the best thing about Python, and Haml/Sass
prove Ruby should adopt it too.

### Brandon Bloom

@brokenladder: Hell yes.

### coulix

Well in my case I am way more productive in Python, especially when prototyping
with Django. It all depends on the mastering level of the tool. One point I
will give to rails is haml tho. We need a proper equivalent well integrated in
Django/Python.

### Rafael Rosa Fu

Hi,

Next time you need to authentication, try Devise instead of Authlogic, and
check Ruby Toolbox for more interesting Ruby tools.

http://github.com/plataformatec/devise
http://ruby-toolbox.com/

Cheers

### Kevin

Could you expand on your file watcher script?  Running a background process
(e.g. re-compile) on change is a common pattern that I haven't found a tidy
solution to...

### Brandon Bloom

@kevin: I've written a bunch of watchers in the past. Each had their pros and cons.

Steve has a simple Python script here: http://stevekrenzel.com/articles/autoreload

However, in this case I used FSSM: http://github.com/ttilley/fssm

FSSM has the advantage that it uses the platform's native file notification
mechanism, so it tends to respond more quickly (no polling delay).

### T T

Awesome post! I've been comfortable with Django though I am dabbling into Rails
but I'm having trouble with getting to grips with Ruby. Would you recommend
anything you've read that allowed you build a working prototype? I'm curious
about how you managed to learn it so quickly. Thanks!

### Brandon Bloom

@TT: I took advantage of the fact that many people switch to or from Python. I
searched for phrases like "python's foo in ruby". Ruby is so similar to Python,
it was pretty easy to just pretend it's the same and go back for cleanup as you
learn the idiomatic approaches.

Also, I generally just have an interest in programming languages. I'm pretty
fluent with about a half dozen languages, so each marginal new language is that
much easier to pick up.

As an aside: the "end" keyword is just sooo unnecessary!
