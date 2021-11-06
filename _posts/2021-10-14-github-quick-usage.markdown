---
layout: post
title: Github quick usage
categories: misc
tags: [misc]
---

## Create github repository
On github main page, click "New", fill "Repository name" and choose "Public" or "Private" as you need.
Please add deploy key if you need to push to private repository.  
Go to your-repository-page->Settings->Deploy keys and "Add deploy key"


## Push to github

### Without local repository

You already have a remote repository and want to develop it locally
* git clone git@github.com:successcw/your-repository-name.git
* change or add something
* git commit -a
* git push

### With local repository
You have created a local repository and want to push it on github
In this case, please don't check "Initialize this repository with a README" when create repository on github

#### Local repository work flow
* git init
* add something
* git add "something"
* git commit -m "commit message"
* git checkout -b main master

#### push
* git remote add origin github_repository_url
* git push origin main

## Reference
1.[github][1]  
2.[How to Push Local Git Repo to Remote Repo in GitHub][2]


  [1]: https://docs.github.com/en/github/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line
  [2]: https://jdhao.github.io/2018/05/16/git-push-local-to-remote/
