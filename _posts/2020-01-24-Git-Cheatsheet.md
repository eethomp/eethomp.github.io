---
layout: post
title:  "Git cheat sheet"
---

Everyone has a git cheatsheet. Here's mine to get out of a branch and kill it. I work with a developer who codes at approximately light speed and I'm a Rails beginner. If I start on something and it sits for a day or two it sometimes isn't worth the effort to merge.

List branches
```
> git branch

* 15
  develop
```
The command opens a list in less. The * indicates which branch you are in. Here I have branch 15 and develop. Hit q to close the list. To remove a branch you have to leave it first.

```
> git checkout develop
> git branch -d 15
```

Git is kind and tries to keep you from doing stupid things. In this case there is so much badness in my branch I want it gone.
```
error: The branch '15' is not fully merged.
If you are sure you want to delete it, run 'git branch -D 15'.

❯ git branch -D 15
Deleted branch 15 (was c1a9f5c).
```
Now update your develop branch.

```
> git pull
```
