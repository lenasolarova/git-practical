# Conflicts

## What are conflicts, and how do they happen?

Conflicts in git occur when trying to integrate changes from two different
states, where different changes are made in one place. The most common case is
when performing a *merge* of one branch into another, where there's a change to
a particular line in a particular file on both sides of the merge.

Git doesn't know what to do automatically, so it just stops in the middle of
the action, and asks you to intervene.

## Practical One

First, let's make a new branch and add a file:

``` bash
git checkout -b left main

echo "Left is the best" > conflict.txt

git add conflict.txt
git commit -m "Left is the best"
```

Next, for the purposes of demonstration, let's create another branch, 
called `right`:

```bash
git checkout -b right main

echo "Right is the best" > conflict.txt
git add conflict.txt
git commit -m "Right is the best"
```

We've made changes here in the same file, in two separate 
branches. If we try to integrate these branches with a merge, 
we're going to see a conflict. Let's merge `the-left` into 
`the-right` to see a conflict:

```bash
git merge left
```

We now are told that there has been a conflict while perfoming the merge. Git
has stopped the merge process and asked us to resolve the conflict before
continuing. We can check the status of our repo to see the unmerged paths and
instructions on how to resolve the conflict.

```bash
git status
```

Let's open 'conflict.txt' in an editor and go through how to resolve a
conflict. This is the content of our file:

```
<<<<<<< HEAD
Right is the best
=======
Left is the best
>>>>>>> left
```

What are the components of this block? This block is saying that the version of
the change in HEAD (right branch) is above the '=======' and everything below
it is the change in the branch named 'left'. We can choose to keep one of these
changes, keep both, or change the content to something different.

Let's update the contents of the file to:

```
Right and Left are both equally good
```

With our conflict manually resolved, we need to `git add`:

```
# Stage our change and mark conflict as resolved
git add conflict.txt
```

Lets check our `git status` once more.

```bash
git status
```

It tells us that all the conflicts have been resolved, but we are still in the
merging process. Finally, commit the integrated change. This will prompt for a
commit message. Leaving the default message is fine.

```bash
git commit
```

## Diving deeper

#### Aborting a merge

We can abort a merge that's in progress by using the `git merge --abort`
command.

##### Minimising merge conflicts

To minimise merge conflicts keep commits small and integrate changes often.

# Next Section
[Recap](./05-rundown.md)

