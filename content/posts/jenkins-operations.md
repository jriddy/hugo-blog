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
its shortcomings to go unnoticed, or at least underreported.  But these shortcomings are massive,
and teams that have moved to better technologies will have significant competitive advantages
compared to those stuck using Jenkins.

You could be forgiven for missing the changes that have happened in the CI space in the last 10
years or so.  For a long time, Jenkins really was the only worthwhile game in town.  Sure,
JetBrains fans might have used [Team City], and open source projects used to use [TravisCI] on
GitHub.  But those were special cases, and if you were serious about doing "real" CI for a larger
org, that meant running Jenkins.  Those watching the open source space around CI might have seen a
few new tools rise like [GoCI] or [Concourse] or [Drone], but nothing really seemed to challenge
Jenkins' place at the top of the pack.

And this is a shame.  For the last 10 years has been playing a game of catch-up to innovations that
have happened in other CI systems, mostly closed-source ones.  And it still pales in comparison to
its better competition.  That begs the question:  if Jenkins is so inferior, why do so many teams
treat it as the go-to for CI?  It's a simple matter of familiarity, as far as I can tell.  Since most of the better innovation is happening on paid platforms, developers that haven't used these platforms just _don't know what they're missing out on_.

## Good enough

## Modern development and release

I think to better understand why Jenkins has gone from being a clear front-runner in CI to a relic
of the past in so short a time, it's useful to look at how our expectations for software delivery
have changed in the past 10 years or so.

Ten years ago, I was working as a software developer, and I had _never heard_ of Continuous
Intgration.  I had only just started to understand what a Pull Request was.  It was absolutely
normal for me and my team to spend days or weeks developing features in isolation, and at the end
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

Starting somewhere in the 2010s, the DevOps movement started pushing continuous integration and
delivery practices more mainstream.  Groups like [DORA] were able to show more concrete business
value for being able to ship often, and tools like [Travis CI] brought speculative merge CI to the
mainstream, as it became the default way PRs worked in open source development for many projects.
The expectation of being able to run a "pipeline" of steps defined in-repo on the branch one was
aiming to merge was a real game changer for maintainers, who could reject problematic code before
it landed in their primary branches, and with the in-repo config feature this extended to CI code
itself.  These features, if used correctly, can really minimize the amount of build cop and
integration work that needs to be done to keep a CI-enabled project healthy, which can dramatically
improve the throughput.

At the time, Travis CI was kind of limited as was only free for open souce use, so as cool as these features were, they were not available to many organizations, who didn't want to pay for a service like Travis for a wide variety of reasons.  However, Jenkins caught up.

## Operating Jenkins

At first glance, Jenkins seems dead simple to operate.  There's really very few moving parts, and
it's just a JVM process that needs a decently sized machine with lots of disk to keep its state.
Compare this to something like [GitLab], which needs a database, message queue, and cache to run
effecitively, or, god forbid, _[TaskCluster]_, which seems to have about 10 moving parts at the
very least, this seems a lot better.  You just have to manage the one machine.

But as you increase demands, this makes the actual experience of _running_ Jenkins much, much more
complicated and full of unknown risks.  You want to have declarative infrastructure?  Use [JCasC],
but be careful!  Who knows if running that job config will set off a cascade of actions that can
lock up your machine.  You want backups?  Okay cool, it's just files on a disk, there's all kinds
of tools for that.  But how do you restore it for disaster?  How fast and reliable is that restore?
It's anybody's guess unless you've practiced it repeatedly with your actual workloads.  You want
reliable, automated upgrades?  Forget it, every upgrade is unique, requires downtime, and can
result in dependency hell trying to figure out which versions of dependent packages need to be
installed, as well as mismatches between package versions and config.  You want HA?  Good luck.
Jenkins expects unique access to a disk, so having a shared network drive will not work the way
you'd expect.  Guess you need to shard your workloads somehow.  Let's hope you or your client teams
thought about that up-front, because otherwise it's going to be hell to migrate to it.

Not having a database to manage makes things easy to start with, but there's a good reason the
software world has settled on external databases as the best way to manage state in long-running
applications.  Running applications and managing persistent data come with very different
challenges.  Moving them into different places as a basic divide-and-conquer move lets us treat
applications as _mostly_ stateless, which greatly simplifies things like upgrades and HA setups.
Using well-understood technologies like Postgres and Redis allows you to leverage the enormous
amount of expertise that exists out there in the form of text, video, or _actual humans you can
hire_ to handle the data layer.  Hell, if you don't want to get good at Postgres, you can use an
external service like my current employer [Aiven] to handle that bit for you.  This can free you
and your team up to focus on getting the intricasies of the application layer right.

The application layer of a CI system can be quite intricate.  But at least when its state can be
located in a different layer we can start talking about real immutable, declarative infrastructure
and a practicable HA model.  Some of this can probably be achieved with sophisticated sharding and
routing rules around Jenkins, but there's no out-of-the box solution that I know of and no
literatur I've found out there that explains how to do this repeatbly.  Once again, it's
_theoretically_ possible to do whatever you want with Jenkins.  Just not easy.  And thus this
seeming simplicity of the Jenkins deployment model might lure you in with low initial operation
costs, but that cost curve massively escalates as you try to do anything more complex with it.

This cost hits a lot quicker than you might guess.  Any Jenkins setup worth using requires a
tremendous number of plugins, which each have their own dependencies.  This huge surface area makes
it easy to hit real bugs and security vulnerabilities on a regular basis.  This means you _should_
be updating it regularly, which means you should hit several of these problems very early in your
project or organization's life.  The only ways to avoid these issues are essentially various ways
to bury your head in the sand:  you can treat major CI downtime as though it isn't a problem, you
can kinda ignore security issues by sticking it behind a VPN, and you can assign a surprisingly
high number of developers to dealing with these issues.  None of these address the core problems
with Jenkins, which is that it wasn't designed to be operated at scale and speed, and most attempts
to fix that are hopeless kludges.

### A note on JCasC

As I mentioned before, Jenkins _does_ have a "configuration-as-code" system called [JCasC], which
aims to be a sorta Terraform for Jenkins, providing a way to give ostensibly declarative
descriptions of the desired system and job configurations to the Jenkins instance, and letting the
JCasC plugin do its magic and apply the diff between your desired config and the state of the
Jenkins instance.  This is definitely a huge improvement over point-and-click management, but it
falls short of being a really viable, scalable way to handle Jenkins deployments.

The big pain point here is not really knowing what will happen when you reload CasC.  Normally,
everything works swimmingly, and all your config gets reloaded, with any existing running jobs
completely unchanged.  But ocassionally, and unpredictably, reloading CasC will make Jenkins trip
over bizarre and hard-to-reproduce edge cases and lock up the instance.  This is made all the worse
by the difficulty of achieving HA, which means you're probably running this CasC reload on a Jenkins instance in active use by your developers.  A non-comprehensive list of issues my team has hit while performing CasC reloads:

- JVM memory usage spikes that lock up the instance until it is restarted
- Livelock from cleanup threads, since a CasC reload triggers the scan and cleanup jobs for several
  project types to all run at once
- CasC simply not working until Jenkins is restarted
- Unexpectedly invalid configurations being applied that seemingly have no effect but cause Jenkins
  to refuse to start when restarted, claiming invalid config but offering no further details

Some of this shouldn't be too surprising to anyone familiar with using infrastructure-as-code in
practice.  Cloud infrastructure IaC can cause major issues, particularly when stateful services are
involved.  But this is a big reason why tools like Terraform have dry-run commands like `terraform
plan`.  It at least lets you read, audit, and record what will happen during an IaC application, so
you can identify potential risks before the fact, or have a good idea where to look after if
something goes awry.

## Myth of flexibility

Probably the most pervasive myth about Jenkins is that it's flexible.

  There's a pervasive "running Jenkins is
easy" myth that seems to rear its head and influence decision makers.  For one, managers and other
decision makers may have used Jenkins before in situations with simpler requirements and lower
expectations than current projects, causing them to greatly underestimate the cost of running
Jenkins.  For another, many people may have seen Jenkins deficiencies hidden behind

