---
title: "Cleaning up multiple local git folders"
excerpt: "Using bash to tidy up multiple git projects all at once"
tags: 
  - software
image:
  path: /images/git-banner.png
  thumbnail: /images/git-banner.png
---

When working on a new software project cloning multiple repos is par for the course. On top of this, I'm a bit of clean freak in both my living space and my virtual spaces. I like a zero-inbox, my shirts are organized by season, it's a thing. I also love it when my local repos are as up to date as possible. Cleanliness aside, it just makes sense, since if you want to start a new branch you want to start at the newest state.

Rather than manually going into each directory and pulling down the latest manually, I made a short script (aptly named `cleaner.sh`) that I keep in the root of the workspace. The script does what I mentioned earlier and then some.

Here's the gist of what I want to accomplish for each repo:

* Checkout master
* Fetch new tags/branches
* Delete all non-master (local) branches
* Pull down the latest

Where I usually place the script:

```bash
$ ls
repo-foo    repo-bar    repo-baz    cleaner.sh
```

## `cleaner.sh`

Nothing fancy, it meets my needs, feel free to re-use it. It's also available on my [Github](https://github.com/stevemart/junk_drawer/blob/master/workspace_cleaner.sh).

```bash
#!/bin/bash

# save current directory so we can go back after it's run
previous_dir=$(pwd)

# accept a path argument, default to current dir if not supplied
path=$1
if [[ -z "$path" ]]; then
    echo "no path specified, using current directory"
    path=$(pwd)
fi

# output the folders we will clean
cd $path
folders=$(ls)
echo "cleaning folder:"
echo $folders

# for each folder, do a few checks...
for dir in */; do
    cd $dir
    echo "cleaning folder $dir"

    # check if .git exists, it could just be a folder
    if [ -e ".git" ]; then
        git checkout -f master

        # check if there are any non-master branches to delete
        if [[ $(git branch | grep -v "master") ]]; then
            git branch | grep -v "master" | xargs git branch -D
        fi
        git fetch
        git pull origin master
    else
        echo "skipping $dir as it's not a git project"
    fi
    cd ..
done

# go back home
cd $previous_dir
```

## Thoughts

I've been using this for a while and finally thought I should share it. I hope others find it useful and can modify it to meet their needs :)
