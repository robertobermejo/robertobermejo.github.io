---
title: "Hugo Plus Github Equals to Hello World"
date: 2019-09-22T13:05:18+02:00
draft: true
coverMeta: out
tags:
- content
- devops
- hugo
comments: true
---
---

# Objetive

I want to publish some content on my blog using some content generator, and I don't want to hire a hosting. I can use [Wordpress](http://wordpress.com), but I prefere to use some tecnlogies that let me know new ways of publish content.

So, I will use [Hugo](https://gohugo.io/) for this pourpose. There are a lot of tools like [Jekyllrb](https://jekyllrb.com), [Ghost](https://ghost.org), [Grav](https://getgrav.org), [Pelican](http://getpelican.com) and [more alternatives](https://alternativeto.net/software/hugo/).

The last step will be setup Github actions. When I push the editor branch, on master must be published the public content.

## Creating the proyect.

The first thing that I need to to is install the dependencies, prepare the repository and commit the base configuration on the editor branch.

{{< codeblock "shell" >}}
# Install hugo on mac using Brew
brew install hugo
# Create new hugo site
hugo new site Blog
# Initialize git repository
cd Blog & git init
# Add all files for initial commit on editor branch
git checkout -b editor
git add *
git commit -m "Initial commit"
{{< /codeblock >}}
The next step is select a [theme](hugo-tranquilpeak-theme) and create some content (like this post).
{{< codeblock "shell" >}}
# Add theme as git submodule
git submodule add https://github.com/kakawait/hugo-tranquilpeak-theme.git themes/hugo-tranquilpeak
# Copy default configuration and update with my custom parameters
cp themes/tranquilpeak/exampleSite/config.toml .
vim config.toml
# Commit this changes
git add .gitmodules themes/hugo-tranquilpeak config.toml
git commit "Add Hugo Tranquilpeak theme and update configuration"
# Create this post, and add some summary content
hugo new content/post/Hugo-plus-Github-equals-to-Hello-World.md
git add content/
git commit -m "Add first post"
{{< /codeblock >}}

And that's setup Hugo with a first post.