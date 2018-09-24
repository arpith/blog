---
layout: post
date: 2017-04-07 13:20:00 ET
title: "San Francisco on MacOS"
---
# San Francisco on MacOS

Inspired by Booking.com and Medium, I decided to make the switch to San Francisco for feedreader.co, my open source rss feed reader.

![Left: Helvetica Neue; Right: San Francisco](https://cdn-images-1.medium.com/max/3368/1*IoMb_CLZFoRVHq4hIGWHww.png)*Left: Helvetica Neue; Right: San Francisco*

Before the upgrade, this is what [feedreader.co](http://feedreader.co) looked like:

![Helvetica Neue](https://cdn-images-1.medium.com/max/3368/1*iCLtykx2cUifivWuZFx0iQ.png)*Helvetica Neue*

Asking Safari and Chrome on macOS to render San Francisco required starting the font-family declaration with -apple-system, BlinkMacSystemFont

This meant our declaration [now looked like](https://github.com/feedreaderco/web/commit/16b7ec8eaa2a9a5b4f051a0d89d49577dc1171d6):

    font-family: -apple-system, BlinkMacSystemFont, ‘Helvetica Neue’, helvetica, sans;

And this is what [feedreader.co](https://feedreader.co) looks like with San Francisco:

![San Francisco on MacOS](https://cdn-images-1.medium.com/max/3324/1*RK4VriGl17u2AEKpqjjZeA.png)*San Francisco on MacOS*

Interested in following all your favorite websites in one place? [Sign up here](https://feedreader.co/) or just [email me](mailto:arpith@feedreader.co)!
