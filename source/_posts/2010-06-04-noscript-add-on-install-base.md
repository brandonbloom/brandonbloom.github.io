---
layout: post
title: "NoScript Add-on Install Base"
date: 2010-06-04
comments: false
---

The <a href="http://noscript.net/" id="zj_s" title="official NoScript site">official NoScript site</a> downloads via the <a href="https://addons.mozilla.org/en-US/firefox/addon/722/" id="kbbv" title="Mozilla Add-ons page">Mozilla Add-ons page</a>, so its download count is probably accurate: 67,616,402

NoScript does not publish their individual add-on usage statistics, but the global download/usage ratio can be calculated from the statistics on the <a href="https://addons.mozilla.org/en-US/firefox/" id="dq5o" title="Firefox Add-on home page">Firefox Add-on home page</a>:

* 1,962,617,946 add-ons downloaded
* 157,090,095 add-ons in use

About 8% of downloaded add-ons are still in use. Assuming NoScript's usage
ratio is comparable to the average, approximately 5.4 million installations of
FireFox are running NoScript. Let's ignore the fact that NoScript's usage ratio
is probably much lower than the average, due to the fact that it <i>breaks most
web pages</i>.

I'd wager that the average NoScript user has at least two machines, so the
total number of NoScript users is probably less than 2.7 million.

<a href="http://www.google.com/publicdata?ds=wb-wdi&amp;met=it_net_user&amp;idim=country:USA&amp;dl=en&amp;hl=en&amp;q=number+of+internet+users+in+america" id="seha" title="There are over 230 million internet users in the USA">There are over 230 million internet users in the USA</a>.

Even if 100% of NoScript users were Americans, they form 1% or less of the
general population. If you, like me, believe that I have been generous to
NoScript here, it is likely that no script users are no more numerous than 1 in
1,000.

Even if the user is using NoScript, they can whitelist your site. You can
probably add `<noscript>WARNING: THIS SITE IS BUSTED WITHOUT JS</noscript>` to
the top of your page and call it a day. If you are feeling generous, redirect
no-script users to the mobile version of your site and tell them why.

tldr: Assume that human user agents have Javascript.

## Imported Comments

### Anthony DiSanti

What's the inspiration behind this post?  Did you think you actually needed to
support a strong noscript experience?  JS is mandatory at this point, screw
anyone that's turning it off.

### Brandon Bloom

Facebook's BigPipe tech announcement prompted some Javascript-required
discussions on Hacker News.
