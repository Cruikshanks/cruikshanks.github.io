---
layout: post
title: "Local gems for local projects"
description: "Configuring Bundler to reference the local version of a gem"
date: 2017-06-16
permalink: blog/local-gems-for-local-projects/
---

When working if I find the same code is needed in other projects, I'll shift it to a [gem](http://guides.rubygems.org/what-is-a-gem/).

This is all well and good, but I'll sometimes find I want to make changes to project ***foo***, that depend on changes to gem ***bar***. This leaves me with 2 options

1. Update the reference in the gem file to add the argument `path`
  - **Pro** I can immediately get on with hacking the solution
  - **Con** I'm liable to forget I made the change and so will push a change that breaks the build and other peoples environments
2. Make the changes to the gem, push them and then `bundle install`
  - **Pro** the Gemfile is left untouched, removing the risk of pushing a broken version
  - **Con** I have to commit the changes, and will find that when used they are not right or are incomplete

## A third way

In the truest meaning of the "[Third way](https://en.wikipedia.org/wiki/Third_Way)", [Bundler](http://bundler.io/) does provide an option that means we don't have to make changes to the gemfile, whilst still working against a local version of the gem.

Using the **Bundler** [config](http://bundler.io/v1.3/man/bundle-config.1.html) command you can tell it when looking for gem **bar**, use my local version.

```bash
bundle config --local local.quke /Users/acruikshanks/projects/defra/quke
```

N.B. The `--local` flag scopes it to just this project.

## Strike 1!

The gem might be referencing a repo rather than [rubygems](https://rubygems.org/)

```bash
gem "quke",
  git: "https://github.com/DEFRA/quke",
  branch: "master"
```

If you only ran the `bundle config` command you'd be likely to hit an error.

```text
Local override for quke at /Users/acruikshanks/projects/defra/quke is using branch fix/make-the-tea but Gemfile specifies master
```

Basically the branch specified in the gem file doesn't match what is currently checked out in your gem project (and why would it!?)

To resolve this run `bundle config disable_local_branch true` to silence the error message.

## Strike 2!

So you do this, and think all is well. Then you start making changes to your gem code, but your project doesn't see them.

You might even run `bundle update my_gem` to try and resolve things to no avail.

There is a gotcha with using this method. It only sees changes that have been ***committed***. That's the one benefit that using `path` has over this method. But if you remember this and make use of some *WIP* commits that you'll clean up later, its easy to live with.

## Strike 3, you're outta here!

Not sold? Or simply want to switch back to using the proper source for final testing. You can reverse the change using

```bash
bundle config --delete local.pafs_core
```

## Thank you

Thank you to [Tony Headford](https://www.linkedin.com/in/tonyheadford/?ppe=1) for telling me the right combination of commands and gem file content to use (others had attempted this before but they still seemed to require messing with the gem file).

Also to [Thilo Rusche](https://twitter.com/thilorusche) for his [post](https://coderwall.com/p/tqdrhq/work-against-local-gems-without-modifying-your-gemfile), which confirmed what I was doing and highlighted why my changes couldn't be seen.
