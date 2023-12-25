---
title: "Jenkins is a Turing Tarpit"
date: 2023-12-04T15:59:19-05:00
draft: true
---

> Beware of the Turing tar-pit in which everything is possible but nothing of interest is easy.
>
> — Alan Perlis, [Epigrams on Programming](https://dl.acm.org/doi/10.1145/947955.1083808)

For many many years, Jenkins was the de facto CI solution for any organization of considerable size.

Jenkins is ill-suited for the demands of modern development pressures, practices, and processes and
you shouldn't use it.  You should choose something else to run your CI. To some of you, this may
seem so painfully obvious as to not even merit mentioning, while to others of you this may seem
like a rather extreme hot-take.  This essay is aimed at the latter crowd.  Because Jenkins has been
used so universally for so long by so many people, I think its place in the status quo has allowed
its shortcomings to go unnoticed, or at least underreported.  But these shortcomings are massive, and teams that have moved to better technologies will have significant competitive advantages compared to those stuck using Jenkins.

You could be forgiven for missing the changes that have happened in the CI space in the last 10
years or so.  For a long time, Jenkins really was the only worthwhile game in town.  Sure,
JetBrains fans might have used [Team City], and open source projects used to use [TravisCI] on
GitHub.  But those were special cases, and if you were serious about doing "real" CI for a larger
org, that meant running Jenkins.  Those watching the open source space around CI might have seen a
few new tools rise like [GoCI] or [Concourse] or [Drone], but nothing really seemed to challenge
Jenkins' place at the top of the pack.

Sadly it's been _outside_ the open source ecosystem where the most significant innovations have taken place.

## Modern development and release

I think to better understand why Jenkins has gone from being a clear front-runner in CI to a relic
of the past in so short a time, it's useful to look at how our expectations for software delivery
have changed in the past 10 years or so.

Ten years ago, I was working as a software developer, and I had _never heard_ of Continuous
Intgration.  I had only just started to understand what a Pull Request was.  It was absolutely
normal for me and my team to spend days or weeks developing features in insolation, and at the end
we'd ask the lead developer to merge our branches.  He'd take a look at the patch briefly, and if
it looked good enough, he'd merge the changes, resolving any conflicts himself.  When we had
features ready to ship, we'd do a release.  There wasn't really much more process than that.  We
were a Python shop, so the "build", to the extent that there was one at all, was quite minimal, and
all testing was manual against the hardware and external systems we worked with.  I think if you
had tried to explain CI to us, we might have been a bit confused.

At the same time, Jenkins had been around for quite a while.  And it was doing wonders at bigger
shops with more complex build processes, where it ensured that main builds were at least buildable,
and often invoked the duties of a designated _build cop_ to fix things or at least push the big red
button to stop merges when stuff broke.  This level of automation was revolutionary at the time and
for many teams mostly solved the enormous pain point that was feature integration.  Sure, being a
_build cop_ sucked, but it was better than being a release engineer at a time when that meant
resolving incompatible changes from feature developers.

This was

## Operating Jenkins

## Myth of flexibility


