---
title: "Move a repo from GitHub Enterprise to GitHub"
excerpt: "Using bash to move open source an internal project on GitHub Enterprise"
tags: 
  - software
image:
  path: /images/git-banner.png
  thumbnail: /images/git-banner.png
---

Occaisionally internal projects are open sourced. We have GitHub Enterprise (GHE) internally, and then host open source projects on Github.com/IBM. The quickest way I've figured out to open source the code is to create a new repo on public github and run a quick and dirty script (aptly named `mover.sh`) that allows me to specify the org and repo on GHE. Note, that using the following approach *will copy the entire commit history*. Be careful you don't have any secrets or passphrases in there!

How I usually run the script:

```bash
$ ./mover.sh internal-user repo-name
```

The repo is the cloned, remotes are updated, and it is pushed back to the new repo. The only bit of manual work is to create the repo in the first place. More on that soon! *Foreshadowing!*

## `mover.sh`

Nothing fancy, it meets my needs, feel free to re-use it. It's also available on my [Github](https://github.com/stevemar/junk_drawer/blob/master/mover.sh).

```bash
#!/bin/bash

# this moves content from GHE to GH (IBM)
# the first arg is the org name
# the second arg is the repo name, which will be used for the new repo as well

read -p "Did you forget to create the repo on github.com/IBM? " -n 1 -r
echo    # (optional) move to a new line
if [[ ! $REPLY =~ ^[Yn]$ ]]
then
    [[ "$0" = "$BASH_SOURCE" ]] && exit 1 || return 1 # handle exits from shell or function but don't exit interactive shell
fi

org=$1
repo=$2

## clone the repo to be moved and cd into it
git clone git@github.ibm.com:$1/$2.git
cd $2

## rename the origin branch to something else to avoid conflicts
git remote rename origin destination

## go to github and create an empty repo, add the new repo location
git remote add origin https://github.com/IBM/$2.git

## push code up to new remote branch
git push -u origin master

## note that if using the below command with 2FA, you will need to
## use a personal access token as a password along with your username!
```

## Thoughts

There are plenty of other ways or similar approach to solve this problem, check out these posts:

* [Git Mover (Python)](https://github.com/ahadik/git_mover)
* [Git Mirror](https://help.github.com/enterprise/2.2/admin/articles/moving-a-repository-from-github-com-to-github-enterprise)
* [Gist with similar approach](https://gist.github.com/niksumeiko/8972566)
