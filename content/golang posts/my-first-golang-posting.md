---
title: "How to use Hugo to build a Fast and Flexible Static Blog"
date: 2019-07-14T12:49:51+08:00
draft: true
toc: false
images:
tags:
  - go
  - hugo
---

## Hugo Quick Start
[github.com/gohugoio/hugo](https://github.com/gohugoio/hugo)   
[Quick Start](https://gohugo.io/getting-started/quick-start/)

### Install Hugo
[Binary (Cross-platform)](https://github.com/gohugoio/hugo/releases)    
You don't need to install Go to enjoy Hugo if you grab a precompiled binary!  

Git
```
git clone https://github.com/gohugoio/hugo.git
cd hugo
go install --tags extended
```
Go
```
go get github.com/gohugoio/hugo
cd hugo
go install --tags extended
```
There are two versions of Hugo,**remove** `--tags extended` to install the default version if you don't want/need Sass/SCSS support.   
If you see this `exec: "gcc": executable file not found in %PATH%` warning,install [MinGW](https://nuwen.net/files/mingw/mingw-16.1-without-git.exe) and add **bin** directory of gcc to **Windows Environment Variables**.

### Get dependencies
Because of network issues,I can't use `go get` command to get `golang.org/...` dependencies.But Google create a [mirror repo](https://github.com/golang) on github,you can use Git to get these dependencies,such like:
```
git clone https://github.com/golang/net.git
git clone https://github.com/golang/image.git
git clone https://github.com/golang/sync.git
git clone https://github.com/golang/sys.git
git clone https://github.com/golang/text.git
```
**Notice**:you must manually git clone the repository to `$GOPATH/src/golang.org/x/` directory.

### Create your Blog/Website

Create a New Site.
```
hugo new site yourblogname
```

Add a Content.
```
cd your/blog/root/path
hugo new posts/my-first-post.md
```

Get a Theme with Git,you must delete themes/.git directory after clone finished.
```
cd your/blog/root/path
git clone https://github.com/rhazdon/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
```

Copy the code below to config.toml,and you can configure it whatever you like.
```
baseurl = "/"
languageCode = "en-us"
theme = "hello-friend-ng"

[params]
  dateform        = "Jan 2, 2006"
  dateformShort   = "Jan 2"
  dateformNum     = "2006-01-02"
  dateformNumTime = "2006-01-02 15:04 -0700"

  # Metadata mostly used in document's head
  description = "Homepage and blog by Djordje Atlialp"
  keywords = "homepage, blog, science, informatics, development, programming"
  images = [""]

  # Directory name of your blog content (default is `content/posts`)
  contentTypeName = "posts"
  # Default theme "light" or "dark"
  defaultTheme = "dark"

[languages]
  [languages.en]
    title = "Hello Friend NG"
    subtitle = "A simple theme for Hugo"
    keywords = ""
    copyright = ""
    readOtherPosts = "Read other posts"

    [languages.en.params.logo]
      logoText = "hello friend ng"
      logoHomeLink = "/"
    # or
    #
    # path = "/img/your-example-logo.svg"
    # alt = "Your example logo alt text"

	# You can create a language based menu
    [languages.en.menu]
      [[languages.en.menu.main]]
        identifier = "about"
        name = "About"
        url = "/about"
      [[languages.en.menu.main]]
        identifier = "showcase"
        name = "Showcase"
        url = "/showcase"

# And you can even create generic menu
[menu]
  [[menu.main]]
    identifier = "about"
    name       = "About"
    url        = "/about"
  [[menu.main]]
    identifier = "blog"
    name       = "Blog"
    url        = "/posts"
```

Start the Hugo server with a theme,and generate the drafts.
```
hugo server -t=yourtheme --buildDrats=true
```

Generate the static pages for deploy.
```
hugo
```

## Auto Deployment with Wercker
https://gohugo.io/hosting-and-deployment/deployment-with-wercker/

Notice:You need register wercker account via a VPN probably,If you see the alert 'Please cofirm you are not a robot'.

Wercker is a CI tool via configure wercker.yml.Such like
```yml
box: debian
build:
  steps:
    - arjen/hugo-build:
		theme:            hello-friend-ng		# optional, default: ""
		flags:            --buildDrafts=true	# optional, default: ""
		# the two variables above is enough
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
        repo: yourgithubaccountname/your.github.repo
```