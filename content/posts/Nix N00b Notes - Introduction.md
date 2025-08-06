---
title: "Nix N00b Notes: Introduction"
date: "2025-08-06T11:38:41-0400"
draft: false
tags: ["Nix", "Tech"]
---
> [!info] Nix N00b Notes Series
> This is part 1 of a series where I try to learn to use Nix to manage my Mac laptop. Yes, I can think of better things to do with my time, but I enjoy inflicting this specific type of pain on myself.

I've found that most tech folks have at least one topic of extreme interest and obsession that seems insane even to our peers. If you're not a computer nerd, you probably think we're all crazy—and you're absolutely right—but these interests are truly _arcane_, much more so than run-of-the mill tech stuff you deal with at work. Common examples include home automation, microcontrollers, formal methods, obscure programming languages, and alternative Unices. Each one of these is a warren of technical rabbit holes, and while dark and foreboding to most, they can feel comforting and cozy to those that love them.

Mine is _build systems_.[^1]

These are systems that deal primarily with transforming source code into runnable programs, and associated tasks like installing software and performing a slew of development tasks. Sounds simple enough, right?

Well, what starts as something simple inevitably balloons into a complex orchestration of tasks with multiple dependencies that need to be supplied to other tasks, which feed yet more tasks, and so on and so forth. So it turns into a complex graph and configuration problem.

And because of the complexity and computational weight of these tasks, it's common to look for ways to avoid doing rework or only doing partial work. This leads to an emphasis on _reproducibility_ so that we can more reliably determine when it's safe to re-use pre-existing results, since, if a task is reproducible, we don't need to do it again as long as we have a copy of its results when performed on the same inputs.

I've spent a fair amount of time in some of these _reproducible build tool_ communities, notably [Pantsbuild](https://www.pantsbuild.org/) where I was briefly a maintainer[^2]. Other notable tools like this include [Bazel](https://bazel.build/) and [Buck](https://buck2.build/). These are developer tools, aimed primarily at solving problems that occur in larger [monorepos](https://en.wikipedia.org/wiki/Monorepo). But that's not the only application of this kind of technology. In fact, it's common in these tool communities to get questions about how your system integrates with _Nix_.

Nix takes the reproducible build notion and applies it to _packaing and installing software_. If this seems boring to you, I don't blame you, but remember this is one of my rabbit holes. I'm at home here. Packaging and installing software, and more broadly, configuring a computer system reliably, is a surprisingly complicated problem, and the tools in this space have to be complicated enough themselves to meet the problems in the domain. But what if we could shave off a whole layer of complexity by making our packages and installations fully deterministic? That is the value proposition of Nix.

Sounds great, sign me up! Except it's really complicated! In making these things deterministic, Nix has to jettison lots of concepts you're used to and replace them with their own home-grown versions of them. Reproducible systems have this need to strongly control the world the operate on, so they tend to invalidate lots of previous knowledge you might be bringing to the table, and you have to relearn a bunch of things in a new way.  So you really need to make the time to properly learn Nix if you want to use it.

Truth be told, I've wanted to dive into Nix for a long time. In fact, I think this is my 3rd or fourth attempt to do so. But hot damn is there a learning curve! A colleague of mine quipped, "Nix doesn't have a learning curve, it has a learning cliff!" I'd have to agree. At-a-glance, the Nix ecosystem has:

- Its own domain-specific language, eponymously called _Nix_
- A _huge_ package repository, Nixpkgs
- A Linux distribution based on Nix entirely, NixOS
- A MacOS translation/compatibility layer
- An "experimental" feature called _flakes_ that nevertheless seems to be the recommended modern way to use Nix
- Multiple module systems, including at least Flake and Nixpkgs modules
- A build system, which I think is mostly `mkDerivation`, not 100% sure yet

...and that's leaving behind a bunch of stuff like multiple installers, loads of legacy cruft, a handful forks with varying degrees of traction, community politics out the ass, a corporate sponsor with decidedly muddy incentives, and still no clear one best way to do things after 20 years of development.

So buckle up, baby! It's going to be a fun ride as we learn how to do _Nix_!

## Goals

I should start out by specifying what I'm hoping to gain from using Nix.

I want a consistent and convenient way to manage my settings across multiple systems. I use multiple devices, and I've always found it a struggle moving from one device to another effectively while keeping a relatively clean home directory. One of the more popular tools in the Nix ecosystem is [Home Manager](https://github.com/nix-community/home-manager) which aims to do exactly that. I've tried using a [dotfiles repo](https://dotfiles.github.io/) and [Chezmoi](https://www.chezmoi.io/) but something about it has never fully loaded in my head, and while it's clearly very powerful, it does ultimately leave a lot on you to write scripts and templates to handle various situations. Home Manager seems to promise a bit more, at the expense of more initial complexity, and needing a working Nix install to use it.

I also want a way to automatically manage dependencies for development. I work with a variety of different languages and tools, and having a way to specify what is needed for working with a specific environment seems like a great ability. I know you can define a Nix Flake for a given project and set up your IDE to load it when you open the project. This seems a lot more compelling to me than relying on clunky Makefile recipes, ad-hoc scripts, documentation, especially since I can easily reproduce (and therefore test) that I'm installing everything I need to work with a given project.

Lastly, I'm just interested in Nix for curiosity's sake. I want to understand how it fits in the spaces around configuring systems and doing "immutable"-type architecture work. How does it compare to imaging systems like CoreOS? What design decisions and trade-offs does it make, and what knock-on effects do those decisions have? Does it do some things so well that it makes its drawbacks worth it? If it's such a powerful tool, why hasn't it caught on in the mainstream yet? Is it just too far ahead of its time, or does it have real limitations that will prevent it from ever becoming a normal way to use a Unix or Linux system?

## Requirements

Here's laundry list of things I do that I expect Nix to support in some way for me:

- I have 2 MacOS machines: an M1 MacBook Pro and an M3 MacBook Air that I use for work and personal stuff, respectively.
- It needs to elegantly support using the [Fish shell](https://fishshell.com/) since I refuse to use a jank-and-cruft-riddled POSIX shell as my daily driver.
- Using Nix Flakes for development should be a fun and mostly transparent way to load baseline dev requirements for a project. It should not get in the way of language-ecosystem tools once loaded.
- My most commonly used tools should be easy to install and configure.
- For Linux, I have multiple concerns:
	- I use and plan arbitrarily many Linux VMs, either locally hosted or on the cloud. I want to treat these as ephemeral, and get a consistent environment on boot.
	- These are not necessarily NixOS machines, so installing Nix needs to be straightforward and fully automated. Using Nix (for Home Manager at least) shouldn't interfere with software from the distro's primary packaging system.
	- I'd like it to play nice with SELinux. I know this was a problem in the past, but I understand work has been done on this.  I'm curious to see how applicable it is now.
- Everyday workflows should not be excessively slow. This is a big concern for me with Nix, where I worry I'll just be building software all the time to do basic things like adding a package. And anecdotally, in my previous forays into Nix, I've felt like shell startup times and other things have felt less than snappy. I do wonder if this is a user error though.
- Visiting forums and chat rooms should only be necessary for novel problems. It's not that I don't want to engage with the community—the community is what makes an ecosystem like this work—but I shouldn't have to ask humans to figure out basic things.
- Finally, I'd like to _mostly_ stop thinking about Nix at some point. I know that tools like this are too fundamental to every get out of the way, but it would be nice if I only had to think about it deeply when I'm doing something weird or have a real problem I need to troubleshoot.

## Getting started

One of the big speed bumps I've run into before when trying out Nix is the sheer number of options inducing analysis paralysis. Nix is a huge ecosystem with a lot of different tools and ways-of-working, so I want an opinionated way to start out. I'm sure I'll want to change some things later, but I'd rather wade around in a suboptimal kiddie pool while learning to swim then just throwing myself in the sea and drowning straight away.

After looking around a bit, I've found what looks like a [decent starting point](https://github.com/dustinlyons/nixos-config) for what I need: it's flake-based, works on both NixOS and MacOS, and comes with instructions. It uses the [Determinate Nix](https://determinate.systems/nix/) installer, which seems to be some kinda enterprise thing that's made available for free? Well if it works, it works; I won't sweat it. Nevertheless, the instructions in the README of this config template say to _not_ install Determinate Nix itself when prompted, so I'll follow that advice. Other than that, it's very simple. It creates a partition for its store, and sets up its users.

This setup seems to basically work, and even if I don't need half the stuff if offers, I think I can peek through the config files and polish some of the tackier bits to my liking. I will say, I find it kinda off-putting that I need to run a whole build to update anything, but this seems to be kinda the norm with reproducible build systems: the system wants to manage _all the things_ and rely on robust caching to make it fast. In practice I don't think it's a huge deal, but it's certainly something I'll keep an eye on.

Anyways, after doing a few iterations of config file `nix run .#build-switch`, which is a script wrapper around `sudo darwin-rebuild switch`, I've got things mostly figured out, and I'm adding software from both Nix itself (stuff like `ripgrep` and `hugo`) and Homebrew (seems to be the best way to add GUI apps in MacOS), and I've removed that pesky custom Emacs build I don't need. Looking good. I suspect I might want to refactor how packages are handled so I can customize different systems, but lets not get too fancy just yet.

For now I'll read up on some of the theory around what I just did to try to make sense of it. Next time, I'll take a look at customizing my home environment with all things I like, including Fish shell. After that I hope I can look into using Flakes for installing per-project dependencies for development.

[^1]: Er, _one_ of my points of obsession is build systems. I'm pretty nerdy about programming languages too.

[^2]: I'm not 100% sure why they made me a maintainer. I may have contributed two patches while I was there. I mostly hung out in the project Slack dispensing bad jokes and dubious advice. Still, that was enough to learn a lot about the community around reproducible build tools.
