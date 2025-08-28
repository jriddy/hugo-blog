---
title: "Shell is Jank"
date: 2025-08-25T16:19:58-04:00
draft: true
---
The standard-ish Unix shell is a janky, disjointed monstrosity riddled with incongruous features agglomerated from inconsistent design decisions made at various times and places by an unknown multitude of developers.  It is also one of the most important pieces of software in the world today.  Sure would be nice if it didn't suck.

## Digital wizardry

Early in my Unix journey, I was in _awe_ of the shell.  Watching my friend who had grown comfortable with the shell was like watching a master work with his tools.  The data yielded to his precise commands like putty in his hands.  The arcane symbols and abstruse output flew by, totally unintelligible to me, but clearly full of meaning for him, as he worked to configure some webserver on a Linux machine.  Was he a wizard?  No.  He was a _hacker_.  I wanted to be one too.

_Awe_ is the right word for what I felt here.  We use _awesome_ quite casually these days, but to say we're _in awe_ is reserved for a more serious, almost spiritual experience.  Up until then, I had only really dealt with windowed GUIs.  GUIs are neat, but they are fundamentally grounded in a tactile metaphor that makes them seem mundane, and limited in that you can only ever do what the GUI developer had envisioned you could do.  These shell prompts seems so abstract and unbounded by comparison.  I would not limited by the software.  I could do _whatever I want_, provided I could craft the right command.  Here was alchemical power, right at my fingertips, if only I could read the symbols.

It sounds crazy to attribute this admiration to shell, right?  It's just goofy lines of text on the screen.  But this is really a big part of what got me into computers.  It's why I installed Linux on my parents computer when I was 13 and made crash.  It's why I learned to program in high school.  There was expressive power available to those who could learn how these things work.

This metaphor isn't totally unique to programmer-types.  Even Hollywood loves to makes shell and code fly by on a hacker's screen as a visual shorthand for their technical wizardry.  We make fun of these depictions now, but there's a reason why they work.  It's alluring to think there's secret knowledge available to us just beyond our reach: that if we could master the arcane incantations it requires, we could bend reality to our will.  So the trope persists.  Regular humans use GUIs.  Real hackers use shell.

## An indispensable tool of the trade

This isn't just a silly affectation.   I genuinely use shell in my work more than almost any other tool.  And I'm not just banging out one-lines into an interactive prompt, although I do plenty of that.  I commit shell scripts.  I write tests for shell scripts.  I use shell scripts as fundamental elements of production workflows.

As a SiteReliabilityDevSecOpsPlatformInfra Engineer, a huge amount of my work involves bridging disparate domains and configuring big, complex pieces of software in such a way that they work together.  Shell is the near-ideal tool for this.  It's ubiquitous.  It's approachable.  It's malleable.  It yields in my hands like putty.  It is the perfect _glue_ language.

Perhaps calling it _glue_ is underselling it.  We typically don't think of glue as super important in construction projects—although you might be surprised how critical adhesives are for a variety of uses—nor do we think of glue as having much internal complexity.  Shell ends up being more like _custom couplings_ between various parts of a system: small pieces improvised to get a component wired up to another or to shuttle some critical pieces of data across a gap.

- Glue
- DSL for running programs
- Python/Ruby can do this but they can also be terrible because it's too easy to make god programs

## Cracks in the Mortar

- "Everything is a string" typing
- Getting structured data out of programs and into variables is a nightmare
- Bizarre syntax
- Shell is a DSL for running programs, but can't handle errors sensibly
- Functions vs process differences
- Loads of gotchyas and weird accumulated behaviors

## From Alchemy to Chemistry

- Still need to be compatible
- Bash/Zsh fixes none of this
- Fish provides a great interactive experience but still has many of the same problems
- Need to have good error handling by default
- Types don't solve everything (looking at you, Microsoft)
- JSON is a great default
- Oils seems like a great idea, but it's still kinda raw

## Recommendations

- Don't get bogged down in bad shell land, it's bad for your brain
- You need to know a good solid subset of compatible Bourne shell because history
- Try something else for your daily driver
