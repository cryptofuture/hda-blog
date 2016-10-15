---
title: Find GitHub user email
tags:
  - github
  - github user email
  - email
date: 2016-05-16
---

I already reported this to GitHub team, and got reply:

> Thanks for the submission. We have reviewed your report and determined that it does not present a security risk. As a result, it is not eligible for reward under the Bug Bounty program. That user has the email address you referenced configured for web edits. The patch file endpoint uses whatever email address was used to make a commit. If a users wants to keep their email address private they must configure both GitHub.com as well as their local Git client as follows: <https://help.github.com/articles/keeping-your-email-address-private/>

But how many people mess with git client configuration? <!--more-->

Most people think that hidden email in github profile settings is enough. But actually it is not. For exp. you can push from your friend laptop, and most likely your git client not configured on every machine you use. And GitHub opens great data mining possibilities to you.  
All you need is to find a pull request from person, and with high chance you will find her/his email soon.

#### How it works

Use `https://patch-diff.githubusercontent.com/raw/github_username/repository_name/pull/pull_number_here.patch` url and if person didn't configured git client, or configured client in non-anonymous way you will probably find her/his email in `From` field.

You can check several or first person's pulls. Sometimes people use work/private email address with git client. And as I already wrote before.

>*GitHub opens great data mining possibilities to you.*

But sadly, this opens great possibilities to spammers too. Just check bitcoin related project for exp., and soon you will end with great bitcoin users email database.

#### How to fix it

You need to configure git client global settings on ever machine you use.
You can use [link](https://help.github.com/articles/keeping-your-email-address-private/) from post begining.   
This is how your `.gitconfig` should look after:

```
[user]
	name = Anonymous
	email = github_username@users.noreply.github.com
```




