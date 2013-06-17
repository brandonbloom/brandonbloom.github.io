---
layout: post
title: "PowerShell: condemned to reinvent"
date: 2009-04-20
comments: false
---

I tried PowerShell when it was first released, but never used it for real work.
I recently attended a "brown bag" presentation about PowerShell. This
presentation spurred me to augment our team's environment with PowerShell and I
have been using it every day since.

In the past weeks using and abusing PowerShell, I have drawn two conclusions:

* PowerShell has a killer set of standard tools with brilliantly designed
  usability.
* The PowerShell team doesn't understand UNIX and therefore were condemned to
  reinvented it, poorly -- with apologies to Henry Spencer.

First things first: if you spend any time working with Windows, get PowerShell.
Now. Stop reading my blog and go [download it][1] immediately. It mops the
floor with cmd.

The key premise behind PowerShell is that it operates on live .NET objects.
This is beneficial because it eliminates a lot of the text cutting and
manipulation common in shell scripts. Additionally, it puts the full .NET Base
Class Library into your scripting toolbox. PowerShell tools, known as
commandlets, typically only render the most common fields for their objects,
but the less common fields are easily available in memory. By convention,
Commandlets are named with a `verb-noun` pattern and support a common command
line parsing behavior. The repository of commandlets and the command line
options of each are easily queried and highly consistent. All this meta-data
makes PowerShell a breeze to learn.

I fell in love with the the discoverablity and ease of use when I tried to kill
a collection of runaway processes:

```
PS> get-command -noun process

CommandType   Name           Definition
-----------   ----           ----------
Cmdlet        Get-Process    Get-Process [[-Name] <String[]>] [-Verbo...
Cmdlet        Stop-Process   Stop-Process [-Id] <Int32[]> [-PassThru]...

PS> get-process notepad | stop-process
PS> get-alias | where { $_.definition.contains("Process") }

CommandType   Name   Definition
-----------   ----   ----------
Alias         kill   Stop-Process
Alias         ps     Get-Process

PS> ps someotherapp | kill
```

OK, that's pretty cool and oh-so-very Unixy -- right? Wrong. Notice the
"CommandType" column in the results of get-command. There are many other types
of commands besides commandlets: functions, filters, scripts, applications,
etc. Each of these has slightly different semantics for pipes and parameters.
Applications, for example, have no way of accepting .NET object pipes. You must
develop a separate commandlet. Yikes!

Compare to Unix: all commands are applications which accept a command line and
pipe byte streams in and out. Much simpler, but byte streams aren't as
friendly, discoverable, and maintainable as object streams. However, the
brilliantly simple thing about Unix is that, when you get right down to it,
object streams are just byte streams! There is absolutely nothing stopping you
from implementing `get-process` and `stop-process` as Unix programs which pipe
object references, JSON, pickled Python objects, XML, S-expressions, or any
other data format you fancy. Doug Mcllroy, the inventor of Unix pipes, was
right: text streams are the universal interface.

Actually, this is no different on Windows. All of the PowerShell commandlets
could have been implemented as applications which import a library. This
library would replace `main` in much the same way as `winmain`, provide a
metadata enriched implementation of [getopt][2], `man`, etc. There is no need to
invent a new shell in order to acquire the power of piping objects. Sure, cmd
is old and needed to be retired for many other reasons, but it is a real shame
that the PowerShell toolset is not available to those of us stuck in batch
scripts.

Personally, I would really like to see such a library developed. Microsoft has
certainly proved one thing with PowerShell: steep learning curves are not
intrinsic to command line interfaces. Unfortunately, commandlets are two steps
forward and one step backwards. I have no doubt that we can retake that forward
step.

## Imported Comments

### klumsy

I find the 2nd point interesting given that most of the powershell team were
unix only guys before this project.

### Brandon Bloom

I don't know the history of PowerShell or any of the team members. I'm sure
they are all very smart and have a strong grasp of Unix in general. Maybe they
understand exactly what I'm getting at, but explicitly rejected it for some
non-obvious reasons (such as scheduling, implementation complexity, target
users, etc). They probably know better than I do. My apologies for exaggerating
to make a point :-)

### Howard T. Snidbiscuits

I agree, PowerShell is way better than the primitive, DOS style Windows CMD.
However, it's nothing like a normal Unix shell for me... and I'd rather just
use cygwin.

### Kosta

Sorry but no: Pipelining objects is much smarter. You can still pipe strings or
binary data if you want, but you don't have all the drawbacks.

And then there's the security implications. While theoratically possible, are
all your scripts safe against whitespace|"'``` injection? Piping objects is sooo
much easier AND it does not kill your system if the bytestream contains `rm -rf /`.

Just my two cents...

### Powershell Jedi

Unix is optimized around flat files windows is optimized around objects. It's
not the they don't understand Unix. The Powershell Team has a strong unix
Background. It's just just that M$ has 87% market share and a trillion dollars
in the bank share that says F@ck unix.

### Brandon Bloom

Piping objects has draw backs too. Here's a case that came up on the internal
PowerShell mailing list...

Unix:

```
cat foo | wc -l
```

PowerShell:

```
Get-Content foo | Measure-Object -line
```

Ignore the line length, as I didn't use any aliases for the PowerShell version.
The issue here is that Get-Content returns a String, not a StreamReader. The
result is that for large files, the entire String must be read into memory and
for really large files, swapped back out to disk!

You could easily work around this with objects; this example is just to show
that objects have drawbacks just as byte streams do. Everything is always a
balance. I like the Unix approach because it is a simple system with drawbacks
that are well understood. I dislike the object approach because it is a complex
system with less obvious drawbacks that don't bite you until much later.

### Len

Are you kidding?  MS couldn't even get the *simplest* thing right.  Their
"compare-object" is supposed to be their "equivalent" to diff.  Not so.  Not
even close.  Try comparing these two files:

```
file 1:
a
b
c
d

file 2:
d
c
b
a
```

Unix diff correctly shows how they are different.  MS compare-objects says they
are the same.  Why?  Because the latter doesn't care about order.

Microsoft is not "very smart".  They are demented.


[1]: http://www.microsoft.com/windowsserver2003/technologies/management/powershell/download.mspx
[2]: http://www.gnu.org/software/hello/manual/libc/Getopt.html
