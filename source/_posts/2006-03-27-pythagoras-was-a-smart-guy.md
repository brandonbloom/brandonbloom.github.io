---
layout: post
title: "Pythagoras was a smart guy"
date: 2006-03-27
comments: false
---

<div class='blogger'>
  <div class='post'>
    In <a href="http://www.st.cs.uni-sb.de/edu/seminare/2005/advanced-fp/docs/sweeny.pdf">a recent presentation by Epic's Tim Sweeny</a>, it was stated:<br /><br /><blockquote>Factoid: C# exposes more than 10 integer-like data types, none of which are those defined by (Pythagoras, 500BC).<br /><br /></blockquote>This got me thinking; why do I, a programmer, have to tell the computer exactly how much memory to allocate for a number? Why can't I define a variable as a Whole Number or as a Real Number?<br /><br />For one thing, the computer can not predict how large a number may grow or what level of precision is required. In theory, any integer can have an infinite size, but in practice 32- or 64-bit integers have proven acceptable in most instances. This is because generally integers used in software nearly always represents a very real, very finite count.<br /><br />Currently, it is left as a task to the programmer to determine how big a number can grow, but why not leave it to the programmer to define semantics of a number and allow a computer to choose a representation size? Should a programmer choose to omit any such constraints, use an "infinite" precision variable by default and leave semantic definitions as an optimization exercise.  </div>
  <h2>Comments</h2>
  <div class='comments'>
    <div class='comment'>
      <div class='author'>hostilefork</div>    <div class='content'>
    Well...Pythagoras may have been smart, but rumor has it that the Pythagoreans killed the guy who proved the existence of irrational numbers!<BR/><BR/>http://en.wikipedia.org/wiki/Hippasus<BR/><BR/>In any case, I agree with your idea that bignums should be the default in almost any programming system:<BR/><BR/>http://en.wikipedia.org/wiki/Arbitrary-precision_arithmetic<BR/><BR/>This was driven home recently when a friend of mine was confused by the results of a floating point calculation.  I had to explain IEEE 754, and was thinking that it really does seem like something the average scripter or excel user should not have to learn.<BR/><BR/>Would be even more fun if we could store all calculations symbolically, and have some kind of mathematical engine behind the scenes.  The symbol engine would reduce expressions whenever it could (e.g. sin(pi) =&gt; 0), and would create string caches of the approximate values to whatever precision you wanted for display.<BR/><BR/>That might take a while.  :)      </div>
    </div>
    <div class='comment'>
      <div class='author'>Brandon Bloom</div>    <div class='content'>
    Threw him overboard? That's an irrational response! (har har har, oh puns)<BR/><BR/>I'd love to see symbolic computations as the default for a mainstream programming language. Software is about abstraction, so it seems reasonable that the default behavior is as abstract and general as possible. If performance suffers, pervasive type inference should be capable of switching to IEEE standard doubles with a single type annotation.      </div>
    </div>
</div>
