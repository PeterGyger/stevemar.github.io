---
title: "Contributing to Open Source projects: Googliser"
excerpt: "Adding a new feature to a slick CLI tool -- JFDI"
tags:
  - software
image:
  path: /images/contrib-to-os-googliser/opensource.png
  thumbnail: /images/contrib-to-os-googliser/opensource.png
---

*Background image from: [https://opensource.org](https://opensource.org)*

Open source projects don’t all have to be massive endeavours like [Kubernetes](https://github.com/kubernetes/), [OpenStack](http://opendev.org/openstack), or [Android](https://android.googlesource.com/). It’s great that these initiatives were open sourced, that vendors have been able to play nice, and they’ve become a standard. But the reality is, as a developer, you are far more likely to use a smaller open source library in your day-to-day work that are maintained by a few passionate folks, like [Homebrew](https://github.com/Homebrew) or [python-requests](https://3.python-requests.org/) or [markdownlint](https://github.com/DavidAnson/markdownlint).

> There are a bunch of blog posts about contributing to open source. I really appreciated Ian Stapleton Cordasco’s words from his [blog post](https://blog.ian.stapletoncordas.co/2019/02/open-sources-affects-on-a-career.html).

Back to the story. A colleague at work mentioned he found a cool project called [Googliser](https://github.com/teracow/googliser/) to download images from Google’s Image Search. He needed images to build visual recognition models. Trouble was, Googliser didn’t have a feature he wanted, specifically to filter by license, so he avoided using it. Womp womp.

I looked at the code for the project, realized it was just a bunch of bash, and said something along the lines of “Why don’t you just add that feature?”. He was confused, not having ever contributed to open source projects he didn’t know where to start (my first dive into the world of open source was back in 2012).

So I went home early that day and whipped up a pull request to add the feature he was looking for. You can see [the pull request](https://github.com/teracow/googliser/pull/19) it in all its glory.

![googliser-pr](/images/contrib-to-os-googliser/googliser-pr.png)

> Pro tip: As Colleen Murphy has said many times over, commit messages matter, so make them good! [Watch the video](https://www.youtube.com/watch?v=pU-VasVPNAs)

So why am I writing this? I’m hoping that you are encouraged by this post to pick a small open source project you use on a day to day basis and contribute back to it. Submit a small PR, take a look at the code, open an issue (heck, even if it’s to just say thank you!). Github makes all of this incredibly easy, go and put yourself out there. Most maintainers are appreciative of the feedback, you’ll be changing someone’s day for the better!

And, plus, the kudos are always cool.

![googliser-thanks](/images/contrib-to-os-googliser/googliser-thanks.png)
