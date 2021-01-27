---
layout: post
title: "Gerrit code review tool: git-review"
author: "Borting"
categories: journal
tags: [Gerrit,Git]
image: plum.jpg
---

[git-review](https://opendev.org/opendev/git-review) is a command line tool helps submit code to Gerrit for review.

# Installation

You need to install python (python3 is preferred) in advance.
It is also suggested to install git-review with version > 1.28.0.
```bash
# Install pip
$ sudo apt-get install python3-pip
$ sudo easy_install pip

# Install git-review
$ sudo pip install git-review
```

# Git Repos Configuration

## Configure git-review

Enter the root dir of git repos.
```bash
# Default name use for git remote
$ git config gitreview.remote origin

# Default username used to access the repository
$ git config gitreview.username "USER_NAME"

# Submit changes to the currently-tracked branch by default
$ git config gitreview.track true
```

Run the repo setup commands.
```bash
$ git review -s
```

(Optional) Set default branch to commit.
```bash
$ git config gitreview.defaultbranch BRANCH_NAME
```

## Configure Gerrit

Gerrit treats each review as a `change`, and use `Change-Id` to identify commits that belong to the same review.
This allows a second commit to be updated to the same change to address reviewer's comment.
We can setup a hook to generate change-id automatically after commit.
```bash
$ scp -p -P 29418 user_name@your.gerrit.server:hooks/commit-msg /path/to/local/git/repos/.git/hooks/
$ chmod u+x /path/to/local/git/repos/.git/hooks/commit-msg
```

# Usage

Here lists some frequently used commands.

## Prepeare Changes

Some Gerrit projects may require commits to be submitted with a [sign-off](https://gerrit-review.googlesource.com/Documentation/user-signedoffby.html).
```bash
$ git commit -s
```
## Change Submission

Submit commit to a branch.
If branch is net set, push to default branch.
```bash
$ git review BRANCH_NAME
```

Submit commit to a branch and add reviewers.
```bash
# mail list are seperated by whitespace
$ git review BRANCH_NAME --reviewers mail1 mail2
```

Submit commit to a branch and add topic
```bash
$ git review BRANCH_NAME -t "TOPIC"
```

Submit commit as a draft
```bash
$ git review BRANCH_NAME -D
```

## Update Changes after Submission

If you have created a branch for that change, switch to that branch.
Otherwise, fetch the change from Gerrit.
```bash
$ git review CHANGE_ID
```

## Code Review

List changes waiting for review
```bash
$ git review -l
```

Fetch a change from Gerrit for revirw.
```bash
$ git review -d CHANGE_ID
```

Fetch a specific patchset of a change.
```bash
$ git review -d CHANGE_ID,PATCHSET_NUM
```

# Best Practice

My best practice is that: creating a branch at local for preparing submission and deleting that branch after code was reviewed and merged.

Creating a local branch in advance brings convinence when:
* updating modified commits per reviewers' suggestion, and
* resolving conflicts when merging or rebasing.


# Reference

* [\[OpenStack\] git-review](https://docs.openstack.org/infra/git-review/)
* [Using git-review to push and review changes](https://osm.etsi.org/wikipub/index.php/Using_git-review_to_push_and_review_changes)
* [\[MediaWiki\]Gerrit/git-review](https://www.mediawiki.org/wiki/Gerrit/git-review)
* [Git Review + Gerrit 安装及使用完成 Code-Review](https://blog.csdn.net/aixiaoyang168/article/details/77179008)
* [Debian git-review manpage](https://manpages.debian.org/testing/git-review/git-review.1.en.html)
* [\[Gerrit\] commit-msg Hook](https://gerrit-review.googlesource.com/Documentation/cmd-hook-commit-msg.html)
