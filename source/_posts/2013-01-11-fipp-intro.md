---
date: 2013-01-11
title: 'Fipp: Fast Idiomatic Pretty-Printer for Clojure'
---

I decided to create a new pretty printer in the spirit of "Data All The
Things!" And it's fast too!

Fipp, the Fast Idiomatic Pretty-Printer for Clojure, is a pretty printer with
linear runtime and bounded space requirements. Unlike clojure.pprint's
side-effectual API, Fipp is configured by pretty print documents that are
similar to Hiccup HTML templates. It's based on some fancy pants research
pretty printers from the Haskell world.

You can see my implementation and lots more notes at
https://github.com/brandonbloom/fipp
