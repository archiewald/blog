---
layout: post
title: "1ppm#5 learngitbranching.js.org"
date: 2018-06-16 8:00:00 +0100
categories: 1ppm
comments: true
---

For the 5th 1-project-per-month-challenge I decided to clean up my knowledge about git and also expand it with some helpful techniques. I never got a moment to dive into some course before I actually started using it. For most time using git was pretty straightforward. When it came to collaboration with few colleagues on same codebase it got trickier. 

On our work-team slack I found recommendation that [learngitbranching.js.org](https://learngitbranching.js.org/) is worth to give a try to understand somer git techniques better. So I did! And I can totally recommend to go carefully through all the challenges, you can also launch the app in playground mode which can be very useful to test some approaches or present your ideas to others.

Below you can find short explanation on most useful git commands/techniques I learned and use in day-to-day work:

#### __cherry-pick__

With this command you get a possibility to pick whatever commit you like and add it on the end of your branch. Very useful and simply to understand command. Say you need some functionality your colleague implemented on another branch but you don't want to merge the whole branch, just pick the cherry-thing :) Then get the id of the commit or more and type:

```
git cherry-pick <Commit1> <Commit2> <...>
```

#### __reverting changes__

Such an important stuff to learn. You made a mistake, forgot to switch to another branch, pushed something you didn't mean to... WHAT DO??!!! take it easy, most of the time you can easily __undo__, just need to learn the tools.

Say you just commited some changes in your local repo and suddenly regret it. You didn't name your commit correctly? You would like to include some more changes in this commit? Maybe it appeared it doesn't work? Easy thing you can do is

```
git reset --soft HEAD~1
```

This will delete you last commit thanks to `HEAD~1` but leave your changes with `--soft` flag. You can edit your changes and commit again. Your working tree will remain nice and clean :)

If you already pushed your commit to the repo and there is a possibility that some of your colleagues pulled it to his local project, `git reset` is not your friend. Since it will just delete the commit, it might bring inconsistency in shared code. Git itself will make sure you've been warned - you will need to  use `--force` flag if you decide to push your changes with deleted commit.

What you would rather do in this case is to push a commit which brings changes exactly reverted comparing to the one you want to delete. So if there is exactly one commit you want to revert, use

```
git revert HEAD~1
```

It will create a new commit with reverted changes - so the code itself will look the same as before you commit the faulty one.

#### __git rebase__

Basically you have 2 ways combining work between branches - `git merge` and `git rebase`. Git merge is usually the first choice but it is worth to know how to use git rebase as well.

After learngitbranching.org.js

> Rebasing essentially takes a set of commits, "copies" them, and plops them down somewhere else.
>
> While this sounds confusing, the advantage of rebasing is that it can be used to make a nice linear sequence of commits. The commit log / history of the repository will be a lot cleaner if only rebasing is allowed.

I find particullary useful to use git rebase with interactive mode with `-i` flag. It happens that after some actions done on your feature branch, it includes changes you would not like to in pull request and it gets very messy.

So if you would like to clean your `feature-branch => master` pull request, simply run 

```
git checkout feature-branch
git rebase -i master
```

This will open your text editor with something like below (proudly copied from [here](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History))

```
pick f7f3f6d changed my name a bit
pick 310154e updated README formatting and added blame
pick a5f4a0d added cat-file

# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

You can simply edit accordingly and clean up your branch. Of course you need to be careful - be sure that no one else is working on this branch anymore, since if you delete some commits you get the same effect as discussed within `git reset` section above. Don't be supprised that you need to push with `-force` flag again :)