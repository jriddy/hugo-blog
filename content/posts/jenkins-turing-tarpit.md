---
title: "Jenkins is a Turing Tarpit"
date: 2024-01-07T10:02:32-05:00
draft: true
---

> Beware of the Turing tar-pit in which everything is possible but nothing of interest is easy.
>
> — Alan Perlis, [Epigrams on Programming](https://dl.acm.org/doi/10.1145/947955.1083808)

Jenkins, long considered the go-to solution for continuous integration systems, is perilously
ill-suited for the demands of modern software development.  It is a relic of another time, kept
alive by the well-meaning work of talented developers who are nevertheless applying figurative
lipstick to the metaphorical pig in their valiant but misguided efforts to keep it relevant into
today's world.  But it is not relevant in today's world.  It is a _Turing tarpit_ that makes CI
development significantly harder than it needs to be, and will hinder or even prevent your team
from succeeding in the modern software environment.

The term _Turing tarpit_ usually is applied to esoteric, intentionaly perplexing programming
languages like [Intercal], [Brainfuck], or [Whitespace], which are mostly gag tools meant to meet
the technical requirements of being a programming languages--i.e., [Turing-completeness]--without
providing meaningful abstractions or ergonomics for completing real world tasks in a way that makes
sense to humans.  I apply it here in a broader sense: a system that has the theoretical capacity to
do everything that it is supposed to do without really making it easier, at least not for the tasks
to which it is commonly applied.  Jenkins does have many domain-specific tools for doing CI-like
things: job scheduling and triggering, test reporting, artifact storage, and so on. (These are
nearly all implemented by plugins, which--well, that's a whole other article I need to write.)  But
they all kinda suck when you try to push them even just a little beyond the very narrow scope of
uses they were created for.  So working with Jenkins ultimately _feels_ like a sandtrap:
everything you need is technically there, but nothing worth doing is easy.

This may come across as a hyperbolic, click-baity hot-take, but I mean what I say: _Jenkins is
where DevOps dreams go to die._  You should not use Jenkins.  It will cost you.  Other
organizations that chose something better will have a measurable competitive advantage over you.
There are vanishingly few exceptions to this maxim, and if you're in doubt at all, you're probably
not among them.  How do I know this?  I've worked with Jenkins more-or-less directly for the better
part of the last 6 years, and I've only ever achieved good DevOps results when we've moved away.

# Better options exist

A number of years ago, I was working QA for a small startup focused on computer vision.  We were
running Jenkins for CI--as one does--but it was an old, old version and it was always broken.  One
of the senior devs took about a month to set up a new Jenkins instance with all the new hotness
configured: multibranch pipelines, pipeline views, all kinds of great stuff.  It was better, but it
still broke frequently, for all the usual reasons old-school CI systems break: missing
dependencies, wrong versions, generally leftover broken state from previous runs.  Productivity was
low and the product was pretty lousy, but the expectations for polish and speed were low since we
didn't really have any paying customers and we could cobble together a good-enough demo whenever we
needed.  It sucked, and it was painful, but it was _possible_, so there was little management
pressure to fix these things.

At some point we hired a new developer who seemed to really hate Jenkins, and I didn't know why.
Certainly it had its problems, but it's a lot better than doing this all manually!  Even if things
break all the time, as long as we fixed them, it was okay.  Still, the new developer insisted
things could be better.  He new developer gave the old college try at making Jenkins more workable,
but soon gave up and focused on convincing us to switch to a SaaS CI offering.  We eventually
landed on [GitLab], which at the time only cost $5 per seat per month, so it was a fairly easy sell
to management.

Within two weeks of trying GitLab, commit throughput had _doubled_.  The amount of time it freed up
for senior developers and QA gave us the freedom to rapidly clean up our process.  Within 6 weeks,
we had implemented rudimentary continuous delivery and were averaging 2 merges per contributor per
day.  It became a fun little challenge to see how much we could change without breaking things.  We
starting ringing a bell every time a merge happened.  If you broke main you had to wear a rubber
chicken hat until you fixed it.  Spirits were high and we were making real progress on work we had
previously thought impossible.

This experience left a lasting impression on me.  Sure, not all of the problems we fixed were
directly a result of Jenkins, but it did feel like using the more sensible defaults of a newer
system had left us with a much easier-to-maintain system, which gave us the time and energy to
focus on fixing our deeper problems.  After living it, this insight felt so obvious that it has
become shocking to me how many companies rely on dated and clunky Jenkins setups to build, test,
and ship their code.  I've worked with three Jenkins systems or clusters since then, and they all
showed some of the same problems I saw in this first instance: constant breakages, clunky pipeline
structures, rampant hackery, and ever-present workarounds to deal with the shortcomings of the
system.  Worst of all, these problems tend to go mostly unnoticed, or remain attributed to general
"tech debt" in away that avoids singling out any particular technical decision for blame.

# Modern expectations for CI systems

So what is it about newer, more _modern_ systems that can give such huge gains in efficiency?  Or
rather, what is it about Jenkins that makes using it to drive your delivery pipelines so
cumbersome?  I think most of it stems from Jenkins' core pre-dating most modern trends in software
delivery and infrastructure administration, to the point where its core design decisions don't
align with how modern teams build and deploy software.  So to that point it might be useful to
enumerate what those expectations are, and then explain how Jenkins has tried--and failed--to keep up with the times.

This raw list of requirements may very in applicability from simple no brainers to stuff that may
seem unnecessary for really small orgs.  This is intentional.  Since Jenkins is often positioned in
arguments as a more _serious_ choice for real teams, I'm including requirements of operating CI at
a more significant scale, where one may need to support over 100 engineers running 10k+ minutes of
builds per month:

- Ability to deploy and configure with infrastructure-as-code
- High availability, or at least limited downtime and rapid recovery options
- Well understood scaling requirements
- Fine-grained permisison models
- Security auditing capabilities
- Scheduled jobs
- Source control-triggered jobs
- Manual user-triggered jobs
- Build parallelism
- DAG-like pipelines
- Dynamic control of pipeline execution
- Sensible integration with container systems like Docker
- UI extensibility
- Extensive user and adminstrator documentation

And all of these requirements are to support the primary modern DevOps goal: developers ship
quality code multiple times per day.  This is often expressed as the goal of achieving elite [DORA]
metrics: high deployment frequency, low lead time for changes, low change failure rates, and low
mean time-to-recovery when failures occur.  All of these things require a high quality, reliable,
and available build and test infrastructure to support.

Jenkins falls short in most of the operational concerns needed to support this.  The big issue is
its fundamental lack of any kind of high availability model means your Jenkins instances are huge
points-of-failure in your development pipeline.  So you have to tread really carefully when you
make any changes.  Jenkins does possess an infrastructure-as-code system called [JCasC], but it has
some deep flaws that can make it unweildy to use in practice, and the potential for error on your
non-HA Jenkins system can make your team really gun-shy about experimenting with ways to improve
this.  Scaling in Jenkins is certainly possible, although much of getting performance out of it is
about making sure to always execute code in agents, rather than on the coordinator, which means
changes to user workloads can drastically impact how well your non-HA single-point-of-failure
coordinator instance performs in a way that is hard to predict or diagnose.  There's a lot to know
about operating Jenkins at scale, and it's not at all well documented out in the wild, so I will
write another post at some point about this.  For now, suffice to say that Jenkins does not meet
operational requirements for a mission-critical system.

# The myth of flexibility

Probably the most common counter-argument I get to my "Jenkins sucks" skreeds is that, while
Jenkins may indeed be old, clunky, and difficult, at least it's flexible.  This is often presented
as an point up on other systems that provide a declarative YAML input format for your CI
definitions.  Rather than limiting you to what a CI system creator thinks you should do, Jenkins
executes a build pipeline script in a sandbox for you.  But Jenkins' design choices here don't
actually result in much more flexibility than you might have in competing systems, which have
by-and-large converged on YAML as a default configuration language.

YAML itself has a thousand problems that I won't go into here, but it does the job well enough that
a huge majority of systems for managing builds, CI, infrastructure, use it extensively.  Notably,
its flexible support for arbitrary string content makes embedding scripts into it pretty easy, but
its obvious structure and declarative nature means you can do cool things like scan it before
executing it, or even generate it from other code if you like.   In theory, using a language like
Groovy lets you do that generation on the fly, but in practice but security, governance, and ease-of-use concerns
have led to Jenkins pushing a declarative DSL syntax.

What this boils down to is that you're strongly encouraged to use an underspecified and
under-documented DSL in an awkward programming language to express fewer concepts than you can in a
competing config format.  Of course, you can always break out of this with script blocks or a
shared library, but this can affect performance in dramatic ways by putting computational load on
your coordinator instead of your agents, a practice that the official docs expressly recommend
avoiding.  So much of the advantage of using a programming language is lost.

And

After all, you can always just write some Groovy or even write a plugin to do what you want.

# What should you do?

I don't want to be entirely negative about Jenkins.  I know that some teams have used it to great
effect, especially in the past, and if you've made the proper investment into building the
expertise you need to meet modern CI expectations with a legacy system like Jenkins, it's possible
you're doing fine.  But if you're not in that rare group, you're probably paying a lot and getting
less than you could with virtually any of the commerically available options.  If you have detected
software quality, delivery velocity, or developer experience as major pain points, and you're still
using Jenkins, you're probably doing it wrong.


