---
layout: post
title: "File systems for people"
date: 2006-03-27
comments: true
categories:
---

Using Blogger, I am able to create and edit blog posts from a set of simple
lists without any concern for how the data is stored. There is no "save" button
to be clicked in OneNote. Blogger and OneNote provide intelligent data handling
for me. Yet, if I create a document in Microsoft Word, I am forced to provide a
file name and save location if I want to store my document.

As a user, I can utilize a file system to organize my documents for me, but
generally, I just don't care. As long as all of my files are grouped by project
(read: one level deep) I can find practically anything I need. N-tier grouping
is overkill for nearly all typical user scenarios.

File systems define data relationships by hierarchical locality. If two files
are in the same folder, they are probably related, but there is no guarantee of
that. Data has a lot of inherent relationships that goes unexploited by current
day software. Source documents such as code and Photoshop files are in no way
linked to their output documents such as executables and web graphics. I might
have to click three dozen times to find the file for the image that is embedded
in an a Word document or I may never find it (it could have been deleted).

We need a directed graph having files as nodes and relationships as edges.
These relationships need to be automatically inferred whenever possible.
Relationships should be able to span from RAM to hard disks, from one hard disk
to another, across the internet, and everywhere in between.

Data collections need to be easily searchable. Querying and filtering data
should not require construction of a database; tables and indexes should be
created automatically to accommodate data access patterns.

As much as possible, the work of managing data needs to be offloaded to
computers so that humans can be left with the task of creating and utilizing
data.
