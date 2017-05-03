+++ 
title = "First try of git-svn on CafeMol"
description = "An example of using git-svn for CafeMol."
date = "2016-09-15T20:25:00"
categories = ["technique"]
tags = ["git", "svn"]

slug = "git-svn-CafeMol"
summary = "An example of using git-svn to organize code.  Specially for git-lovers to use svn."
+++ 

`git-svn` - Bidirectional operation between a Subversion repository and Git.[^1]

## Nonsense
---

Luckily, when I began to consider version control for my poor codes, there had
already been Git and Github.  Nearly three years ago, I used Git to organize my
thesis.  That was really nice memory during the last days of my "miserable" PhD
life (:P).

However, I had to learn and use SVN since the beginning of my postdoc career.  I
struggled to use git on my own copy of code that was pulled from the SVN
server.  I added `.svn` to `.gitignore`, carefully picked out the modifications
from `git diff`, manually applied them to the SVN repository and then `svn commit`.

Git provided `git svn` to control changes between Git and Subversion.

<br />
## Description
---

Here I used CafeMol as an example to show the basic usage of git svn.

<br />
### Pull a copy from SVN server

This is the very first step.

```bash
git svn clone http://server.name.net/svn/cafemol/trunk cafemol --username tan
```
    
Many online tutorials suggest using the option `-s` to automatically pull down
all the svn branches.  In my case I don't think it's a good idea.  For CafeMol
there are (too) many developers who have their own svn branches, which are
usually just "Medium-Rare".  Besides, `git svn clone -s` will even create git
branches for the deleted svn branches.  Therefore if you want a clean list every
time you run `git branch -a`, forget the `-s` option.

<br />
### Get a list of `gitignore` from svn repository

Simply run:

```bash
git svn show-ignore >> .git/info/exclude
```
    
<br />
### Basic usage

#### Update to the latest version

```bash
git svn rebase
```
    
#### Push local changes to svn server

```bash
git svn dcommit
```
    
#### Read logs
    
```bash
git svn log | less

# or:
git log
```
    
#### Create local branches and normal commits

These are exactly same as normal git commands:

```bash
git checkout -b develop
git add .
git commit -a
```
    
Prepare to merge changes to `master`:

```bash
git rebase master
git checkout master
git merge develop
```
    
The main reason to use `rebase` instead of `merge` is to keep simple commit
history for svn log.

<br />
### Caveats

It's always a good habit to make local changes on developing branches first, and
then merge them to master and then push to remotes.  However, `git svn dcommit`
rewrites the commit information and assign a new "git-svn-id" to commit every
time.  This results in a strange topology of branch tree: same parents will have
different names.  Thus it's recommended that after every "dcommit", one should
clean local branches to avoid possible weird behaviors.



[^1]: https://git-scm.com/docs/git-svn
