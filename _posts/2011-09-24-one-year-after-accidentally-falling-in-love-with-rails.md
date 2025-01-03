---
layout: post
title: "One year after accidentally falling in love with Rails"
date: 2011-09-24
permalink: /blog/:year/:month/:day/:title/
---

About a year ago, I wrote a post titled&nbsp;<a href="http://blog.brandonbloom.name/2010/09/how-two-pythonistas-accidentally-fell.html">How two Pythonistas accidentally fell in love with Rails</a>. Some people predicted that the honeymoon would be over quickly and that I would soon hate it.

<b>So, do I still love Rails?</b>

Yes. Absolutely.

The prototype and first beta version of <a href="http://www.thinkfuse.com/">my startup</a> was built entirely on Rails. The framework enabled us to build our site significantly faster than we would have been able to without it. For that, I still love Rails. It was critical in helping us get to where we are today.

It's the perfect <i>hammer</i> for the v1, nebulous direction, rapid development,&nbsp;<i>nail</i>.

<b>That said, we're not using Rails for any new components.</b>

After getting a lot of feedback from our first beta, we realized we had the wrong approach to the UI.&nbsp;Now, in our second attempt, we've got a much richer client-side front-end, written in <a href="http://jashkenas.github.com/coffee-script/">CoffeeScript</a> and bootstrapped by <a href="http://nodejs.org/">Node.js</a>. Ruby/Rails is simply not the right tool for that job. In the new world of the browser app, even the folks of 37signals have been accused of&nbsp;<a href="https://github.com/rails/rails/commit/9f09aeb8273177fc2d09ebdafcc76ee8eb56fe33">propping&nbsp;up their successor</a>.

Luckily, we had a pretty good handle on the models in the problem domain. So I ran `git rm -r app/views` and effectively turned the Rails app into a Json API backend. The models have evolved, but not by much. In fact, there were a few months where the Ruby code remained practically unchanged while we developed the frontend.

As time goes on, we've been breaking our API layer apart into several services. It makes development, deployment, and versioning easier. Service Oriented Architecture is working great for us... now that we know what services we need. These services are each too simple to justify the overhead of Rails. Some are Node.js, some are Sinatra, some are cron jobs and shell scripts. We're polyglots, so we're careful to choose the right tools for each job.

We'd have gotten it very wrong if we tried to build services from the start. The monolithic Rails app was the correct evolutionary intermediate form.

<b>Given a time machine, would I have done anything differently?</b>

Only one thing:&nbsp;<a href="http://seldo.com/weblog/2011/08/11/orm_is_an_antipattern">I wouldn't have used ActiveRecord</a>.

I guess&nbsp;<a href="http://blog.brandonbloom.name/2009/10/orms-and-declarative-schemas.html">I should have seen that one coming</a>. ORMs are an insidious, broken abstraction. At first, they save you time and make you feel productive. They work out splendidly for a while, but like a virus, they infect every part of your application. One day, you hit that abstraction brick wall and it's too late to do anything about it without significant effort.
<div>
</div><b>Are there things I hate about Rails?</b>

Ignoring ActiveRecord, only one thing really stands out: Performance.

I'm not even talking about page load times. I'm talking about development time.

Even with our modestly sized application, the impact on productivity is&nbsp;atrocious. Our test suite runs far too slowly to be waited on for every checkin. Rake's startup time is too great to for its usage to be considered "interactive". Simple shell scripts have replaced practically all of our custom tasks; they are instantaneous.

<b>Isn't this basically the same <a href="http://blog.brandonbloom.name/2009/08/dropping-django.html">story I told about Django</a>?</b>

Yeah, I guess it is.

The story basically goes:
<ol><li>Use a framework</li><li>Be productive</li><li>Encounter shortcomings</li><li>Stretch it to work</li><li>Watch it break</li><li>Replace components</li><li>Loop steps three to six</li><li>Be productive</li><li>"Where's the framework?"</li></ol><div>I still stand by my original&nbsp;criticisms&nbsp;of Django (although, I'm sure much as changed in the last year). I'm also re-affirmed in my belief that big frameworks are generally bad. It just so happens that Rails is good enough and matches my mindset well enough to justify the framework pains.</div>  </div>

## Imported Comments

#### rweald
If you are annoyed by your slow test times you should watch this presentation by Corey Haines at gogaruco
http://confreaks.net/videos/641-gogaruco2011-fast-rails-tests      </div>

#### Scott

I'm going through using Django (1.2) coming from Rails to maintain and extend a legacy web app.  Generally I find Django more limited and painful than the Rails equivalent.  Maybe it's the way we create apps or just personal preference.

I think it really depends what kind of web application you're building as to what works out well.

Most of the web apps we deploy have pretty limited usage and are closer to the prototype side of things. They are niche, custom, broadly educational-market things that have a lot of customized UI flourishes, but underneath everything are more or less standard data driven forms-type apps.

The indentation of Python that some people fuss about doesn't bother me at all since I indent correctly anyway. Python's methods often feel misplaced with the wrong objects to me, though.

The Ruby 'end' statements take a bit more space but Ruby optimizes in other ways such as with blocks and things that make the code shorter in other ways.  It's more or less a wash as far as I can tell for code brevity.

A lot of people I know and respect love Python and Django.  I can't tell them they're wrong, but I'm personally much more productive in Ruby even after getting used to Python for a number of months.

Python performance is better than Ruby's, but Rails 3.1/Ruby 1.9.2 feels much faster than previous older versions of Ruby and Rails to me.

I don't think there is a clear winner for all situations in the Ruby vs. Python and Rails vs. Django religious wars, but I know I prefer and work much better with Rails than with Django even after some significant ramp-up time.      </div>
