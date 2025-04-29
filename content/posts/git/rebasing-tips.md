+++
title = 'How To Use Git Rebasing Effectively As A Beginner'
date = 2025-04-29T17:27:49+05:30
draft = false
tags = ["git", "git-rebase", "tips", "docs"]
summary = "Introduction into the git rebase feature, explains how to make changes to branches and commits"
+++

You might feel the things you are doing are just repetive and functional (_but old!_), after you get familiar using `git` regularly. You will face a situation where you need to make some specific adjustements to the commits or something similar. It is doable but more manual, you'd wish if there's a feature that can accomplish what you want to do now.

Among all that you have wished, the **`rebase`** is one of them!

# **What is `rebase`?**
From my understanding I can't mention a specific definition for this, but let's keep this simple and let me phrase that in my own sentence.

It does exactly as it sounds, because it picks the **current branch(`A`)** and places it on top of the **specified branch(`B`)**. 
> _i.e, All the changed that were made inside the `A` branch will be stacked on top of the branch `B`._

Apart from that, we can do so many things by using `rebase`,
- `git pull --rebase upstream main` - To pull the upstream branch to local by rebasing local branch on top of the upstream branch. (i.e, _local branch changes will be stacked on top of the incoming branch_)
- `git rebase -i HEAD~8` - To make changes to commit you have made, such as `squash`, `edit`, `pick`...

### Make Changes to the Commits by `rebase`

Determine the number of commits you need to process first,
```sh
git log --oneline --decorate
```
```sh
5bb8375 (HEAD -> main, orign/main, orign/HEAD, restic) posts: published jrnl along with featured image
ee0dcb2 (orign/restic) revamp: major changes made to title and tags were added based on the post
a3efcb5 posts: added restic backup with a feature image
2ae860b update: added feature image for git usage post
1b5cef8 update: image feature image added for omv post
21aaf5a update: publishing the omv post
```
It displays the commit in short format, and determine the amount of commit that needs to be processed.

We need to remove the commit and it's changes on "`a3efcb5 posts: added restic backup with a feature image`" from the history. So let's rebase only `3` three commits (top to bottom), remove the commit from the line and save the changes.

**Rebase**:
```sh
git rebase -i HEAD~3
```

```diff
5bb8375 posts: published jrnl along with featured image
ee0dcb2 revamp: major changes made to title and tags were added based on the post
- a3efcb5 posts: added restic backup with a feature image
```
```sh
Successfully rebased and updated refs/heads/main.
```

### Make Changes Across All Commits
We need to remove a **directory/file** from **all/specific** commits, and let's *remove a file named `hugo.yaml`*.
```sh
git log --oneline --decorate
```
```sh
a3efcb5 posts: added restic backup with a feature image
2ae860b update: added feature image for git usage post
1b5cef8 update: image feature image added for omv post
```
We'll be removing `hugo.yaml` from the last 2 commits.

**Rebase into each commit, make changes & save**
```sh
git rebase -i HEAD~3
```
```diff
pick a3efcb5 posts: added restic backup with a feature image
- pick 2ae860b update: added feature image for git usage post
- pick 1b5cef8 update: image feature image added for omv post
+ edit 2ae860b update: added feature image for git usage post
+ edit 1b5cef8 update: image feature image added for omv post
```
Replace **`pick`** with **`edit`** to the commits you want to make changes. Save the changes after modifying it.

```sh
Stopped at 2ae860b...  update: added feature image for git usage post
You can amend the commit now, with

git commit --amend 

Once you are satisfied with your changes, run

git rebase --continue
```

- We are dropped at the commit `2ae860b` (_as a temporary branch_).
- Remove the `file/folder` you want and `git commit --amend`.
- Continue rebasing to the next commit `1b5cef8` using `git rebase --continue`.
- Repeat this step, until you finish all the `edit`.
