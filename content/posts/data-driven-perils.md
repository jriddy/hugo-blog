---
title: "Data Driven Perils"
date: 2023-09-29T07:25:10-04:00
draft: true

series-outline:
  - Many companies aspire to be data-driven but actually doing that well is extremely hard and there are tons of pitfalls
  - Measuring the right data is hard:
    - What is easy to measure is often not the most useful thing
    - Most measurable quantities are proxies for things we care about, and the degree to which that proxy corresponds to our goals is itself hard to measure
    - It's very easy to collect bad data and not know it
  - People react to being measured:
    - Sometimes just means pursuing less-than optimal solutions, because we're aiming at a proxy metrics rather than the real thing
    - Other times we'll just flat-out game the system
    - Most of the time, we can adjust what we measure or how we measure during the period, which can corrupt the data or influence results as we like
  - Discussions around data will be dominated by narratives anyways:
    - Narratives are what drives decision making in organizations; data points are the details that fill out that world
    - We're typically able to cherry-pick quite liberally to tell the stories we want to
    - Post-hoc analysis and interpretation can be used to paint drastically different pictures of events
  - Metrics are okay, but you need to understand their weakness to use them effectively. They should be understood as a tool to aid understanding and develop narratives, not as a scientific window into the inner workings of your organization or systems.

---

Hi, I'm Josh.  I'm a software engineer, and I dread quarterly planning.  I rue the estimation
process.  I deplore having to balance customer needs, business priorities, concerns about technical
debt, and team wishlists.  But most of all, I loathe setting _metrics_ by which my work will be
measured.

I don't think any of these things are bad.  Obviously we need to plan and planning involves picking
some things we like, some things we don't, and saying no to a lot of other things.  Estimation, for
all its problems, is necessary to have some basic idea of what we can accomplish.  I think these
things can be improved in software organizations.  But metrics?  Metrics suck.  They don't have to,
but they way they tend to be measured and used, they usually do.  I hope in this essay and series, I can explain _why_ they suck and in what specific _ways_ they suck and what we can do to make it better.

Most tech companies aspire to be _data-driven_, and this almost always defaults to meaning numerical metrics.

Tech companies aspire to be _data-driven_.  It's almost a foregone conclusion at this point.  This
is the default Is it a cliché yet that all tech companies seek to be data-driven?  If not, it
should be.  Your average tech exec certainly wouldn't want to be caught _not_ data-driven,
following some squishy humanistic _narrative_ over _cold, hard logical facts_.  At least, not
unless they had some guiding philosophy that suggested otherwise.

This desire to be (or at least _appear to be_) data-driven does little to change what
decisions are made at these organizations, and usually only exists to defend management
from criticism.

Numbers give the appearance of science.  The old saw is that 70% of statistics are made up on the
spot, with that statistic itself inevitably being made up on the spot.  But the sense behind that
aphorism works because we all intuitively understand how just adding a plausible number to data can
bypass our critical thinking and get us to accept it as facts.   In public discourse, you can get
caught using bad data, since there do exist fact-checkers.  And if you're truly making up data,
it's pretty easy to call someone on it in casual conversation.   But in the context of
organizational meetings backed by dashboards derived from data that has ostensibly been collected
from _somewhere_, it's a lot harder to challenge the validity of the data on the spot.

This is made worse because you never know which data point someone will rest their narrative on,
and how valid that particular data point is.

## It's hard to measure the right data

People sometimes assume that any data is good, or that it's better than no data.  This discounts
the fact that data can be _bad_ or _wrong_.  By _bad data_, I mean low quality or low resolution
data that suffers from collection or sampling issues that make it fundamentally incorrect in some
way.  Bad data simply doesn't properly represent the ground reality it purports to.  _Wrong_ data
is data that is either not pertinent or not specific, but lends itself to drawing conclusions that
are unwarranted.  Both cases are insidious in that the presence of data tends to turn off critical
reasoning about that data, and we tend to just assume that it is valid as-is.

But getting _good_ data displayed in the _right way_ remains a subtle and difficult problem.  The
trap here is how easy it is to get started. Modern tools make it absurdly easy to put together a
dashboard, which can make for a snappy presentation or cool operations workstation look.  But
nothing about our tools helps us judge the quality or relevance of our data to the goals we want to
apply it towards.
