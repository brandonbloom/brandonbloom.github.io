---
layout: post
title: "Simplicity belongs in APIs"
date: 2008-10-08
permalink: /blog/:year/:month/:day/:title/
---

In [a recent post][1], I insisted that flexibility belongs in APIs.

Simplicity also belongs in APIs. There are two key things to simplify in APIs:

* Common tasks
* Surface area

Since you are jam packing your APIs with loads of powerful and flexible
functionality, you might have a lot of functions and data structures. You may
have hoards of configuration options or capability bits, and a whole mess of
operations to perform on them. However, your UI is simple and has picked
sensible defaults for every operation. Your APIs should do the same. In fact,
you should expose the same simplified operations your UI uses, so that
developers do not have to guess what arguments you pass or in what sequence you
composed more complex operations. If you have widely used private helper
methods you've developed for your UI, you have identified common tasks for
which your helper methods should be promoted to address.

Developers should be unwittingly led to these simplified methods, they
shouldn't have to go searching for them. For example, the .NET framework
provides a significant number of overloads for opening a file. The most complex
of these takes parameters for file access, sharing, creation behavior, and
more. The simplest of these, just opens the damn file. Reading all of the lines
from a file is a very common task that involves opening a file, iterating over
each line, accumulating the results, and then closing the file. Or you could
just call File.ReadAllLines and simply get an array of strings.

ReadAllLines is completely implemented using StreamReader, but was placed in
the File class instead of the StreamReader class because more people would look
there first. Organize APIs by how naive developers would look for them, not
experts. Remember that developers, like users, are perpetual intermediates.
They are only going to write the code once (as beginners) and iterate on it
several times until it works (as intermediates). After that, they will move on
to something else. There won't be time for them to become experts.

You have limited screen space for features, but you can always fit more APIs.
You develop a great UI by removing the features you don't need. However, you
develop a great API by avoiding adding the features that you don't need. If
there is a set of parameters no one will ever change, remove the ability to
change them. If there are multiple approaches to the same common task, choose
one. The less surface area there is for a developer to search and understand,
the more likely they are to quickly choose and implement an effective solution.

In a future post, I will discuss my favorite technique for designing APIs.

[1]: /blog/2008/08/28/flexibility-belongs-in-apis/
