---
title: "Quickly create license files in GitHub"
excerpt: "Use GitHub's license templates to quickly create a well structured license file in your repo."
tags:
  - software
  - github
image:
  path: /images/generic/github-logo.png
  thumbnail: /images/generic/github-logo.png
---

Did you know you can create a well structured license file for your GitHub repo in 4 clicks? You do now!

1. Go to the root of your repo and click on the _Create new file_ button (click 1).

   ![Create a new file](/images/create-github-license/create-new-file.png)

1. On the next screen type in `LICENSE`, a button saying _Choose a license template_ should pop up, click it (click 2).

   ![Write in "LICENSE"](/images/create-github-license/file-name-template.png)

1. You'll be brought to a screen that allows you to pick from many licenses. I stick with Apache, click _Apache 2.0_ and click _Review and submit_ (clicks 3 and 4).

   ![Choose your template](/images/create-github-license/choose-template.png)

1. At this point a new commit will be staged for you, if you have write permission you can choose to merge to master directly or create a pull request and merge it that way.

   ![Merge it](/images/create-github-license/new-pr.png)

That's it, you're done, all in 4 clicks!

> Okay, maybe I didn't mention you have to punch in 7 characters too, but that wouldn't make a catchy title.

## Thoughts

* This also works for Code of Conducts, just type in `CODE_OF_CONDUCT.md` instead of `LICENSE`.
* Works if you don't have write permission to the repo too, GitHub will automatically fork the repo to your account.
