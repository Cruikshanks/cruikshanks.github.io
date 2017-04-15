# My personal website

[![Build Status](https://travis-ci.org/Cruikshanks/cruikshanks.github.io.svg?branch=master)](https://travis-ci.org/Cruikshanks/cruikshanks.github.io)

I've moved to using [GitHub Pages](https://pages.github.com/) to host my personal site, backed by [Jekyll](https://jekyllrb.com/).

I have only just set this up so expect numerous changes in the coming days.

## Installation

First clone the repository

```bash
git clone https://github.com/Cruikshanks/cruikshanks.github.io.git && cd cruikshanks.github.io
```

Then install the dependencies

```bash
bundle install
```

## Execution

To start the app use

```bash
bundle exec jekyll server
```

This should output something like the following

```bash
Configuration file: /Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io/_config.yml
Configuration file: /Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io/_config.yml
            Source: /Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io
       Destination: /Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.397 seconds.
 Auto-regeneration: enabled for '/Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io'
Configuration file: /Users/acruikshanks/projects/cruikshanks/cruikshanks.github.io/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

## Adding new posts

To add a new post create a file in the `_posts` directory with a filename formatted the following way `yyy-MM-dd-name-of-post.md`.

Then a the top of the file add the following front matter

```jekyll
---
layout: post
title: "The title of my post"
date: 2017-03-30
---
```

A link to the post will be automatically added to the index.html page.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/cruikshanks/cruikshanks.github.io.

## License

This code is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT). The content of all posts are licensed under the [Creative commons license](http://creativecommons.org/licenses/by/4.0/).

> If you don't add a license it's neither free or open!
