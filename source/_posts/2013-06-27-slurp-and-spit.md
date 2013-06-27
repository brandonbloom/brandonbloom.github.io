---
layout: post
title: "Slurp & Spit"
date: 2013-06-27 01:04
comments: true
categories: 
---

## Does nobody teach fopen anymore?

Many programmers start with "Hello World". Shortly after that, it's "Hello
$NAME", with a string read off stdin. But soon, budding engineers get tired of
reminding their computer of their name each and every time their code runs.
When I was first learning to program, file IO was an early requirement. We were
taught how to read and write "data files" using little more than fopen,
fscanf, fprintf, and fclose.

Fast forward five to ten years; you find yourself writing your 10 millionth SQL
query, wishing for simpler times. Spend some more time with the NoSQL database
du jour and the humble fopen function will be but a distant memory.  Until that
one fateful day arrives where you've got a relatively simple program and
encounter the need for equally simple durability. Five years ago, you'd have
cracked your knuckles and hacked out a pair of "save" and "load" functions.
Today, you add a dependency on your favorite database driver, switch to the
shell, type `createdb myapp` and then dutifully begin defining a lovely schema.
Of course, now you need to either rework your models to conform to some horrid
ORM layer, or write save/load-style "hydrate" and "dehydrate" methods anyway.

Now, at ten years and a day, you decide it's finally time to learn that hip new
programming language that everybody is talking about. You've got your book out,
you've rocked through all your favorite technical interview puzzles, and you're
ready to put together a little web service for personal or small group use.  If
this was yesterday, you'd know exactly what dependency to add and how to
proceed, but that was yesterday. Today, you do some Googling or hop into IRC
looking to find out what's popular.

Why don't you even consider fopen?


## But files aren't "Web Scale"!

Is that really true? And do you really care? Should you really care?

The answer to all of these questions is "No". Files can easily be "web scale".
As of 2013, Hacker News is still running as a single process, on a single core,
of a single server, backed by a directory structure of simple data files.
Nearly 2 million page views are served daily. See [this thread][1] and
[this submission][2] for details.

The bottom line on this subject is that 1) You're going to need significant
cleverness to scale any service regardless of whether you use a database or the
file system directly. And 2) You probably won't need to scale hugely anytime
soon, anyway. Being crushed by traffic is a Good Problem To Have, so let's
worry about the Ghost Town problem first. 3) You can always refactor later.


## Data Literals: print, read, spit, and slurp

This article was prompted by a reoccurring discussion started by newcomers to
the Clojure IRC channel. Having learned the basics, folks want to hit the
ground running with stateful and durable services. Coming from a background in
some big framework or some hairy application, these folks ask those suggestive
questions about databases, driver libraries, and the like. Often, upon
interrogation, they only have a couple kilobytes or megabytes of total data to
be stored. In such a situation, the advice is always the same: Put that data
into an atom, then use `spit` and `slurp` to save and load.

```clojure
(def db (atom {...}))

(defn save-data []
  (spit "somefile" (prn-str @db)))

(defn load-data []
  (reset! db (read-string (slurp "somefile"))))
```

Because Clojure encourages the use of printable, readable, "pure" data, your
save and load functions are virtually free! If you're not familiar with
Clojure, then consider working with pure JSON in Node.js:

```javascript
var db = {...};

function saveData() {
  fs.writeFileSync("somefile", JSON.serialize(db));
}

function loadData() {
  db = JSON.parse(fs.readFileSync("somefile"));
}
```

Things aren't quite as easy in languages lacking data literals, but nearly
every popular language has some kind of automatic serialization library.
However, even if you do need to write your own save and load functions, it's
a pretty straightforward, if somewhat tedious, process.

Even--or especially--experienced programmers are surprised by just how far
this brain-dead-simple durability scheme will go.


## Atomicity

One other objection to files is that it's difficult to ensure an application
enjoys the same guarantees that a well-tested ACID database affords. Never mind
the fact that the majority of Rails applications suffer from dozens of
consistency bugs because most developers forget to wrap related updates in
transactions; it is true that incorrectly implemented file IO can cause
catastrophic data loss.

If you plan to deploy spit and slurp into production, you'd be advised to write
to a temporary file and utilize an atomic rename. This ensures that a failure
during writing won't corrupt your database. See `man rename` for more.

```javascript
function saveData() {
  fs.writeFileSync("somefile.tmp", JSON.serialize(db));
  fs.renameSync("somefile.tmp", "somefile");
}
```

Just this one little tweak and this bad boy is production ready. Clojure
programmers can use Java's `File.renameTo` method.

Remember to configure your backups!


## Don't Stop The World

Experienced Node.js programmers likely cringed at the "Sync" suffix on the
above file operations. These operations will block while data is being read or
written. In addition to yielding clearer example code, synchronous operations
do not require coordination. If your application only needs to serve a handful
of users, you can still write 100 megabytes of data in less than half a
second. Even if you write every change to a file on every request, your ten
users might not even notice your blocking IO. As you scale up, you'll need to
either split your data files up and/or start writing files asynchronously.

Asynchronous means coordination. Coordination means locking or queues. This
stuff really isn't as scary as it sounds, but it will have to wait; this post
has already gotten far too long. However, I don't want to lead you down the
wrong path, so I should mention that, unlikely Node.js, synchronous writes on
the JVM will not block other requests. You practically don't have a choice, but
to be asynchronous.  If two requests both call your save function at the same
time, the resulting race condition can lead to incremental data loss. Luckily,
Clojure's agents and persistent data structures provide for a super quick fix:

```clojure
(def save-agent (agent nil))

(defn save-data []
  (send-off save-agent (fn [_] (spit "somefile" (prn-str @db)))))
```

Different web servers across all the different languages have varying
concurrency support and configuration defaults. Whether or not you use file IO,
you should be aware of the concurrency story for your platform and its impact
on your application.


[1]: https://news.ycombinator.com/item?id=5229522
[2]: https://news.ycombinator.com/item?id=5253773
