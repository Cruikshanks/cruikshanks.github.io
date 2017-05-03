---
layout: post
title: "Jekyll and GitHub pages oh my!"
description: "What I learnt building my site using Jekyll and GitHub pages"
date: 2017-04-15
permalink: blog/jekyll-and-github-pages-oh-my/
---

So it was always on the to-do list to sort out my site, and recently I embarked on a venture (more about that in future posts) which involved me sending someone a link and them ending up on said site.

Only it wasn't up, and to be honest if it was there wouldn't have been anything to see. I'd already been pondering posting my lessons learnt rather than just squirreling them away in [Evernote](https://evernote.com/), so this felt like the time to pull my finger out and fix it.

I'd played with [Jekyll](http://jekyllrb.com/) as part of some work stuff, and knew about [GitHub Pages](https://pages.github.com/) so figured this would be a good opportunity to get to grips with both. Plus it seemed a low friction way to maintain my personal web site in the future.

## It's for newbs, right?

Or certainly that's what the GitHub Pages [intro page](https://pages.github.com/) made me believe. I started by following the steps provided and yep, I had my `hello world` version up and running in no time.

But I knew I wanted the ability to create and run stuff locally, and like, presumed Jekyll had to feature somewhere so moved onto the next step of [setting up Jekyll](https://jekyllrb.com/docs/quickstart/) (this is linked to from the GitHub Pages site).

So got that all running, pushed it up and now I had an out of the box Jekyll site accessible from <http://cruikshanks.github.io>.

## Themes

Well that's nice, but the first thing I wanted to do was select a different theme. And from what [I was reading](https://help.github.com/articles/adding-a-jekyll-theme-to-your-github-pages-site/) that just seemed to require a tweak to the `_config.yml` file.

But whenever I did this it just caused a mass of errors. Plus the docs weren't quite lining up with how I'd created my site.

Did I need to do this in my [GitHub settings](https://help.github.com/articles/adding-a-jekyll-theme-to-your-github-pages-site/#adding-a-jekyll-theme-to-your-site) as well? Or was I supposed to be [adding the theme](https://help.github.com/articles/adding-a-jekyll-theme-to-your-github-pages-site/#adding-your-theme-as-a-gem-to-your-gemfile) as a gem to my Gemfile?

Whatever I was trying my stuff simply broke the moment I touched it. Clearly I was missing something, but I couldn't tell if it was my ignorance or existing knowledge that was getting in the way.

## Some googling later

After some googling and experimentation I realised the following

- If you want the simplest, out of the box GitHub Pages solution, then create your `my-org-name.github.io` project, go to settings, select your theme, and start adding stuff via the GitHub web UI
- If you want a supported Jekyll solution, so you can run the site locally to view changes before pushing them then find the theme you're interested in and fork the project
- The latest version of Jekyll themes are managed via the `_config.yml` and `Gemfile` using gems, but this is [not supported in GitHub Pages](https://jekyllrb.com/docs/themes/#installing-a-theme)
- There are some nice themes out there (I started looking into [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes)) but if you have no clue what you are doing, changing anything just seems to lead to lots of errors.

## Ta-da!

Then I came across the [GitHub Pages page](https://jekyllrb.com/docs/github-pages/) in the Jekyll docs and it led me to [Creating and Hosting a Personal Site on GitHub](http://jmcglone.com/guides/github-pages/), a *marvellous guide* in Jekyll's own words.

Rather than generating a project with Jekyll or forking an existing one from GitHub, the guide has you create a project from scratch.

This helped cement how layouts work and how they link with the actual content, what [front matter](https://jekyllrb.com/docs/frontmatter/) does and essentially how a Jekyll site is structured. I could then combine this with lessons learnt during my experimentation to get a desired result. A GitHub pages compatible Jekyll based web site that I could deploy and run locally, which *I understand*.

After feeling like a dunce trying to get this up and running that last point was critical.

## Small steps

So [what I'm left with](https://github.com/Cruikshanks/cruikshanks.github.io) is a very basic web site which does very little at the moment, and which has a tonne of ways it could be improved. But its simple, easy to maintain and something I can build on going forward.

I can now get on with recording what I've learnt as posts, if only to prove to anyone watching that I do care about my work. If they happen to be of help; all the better!
