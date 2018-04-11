---
layout: post
title: "git diff tricks"
date: 2017-10-17 11:17:07
categories: git linux mac tips
---

## Exclude files from git diff

To remove pom.xml (or another) files from git diff output, use following command:
```bash
git diff COMMIT_TO_COMPARE  -- . ":(exclude)*/pom.xml" ":(exclude)pom.xml"
```

## Run git diff in GUI
[git difftool](https://git-scm.com/docs/git-difftool) allows you to use different diff tools to review changes in the git repo.

To see available tools run it as following:

```bash
$ git difftool --tool-help
'git difftool --tool=<tool>' may be set to one of the following:
    bc
    bc3
    emerge
    opendiff
    vimdiff
    vimdiff2
    vimdiff3

  user-defined:
    sourcetree.cmd opendiff "$LOCAL" "$REMOTE"

The following tools are valid, but not currently available:
    araxis
    codecompare
    deltawalker
    diffmerge
    diffuse
    ecmerge
    examdiff
    gvimdiff
    gvimdiff2
    gvimdiff3
    kdiff3
    kompare
    meld
    p4merge
    tkdiff
    winmerge
    xxdiff

Some of the tools listed above only work in a windowed
environment. If run in a terminal-only session, they will fail.
```

From time to time I want to use [beyond compare](https://www.scootersoftware.com/) to review the changes:

```bash
git difftool --tool=bc3
```
