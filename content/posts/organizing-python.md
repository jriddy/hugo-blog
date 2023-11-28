---
title: "Organizing Python"
date: 2023-11-28T07:35:21-05:00
draft: true
---

A major problem I've seen with larger Python projects is very poor organization.

For smaller projects, most projects either simply follow the guidelines of a given framework if one
is in use, or else developers fall back to a basic attempt to organize by theme.  There's seldom
any guiding principles that would direct a particular pattern of organization, and the only thing
that really pushes developers to create new modules is the sense that we don't want individual
modules too big.  But code organization can actually affect real engineering outcomes.

## Why organization matters

You might think that focusing on code layout and organization is just idle bikeshedding.  But it can have major effects on things:

1. Clear API and contract boundaries
1. Import time
1. Developer productivity
1. Flexibility of use

### API

The most obvious reason to organize your code well is to help _communicate the structure of your
API_ to collaborators and client developers.  A guiding principle here is that you should avoid
breaking your API.  Usually we think of this as something that applies to redistributable
libraries, but this can go as much for internal library code just as much as for public libraries.

A pattern I see really often is
