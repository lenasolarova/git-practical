# Under the Covers

```bash
$ tree -L 1 ./.git
./.git
├── branches
├── COMMIT_EDITMSG # Text of most recent commit message
├── config         # Local configuration specific to this repo
├── description
├── FETCH_HEAD     # Info about what was fetched from remotes
├── HEAD           # Special ref that points to current ref in ./git/refs
├── hooks          # Dir containing scripts that run at specific events
├── index          # Binary representation of the index/cache/staging area
├── info           
├── logs           # Dir containing info used by reflog
├── objects        # Dir containing all "objects" (loose or packed)
├── ORIG_HEAD
├── packed-refs    # (compressed)
├── refs           # Branch, tag, remote, stash info
└── rr-cache
```

* The stuff outside the .git directory is the checked-out working directory.
* Only .git contents (expanded) exist on server: bare repo.

## COMMIT_EDITMSG

`COMMIT_EDITMSG` can be useful with the hub command to create a pull-request on github with a single command

```bash
# Create a PR against main branch of the repo in the feedhenry org
# with the contents of the most recent commit message as the description
git pull-request -b feedhenry:main -F ./.git/COMMIT_EDITMSG
```

## Git objects - what are they?

* Blob - Think of blobs as file contents (deflated) with a size & type header.
* Tree - Trees refer to blobs (and other trees), similar to how a directory points to files (and child directories).
* Commit - Commits consist of a tree, parent commits, metadata (message, author details, time, etc).
* Tag - An annotated tag is an object type that points to another object, and contains metadata such as timestamp, tag name, description, tagger and gpgsig. Most commonly refers to a commit.

### Inspecting Objects with git cat-file

```bash
# What type of object is it?
$ git cat-file -t $(git rev-parse HEAD)
commit

# What size is it (in bytes)?
$ git cat-file -s e29b7eef219e38d005b1360214322a336a56729e
1079

# Pretty-print it
$ git cat-file -p $(git rev-parse HEAD)^
tree 2320f5bb8cf059571ed69d705ba792c7939296f5
parent e67aeb6f2a1c521f62106ede0b53f3a30ce57a36
parent d4c13babe4fb19550455d96cd067a19f745d999e
author Jason Madigan <jmadigan@redhat.com> 1480413570 +0000
committer GitHub <noreply@github.com> 1480413570 +0000

Merge pull request #2 from fheng/readme-updates

README updates
```

### Crazy example with no files or porcelain commands

Porcelain provides a more user-friendly interface to the plumbing
https://git.io/v1Bt5

```bash
# Create a new branch from main & checkout
git co -b ungit main

# Make a new blob object
blob_sha1=$(echo "Let's ungit" | git hash-object -w --stdin)

# Add the blob as a file in the index
git update-index --add --cacheinfo 100644 ${blob_sha1} ungit.txt

# Make a tree object from the current index
tree_sha1=$(git write-tree)

# Make a commit object with the newly-created tree
commit_sha1=$(echo "Ungit all the things" | git commit-tree -p HEAD ${tree_sha1})

# Update the current branch with that new commit
git merge ${commit_sha1}

# WTF, where's my ungit.txt?!
ls && git status
```

## Reflog - git's safety net

Git commands that change the state of the local repo will write an entry to the reflog

```bash
git reflog # git log -g --abbrev-commit --pretty=oneline
a21f4c4 HEAD@{0}: merge a21f4c412fa194021faa859193380a26b54a024d: Fast-forward
c838b24 HEAD@{1}: merge c838b2447de706f53eaabed16c371abbb6d82b03: Fast-forward
e29b7ee HEAD@{2}: checkout: moving from cp-conflict-example to ungit
bf196ea HEAD@{3}: commit (cherry-pick): Bumping version to 2.0.8
e29b7ee HEAD@{4}: checkout: moving from cp-example to cp-conflict-example
b2d9004 HEAD@{5}: checkout: moving from main to cp-example
e29b7ee HEAD@{6}: checkout: moving from cp-example to main
b2d9004 HEAD@{7}: cherry-pick: name change
e29b7ee HEAD@{8}: checkout: moving from main to cp-example
e29b7ee HEAD@{9}: clone: from git@github.com:fheng/git-tutorial.git

# Checkout a previous state
git checkout HEAD@{2} # detached head, consider creating a branch here if important

# Reset current branch to an earlier state
git reset [--hard] HEAD@{4}
```


# Next Section
[Usefull Links](./links.md)