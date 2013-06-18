---
layout: post
title: "Stupid brain trick: Tests and vertical splits"
date: 2011-08-16
comments: false
---

Recently discovered a stupid brain trick that I thought was worth sharing.

When writing code with unit tests, I find it helpful to view the tests and the
implementations side by side. I use vertical splits in Vim to do this.

The stupid brain trick is this: <b>Put your tests in the left-hand split!</b>

Why? I traditionally used the left-hand split for the "main" thing I'm working
on and the right-hand split for reference. I've watched many other developers
do the same thing. The right split is constantly sub-split horizontally and
always changing to various different files.

In trying to be better about writing tests _first_, I created a test code file
before the implementation file, so it just happened to be in my left-hand
split. After an hour or so of development, I realized that the "main" file
placement of the tests meant that I focused more on them. I viewed the actual
implementation file as just some ancillary file I had to tweak to get my tests
to pass. It changed the focus of what I was doing from "Write a method that
does X" to "Describe X". Describing something involves breaking it up in to
smaller and smaller pieces until the implementations are so trivial that they
basically write themselves.
