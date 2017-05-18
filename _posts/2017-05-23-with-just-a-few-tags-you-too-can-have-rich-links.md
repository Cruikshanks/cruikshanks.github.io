---
layout: post
title: "With just a few tags you too can have rich links"
description: "How to get a fancy box like this when you add a link to your site in a post"
date: 2017-05-23
permalink: blog/with-just-a-few-tags-you-too-can-have-rich-links/
---

A key collaboration tool at work is [Slack](https://slack.com/), and for those times when left with idle hands (queuing, kettle boiling, hiding in the toilet from the kids) I'm checking [Twitter](https://twitter.com/AlanCruikshanks). What they have in common is when you add a link to your message/tweet they present it in a nice box.

<img src="/assets/images/rich_text_slack_example.png" alt="Slack example of a rich preview" width="800"><br>
*Example from Slack*

<img src="/assets/images/rich_text_twitter_example.png" alt="Twitter example of a rich preview" width="800"><br>
*Example from Twitter*

Ignorantly (and ashamedly) I just assumed that each of these apps, and any others that do something like this automatically generate them from the link you provide. So when I came to start creating my own stuff there was nothing I needed to do.

## Pity the fool!

Imagine my disappointment when proudly tweeting a link to my first post, I found the link just hung there in its vanilla, naked pale blue ugliness.

[Plonker](http://dictionary.cambridge.org/dictionary/english/plonker), of course I need to do something at my end!

So just in case you're as ignorant as me, this post will cover how I added rich preview support to my site.

## It's meta

Props to [Richard Oosterhof](https://twitter.com/r_oosterhof) and his post [How to optimize your site for Rich Previews](https://medium.com/@richardoosterhof/how-to-optimize-your-site-for-rich-previews-527ed13a6d69) for putting me on the right track.

You basically need to add a bunch of [meta](https://en.wikipedia.org/wiki/Meta_element) tags to the `<head>` section of your page. **Richard's** post talks about the following tags

- `title`
- `description`
- `og:title`
- `og:description`
- `og:image`

He also states your site should have a [Favicon](https://en.wikipedia.org/wiki/Favicon) (so that was actually the first thing I fixed using [RealFaviconGenerator](http://realfavicongenerator.net/)).

So what exactly was the syntax? Well I'd seen people post enough [Medium](https://medium.com/) articles like **Richard's** to know it worked for them, so I just pinched what I found using 'view source'.

```html
<meta name="title" content="How to optimize your site for Rich Previews – Richard Oosterhof – Medium" />
<meta name="description" content="You probably have seen them already, when you send a website link in chat apps…" />
<meta property="og:title" content="How to optimize your site for Rich Previews – Richard Oosterhof – Medium" />
<meta property="og:description" content="You probably have seen them already, when you send a website link in chat apps…" />
<meta property="og:image" content="https://cdn-images-1.medium.com/max/1200/1*VDDZ-GgOIHnd4Au4ncYlMg.png" />
```

All this stuff goes into the `<head>`, and **Richard** has even built a handy tool to check you've got it setup correctly at [richpreview.com](http://richpreview.com/).

Thanks **Richard**!

## But I'm different

That's great if you're hand cranking each static page, but no good for me, as I'm using [Jekyll](http://jekyllrb.com) to generate my pages from [Markdown](https://en.wikipedia.org/wiki/Markdown) documents.

However the implementation didn't require major changes. The site uses a default [layout](http://jekyllrb.com/docs/templates/) for all pages, so the place to put the tags was [it](https://github.com/Cruikshanks/cruikshanks.github.io/blob/master/_layouts/default.html).

As my post pages don't feature unique images I chose instead to go with a single default image I'd use for all pages. With that I could simply hard code the url to the image in the `og:image` tag.

`title` and `description` needed to come from each page though. **Jekyll's** concept of [front matter](http://jekyllrb.com/docs/frontmatter/) already provided `title`, so next I needed to go back through all my pages and add a new property for `description`.

So the final bit was to pull `title` and `description` from each page. As **Jekyll** uses the [Liquid](https://shopify.github.io/liquid/) templating language this just meant inserting `{% raw %}{{ page.title }}{% endraw %}` into the relevant tags. So the final implementation was

```html
<meta name="title" content="{% raw %}{{ page.title }}{% endraw %}" />
<meta name="description" content="{% raw %}{{ page.description }}{% endraw %}" />
<meta property="og:title" content="{% raw %}{{ page.title }}{% endraw %}" />
<meta property="og:description" content="{% raw %}{{ page.description }}{% endraw %}" />
<meta property="og:image" content="http://cruikshanks.co.uk/assets/images/default_rich_preview.png" />
```

## That it?

Not quite. I then got curious so Googled what an **og:** meta tag was and came across <http://ogp.me/>. Turns out adding these tags turns your web page into a graph object, and that enables it to _become a rich object in a social graph_.

I'll leave you to read further on that, suffice to say what I'd added didn't meet the minimum requirements for **Open graph**, plus there were some optional tags that might be relevant.

So I expanded the tags to include a few more.

```html
<meta property="og:type" content="website" />
<meta property="og:url" content="http://cruikshanks.co.uk/{% raw %}{{ page.permalink }}{% endraw %}" />
<meta property="og:site_name" content="cruikshanks.co.uk" />
<meta property="og:locale" content="en_GB" />
```

Again you can see how I used **Liquid** to grab a front matter property to build the url dynamically for each page.

## Now are we done?

Almost! Seeing as generally I intended to use [Twitter](https://twitter.com/AlanCruikshanks) to post about my links I thought it worth while to also do some Googling for rich preview support in **Twitter**.

And lo, there be documents and a bit more to do. **Twitter** calls them [Summary](https://dev.twitter.com/cards/types/summary) cards and on initial reading appears to rely on **Twitter** specific tags, for example `<meta name="twitter:title" content="My title" />`. However further research turned up [Cards Markup Tag Reference](https://dev.twitter.com/cards/markup).

This showed that where a **Twitter** meta tag did not exist, it would fall back on **Open graph**. I also found a handy [card validator](https://cards-dev.twitter.com/validator) so you can check that your pages meet the requirements for **Twitter** to display a summary card.

So the final set of tags I added to my [default layout](https://github.com/Cruikshanks/cruikshanks.github.io/blob/master/_layouts/default.html) were

```html
<meta name="twitter:site" content="@alancruikshanks" />
<meta name="twitter:image:alt" content="Avatar for Alan Cruikshanks" />
```

`twitter:site` is required by **Twitter**, and `twitter:image:alt` allows me to provide alt text for my image, something I'll always take advantage of.

## Mic drop

That's it for me and this site at least. It may well be if I was to post links in other apps I wouldn't get a preview, but I'll tackle those as I find them. For now, I'm **Open graph** compliant and **Twitter** likes me, so I shall leave it there.
