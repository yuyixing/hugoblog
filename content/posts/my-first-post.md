---
title: "使用Hugo快速搭建静态博客"
date: 2019-07-14T12:49:51+08:00
draft: true
toc: false
images:
tags:
  - go
---

## Auto Deployment with Wercker

https://gohugo.io/hosting-and-deployment/deployment-with-wercker/

You need register wercker account via a VPN probably,If you see the alert 'Please cofirm you are not a robot'.

Wercker is a CI tool via configure wercker.yml.Such like
```yml
box: debian
build:
  steps:
    - arjen/hugo-build:
		theme:            hello-friend-ng		# optional, default: ""
		flags:            --buildDrafts=true	# optional, default: ""
		version:          <string> # optional, default: "0.55.6"
		config:           <string> # optional, default: ""
		basedir:          <string> # optional, default: ""
		dev_flags:        <string> # optional, default: ""
		dev_branches:     <string> # optional, default: ""
		prod_branches:    <string> # optional, default: ""
		force_install:    <string> # optional, default: "false"
		install_pygments: <string> # optional, default: "false"
		clean_before:     <string> # optional, default: "true"
deploy:
  steps:
    - install-packages:
        packages: git ssh-client
    - lukevivier/gh-pages@0.2.1:
        token: $GIT_TOKEN	# GitHub personal access tokens,Configure in Application environment variables
        domain: your.domain
        basedir: public
        repo: your.github.repo
```