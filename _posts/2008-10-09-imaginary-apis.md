---
layout: post
title: "Imaginary APIs"
date: 2008-10-09
permalink: /blog/:year/:month/:day/:title/
---

As promised, I will explain my favorite way to design APIs. I call it
"imaginary APIs". I'm far from the first person to have thought of this and
I've even seen this used in practice by others, but I have no idea if there is
an official name for it.

In short, the idea is that you write code which consumes an imaginary API and
then you imagine it works. Then, you write some more code which does something
more advanced with your imaginary API and imagine that works too. Then, you
figure out how to get your magic code to compile, run, and work.

Success is measured by how closely your final API matches your imaginary API
and by how much you need to modify your magic code to be a working sample you
can ship along side your API. That's it.

As an example, let's imagine an API for a simple home movie editing program.

```csharp
var video = new Movie();
video.StoryBoard.Add(Image.FromFile("title.png"));
video.StoryBoard.Add(new CrossFade(TimeSpan.FromSeconds(2.0f)));
video.StoryBoard.Add(Movie.FromFile("scene1.wmv"));
video.StoryBoard.Add(Movie.FromFile("scene2.mpg"));
view.Export("myMovie.avi");
```

A lot of voodoo is going to have to happen behind the scenes to make this code
work! But you will thank yourself when you need to implement the UI and your
client developers will thank you when they want to generate a highly customized
slide show or develop a nifty flip book drawing application.

## Imported Comments

### Doug Jones

I think you might be able to consider "imaginary APIs" as a type of test driven
development.

No matter what you call it, I think it's a good way to start developing a clean
API.

(On a random note, the captcha for this comment is 'ggsux'. Is Google self
bashing now?)

### Brandon Bloom

It definitely is a form of test driven development; good call.

However, I think the key difference is that real tests need to compile and run.
Imaginary APIs can exist in an email or document and never actually need to be
compiled. But yes, the next step from here is unit tests :-)
