---
layout: post
title: "Hey browser, these guys are with me"
date: 2017-04-27
permalink: blog/hey-browser-these-guys-are-with-me/
---

This covers something I learnt when, having joined a project late I was asked to pick up some outstanding actions from their recent [PEN test](https://en.wikipedia.org/wiki/Penetration_test). Specifically the test had highlighted that the service was missing a [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

## A what now?

A CSP is delivered via a HTTP response header, and defines approved sources of content that the browser may load. It's seen as an effective countermeasure to Cross Site Scripting (XSS) attacks, is widely supported and usually easy to deploy.

When your browser loads a page it also loads a bunch of other assets along with it; images, fonts, styles, and JavaScript. It does this because we’ve told it to do so via things like our script tags.

The browser however doesn’t differentiate. So if a hacker, via some content they have contributed (a comment is the classic example), manages to inject a properly formed script tag into the page the browser will load it along with everything else.

So a CSP is designed to prevent this by essentially adopting the idea of a white list. The policy sets out what are the valid sources for the various types of assets your page may load, so the browser ***can*** differentiate.

## Getting it in there

The project already had code for adding custom headers, specifically to manage caching. This was done within the application controller (oh, and its a [Rails](http://rubyonrails.org/) project by the way) using a [before_action](http://guides.rubyonrails.org/action_controller_overview.html#filters) filter to call a method that did something like this.

```ruby
response.headers["Cache-Control"] = "no-cache, no-store, max-age=0, must-revalidate, private"
```

So the initial implementation simply added something along the lines off

```ruby
response
  .headers["Content-Security-Policy"] = "default-src 'self'; "\
                                        "script-src 'self' 'unsafe-inline'; "\
                                        "font-src 'self' data:; "\
                                        "report-uri https://mycustomname.report-uri.io/r/default/csp/enforce"
```

Specifically we are saying

- scripts can only come from the service, however we permit [unsafe-inline](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src#Unsafe_inline_script) scripts
- fonts can only come from the service, or via the [data: scheme](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) (a `data: uri` is a way to embed small files inline)
- send any violation reports to a [service we have setup](https://report-uri.io/) for this CSP
- for all other items their source must be the service (`default-src`)

## All done?

Not quite! For *reasons* we spotted that this didn't cover assets we were bringing in to support [Google Analytics](https://analytics.google.com). Also one of those assets was an inline JS script, which our current use of `unsafe-inline` covered. But this was intended to only be temporary until an issue in a dependency was resolved and we could switch it off. So ideally we wanted to also be able to support [nonces](https://scotthelme.co.uk/csp-cheat-sheet/#nonces). Basically these are one time values generated on the server and added to the script tag, but also into the CSP. The browser can then cross check them, see they match and therefore permit the specified inline-script to run.

At this point the temptation is start rolling your own solution but a quick search found one already existed; enter the [Secure Headers gem](https://github.com/twitter/secureheaders).

Not only did this come with support for nonces, but it also highlighted a bunch of other headers we really should be thinking of using.

To implement add a file to `config/initializers` (we called it `secure_headers.rb`). Then configure it using something like this

```ruby
require `secure_headers`

SecureHeaders::Configuration.default do |config|
  config.csp = {
    default_src: %w('self'),
    font_src: %w('self' data:),
    img_src: %w('self' www.google-analytics.com),
    object_src: %w('self'),
    script_src: %w('self' 'unsafe-inline' www.googletagmanager.com www.google-analytics.com),
    style_src: %w('self'),
    report_uri: %w(https://mycustomname.report-uri.io/r/default/csp/enforce)
  }
end
```

So now we're saying

- All external scripts must be sourced from either the service, googletagmanager.com, or google-analytics.com, plus inline scripts are permitted to run
- All images must be sourced from either the service or google-analytics.com (GA because it sends the data to the Analytics server via a list of parameters attached to a single-pixel image request)
- All fonts must be sourced either from the service or via a `data: scheme`
- Browsers should report any violations of this policy to the [service we have setup](https://report-uri.io/) for this CSP
- For everything else default to requiring the source to be the service itself

(By the way, big thank you to [Foundeo Inc](https://foundeo.com/) for putting up an [faq post on CSP that covered Google Analytics](https://content-security-policy.com/faq/)).

## Further reading and references

There are loads of good doc’s on the specifics of how to define a security policy.

- I found **Scott Helme’s** CSP cheat sheet very helpful <https://scotthelme.co.uk/csp-cheat-sheet/>
- Plus his associated blog post <https://scotthelme.co.uk/content-security-policy-an-introduction/>
- For an actual reference I used Mozilla's documents <https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP>

To really road test your site I'd also highly recommend **Scott's** online tool [securityheaders.io](https://securityheaders.io/). If you're like me you'll run this tool and then immediately want to aim for an **A+**!

We also ended up using his CSP violation reporting tool [report-uri.io](https://report-uri.io/) as a simple to use, and most importantly ***free*** solution for capturing any violations.
